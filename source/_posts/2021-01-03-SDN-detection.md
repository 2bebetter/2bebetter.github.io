---
title:  "SDN信息采集"
date:   2021-01-03 10:21:40 +0800
typora-root-url: ..
categories: thesis
---

### UMON: Flexible and Fine Grained Traffic Monitoring in Open vSwitch

来源：CoNEXT: International Conference on emerging Networking EXperiments and Technologies

发表时间：2015

分类：细化粒度

提出问题：现有OVS监控工具既不灵活也不足以支持许多监控应用。

主要贡献

- 设计UMON,将监控与转发分离，并在OVS中提供灵活和细粒度的监控
- OVS架构完美集成在UMON
- 是UMON在可行的代价内完成监控

相关工作：

- Pyretic
- 基于流规则的测量结合动态粒度调整的策略，用于网络异常检测

### DeepGuard: Efficient Anomaly Detection in SDN With Fine-Grained Traffic Flow Monitoring

来源：

发表时间：

分类：细化粒度 机器学习

提出问题：

主要贡献

相关工作

### CounterMap: Towards Generic Traffic Statistics Collection and Query in Software Defined Network

来源：IWQoS: IEEE/ACM International Symposium on Quality of Service

发表时间：2017

分类：网络流量测量

提出问题：现缺少一个具有通用性的细粒度流量统计方法

相关工作

- 传统的流量测量工具：NetFlow，sFlow，Sniffer和SNMP
- 流量收集方法：FlowSense、PayLess
- OpenFlow和sFlow相结合进行异常检测
- Frenetic、Pyretic
- 交换机调度策略：OpenTM、FlowCover

主要贡献：

- 证明了过期流量对网络应用程序的重要性
- 设计并实现了CounterMap平台，用于收集SDNl流量统计和查询
- 提出了一种启发式交换机轮询算法用于减少交换机之间的计数器冗余

### Flow monitoring in Software-Defined Networks

来源：Computer Networks (Elsevier)

发表年份：2018

解决问题：

- 使用OpenFlow实现流量监控的最直接方法是在交换机表中维护每个流的条目。通过这种方式，监控网络中的所有流量会产生很大的限制，因为现在OpenFlow交换机由于其有限的硬件资源（即TCAM条目数和处理能力）而不支持大量流条目

解决方案：

- 使用OpenFlow实现流量采样并执行流级别流量分类，特别强调Web和加密流量的识别。
- 对于每个采样流，我们在交换机中维护一个流条目，记录持续时间以及数据包和字节数。
- 在交换机中初始安装一些规则，这些规则将自动操作以随机区分要采样的流量。

### A parallel approach for detecting OpenFlow rule anomalies based on a general formalism

解决问题：由于可以动态且经常快速地更新软件定义网络（SDN）网络的策略，因此很容易发生策略之间的冲突。由于典型的SDN网络中有大量的交换机和异构策略，因此检测这些冲突是一项艰巨而艰巨的任务。

### DEEPGUARD: Efficient Anomaly Detection in SDNWith Fine-Grained Traffic Flow Monitoring(太难了，看不懂)

来源：

发表时间：2020

解决问题：

### FlowStat: Adaptive Flow-Rule Placement for Per-Flow Statistics in SDN

来源：IEEE Journal on Selected Areas in Communications

发表时间：2019

解决问题：

* 由于TCAM的限制，开关可以插入的流量规则的数量也受到限制。

方法：