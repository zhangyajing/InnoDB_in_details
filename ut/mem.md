# 介绍
在我们编写c代码的时候，有库函数为我们做了封装，提升了开发效率。在引擎中，因为对内存的分配，管理都有自己的一套封装实现。同样对于常见的内存操作，在innodb引擎中也有一套类似的封装.

相关函数:

文件名称|5.7版本|8.0版本
-|-|-
ut0mem.h|180|
ut0mem.ic|318|
ut0mem.cc|204|
|702|

由于这个模块比较简单，我们直接从函数的解读梳理总结：

|函数|注解|备注|
|-|-|-|
|ut_memcpy(void* dest, const void* src, ulint n)|从srt中copy n个字节到dest中去|功能和memcpy一样|
|ut_memmove(void* dest, const void* sour, ulint n)|从src拷贝n个字节到dest|src何dest是允许有overlapping的|
|ut_memcmp(const void* str1, const void* str2, ulint n|比较str1和str2两个字符串的前n个字符是否相等||
|ut_strcpy(char* dest, const char* src|把src都拷贝到dest中||
|ut_strlen(const char* str)|求解字符串的长度||
|ut_strcmp(const char* str1, const char* str2)|比较两个字符串||
|ut_strlcpy(char* dst, const char* src, ulint size)|拷贝src到dst中，但是最多只拷贝size -1 个||
|ut_strlcpy_rev(char* dst, const char* src, ulint size)|和ut_strlcpy函数一样，这个函数是从后面开始复制||
|ut_strcount(const char* s1, const char* s2)|计算s2在s1中出现的次数||
|ut_strreplace(const char* str, const char* s1, const char* s2)|在字符串str中把所有s1都替换为s2||
|ut_str3cat(const char* s1, const char* s2, const char* s3)|把三个字符串s1, s2, s3合并为一个大的字符串||
|ut_raw_to_hex(const void* raw, ulint raw_size, char* hex, ulint hex_size)|把raw binary 二进制数据转换成以NUL结尾的hex十六进制字符串||
|ut_str_sql_format(const char* str, ulint str_len, char* buf, ulint buf_size)|把单引号转义的字符串转变为双引号|在row0row.cc和innobase_raw_format等有使用|

# 小结
可以看到这个子模块相对来说非常的简单，可以理解为就是简单的对库函数的一个封装。不过这里需要注意的是，这其中的函数会涉及到少量的malloc内存分配。这些内存是临时分配的小内存，
跟我们了解的buffer pool内存等需要做一个简单的区分.

