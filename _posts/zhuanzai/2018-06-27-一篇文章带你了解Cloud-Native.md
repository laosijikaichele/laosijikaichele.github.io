---
layout: post
title: 了解Cloud-Native
category: 转载
tags: Cloud-Native
description: 了解Cloud-Native
---

原文：http://dockone.io/article/812
【编者的话】主要讲述Cloud Native概念，背景； Twelve-Factor，康威定律；如何推行Cloud Native；以及VIP的Cloud Native的实践。

***[@Container容器技术大会](http://atcontainer.com/)将于2016年1月24日在北京举行，来自爱奇艺、微博、腾讯、去哪儿网、美团云、京东、蘑菇街、惠普、暴走漫画等知名公司的技术负责人将分享他们的容器应用案例。***

### 背景

Cloud Native表面看起来比较容易理解，但是细思好像又有些模糊不清：Cloud Native和Cloud关系是啥？它用来解决什么问题？它是一个新技术还是一个新的方法？什么样的APP符合“云原生”的呢？等等。下面将会一一解读。

### Cloud Native介绍

Cloud Native是Matt Stine提出的一个概念，它是一个思想的集合，包括DevOps、持续交付（Continuous Delivery）、微服务（MicroServices）、敏捷基础设施（Agile Infrastructure）、康威定律（Conways Law）等，以及根据商业能力对公司进行重组。

可以说，Cloud Native即包含技术（微服务，敏捷基础设施），也包含管理（DevOps，持续交付，康威定律，重组等）。Cloud Native也可以说是一系列Cloud技术、企业管理方法的集合。

### Cloud Native的价值

企业采用基于Cloud Native的技术和管理方法，可以更好的把业务迁移到云平台，从而享受云的高效和按需资源能力。

### Cloud Native和传统Cloud的关系

Cloud Native的技术部分是建筑在传统Cloud的三层（IaaS、PaaS、SaaS）概念之上的：

*   敏捷基础设施对应IaaS部分。
*   微服务则可以对应PaaS和SaaS部分。

当然，Cloud Native比传统Cloud 多了一些企业管理方法。

> 备注：Cloud Native从技术上更强调敏捷基础设施和微服务的概念，这并不意味着它是抛开IaaS、PaaS、SaaS而另起炉灶的。

[![01.png](http://upload-images.jianshu.io/upload_images/10649427-68624f17b3748c17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "01.png")](http://dockone.io/uploads/article/20151111/1d0cb7b7b54582167345bc4e42670b0b.png) 

### Cloud Native的五个层面：

（1）**康威定律**：业务云化推行，从某种意义上讲也是一种变革。既然是变革，必然会涉及组织的各个层面，开发、质量、运维等等都会涉及。康威定律则准确的描述了系统架构和组织的关系：组织决定系统架构！

一个云系统最终长成什么样子，则完全是企业的组织结构决定的，是组织内部、组织之间的沟通结构。

如果要想得到一个合理的Cloud架构，仅从技术入手是不够的，还需要从组织架构入手，才真正有效。

[![02.png](http://upload-images.jianshu.io/upload_images/10649427-0d77cac931541e9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "02.png")](http://dockone.io/uploads/article/20151111/222fe64965b44761ff030b897b23411a.png) 

（2）**DevOps**：（英文Development和Operations的组合）是一组过程、方法与系统的统称，用于促进开发（应用程序/软件工程）、运维和质量保障（QA）部门之间的沟通、协作与整合。它的出现是由于软件行业日益清晰地认识到：为了按时交付软件产品和服务，开发和运维必须紧密合作。

（3）**持续交付（Continuous Delivery）**：是一系列的开发实践方法，用来确保让代码能够快速、安全的部署到产品环境中，它通过将每一次改动都提交到一个模拟产品环境中，使用严格的自动化测试，确保业务应用和服务能符合预期。因为使用完全的自动化过程来把每个变更自动的提交到测试环境中，所以当业务开发完成时，你有信心只需要按一次按钮就能将应用安全的部署到产品环境中。持续交付可以采用：CI（持续集成）、代码检查、UT（单元测试），持续部署等方式，打通开发、测试、生产的各个环节，持续的增量的交付产品。

（4）**微服务（MicroServices）**：微服务首先是一个服务，其次该服务的颗粒比较小。微服务可以采用Docker、LXC等技术手段实现。

（5）**敏捷基础设施（Agile Infrastructure）**：提供弹性、按需的计算、存储、网络资源能力。可以通过Openstack、KVM、Ceph、OVS等技术手段实现。

### Cloud Native兴起的背后诉求

*   更快的上线速度
*   细致的故障探测和发现
*   故障时能自动隔离
*   故障时能够自动恢复
*   方便的水平扩容

### Cloud Native可以解决上面的诉求：

*   持续交付、DevOps、微服务解决-->更快的上线速度
*   微服务解决-->细致的故障探测和发现
*   微服务解决-->故障时能自动隔离
*   敏捷基础设施、微服务解决-->故障时能够自动恢复
*   敏捷基础设施、微服务解决-->方便的水平扩容

### 如何推行Cloud Native

推行Cloud Native可以从如下几方面入手：

*   组织变革：根据康威定律，如果要达到比较理想的云化效果，必须进行组织变革。一个合理的组织架构，将会极大提高云化的推行。相信很多公司，在决定搞云计算后，都大大小小进行了部门合并和组织结构调整。这块水比较深，相信很多人有变革的切肤之痛。
*   推行DevOps文化：在公司层面推行DevOps文化，倡导开放、合作的组织文化，打破“部门墙”。
*   推行持续交付：联合开发、质量、运维各个环节，打通代码提交、代码检查、UT、开发环境部署、staging环境部署、线上环境部署等流水线。
*   建设敏捷基础设施：这部分是整个Cloud Native的根基。这部分可以采纳的技术非常多，开源的、商用的都比较多。
*   采用微服务架构：微服务架构是Cloud Native的一个核心要素。微服务包含几方面的内容： （1）有支撑微服务的平台。
    （2）有符合微服务平台规范的APP。
    （3）如何引入微服务。
    （4）微服务核心技术点。

### 微服务的4个方面

#### （1）有支撑微服务的平台

支撑微服务的可选技术框架比较多，每个公司都可以根据自身情况选择合适的技术框架。比如：

*   Kubernetes
*   Mesos+Docker
*   OpenShift V3
*   Machine + Swarm + Compose
*   OpenStack + Docker
*   Cloud Foundry Lattice 其他技术等等

这方面的资料非常多，不再细讲。

#### （2）有符合微服务平台规范的APP

APP要符合12因子（Twelve-Factor）的规范：

*   基准代码（Codebase）：代码必须纳入配置库统一管理。
*   依赖（Dependencies）：显式的声明对其他服务的依赖，比如通过Maven、Bundler、NPM等。
*   配置（Config）：对于不同环境（开发/staging/生产等）的参数配置，是通过环境变量的方式进行注入。
*   后台服务（Backing services）：对于DB、缓存等后台服务，是作为附加资源，可以独立的Bind/Unbind。
*   编译/发布/运行（Build、Release、Run）：Build、Release、Run这三个阶段要清晰的定义和分开。
*   无状态进程（Processes）：App的进程是无状态的，任何状态信息都存储到Backing services（DB，缓存等）。
*   端口绑定（Port binding）：App是自包含的，所有对外服务通过Port Binding暴露，比如通过Http。
*   并发（Concurrency）：App可以水平的Scaling。
*   快速启动终止（Disposability）：App进程可以被安全的、快速的关闭和重启。
*   环境一致性（Dev/prod parity）：尽可能的保持开发、staging、线上环境的一致性。
*   日志（Logs）：把日志作为事件流，不管理日志文件，通过一个集中的服务，由执行环境去收集、聚合、索引、分析日志事件。

#### （3）如何引入微服务

直接对原有系统进行微服务改造，是比较困难的，几乎是不现实的。比较合理的方法是新系统采用微服务开发，对老系统进行服务封装，系统架构如下所示：

[![03.png](http://upload-images.jianshu.io/upload_images/10649427-8f8010af8f2db3ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "03.png")](http://dockone.io/uploads/article/20151111/d8d1d94734b57c3a92c135a130134996.png) 

Monolith系统：对应企业老的系统。

The Anti-Corruption Layer：反腐层，这层完成对老系统的桥接，并阻止老系统的腐烂蔓延。它包含三部分：

1.  Facade：简化对老系统接口的对接。
2.  Adapter：Request，Response 请求协议适配
3.  Translator：领域模型适配，转换微服务模型和老系统模型。

新系统（微服务）：新系统开发采用微服务架构。

#### （4）微服务核心技术点

主要包含如下几个方面：版本控制的分布式配置中心、服务注册和发现、路由和LB、容错、API网关/边缘服务。

##### （4.1）版本控制的分布式配置中心

支持配置信息版本化控制，可审计，安全，配置更新不需要重启，分布式。这部分可以参考Spring Cloud Config Server、Spring Cloud Bus。

[![04.png](http://upload-images.jianshu.io/upload_images/10649427-2023460a0079e43c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "04.png")](http://dockone.io/uploads/article/20151111/f6475377da327e17f58822bdc24690f6.png) 

1：用户提交配置更新
2：配置server更新
3：对APP A发起Refresh更新配置操作
4a：发送消息到Bus
4b：Pull 更新的配置
5a：APP C接收消息
5b：Pull更新的配置
6：其他节点同步更新

##### （4.2）服务注册和发现

[![05.png](http://upload-images.jianshu.io/upload_images/10649427-68a2a80c98fd7464.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "05.png")](http://dockone.io/uploads/article/20151111/763a5b502eeb82de6125b162b2d46220.png) 

这个是传统的服务注册和发现架构图。服务注册方式，常见的包括DNS，基于ZooKeeper的服务注册方案等等。

> 备注：Consumer和Producer之间一般还有个LB。

##### （4.3）路由和LB

[![06.png](http://upload-images.jianshu.io/upload_images/10649427-d3d4b33ff406bcbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "06.png")](http://dockone.io/uploads/article/20151111/a88e7819519775c2c9ca51a735f17bf2.png) 

##### （4.4）容错

介绍两种模式：
（A）电路熔断器（Circuit Breaker）： 该模式的原理类似于电路熔断器，如果电路发生短路，熔断器能够主动熔断电路，以避免灾难性损失

[![07.png](http://upload-images.jianshu.io/upload_images/10649427-9ee292298bf2fd25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "07.png")](http://dockone.io/uploads/article/20151111/1ec2c3f576fee6b4347a710c9c34496e.png) 

1.  正常状态下，电路处于关闭状态（Closed），调用是直接传递给依赖服务的；
2.  如果调用出错，则进入失败计数状态；
3.  失败计数达到一定阈值后，进入熔断状态（Open），这时的调用总是返回失败；
4.  累计一段时间以后，保护器会尝试进入半熔断状态（Half-Open）；
5.  处于Harf-Open状态时，调用先被传递给依赖的服务，如果成功，则重置电路状态为“Closed”，否则把电路状态置为“Open”；

（B）舱壁（Bulkheads）：该模式像舱壁一样对资源或失败单元进行隔离，如果一个船舱破了进水，只损失一个船舱，其它船舱可以不受影响 。

这种模式比较常见的思路为：

1.  采用微服务是首选，比如Docker。Docker是进程隔离的，单个Docker失效不会影响其他Docker容器。
2.  把大的并行处理工作，由多个线程池来负荷分担。

##### （4.5）API网关/边缘服务

[![08.png](http://upload-images.jianshu.io/upload_images/10649427-1d65c207f288b84a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "08.png")](http://dockone.io/uploads/article/20151111/d43b27b29e23860480c691c75b25e166.png) 

API Gateway：

1.  对设备侧（PC,Mobile等）提供简化的单一服务接口；
2.  它内部聚合后台几十甚至上百微服务。

> 价值：它的主要作用是简化设备侧开发的复杂度，减少微服务网络调用数量和网络延迟问题。

### VIP Cloud Native实践

参考Cloud Native理念，分别介绍VIP实践。

1.  **组织层面**：Cloud的推行是由专门的云平台部门来完成的。云平台拉通开发、QA、运维等各个部门，一起推动电商业务的云化。在推行Cloud过程中，我们也碰到了大部分公司都面临的“部门墙”问题。因此，目前我们在尝试“项目制”运作方式：成立一个项目组，把相关利益责任人拉进来，由PMO推动落实。
2.  **持续交付**：目前我们已经开发了CI、CD，正在并联合QA Tool工具部，线上发布系统，打通开发、QA、运维等，形成端到端的持续交付流程。持续交付的流程图如下：

    [![09.png](http://upload-images.jianshu.io/upload_images/10649427-6a989d4952b3fabc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "09.png")](http://dockone.io/uploads/article/20151111/fd2891c9311f577348786ce2b3ba8568.png) 

3.  **敏捷基础设施**

    [![10.png](http://upload-images.jianshu.io/upload_images/10649427-5b216ec95e87d49a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "10.png")](http://dockone.io/uploads/article/20151111/74abfb7ddd042ffb118568b65108c6a1.png) 

4.  **微服务**：目前唯品会部分业务在尝试采用基于Docker的微服务架构，大部分还是采用SOA服务架构。一个服务的概念，对应于我们内部的一个业务域概念，目前有1K多的业务域。随着VIP业务的快速发展，单个业务域的容量也会快速增长。因此，业务域也在逐步的拆分中，业务域数据也会增长几倍。
5.  **12因子（Twelve-Factor）评估**：由于是基于SOA架构设计，大部分可以满足12因子（Twelve-Factor）的规范。
    评估参考下面表格：

    [![11.png](http://upload-images.jianshu.io/upload_images/10649427-3e4255020ad5d2ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "11.png")](http://dockone.io/uploads/article/20151111/99b5081155cd632fa2f0cac7535ca8bd.png) 

### Q&A

**Q：如何达到分享老师这种技术深度、广度、理念？**

> A：过奖了，谢谢。有几点可以和大家分享一下：
> 
> 1.  技术都是慢慢积累过来的，你做的久了，自然知道的就多些，做技术贵在坚持。
> 2.  多和一些大牛学习和交流很重要，最直接的就是多向你的直接领导请教；
> 3.  多学习，每天坚持学习；
> 4.  平台很重要，有个很好的发展平台可以让你多学习很多内容。

**Q：唯品会属于康威定律中哪类公司，对促进Cloud Native都作了哪些组织结构上的调整？**

> A：为了推行Cloud Native，我们成立专门的云计算部门。但是为了推行云，我们需要和周边各个部门打交道。推行IaaS还好说，因为这块基本上是标准化的东东，但是在推行CI、CD时碰到很多问题。CI、CD是和业务密切相关的，规范的推行，必然会给大家带来额外的工作量。针对这些问题，我们是联合多个部门一起搞，项目制运作。

**Q：在实际使用过程中，12因子是定性的还是定量的，如何检测？**

> A：应该是定性和定量结合更为合适。比如代码统一放入Git库，这个是定性。但是代码提交次数，代码圈复杂度大小，CI成功率等就是定量的。

**Q：针对移动端的自动化测试，你们是怎么做的？你们的服务发现是如何做的？**

> A：移动端的自动化测试，这块了解有限，暂时无法答复你。服务发现，我们有两种模式：DNS传统模式，另外一个就是基于自研OSP开放平台的服务注册、服务发现模式。

**Q：灰度发布采用的什么策略，是AB两个集群轮流替换的方式吗？**

> A：目前是按批次分批发布的，比如50个节点，先升级1个，再升级20，最后升级29个。

**Q：自动化测试是怎么做的，尤其是Web应用的自动化测试，采用的什么工具？**

> A：我知道的有Test link。

**Q：这些云上的数据库是如何部署的，处理的数据规模有多大，事务吞吐量多少，扩容这块如何实践的？**

> A：DB有专门的部署工具，对于规模和事务吞吐量、扩容问题，这个比较敏感，可以线下专门交流。

**Q：请问，唯品会目前云上用的是什么数据库？**

> A：MySQL、MongoDB。

**Q：请教一下，后台数据库是如何分离的，比如，是不是先从用户微服务中拿用户列表，在用用户ID去商品微服务中去拿这个用户的商品列表？** 

> A：后台DB链接信息一般可以作为环境变量注入App中。

**Q：在基础设施上您同时使用了虚拟机和Docker，请问这两种基础设施承载业务也不同吗？**

> A：虚拟机目前是开发测试环境用。Docker后续会直接用在生产环境，主要是无状态App甚至缓存。其实VM大部分场景都可以使用Docker代替。

**Q：在服务注册和发现的图里Consumers应该不会直接连接Producer吧， 一般会通过企业治理esg连接？**

> A：Producer和Consumer之间一般还有个LB。

**Q：能把预发布环境理解为集成测试环境吗？**

> A：预发布是上线前的一个环节，链接的DB都是线上真实环境的。集成测试环境，还在预发布环节的前面。

**Q：作为环境变量到终端DB安全性如何保证，难道是每个用户一个DB？**

> A：一般是后台service链接DB的吧，用户是无法接触DB的。

**Q：那个动态更新配置的服务是需要启动一个守护进程去更新吗？**

> A：一般来说，client都会安装一个配置agent，它来负责从配置server pull配置进行更新。

**Q：怎么理解版本控制的分布式配置中心，主要是配置的哪些信息？**

> A：比如Nginx配置（端口、vhost等）、tomcat配置、DB链接信息、缓存链接信息等等。

**Q：12因子是否过于严格，嘉宾的表格里关于应用无状态也只是部分满足？**

> A：12因子主要针对App的，它不适用于service层面，比如对于db，mc的服务是不适合的。

**Q：Gateway实现是使用开源框架（kong、loopback）还是自己实现？**

> A：既可以自己实现（比如Java、Nginx），也可以采用开源的，比如Zuul。

**Q：必要时进行协议转换（比如Http转为AMQP）？**

> A：比如外部是通过http rest请求的，拿到这个请求后，把参数封装，然后以mq的方式发给后台其他服务。

**Q：微服务一定是无状态的吗？**

> A：这个不一定的。

**Q：能介绍OpenStack 在项目中怎么和Docker等结合使用的吗？**

> A：目前我们的CI采用Docker，Docker直接跑在VM（VM由OpenStack创建）上。对于OpenStack底层和Docker如何结合，目前还在方案评估中。

===========================================================
以上内容根据2015年11月10日晚微信群分享内容整理。分享人：**赵守忠，唯品会云计算高级架构师。有6年云计算相关经验，在IAAS、PaaS、电商系统云化有较为丰富的经验和心得。擅长IaaS的异构计算设备、异构存储设备、异构网络设备、异构虚拟化；擅长PaaS的CI、CD、App生命周期管理，云服务等业务。专注Openstack、Cloudfoundry、Kubernetes、Docker等领域。**DockOne每周都会组织定向的技术分享，欢迎感兴趣的同学加微信：liyingjiesx，进群参与，您有想听的话题可以给我们留言。
