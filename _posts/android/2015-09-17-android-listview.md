---
layout: post
title: ListView图片加载
category: android
keywords: android,ListView,缓存机制
---

最近在学习Android开发，自己根据慕课网上的视频教程学习listview，但都讲的比较单一，于是我自己把功能都整合起来做了一个listview类似新闻的列表，包括下拉刷新，上拉加载更多的功能，以及图片异步加载功能。

要实现这样一个功能，我们需要考虑以下几点：

- 1、图片需要异步加载
- 2、图片需要在listview空闲的时候去加载，其它时间加载的话，会占用UI线程
- 3、需要缓存图片
- 4、每次只加载listview可见项的图片

对于像我这样的新手，初次做起来可以很困难，最主要的是要有耐心，慢慢的实现，慢慢的个性，总会完成的。

项目放在了[github](https://github.com/MurphyNeo/Android-ListView
)上，有需要参考的朋友可以看看，可能代码方面并不是让人很满意，还望各路高手指点指点。




