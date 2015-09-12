---
layout: post
title: Oracle之in与or
category: oracle
keywords: oracle,in,or
---

## In查询转换
in后面可以跟枚举方式、数组、子查询。例如：
- select * from user where userId in (select id from login);
需要注意的是In在Oracle中默认会转换成Or,它们就好比是同义词，这是在提交到优化器之前发生的，叫查询转换，查询转换是自动转换的。但是如果in的值太多，就可能认为or更复杂，Oracle就不查询转换了

## Or扩展
in查询转换为or语句的话，提交到优化器，可能还将or语句再次转换扩展成union语句，例如：
- select * from t where id in (1,2,3) or city in ('shanghai','beijin');
这里有个or语句，优化器根据索引，成本分析等，可能会扩展为union all语句，我们看转换结果：
- select * from t where id in (1,2,3) union all select * from t where city in ('shanghai','beijin');
or在内部转换成了union all了。

## 技巧
- id in (1,2,3,...500,...1000)
如果一个语句in了这么多值对于CPU分析是非常难的，因为id要去比较这1000个值，比较是需要CPU资源的，比如我们有个字段有以下几个值：
- 1，2，2，3，3，3
总共6个值，1个1，2个2，3个3；in (1,2,3)与in (3,2,1)你会怎么写？
比如我的值是3：
- 3 in (1,2,3)
第1次比较，3<>1，第2次比较3<>2，第3次比较才匹配，要比较3次，这里有3个值是3，总共就要比较3*3=9次。
- 3 in (3,2,1)
第1次，3跟3比，发现匹配，就不用比较2了，所以3个3，总共比较了3次。
由此我们可以知道in的值与顺序都是相关的
```
if i = 1 then
	...
elsif i = 2 then
	...
else
	...
end
```
总结：我们在写Sql时，尽量要减少比较次数，减少CPU资源，写in的时候，要将容易的放在前面，什么是容易的，也就是命中率高的值，在上面例子中的字段中有6个值，其中3的值最多，相对也就命中率最高，所以将3写在最前面，这样可以减少比较次数，从而减少CPU资源。







