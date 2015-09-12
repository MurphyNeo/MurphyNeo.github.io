---
layout: post
title: Oracle Sql小技巧(一)
category: oracle
keywords: oracle,sql,技巧
---

## 带边界条件与不带边界条件

我们在写Sql的时候，经常会碰到表达式如：
- i >= 2

- i > 1
如果i是整型的值，两个表达式是表示的一样的功能，但是i>=2是有边界的条件表达式，i>1是无边界的条件表达式，Oracle在评估执行的路径时，有边界的要计算的准确些，所以，能用有边界的尽量用有边界的表达式
between就是有边界的操作符，between相当于>= and <=,小的值一定要写在前面

