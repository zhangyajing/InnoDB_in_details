# 动态数组（vector）

涉及文件：
```cpp
include/ut0vec.h
include/ut0vec.ic
ut/ut0vec.cc
```

所有内存分配操作都是通过分配器完成的，分配器负责在使用完vector时释放内存。

内存分配器（ib_alloc_t）定义：
|字段|类型|用途|
|-|-|-|
|mem_malloc|ib_mem_alloc_t|malloc函数指针|
|mem_release|ib_mem_free_t|free函数指针|
|mem_resize|ib_mem_resize_t|重置堆大小指针|
|arg|void*|堆句柄，若为非NULL则指向堆实例，若为NULL则表示是系统malloc方式|

vector定义：
|字段|类型|用途|
|-|-|-|
|allocator|ib_alloc_t*|分配器（因一种大小不能适用于所有情况）|
|data|void*|数据元素|
|used|ulint|被使用的数据元素个数|
|total|ulint|已分配的数据元素个数|
|sizeof_value|ulint|一个数据项的大小|


相关函数：
|函数名|说明
|-|-|
|ib_vector_create()|创建指定大小的vector|
|ib_vector_free()|释放vector，确保vector拥有分配器|
|ib_vector_push()|push元素到vector，容量不够时将增加容量|
|ib_vector_pop()|弹出vector尾部的元素|
|ib_vector_remove()|移除指定元素，返回指向“被移除”元素的指针|
|ib_vector_size()|获取vector元素个数|
|ib_vector_resize()|增加vector容量|
|ib_vector_is_empty()|判断vector是否为空|
|ib_vector_get()|获取第n个元素|
|ib_vector_get_const()| TODO Const version of the get n'th element.|
|ib_vector_get_last()|获取最后一个元素，vector需不为空|
|ib_vector_set()|设置第n个元素|
|ib_vector_reset()|重置vector容量为0|
|ib_vector_last()|获取vector最后一个元素|
|ib_vector_last_const()|TODO 获取vector最后一个元素|
|ib_vector_sort()|对vector元素进行排序|
|ib_heap_free()|TODO The default ib_vector_t heap free. Does nothing. |
|ib_heap_malloc()|The default ib_vector_t heap malloc. Uses mem_heap_alloc().|
|ib_heap_resize()|TODO 调整vector堆大小，因无法调整堆的大小，必须将旧ptr中的元素复制到新ptr中|
|ib_heap_allocator_create()|TODO 使用传入的堆创建堆分配器|
|ib_heap_allocator_free()|释放堆分配器|

![IMAGE](/ut/vec.excalidraw.png)

图1 vector内存结构示意图