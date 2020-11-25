---
layout: post
title:  "Software-Defifined Networking Approaches for Link Failure Recovery: A Survey"
date:   2020-11-25 11:21:40 +0800
typora-root-url: ..
categories: thesis
---

* content
{:toc}
## Software-Defifined Networking Approaches for Link Failure Recovery: A Survey

[Software-Defifined Networking Approaches for Link Failure Recovery: A Survey](https://ideas.repec.org/a/gam/jsusta/v12y2020i10p4255-d361650.html)

SDN使用主动和被动两种主要方法来处理链路故障。

在主动方法中，备用路径是预先配置的，并且在链路故障的情况下，中断的流将转发到备份路径，不需要与控制器进行交互。

相反，在反应性方案中即被动方法中，需要与控制器联系以寻找替代路径，在控制器计算路径结束之后，重新下发新路径的流表规则。

但是，这两种方案都有其优缺点，同时还要在性能和效率上进行权衡。

### 链路故障检测机制

SDN中的故障恢复过程从检测到链路故障开始，如果检测迅速，那么整个恢复过程的总延迟将很小，这就是为什么检测方案对整个过程如此重要。

下表概述了链路故障检测机制，故障链路的检测方法以及检测方案中的相关问题。

* 在[32，33]中提出的方案使用监视周期的概念来减少链路故障恢复时间。在参考文献[32]中，引入了二进制搜索技术以最小化路径上的跳数。
* 类似地，在参考文献[33,34]中，使用启发式方法[35]将监视分配公式化为邮递员问题，以分配监视周期。
* OpenFlow使用了与以太网相同的检测信号的机制，通过在节点之间以固定的时间间隔交换心跳消息来确定网络的状态，通过节点之间的hello数据包的交换速率来检查活动性。因此，如果节点在16±8 ms的常规时间间隔内未收到hello数据包，则会向控制器通知发生故障的链路。
* 生成树协议（STP）[38]和反向生成树协议（RSTP）也已在数据链路层上用于链路故障检测。但是，它们的检测时间跨度为几秒钟，不能保证现代技术的延迟要求。
* SDN社区[36,40,41]中也常规提供OpenFlow快速故障转移（FF）组[1]和双向转发检测（BFD）[39]，用于链路故障检测和恢复。
* 在[42]中提出了一种故障检测机制，称为低错误率故障检测服务（FDLM），它使用心跳消息将检测中的错误最小化。
* 使用多协议标签交换（MPLS）BFD来检测端到端路径中传输网络的故障。该方案通过与数据包一起发送探测消息，当连续的探测消息之间存在间隙时，认为检测到链路故障。
* 在[45]中提出了一种使用基于out 数据包的计数器机制的故障检测方法。对链接上安装的流规则进行标记和监视，然后在目标位置对数据包进行计数。
* 交换机级别的故障检测方案[47]被称为交换机故障检测（SFD），使用故障链路和网络拓扑作为输入。为了识别故障交换机，该算法首先找到故障链路的源和目的地。然后，发现与交换机连接的所有主机，并计算丢包率是否为100％。(**仅限节点故障**)

![image-20201125192853820](/img/2020-11-25-thesis-reading-04/image-20201125192853820.png)

### 链路故障恢复机制

#### Proactive

![image-20201125202420813](/img/2020-11-25-thesis-reading-04/image-20201125202420813.png)

* OpenFlow提供了Fast-FailOver用于故障检测和恢复。

* BFD
* SPIDER

#### Reactive

响应式故障恢复主要依靠SDN控制器。

这种方法的反对者声称反应式方案不能满足CGN的延迟范围，即50毫秒。

![image-20201125210823088](/img/2020-11-25-thesis-reading-04/image-20201125210823088.png)

#### Laege-scale SDN

利用图论对网络进行裁剪。

Distributed controller clustering in software defifined networks.

#### Hybrid SDN

