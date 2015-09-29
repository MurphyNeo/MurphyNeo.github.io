---
layout: post
title: 设计模式(观察者模式)
category: java
keywords: java,观察者模式,设计模式
---

观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。
这个主题对象在状态上发生变化时，会通知所有观察者对象，让它们能够自动更新自己。

实现案例：

```java
//抽象观察者角色
public interface Watcher
{
    public void update(String str);
}
```

```java
//抽象主题角色，watched：被观察
public interface Watched
{
    public void addWatcher(Watcher watcher);

    public void removeWatcher(Watcher watcher);

    public void notifyWatchers(String str);

}
```

```java
public class ConcreteWatcher implements Watcher
{
    @Override
    public void update(String str)
    {
        System.out.println(str);
    }
}
```

```java
public class ConcreteWatched implements Watched
{
    // 存放观察者
    private List<Watcher> list = new ArrayList<Watcher>();

    @Override
    public void addWatcher(Watcher watcher)
    {
        list.add(watcher);
    }

    @Override
    public void removeWatcher(Watcher watcher)
    {
        list.remove(watcher);
    }

    @Override
    public void notifyWatchers(String str)
    {
        // 自动调用实际上是主题进行调用的
        for (Watcher watcher : list)
        {
            watcher.update(str);
        }
    }
}
```

```java
public class Test
{
    public static void main(String[] args)
    {
        Watched girl = new ConcreteWatched();
        
        Watcher watcher1 = new ConcreteWatcher();
        Watcher watcher2 = new ConcreteWatcher();
        Watcher watcher3 = new ConcreteWatcher();
        
        girl.addWatcher(watcher1);
        girl.addWatcher(watcher2);
        girl.addWatcher(watcher3);
        
        girl.notifyWatchers("开心");
    }

}
```




