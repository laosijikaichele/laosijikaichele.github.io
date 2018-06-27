---
layout: post
title: synchronized的访问控制
category: 转载
tags: Java,多线程,synchronized
description: synchronized的访问控制
---
原文：https://blog.csdn.net/lynn_Kun/article/details/73850223  
当一个线程进入一个对象的一个synchronized方法后，其它线程是否可进入此对象的其它方法?   
1、一个线程在访问一个对象的同步方法时，另一个线程可以同时访问这个对象的非同步方法。   
2、一个线程在访问一个对象的同步方法时，另一个线程不能同时访问这个同步方法。   
3、一个线程在访问一个对象的同步方法时，另一个线程不能同时访问这个对象的另一个同步方法。   
在java中每个类对应一把锁，每个对象对应一把锁。   
分几种情况：   
1.其他方法前是否加了synchronized关键字，如果没加，则能。   
2.如果这个方法内部调用了wait，则可以进入其他synchronized方法。   
3.如果其他个方法都加了synchronized关键字，并且内部没有调用wait，则不能。   
4.如果其他方法是static，它用的同步锁是当前类的字节码，与非静态的方法不能同步，因为非静态的方法用的是this。（线程1调用非静态的synchronized方法，线程2调用静态的synchronized方法，两者互不影响。 注：如果线程1和2，同为非静态或静态，则必须等一个线程执行完，再执行下一个。）  
