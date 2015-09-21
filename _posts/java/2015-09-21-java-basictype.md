---
layout: post
title: java基本类型包装类缓存
category: java
keywords: java,基本类型,包装类
---

<table>
	<tr>
		<th>基本数据类型</th>
		<th>包装类</th>
	</tr>
	<tr>
		<td>byte</td>
		<td>Byte</td>
	</tr>
	<tr>
		<td>boolean</td>
		<td>Boolean</td>
	</tr>
	<tr>
		<td>short</td>
		<td>Short</td>
	</tr>
	<tr>
		<td>char</td>
		<td>Character</td>
	</tr>
	<tr>
		<td>int</td>
		<td>Integer</td>
	</tr>
	<tr>
		<td>long</td>
		<td>Long</td>
	</tr>
	<tr>
		<td>float</td>
		<td>Float</td>
	</tr>
	<tr>
		<td>double</td>
		<td>Double</td>
	</tr>
</table>

以上为JAVA中的基本类型与对应的包装类,我们先来看一段源码：

```java
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
        }

        private IntegerCache() {}
    }
```

我们可以看到在Integer中用一个数组对int类型进行了缓存,也就是我们在使用包装类的时候,只要值在它这个范围内都是使用的缓存中的,至于为什么是这么个范围,我想应该这些值是一般情况下常用的。
在包装内中除了Boolean、Float、Double几个外,其它的都有缓存处理,这也是java为了提升性能的方法吧。