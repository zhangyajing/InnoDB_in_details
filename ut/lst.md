# 内存双链表
相关文件：
```shell
ut/ut0lst.h
```

通过一系列宏定义实现的内存双链表，以优化代码执行效率。
需要被链接的结构体对象需包含UT_LIST_NODE_T(t）字段。
一个链表节点可能属于两个或多个链表，但是同一时间只在一个链表上。

链表节点（UT_LIST_NODE_T）定义:
|成员|类型|说明|
|-|-|-|
|prev|Type*|指向前继节点的指针，头节点则为NULL|
|next|Type*|指向后继节点的指针，尾节点则为NULL|
|reverse()|函数|反转节点的前后指针

链表基节点（UT_LIST_BASE_NODE_T）定义：
|成员|类型|说明|
|-|-|-|
|count|ulint|链表中节点的个数（不包括基节点）|
|start|Type*|指向链表头节点的指针（空链表则为NULL）|
|end|Type*|执行尾节点的指针（空链表则为空）|
|node_ptr|NodePtr|指向用作链表成员字段的指针|

相关函数：
|函数名|说明|
|-|-|
|UT_LIST_INIT()|构造函数|
|UT_LIST_ADD_FIRST()|添加节点为链表头节点|
|UT_LIST_INSERT_AFTER(LIST, ELEM1, ELEM2)|将节点2添加到节点1之后|
|UT_LIST_REMOVE(LIST, ELEM)|移除节点|
|UT_LIST_GET_PREV(NAME, N)|获取指定节点的前继节点|
|UT_LIST_GET_LEN(BASE)|获取链表中节点个数|
|UT_LIST_GET_FIRST(BASE)|获取链表头节点（空链表则返回NULL）|
|UT_LIST_GET_LAST(BASE)|获取链表尾节点（空链表则返回NULL）|
|UT_LIST_REVERSE(LIST)|反转链表|
|UT_LIST_CHECK(LIST)|检查链表一致性（即检测计数是否正确，通过倒序遍历实现）|
|UT_LIST_MOVE_TO_FRONT(LIST, ELEM)|将指定节点移动到链表头部|






