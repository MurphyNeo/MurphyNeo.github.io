---
layout: post
title: 设计模式(单例模式)
category: java
keywords: java,单例模式,设计模式
---

在单例模式中有两种写法,相信大家一定都能看懂,最需要注意的地方就是其构造函数一定要为private修饰,不能让外面去new.

```java
public class Singleton{
    private final static Singleton singleton = new Singleton();

    private Singleton(){}

    public static Singleton getInstance(){
        return singleton;
    }
}
```

```java
public class Singleton{
    private static Singleton singleton;

    private Singleton(){}

    public static Singleton getInstance(){
        if(singleton == null){
            //防止多个线程到进入到了这个里面,在里面再做一次判断
            synchronized(Singleton.class){
                if(singleton == null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```