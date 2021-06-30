---
title:  "Edge Cloud Network"
date:   2020-10-26 12:51:40 +0800
typora-root-url: ..
categories: thesis
---

* content
{:toc}

# Edge Cloud Network

## Definition

 边缘云架构用于将权力分散(处理)到网络的边缘(客户端/设备)。传统服务器的计算能力用于执行数据最小化或创建先进的分布式系统等任务。在云模型中，这样的“智能”任务由服务器执行，因此它们可以被转移到其他计算能力较低或几乎没有计算能力的设备上。

有了边缘云，这些处理任务的很大一部分都被转移到了客户端，这也被称为边缘计算(Edge Computing)。边缘计算通常指物联网(IOT)设备，但也指在设备上处理遥测数据(而不是将原始数据发送到云)的游戏硬件。

这为企业创造了很多机会，特别是当他们希望在整个应用程序或高密度平台使用中提供低延迟服务时。

## Benefits

相较于传统的客户机连接到服务器的模型，边缘云架构中数以千计的客户机相互连接可以更加快速有效地执行较小的处理任务。理想的边缘计算环境使数以百万计的物联网设备形成一个庞大的智能网络，可以执行通常只有在非常大的数据中心才可能执行的任务。

通过结合Edge和Cloud，我们可以利用分布式系统的力量，在设备上处理数据，然后将数据发送到云中。在这里，可以使用更少(甚至不可用)的处理能力来处理、分析或保存它。

例如：得益于Edge云架构，共享信息的联网汽车能够自己分析数据，而不用使用服务器的处理能力。与需要在中央服务器上处理的大量数据回程不同，大部分处理工作已经由连接的设备自己完成了。

边缘云的好处还有很多，包括以服务提供商的身份有效部署新服务，或为联网汽车司机或在线游戏玩家提供低延迟体验。

* Contrail Edge Cloud等解决方案将计算、存储和网络资源在基站、集线器、交换站点和中央办公室等轻量级边缘环境中抽象和虚拟化。
* 通过低延迟、自动化和简单的交付，边缘云架构在不牺牲多租户安全云的丰富功能的情况下，加速了网络边缘的服务创建。

## Related work

### [A Novel Load Balancing and Low Response Delay Framework for Edge-Cloud Network Based on SDN](https://ieeexplore.ieee.org/document/8892518)

基于软件定义网络(SDNs)的云计算，需要收集更多的数据到云中进行分析，由于互联网容量有限，这会导致更多的冗余数据和更长的服务响应时间。针对这一问题，提出了一种新的服务编排与数据聚合框架(SODA)，该框架将数据编排为服务，并对数据包进行聚合，以减少数据冗余和服务响应延迟。在SODA中，网络被分为三层。1)数据中心层(DCL)。数据中心(DCs)向网络中的所有设备发布具有特定功能的软件，设备将数据编配为服务，并使用软件聚合数据包以减少服务响应延迟。2)中间路由层(MRL)。根据数据包与路由距离的相关性调整该层数据包的路由路径。数据包相关性越高，路由距离越短，为了减少冗余数据，数据包沿同一路由路径传输的概率就越高。3)车辆网络层(VNL)。移动车辆用于在设备之间传输数据包和服务。进行了一系列的实验和仿真。结果表明，与传统方案相比，该方案具有更好的性能。

### [Optimal VNF Placement via Deep Reinforcement Learning in SDN/NFV-Enabled Networks](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=8&SID=7Fz7cFld277jLHIz9RY&page=1&doc=30&cacheurlFromRightClick=no)

新兴的范式——软件定义网络(SDN)和网络功能虚拟化(NFV)——使得在商用设备上运行虚拟网络功能(VNFs)变得可行且可伸缩，从而以更低的成本提供各种网络服务。得益于集中的网络管理，可以在支持SDN/ nfv的网络中收集关于网络设备、流量和资源的大量信息，根据收集到的信息利用机器学习定制算法，有效优化网络性能。本文研究了SDN/ nfv支持的网络中的VNF布局问题，它可以自然地表述为二进制整数规划(BIP)问题。利用深度强化学习，提出了一种基于双深度Q网络的VNF布局算法(DDQN-VNFPA)。具体来说，DDQN从一个巨大的解决方案空间确定最佳解决方案，然后DDQN- vnfpa按照基于阈值的策略放置VNF实例(VNFIs)。我们在一个真实的网络拓扑中通过跟踪驱动模拟来评估DDQN-VNFPA。评估结果表明，与已有文献中的算法相比，DDQN-VNFPA在业务功能链请求的拒绝数和拒绝率、吞吐量、端到端延迟、VNFI运行时间和负载均衡等方面均有较好的网络性能。

### [An optimal delay aware task assignment scheme for wireless SDN networked edge cloudlets](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=8&SID=7Fz7cFld277jLHIz9RY&page=1&doc=31)

