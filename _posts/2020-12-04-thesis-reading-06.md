---
layout: post
title:  "Multidimensional detection and dynamic defense method for link flooding attack"
date:   2020-12-04 11:21:40 +0800
typora-root-url: ..
categories: thesis
---

* content
{:toc}
## Multidimensional detection and dynamic defense method for link flooding attack

### 链路攻击

#### coremelt

#### crossfire

### Methods

![image-20201204155943072](/img/2020-12-04-thesis-reading-06/image-20201204155943072.png)

* 链路监听模块直接连接到数据平面与控制平面之间的链路，它将一直检测链路并感知可能存在的链路拥塞，一旦发现拥塞出现，则将预警信息发送给多维检测模块
* 多维检测模块在接收到预警信息后，首先对发生拥塞的链路进行定位和判断，若发生拥塞的链路存在构成闭合环路的可能，则对发生拥塞的链路进行检测。当多维检测模块检测到链路洪泛攻击后，将检测信息发送给动态部署模块
* 染色理论动态部署模块在接收到多维检测模块发送的检测信息后对控制器−交换机的连接关系进行迁移。

#### 多维指标检测方法

区分正常用户与恶意敌手的关键在于：正常用户不会发送大量的探测报文给目的节点，并且不会发送大量的探测报文。

假设总有一个正常周期，该周期内只有正常用户的探测行为。在这个时间内，网络空间管理员可以计算正常探测的频率，从而作为参数的正常阈值，两个正常阙值的计算方式如下：

* 源探测频率阈值：计算最大和最小源探测频率，并将两者在多日内的偏差作为平均标准探测偏差，记作$T_{diff}$ 。

* 目的探测频率阈值：计算每个小时内目的节点收到的最大探测数量，并将该值在连续多日内的平均值作为阈值，记作$T_{dest}$

如果在任意一个小时内，其平均值超过了$T_{diff}$ 的两倍，则可以判定该探测行为是不正常的，且它可能是一个侦测攻击。下一个阈值用来判断哪个目标节点被攻击者探测。所有被判定进行侦测的僵尸节点用户将在现有情况或者未来检测中被 SDN 控制器认定是僵尸。

通过会话长度（CL，connection length）、数据分组低速比例（LPR，low packet rate）、数据分组距离均匀性（PDU，packet distance uniformity）、平均低速率数据分组占比（LMPR，low mean packet rate）、低速数据分组占比变化率（LPRV，low packet rate variance）5 维要素对存在异常的转发链路进行多维检测。

#### 基于染色理论的动态部署算法

通过部署异构交换机增加网络的动态性。

在 SDN 中，控制器通常按照分域结构进行部署，每个域内网络由独立的一个或者一组控制器负责管理，该控制器（组）负责该子网交换机的管理、流表推送等。核心控制器（组）负责管理各个域内的控制器。

需要指出的是，当按照控制器组进行管理时，组内仅有一个 Master 控制器，当该控制器出现故障时，组内按照选举算法选取出下一个 Master 控制器接管网络。

![image-20201204161028660](/img/2020-12-04-thesis-reading-06/image-20201204161028660.png)

### Experiment

链路洪泛攻击检测实验和防御缓解实验。

**对比：**

多维指标 vs Renyi 熵

相同交换机 vs 随机分布 vs 染色分布

**性能测试：**

![image-20201204161429665](/img/2020-12-04-thesis-reading-06/image-20201204161429665.png)



