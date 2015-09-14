---
layout: post
title: Oracle Sql小技巧(三)
category: oracle
keywords: oracle,sql,技巧
---

## like

在like后面用%号时，%代表0个或者多个字符，但有时，我们字段中本来就有%或者_字符，我们需要将通配字符转换成普通字符，需要用到ESCAPE。
- job_id like '%SA\_%' ESCAPE '\'
用ESCAPE中指定的'\'来跟特殊字符。

## 注意

很多时候我们在写like的时候，通常需要在表达式的最前面用上%，在oacle如果like的最前符号为%号，那么如果这个字段上有索引的话，将会导致索引不可用，需要特别注意，但如果需求中就是有这种要求怎么办呢？比如：
- col like '%abc';
如果要使用索引就需要用到reverse函数索引，就是反转索引，上面语句就相当于：
- reverse(col) like 'cba%'

关于具体函数的用法或者其它需要注意的地方，大家可以自己查找资料，这里只是讲解了一点皮毛，并没有深入，知道有这么一回事。

