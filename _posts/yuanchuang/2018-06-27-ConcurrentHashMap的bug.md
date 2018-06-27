---
layout: post
title: ConcurrentHashMap的bug
category: 原创
tags: ConcurrentHashMap,Java
description: ConcurrentHashMap的bug
---

JDK的源码都没人仔细看吗？我刚开始看JDK-1.8的ConcurrentHashMap的源码，就发现构造函数有问题，给Java提了bug，果然如此。Bug链接：
https://bugs.openjdk.java.net/browse/JDK-8202422
而且assign给了大神Doug Lea:
![](http://upload-images.jianshu.io/upload_images/10649427-b3abf80229424235.jpg)
我在so上也问了这个问题，有人认为这是一个优化技巧，但在逻辑上有一点说不太通，如果增加一个文档说明，把3/2这个计算参数的说明加进去，也勉强可以说得过去。总之，目前来看，确实是有逻辑缺陷的。
SO链接：
https://stackoverflow.com/questions/50083966/bug-parameter-initialcapacity-of-concurrenthashmaps-construct-method
