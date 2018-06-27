---
layout: post
title: Kubernetes原理简介
category: 转载
tags: Kubernetes
description: Kubernetes原理简介
---

原文：http://valleylord.github.io/post/201601-kubernetes-concepts/
**文章目录**

[1. 操作对象](http://valleylord.github.io/post/201601-kubernetes-concepts/#undefined)
[2. 功能组件](http://valleylord.github.io/post/201601-kubernetes-concepts/#undefined)

Kubernetes是Google开源的容器集群管理系统。它构建Ddocker技术之上，为容器化的应用提供资源调度、部署运行、服务发现、扩容缩容等整一套功能，本质上可看作是基于容器技术的mini-PaaS平台。本文旨在梳理Kubernetes的架构、概念及基本工作流[<sup>1</sup>](http://valleylord.github.io/post/201601-kubernetes-concepts/#fn1)[<sup>2</sup>](http://valleylord.github.io/post/201601-kubernetes-concepts/#fn2)[<sup>3</sup>](http://valleylord.github.io/post/201601-kubernetes-concepts/#fn3)[<sup>4</sup>](http://valleylord.github.io/post/201601-kubernetes-concepts/#fn4)。

| 英文 | 翻译 |
| :-- | :-- |
| Container | 容器 |
| Pod | 容器组 |
| Replication Controller | 复制控制器 |
| Service | 服务 |
| Label | 标签 |
| proxy | 代理 |
| Node | 节点 |
| Kubernetes Master | 主节点 |

## 操作对象

Kubernetes中，可操作的对象有以下一些：

*   Container：容器，即是Docker容器，Kubernetes操作的虚拟机。

*   Pod：容器组。是Kubernetes的基本操作单元，把相关的一个或多个容器构成一个Pod，通常Pod里的容器运行相同的应用。Pod包含的容器运行在同一个Minion(Host)上，看作一个统一管理单元，共享相同的volumes和network namespace/IP和Port空间。

*   Service：服务。也是Kubernetes的基本操作单元，是真实应用服务的抽象，每一个服务后面都有很多对应的容器来支持，通过Proxy的port和服务selector决定服务请求传递给后端提供服务的容器，对外表现为一个单一访问接口，外部不需要了解后端如何运行，这给扩展或维护后端带来很大的好处。

*   Replication Controller：复制控制器。是Pod的复制抽象。确保任何时候Kubernetes集群中有指定数量的pod副本(replicas)在运行， 如果少于指定数量的pod副本(replicas)，Replication Controller会启动新的Container，反之会杀死多余的以保证数量不变。Replication Controller使用预先定义的pod模板创建pods，一旦创建成功，pod 模板和创建的pods没有任何关联，可以修改pod 模板而不会对已创建pods有任何影响，也可以直接更新通过Replication Controller创建的pods。对于利用pod 模板创建的pods，Replication Controller根据label selector来关联，通过修改pods的label可以删除对应的pods。

*   Label：标签。用于区分Pod、Service、Replication Controller的key/value键值对，Pod、Service、 Replication Controller可以有多个label，但是每个label的key只能对应一个value。Labels是Service和Replication Controller运行的基础，为了将访问Service的请求转发给后端提供服务的多个容器，正是通过标识容器的labels来选择正确的容器。同样，Replication Controller也使用labels来管理通过pod 模板创建的一组容器，这样Replication Controller可以更加容易，方便地管理多个容器，无论有多少容器。

*   Proxy：代理。是为了解决外部网络能够访问跨机器集群中容器提供的应用服务而设计的。Proxy提供TCP/UDP sockets的proxy，每创建一种Service，Proxy主要从etcd获取Services和Endpoints的配置信息，或者也可以从file获取，然后根据配置信息在Minion上启动一个Proxy的进程并监听相应的服务端口，当外部请求发生时，Proxy会根据Load Balancer将请求分发到后端正确的容器处理。

## 功能组件

如下图所示是官方文档里的集群架构图，一个典型的master/slave模型。

[![image](http://upload-images.jianshu.io/upload_images/10649427-00a3647e67dae474.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](http://valleylord.github.io/images/201601-kubernetes-concepts/kubernetes-masterslave.png) 

master运行三个组件：

*   apiserver：作为kubernetes系统的入口，封装了核心对象的增删改查操作，以RESTFul接口方式提供给外部客户和内部组件调用。它维护的REST对象将持久化到etcd（一个分布式强一致性的key/value存储）。

*   scheduler：负责集群的资源调度，为新建的Pod分配机器。这部分工作分出来变成一个组件，意味着可以很方便地替换成其他的调度器。

*   controller-manager：负责执行各种控制器，目前有两类：

1.  endpoint-controller：定期关联service和Pod(关联信息由endpoint对象维护)，保证service到Pod的映射总是最新的。
2.  replication-controller：定期关联replicationController和Pod，保证replicationController定义的复制数量与实际运行Pod的数量总是一致的。

slave(称作minion)运行两个组件：

*   kubelet：负责管控docker容器，如启动/停止、监控运行状态等。它会定期从etcd获取分配到本机的Pod，并根据Pod信息启动或停止相应的容器。同时，它也会接收apiserver的HTTP请求，汇报Pod的运行状态。

*   proxy：负责为Pod提供代理。它会定期从etcd获取所有的service，并根据service信息创建代理。当某个客户Pod要访问其他Pod时，访问请求会经过本机proxy做转发。

* * *

1.  [http://www.csdn.net/article/2014-10-31/2822393](http://www.csdn.net/article/2014-10-31/2822393)[↩](http://valleylord.github.io/post/201601-kubernetes-concepts/#fnref1)

2.  [http://blog.csdn.net/zhangjun2915/article/details/40598151](http://blog.csdn.net/zhangjun2915/article/details/40598151)[↩](http://valleylord.github.io/post/201601-kubernetes-concepts/#fnref2)

3.  [http://www.infoq.com/cn/articles/Kubernetes-system-architecture-introduction](http://www.infoq.com/cn/articles/Kubernetes-system-architecture-introduction)[↩](http://valleylord.github.io/post/201601-kubernetes-concepts/#fnref3)

4.  [http://www.csdn.net/article/2015-06-11/2824933](http://www.csdn.net/article/2015-06-11/2824933)[↩](http://valleylord.github.io/post/201601-kubernetes-concepts/#fnref4)
