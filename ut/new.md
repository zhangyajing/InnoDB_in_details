```cpp
const size_t alloc_max_retries = 60;
```
内存可能因为系统负载的原因不能及时的获取到用户程序希望的内存，在引擎内部，会有重试机制，默认最大的重试次数为60次.

另外为了系统的可观测性，方便用户排查分析，会在Performance schema下面注册对应的内存分配行为:
```cpp
PSI_memory_key	mem_key_ahi;
PSI_memory_key	mem_key_buf_buf_pool;
PSI_memory_key	mem_key_dict_stats_bg_recalc_pool_t;
PSI_memory_key	mem_key_dict_stats_index_map_t;
PSI_memory_key	mem_key_dict_stats_n_diff_on_level;
PSI_memory_key	mem_key_other;
PSI_memory_key	mem_key_row_log_buf;
PSI_memory_key	mem_key_row_merge_sort;
PSI_memory_key	mem_key_std;
PSI_memory_key	mem_key_trx_sys_t_rw_trx_ids;
PSI_memory_key	mem_key_partitioning;
```

为了让分配的内存数据，都可以在pfs中进行注册监控，都会在分配内存的地方把源码中的__FILE__, __LINE__ 等两个宏注册到引擎的库中，方便问题的分析与排查。
至此，我们主要介绍下`ut0new.h`中定义的内存分配器：

```cpp
template<class T>
class ut_allocator {
  explicit ut_allocator(PSI_memory_key key = PSI_NOT_INSTRUMENTED); 

  pointer allocate(size_type n_elements, hint, const char* file = NULL, ..) {
    // 在分配内存的时候,首先是会不断的进行重试
    for(size_t retries = 1; ; retries++) {
        if (set_to_zero) {
            ptr = calloc(1, total_bytes); 
        } else {
            ptr = malloc(total_bytes);  // 可以看到，最终调用的都是malloc向操作系统要内存，只是这个过程中可能会失败，当失败了会进行重试， sleep 1秒后重试
        }

        if (ptr != NULL || retries >= alloc_max_retries) {
            break;  // 如果分配到了内存，或者超过了重试的次数，直接退出
        }
        os_thread_sleep(1000000 /* 1s */);
    }

    // 如果重试了之后，依然没有分配到内存，那么就会打印错误日志，抛出std::bad_alloc 异常，退出
    if (ptr == NULL) {
      // ...
    }
      
    ut_new_pfx_t* pfx = static_cast<ut_new_pfx_t*>(ptr);

    // 注册pfs
    allocate_trace(total_bytes, file, pfx);   // 可以简单的把这样的信息类比内存的cookie信息, 只不过这块主要是给引擎跟踪分析的

    return (reinterpret_cast<pointer>(pfx+1));  // 返回分配的内存
  }


  // 有了分配内存就有释放内存
  void deallocate(pointer ptr, size_type n_elements = 0) {
    // 和allocate相反
    ut_new_pfx_t* pfx = reinterpret_cast<ut_new_pfx_t*>(ptr) - 1;
     
    deallocate_trace(pfx);

    free(pfx);
  }

  // replacement new, 复用之前分配的内存
  void construct(pointer p, const T& val) {
    new(p) T(val);
  }

  // 释放在p内存地址上构建的对象T
  void destroy(pointer p) {
    p->~T();
  }
}
```
在这个内存分配器中，就会记录我们之前定义Key, 可以从整体上看，ut0new.h这个头文件中，基本上是把new作了一个完整的封装，提升了内存的监控，分配内存的安全性，分配内存的效率等。
这些都是我们后续分析mem内存堆，buffer pool的一个基础.
