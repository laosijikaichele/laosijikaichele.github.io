---
layout: post
title: Netty的autoread介绍
category: 转载
tags: Netty
description: Netty的autoread介绍
---
原文：https://www.cnblogs.com/yuyijq/p/4431798.html
## autoread

在Netty 4里我觉得一个很有用的功能是autoread。autoread是一个开关，如果打开的时候Netty就会帮我们注册读事件(这个需要对NIO有些基本的了解)。当注册了读事件后，如果网络可读，则Netty就会从channel读取数据，然后我们的pipeline就会开始流动起来。那如果autoread关掉后，则Netty会不注册读事件，这样即使是对端发送数据过来了也不会触发读时间，从而也不会从channel读取到数据。那么这样一个功能到底有什么作用呢？

它的作用就是更精确的速率控制。那么这句话是什么意思呢？比如我们现在在使用Netty开发一个应用，这个应用从网络上发送过来的数据量非常大，大到有时我们都有点处理不过来了。而我们使用Netty开发应用往往是这样的安排方式：Netty的Worker线程处理网络事件，比如读取和写入，然后将读取后的数据交给pipeline处理，比如经过反序列化等最后到业务层。到业务层的时候如果业务层有阻塞操作，比如数据库IO等，可能还要将收到的数据交给另外一个线程池处理。因为我们绝对不能阻塞Worker线程，一旦阻塞就会影响网络处理效率，因为这些Worker是所有网络处理共享的，如果这里阻塞了，可能影响很多channel的网络处理。

但是，如果把接到的数据交给另外一个线程池处理就又涉及另外一个问题：速率匹配。

比如现在网络实在太忙了，接收到很多数据交给线程池。然后就出现两种情况：

1\. 由于开发的时候没有考虑到，这个线程池使用了某些无界资源。比如很多人对ThreadPoolExecutor的几个参数不是特别熟悉，就有可能用错，最后导致资源无节制使用，整个系统crash掉。

[![复制代码](http://upload-images.jianshu.io/upload_images/10649427-22451f9b084fd5d0.gif?imageMogr2/auto-orient/strip)](javascript:void(0); "复制代码") 

<pre style="margin-top: 0px; margin-bottom: 0px; white-space: pre-wrap; word-wrap: break-word; font-family: &quot;Courier New&quot; !important; font-size: 12px !important; max-width: 600px;">//比如开始的时候没有考虑到会有这么大量 //这种方式线程数是无界的，那么有可能创建大量的线程对系统稳定性造成影响
Executor executor = Executors.newCachedTheadPool();
executor.execute(requestWorker); //或者使用这个 //这种queue是无界的，有可能会消耗太多内存，对系统稳定性造成影响
Executor executor = Executors.newFixedThreadPool(8);
executor.execute(requestWorker);</pre>

[![复制代码](http://upload-images.jianshu.io/upload_images/10649427-b6238e9e305ae708.gif?imageMogr2/auto-orient/strip)](javascript:void(0); "复制代码") 

2\. 第二种情况就是限制了资源使用，所以只好把最老的或最新的数据丢弃。

<pre style="margin-top: 0px; margin-bottom: 0px; white-space: pre-wrap; word-wrap: break-word; font-family: &quot;Courier New&quot; !important; font-size: 12px !important; max-width: 600px;">//线程池满后，将最老的数据丢弃
Executor executor = new ThreadPoolExecutor(8, 8, 1, TimeUnit.MINUTES, new ArrayBlockingQueue<Runnable>(1000), namedFactory, new ThreadPoolExecutor.DiscardOldestPolicy());</pre>

其实上面两种情况，不管哪一种都不是太合理。不过在Netty 4里我们就有了更好的解决办法了。如果我们的线程池暂时处理不过来，那么我们可以将autoread关闭，这样Netty就不再从channel上读取数据了。那么这样造成的影响是什么呢？这样socket在内核那一层的read buffer就会满了。因为TCP默认就是带flow control的，read buffer变小之后，向对端发送ACK的时候，就会降低窗口大小，直至变成0，这样对端就会自动的降低发送数据的速率了。等到我们又可以处理数据了，我们就可以将autoread又打开这样数据又源源不断的到来了。

这样整个系统就通过TCP的这个负反馈机制，和谐的运行着。那么autoread涉及的网络知识就是，发送端会根据对端ACK时候所携带的advertises window来调整自己发送的数据量。而ACK里的这个window的大小又跟接收端的read buffer有关系。而不注册读事件后，read buffer里的数据没有被消费掉，就会达到控制发送端速度的目的。

不过设计关闭和打开autoread的策略也要注意，不要设计成我们不能处理任何数据了就立即关闭autoread，而我们开始能处理了就立即打开autoread。这个地方应该留一个缓冲地带。也就是如果现在排队的数据达到我们预设置的一个高水位线的时候我们关闭autoread，而低于一个低水位线的时候才打开autoread。不这么弄的话，有可能就会导致我们的autoread频繁打开和关闭。autoread的每次调整都会涉及系统调用，对性能是有影响的。类似下面这样一个代码，在将任务提交到线程池之前，判断一下现在的排队量(注：本文的所有数字纯为演示作用，所有线程池，队列等大小数据要根据实际业务场景仔细设计和考量)。

[![复制代码](http://upload-images.jianshu.io/upload_images/10649427-2c175bcca1a3605f.gif?imageMogr2/auto-orient/strip)](javascript:void(0); "复制代码") 

<pre style="margin-top: 0px; margin-bottom: 0px; white-space: pre-wrap; word-wrap: break-word; font-family: &quot;Courier New&quot; !important; font-size: 12px !important; max-width: 600px;">int highReadWaterMarker = 900; int lowReadWaterMarker = 600;

ThreadPoolExecutor executor = new ThreadPoolExecutor(8, 8, 1, TimeUnit.MINUTES, new ArrayBlockingQueue<Runnable>(1000), namedFactory, new ThreadPoolExecutor.DiscardOldestPolicy()); int queued = executor.getQueue().size(); if(queued > highReadWaterMarker){
    channel.config().setAutoRead(false);
} if(queued < lowReadWaterMarker){
    channel.config().setAutoRead(true);
}</pre>

[![复制代码](http://upload-images.jianshu.io/upload_images/10649427-4ec31d503f265345.gif?imageMogr2/auto-orient/strip)](javascript:void(0); "复制代码") 

但是使用autoread也要注意一件事情。autoread如果关闭后，对端发送FIN的时候，接收端应用层也是感知不到的。这样带来一个后果就是对端发送了FIN，然后内核将这个socket的状态变成CLOSE_WAIT。但是因为应用层感知不到，所以应用层一直没有调用close。这样的socket就会长期处于CLOSE_WAIT状态。特别是一些使用连接池的应用，如果将连接归还给连接池后，一定要记着autoread一定是打开的。不然就会有大量的连接处于CLOSE_WAIT状态。

其实所有异步的场合都存在速率匹配的问题，而同步往往不存在这样的问题，因为同步本身就是带负反馈的。

## isWritable

isWritable其实在上一篇文章已经介绍了一点，不过这里我想结合网络层再啰嗦一下。上面我们讲的autoread一般是接收端的事情，而发送端也有速率控制的问题。Netty为了提高网络的吞吐量，在业务层与socket之间又增加了一个ChannelOutboundBuffer。在我们调用channel.write的时候，所有写出的数据其实并没有写到socket，而是先写到ChannelOutboundBuffer。当调用channel.flush的时候才真正的向socket写出。因为这中间有一个buffer，就存在速率匹配了，而且这个buffer还是无界的。也就是你如果没有控制channel.write的速度，会有大量的数据在这个buffer里堆积，而且如果碰到socket又『写不出』数据的时候，很有可能的结果就是资源耗尽。而且这里让这个事情更严重的是ChannelOutboundBuffer很多时候我们放到里面的是DirectByteBuffer，什么意思呢，意思是这些内存是放在GC Heap之外。如果我们仅仅是监控GC的话还监控不出来这个隐患。

那么说到这里，socket什么时候会写不出数据呢？在上一节我们了解到接收端有一个read buffer，其实发送端也有一个send buffer。我们调用socket的write的时候其实是向这个send buffer写数据，如果写进去了就表示成功了(所以这里千万不能将socket.write调用成功理解成数据已经到达接收端了)，如果send buffer满了，对于同步socket来讲，write就会阻塞直到超时或者send buffer又有空间(这么一看，其实我们可以将同步的socket.write理解为半同步嘛)。对于异步来讲这里是立即返回的。 

那么进入send buffer的数据什么时候会减少呢？是发送到网络的数据就会从send buffer里去掉么？也不是这个样子的。还记得TCP有重传机制么，如果发送到网络的数据都从send buffer删除了，那么这个数据没有得到确认TCP怎么重传呢？所以send buffer的数据是等到接收端回复ACK确认后才删除。那么，如果接收端非常慢，比如CPU占用已经到100%了，而load也非常高的时候，很有可能来不及处理网络事件，这个时候send buffer就有可能会堆满。这就导致socket写不出数据了。而发送端的应用层在发送数据的时候往往判断socket是不是有效的(是否已经断开)，而忽略了是否可写，这个时候有可能就还一个劲的写数据，最后导致ChannelOutboundBuffer膨胀，造成系统不稳定。

所以，Netty已经为我们考虑了这点。channel有一个isWritable属性，可以来控制ChannelOutboundBuffer，不让其无限制膨胀。至于isWritable的实现机制可以参考前一篇。
