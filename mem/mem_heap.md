# 介绍

|文件名称|5.7|8.0|
|-|-|-|
|mem0mem.cc|481|
|mem0mem.h|517|
|mem0mem.ic|612|
|-|1610|

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

typedef struct mem_block_info_t mem_block_t;
typedef mem_block_t mem_heap_t;

```

通过上面的代码片段，我们知道一个内存堆，它是由一个个的block块组成的，这些block块以双向链表的形式组织在一起，形成了一个堆。其中每个block块的内存结构和堆是一样的，
因此可以简单的把内存堆想象成一个抽象的内存block块集合。

通过这个数据结构，我们知道在innodb引擎中，内存堆总体上来说可以来自两个方向：
1. HEAP_DYNAMIC， 该类型代表内存堆来自malloc/new
2. MEM_HEAP_BUF  该类型代表内存堆的内存来自buffer pool

mem_heap_t 的几个要点知识梳理:
1. 一个mem_block_t 最小空间不小于64字节，标准的大小是8KB, 在非MEM_HEAP_BUFFER模式下分配的空间不大于 page size - 200 字节 (page size 一般为16KB)  TODO? 分析为啥
2. mem_heap_t 有三种类型
   * dynamic
   * buffer pool
   * btr_search

在dynamic模式下都是基于buddy伙伴系统来进行分配的。


|宏名称|注解|备注|
|-|-|-|
|mem_heap_create(size)|这种情况下，默认使用MEM_HEAP_DYNAMIC模式||
|mem_heap_create_typed|和mem_heap_create方式一样，只不过这个宏可以指定mem堆的类型||
||||
||||
||||
||||

函数名称|注解|备注|
|-|-|-|
||||
||||
||||
||||
||||

