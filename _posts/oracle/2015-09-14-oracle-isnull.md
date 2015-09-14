---
layout: post
title: Oracle null的运用
category: oracle
keywords: oracle,sql,null
---

懂一点Oracle的朋友应该都知道null这个值，是为空的意思。
null值是不能做等式操作的，就远不能跟任何值相等，空值的操作包括is null和is not null,不能用＝符号。
但是有个例外：
- update t set c = null;
这里＝不是比较符，这里的＝是赋值语句。
空值意味着该值不可用、未指派、未知的、无效或不适用，它不同于0或者空格，因为空值无法等于或者不等于任何值。
null在成本运算时容易算错，大家要注意，优化器对null值的计算容易不准，另外一个是null在中间字段和最后一个字段的存储方式是不同的。

## 注意
有时候我们在使用not in的时候，中间可能会出现空值，比如：
- city not in ('北京','上海');
oracle会转换成city <> '北京'  and city <> '上海'，但如果是这样：
- city not in ('北京','上海',null);
会转换成city <> '北京'  and city <> '上海' and city <> null
由于null不能做等式操作，所以city <> null结果为null，然后也其它两个用and连接，最后整个表达式的结果就为null，这样就会导致整个结果为null。
所以not in里只要有null值，将不会有任何结果。