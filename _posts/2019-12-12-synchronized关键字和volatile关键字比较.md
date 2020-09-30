---
layout:     post                    # 使用的布局（不需要改）
title:      synchronized关键字和volatile关键字比较               # 标题 
subtitle:   doDispatcher,HandlerInterceptor #副标题
date:       2019-12-12              # 时间
author:     cl                      # 作者
header-img: img/post-bg-unix-linux.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 技术
---

## synchronized关键字和volatile关键字比较:
volatile关键字是线程同步的轻量级实现，所以volatile性能肯定比synchronized关键字要好。但是volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块。synchronized关键字在JavaSE1.6之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁以及其它各种优化之后执行效率有了显著提升，实际开发中使用 synchronized 关键字的场景还是更多一些。
多线程访问volatile关键字不会发生阻塞，而synchronized关键字可能会发生阻塞
volatile关键字能保证数据的可见性，但不能保证数据的原子性。synchronized关键字两者都能保证。
volatile关键字主要用于解决变量在多个线程之间的可见性，而 synchronized关键字解决的是多个线程之间访问资源的同步性。
![Image text](http://47.103.195.194/img/1.jpg)


