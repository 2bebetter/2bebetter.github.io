---
layout: post
title:  "论文阅读"
date:   2020-11-24 10:21:40 +0800
typora-root-url: ..
categories: thesis
---

* content
{:toc}
[Enhanced optimal placements of multi-controllers in SDN](https://link.springer.com/article/10.1007/s12652-020-02554-2)

## Enhanced optimal placements of multi-controllers in SDN

[Rapid Restoration Techniques for Software-Defined Networks](https://www.mdpi.com/2076-3417/10/10/3411)

## Rapid Restoration Techniques for Software-Defined Networks

### Problem

* 网络元素容易发生故障，从故障中恢复既可以主动实现（也称为保护），也可以被动地实现（也称为恢复）。在保护方面，替代解决方案是在发生故障之前预先计划和保留的。但是，在恢复中，解决方案不是预先计划的，当发生故障时需要动态按需计算。
* 保护机制很昂贵，而且备份路径可能比主路径更快失效
* 恢复机制很耗时：（1）计算新路径的时间 （2）更新新路径上的交换机的时间

### Proposal Methods

#### Path recover

* 网络模型

无向图

* Community Detection(CD)

CD可以将具有共同属性的节点组表示为社区。在这里，每个社区被定义为一个子图，其中的节点密集连接，但与网络的其余部分稀疏连接。

目前已经提出的算法：Louvain算法    Girvan     Newman算法

通过将网络图G划分为N个社区，我们假设当发生链路故障事件时，只有一个社区会遭受该特定故障。在大部分链路故障情况下，这种假设是合理的。

* CD模型

社区检测算法提取的社区数量取决于网络拓扑结构和社区检测算法的参数化。

假设路径上连续的路由器必定位于同一社区中。根据Dijkstra算法计算节点之间的最短路径：

$$ P_{Dset} = {P | ∀ r_1,r_d ∈ V : P = D(P_{1,r_d} )} $$

其中$P_{r_1，r_d}$表示任意两个特定节点之间的所有可能路径的集合。

![示例](/img/2020-11-24-thesis-reading-04/image-20201124190517565.png)

* Path Anatomy-Based Approach

控制器首先在受影响的路径上的交换机中删除旧流表条目，然后为备用路径下发所需的规则。当受影响路径的长度较长时，此过程很耗时。网络路径解剖（PA）是此问题的潜在解决方案。

相邻路由器的此序列具有一些中间路由器，称为$r_m$，路径P可被分为两段：

![image-20201124192229346](/img/2020-11-24-thesis-reading-04/image-20201124192229346.png)

找到从$r_1$到$r_d$的新路径以克服故障并不是有效的解决方案，因为规则替换和更新的成本可能很高。 

基于PA可以实现两种方法：

* 第一个使用P的中间路由器来考虑两个部分（即子路径），其中一个正在运行，而另一个没有运行（譬如由于故障）。这意味着受影响路径的一个分段仍可以按原样使用，并且更新该分段的操作成本为零。
* 在第二种方案中，并不使用新的子路径替换路径的故障段，而是将备用路径的搜索空间减少到仅在围绕故障链路的两个节点之间。

### Methods

四个组件：*SDN Controller* 、*Topology Parser* 、*Global Recovery Scheme* 、*Community Detection*、*Path Anatomy*

使用Girvan和Newman将网络拓扑图G的虚拟分区创建为C。

根据Dijkstra算法开发了两种算法来满足所有的路径查找需求：

（1）新数据包到达需要计算新路径

（2）路由故障需要计算新路径

### Experiment

三个评估指标：length、operation、latency。

* length表示表示在链路发生故障时与恢复解决方案时找到的新路径相关的跃点数。
* operation包括添加，删除和修改流条目。
* latency表示安装备用路径所需的时间。

为了评估不同的恢复算法，使用了三种实际的网络拓扑结构-网络存储库中的ERnet ，German50和NR。论文使用Internet拓扑生成器Brite [39]来生成其他拓扑，
利用Waxman算法基于Brite生成随机图。

使用Mininet仿真作为实验环境。