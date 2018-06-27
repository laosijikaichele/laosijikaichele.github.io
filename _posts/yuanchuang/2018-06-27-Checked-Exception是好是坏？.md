---
layout: post
title: Checked Exception是好是坏？
category: 原创
tags: Checked Exception,Java
description: Checked Exception是好是坏？
---

[原文](https://zhuanlan.zhihu.com/p/38533850)

经典书籍《Clean Code》里提倡避免使用Checked Exception，给出的原因大致是：

> Checked Exception违反了开闭原则，底层方法签名增加throws异常声明后，会引起整个调用链的修改。

这句话表述是有问题的，底层方法增加了throws异常声明，只会影响调用该方法的函数，在调用该方法的函数内捕获异常并进行处理就可以了，不会引起整个调用链的修改。

当然调用者也可能不处理异常，而只是简单的把异常向上抛出，但这种把异常简单向上抛出的行为是不好的编程习惯，是应该被避免的，不应该成为不使用Checked Exception的理由。

王垠大大对Checked Exception有很好的分析，参见：

[Kotlin 和 Checked Exception​www.yinwang.org](http://www.yinwang.org/blog-cn/2017/05/23/kotlin)
