# 内存堆涉及的代码

```
                    5.7                     8.0
 mem0mem.cc         481
 mem0mem.h          517
 mem0mem.ic         612
```

![内存堆数据结构](./mem_heap.excalidraw.png)

内存堆核心数据结构
```cpp
struct mem_block_info_t
{
    ulint magic_n;
    char file_name[8];  // 分配内存的文件
    ulint line;         // 分配内存的文件所在行
    UT_LIST_BASE_NODE_T(mem_block_t) base;  // 头节点
    UT_LIST_NODE_T(mem_block_t) list;  // 基础的已分配内存列表
    ulint type;  // 依赖底层分配类型 HEAP_DYNAMIC, MEM_HEAP_BUF, MEM_HEAP_BTR_SEARCH
    ulint free;  // 第一个空闲的地址
    ulint start;  // 第一次创建的时候的free地址
    void* free_block;  备用block, 仅仅在BTR_SEARCH方式可用
}
```

通过这个数据结构，我们知道在innodb引擎中，内存堆总体上来说可以来自两个方向：
1. HEAP_DYNAMIC， 该类型代表内存堆来自malloc/new
2. MEM_HEAP_BUF  该类型代表内存堆的内存来自buffer pool

在代码中，内存堆的内存布局可以参考上图。

```cpp
typedef struct mem_block_info_t mem_block_t;
typedef mem_block_t mem_heap_t;
```

内存堆的数据结构mem_block_info_t， 对于其中的一个block，其类型跟mem_heap_t 代表的内存堆的内存布局是一样的。在内部通过双向链表来把内存堆中的
mem_block_t 串联起来。

```cpp
UT_LIST_BASE_NODE(mem_block_t) // 双向链表的base节点
ut_LIST_NODE_T(mem_block_t) list // 双向链表节点
```

mem_heap_t 的几个要点知识梳理:
1. 一个mem_block_t 最小空间不小于64字节，标准的大小是8KB, 在非MEM_HEAP_BUFFER模式下分配的空间不大于 page size - 200 字节 (page size 一般为16KB)
2. mem_heap_t 有三种类型
   * dynamic
   * buffer pool
   * btr_search

在dynamic模式下都是基于buddy伙伴系统来进行分配的。

