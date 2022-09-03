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

