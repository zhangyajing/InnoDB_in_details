# 介绍
在我们编写c代码的时候，有库函数为我们做了封装，提升了开发效率。在引擎中，因为对内存的分配，管理都有自己的一套封装实现。同样对于常见的内存操作，在innodb引擎中也有一套类似的封装.

相关函数:

文件名称|5.7版本|8.0版本
-|-|-
ut0mem.h|180|
ut0mem.ic|318|
ut0mem.cc|204|

由于这个模块比较简单，我们直接从函数的解读梳理总结：

函数|注解|备注
-|-|
ut_memcpy(void* dest, const void* src, ulint n)|从srt中copy n个字节到dest中去|功能和memcpy一样
ut_memmove(void* dest, const void* sour, ulint n)|从src拷贝n个字节到dest|src何dest是允许有overlapping的
||
||
||
||
||
||
