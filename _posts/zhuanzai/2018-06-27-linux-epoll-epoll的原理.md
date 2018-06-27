---
layout: post
title: Linux-epoll的原理
category: 转载
tags: linux,epoll
description: Linux-epoll的原理
---

原文：http://www.gonglin91.com/linux-epoll-epoll%E7%9A%84%E5%8E%9F%E7%90%86%EF%BC%9Bstruct-epoll_event-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E8%BF%99%E6%A0%B7%E8%AE%BE%E8%AE%A1/

### epoll原理简述：epoll = 一颗红黑树 + 一张准备就绪句柄链表 + 少量的内核cache

1.  select/poll 每次调用时都要传递你所要监控的所有socket给select/poll系统调用，这意味着需要将用户态的socket列表copy到内核态，如果以万计的句柄会导致每次都要copy几十几百KB的内存到内核态，非常低效。
2.  调用epoll_create后，内核就已经在内核态开始准备帮你存储要监控的句柄了，每次调用epoll_ctl只是在往内核的数据结构里塞入新的socket句柄。
3.  epoll向内核注册了一个文件系统，用于存储上述的被监控socket。当调用epoll_create时，就会在这个虚拟的epoll文件系统里创建一个file结点。当然这个file不是普通文件，它只服务于epoll。
4.  epoll在被内核初始化时（操作系统启动），同时会开辟出epoll自己的内核高速cache区，用于安置每一个我们想监控的socket，这些socket会以红黑树的形式保存在内核cache里，以支持快速的查找、插入、删除。
    这个内核高速cache区，就是建立连续的物理内存页，然后在之上建立slab层，简单的说，就是物理上分配好你想要的size的内存对象，每次使用时都是使用空闲的已分配好的对象。
5.  调用epoll_create时，内核除了帮我们在epoll文件系统里建了个file结点，在内核cache里建了个红黑树用于存储以后epoll_ctl传来的socket外，还会再建立一个list链表，用于存储准备就绪的事件，当epoll_wait调用时，仅仅观察这个list链表里有没有数据即可。有数据就返回，没有数据就sleep，等到timeout时间到后即使链表没数据也返回。
6.  通常情况下即使我们要监控百万计的句柄，大多一次也只返回很少量的准备就绪句柄而已，所以，epoll_wait仅需要从内核态copy少量的句柄到用户态而已
7.  这个准备就绪list链表是怎么维护的呢？当我们执行epoll_ctl时，除了把socket放到epoll文件系统里file对象对应的红黑树上之外，还会给内核中断处理程序注册一个回调函数，告诉内核，如果这个句柄的中断到了，就把它放到准备就绪list链表里。

所以，当一个socket上有数据到了，内核在把网卡上的数据copy到内核中后就来把socket插入到准备就绪链表里了。

### [](http://www.gonglin91.com/linux-epoll-epoll%E7%9A%84%E5%8E%9F%E7%90%86%EF%BC%9Bstruct-epoll_event-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E8%BF%99%E6%A0%B7%E8%AE%BE%E8%AE%A1/#%E3%80%90%E8%BD%AC%E3%80%91epoll%E7%9A%84%E4%B8%A4%E7%A7%8D%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F%EF%BC%9A "【转】epoll的两种工作模式：")【转】epoll的两种工作模式：

Epoll的2种工作方式-水平触发（LT）和边缘触发（ET）

假如有这样一个例子：

1.  我们已经把一个用来从管道中读取数据的文件句柄(RFD)添加到epoll描述符
2.  这个时候从管道的另一端被写入了2KB的数据
3.  调用epoll_wait(2)，并且它会返回RFD，说明它已经准备好读取操作
4.  然后我们读取了1KB的数据
5.  调用epoll_wait(2)……

#### [](http://www.gonglin91.com/linux-epoll-epoll%E7%9A%84%E5%8E%9F%E7%90%86%EF%BC%9Bstruct-epoll_event-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E8%BF%99%E6%A0%B7%E8%AE%BE%E8%AE%A1/#Edge-Triggered-%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F%EF%BC%9A "Edge Triggered 工作模式：")Edge Triggered 工作模式：

如果我们在第1步将RFD添加到epoll描述符的时候使用了EPOLLET标志，那么在第5步调用epoll_wait(2)之后将有可能会挂起，因为剩余的数据还存在于文件的输入缓冲区内，而且数据发出端还在等待一个针对已经发出数据的反馈信息。只有在监视的文件句柄上发生了某个事件的时候 ET 工作模式才会汇报事件。因此在第5步的时候，调用者可能会放弃等待仍在存在于文件输入缓冲区内的剩余数据。在上面的例子中，会有一个事件产生在RFD句柄上，因为在第2步执行了一个写操作，然后，事件将会在第3步被销毁。因为第4步的读取操作没有读空文件输入缓冲区内的数据，因此我们在第5步调用 epoll_wait(2)完成后，是否挂起是不确定的。epoll工作在ET模式的时候，必须使用非阻塞套接口，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。最好以下面的方式调用ET模式的epoll接口，在后面会介绍避免可能的缺陷。

1.  基于非阻塞文件句柄
2.  只有当read(2)或者write(2)返回EAGAIN时才需要挂起，等待。但这并不是说每次read()时都需要循环读，直到读到产生一个EAGAIN才认为此次事件处理完成，当read()返回的读到的数据长度小于请求的数据长度时，就可以确定此时缓冲中已没有数据了，也就可以认为此事读事件已处理完成。

#### [](http://www.gonglin91.com/linux-epoll-epoll%E7%9A%84%E5%8E%9F%E7%90%86%EF%BC%9Bstruct-epoll_event-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E8%BF%99%E6%A0%B7%E8%AE%BE%E8%AE%A1/#Level-Triggered-%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F%EF%BC%9A "Level Triggered 工作模式：")Level Triggered 工作模式：

相反的，以LT方式调用epoll接口的时候，它就相当于一个速度比较快的poll(2)，并且无论后面的数据是否被使用，因此他们具有同样的职能。因为即使使用ET模式的epoll，在收到多个chunk的数据的时候仍然会产生多个事件。调用者可以设定EPOLLONESHOT标志，在 epoll_wait(2)收到事件后epoll会与事件关联的文件句柄从epoll描述符中禁止掉。因此当EPOLLONESHOT设定后，使用带有

EPOLL_CTL_MOD标志的epoll_ctl(2)处理文件句柄就成为调用者必须作的事情。

#### [](http://www.gonglin91.com/linux-epoll-epoll%E7%9A%84%E5%8E%9F%E7%90%86%EF%BC%9Bstruct-epoll_event-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E8%BF%99%E6%A0%B7%E8%AE%BE%E8%AE%A1/#struct-epoll-event "struct epoll_event")struct epoll_event
```
typedef union epoll_data {
 void *ptr;
 int fd;
 __uint32_t u32;
 __uint64_t u64;
} epoll_data_t;
 //感兴趣的事件和被触发的事件
struct epoll_event {
 __uint32_t events; /* Epoll events */
 epoll_data_t data; /* User data variable */
};
```
注意epoll_data是个union而不是struct，两者的区别: [http://www.gonglin91.com/cpp-struct-union/](http://www.gonglin91.com/cpp-struct-union/)

所以当我们在epoll中为一个文件注册一个事件时，如果不需要什么额外的信息，只需要简单在epoll_data.fd = 当前文件的fd

**（尽管这并不是必须的，在网上找到的几乎所有的博客都是这样写，呵呵）**

但是当我们需要一些额外的数据的时候，就要用上void* ptr;

我们可以自定义一种类型，例如my_data，然后让ptr指向它，以后取出来的时候就能用了；

**最后，记住union的特性，它只会保存最后一个被赋值的成员，所以不要data.fd,data.ptr都赋值；通用的做法就是给ptr赋值，fd只是有些时候为了演示或怎么样罢了**

所以个人的想法是，其实epoll_data完全可以就是一个void*，不过可能为了一些简单的场景，才设计成union，包含了fd，u32，u64
