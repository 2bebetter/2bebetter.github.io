---
title: "SDN Related Work"
date:  2020-10-27 12:51:40 +0800
typora-root-url: ..
categories: thesis
---

* content
{:toc}

# SDN Related Work

## SDN Routing

### Machine Learning

#### [CFR-RL: Traffic Engineering With Reinforcement Learning in SDN](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=4&cacheurlFromRightClick=no)

传统的交通工程(TE)解决方案可以通过重新路由尽可能多的流来获得最优或接近最优的性能。然而，当频繁地在网络中重新路由流时，他们通常不会考虑负面影响，比如数据包次序混乱。为了减轻网络干扰的影响，一个有前景的TE解决方案是使用等成本多路径(ECMP)转发大部分流量，并使用软件定义网络(SDN)选择性地重新路由少数关键流量，以平衡网络的链路利用率，其中关键流定义为对网络性能有主要影响的流（例如，最拥塞的链路上的流）。关键流重新路由问题可以分解为两个子问题：（1）识别关键流（2）重新路由它们以实现良好的性能。此外，基于规则的启发式算法不能适应流量矩阵和网络动态的变化，因此不可能基于固定的简单规则来设计该问题的启发式算法。在本文中，我们提出了CFR-RL(临界流重定向-强化学习)，这是一个基于强化学习的方案，它学习一个策略来为每个给定的交通矩阵自动选择临界流。然后，CFR-RL通过制定和解决一个简单的线性规划(LP)问题，重新路由这些选定的关键流，以平衡网络的链路利用率。广泛的评估表明，CFR-RL仅重新路由总流量的10%-21.3%，从而实现接近最佳的性能。

#### [RouteNet: Leveraging Graph Neural Networks for Network Modeling and Optimization in SDN](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=5)

网络建模是实现自驱动软件定义网络高效运行的关键因素。然而，我们仍然缺乏能够在有限成本下对关键性能指标(KPI)产生准确预测的功能性网络模型，如延迟、抖动或损失。本文提出了一种基于图神经网络(GNN)的新型网络模型RouteNet，该模型能够理解拓扑、路由和输入流量之间的复杂关系，从而准确估计每个源/目的地每个包的时延分布和丢失。RouteNet利用了GNN学习和建模图结构信息，因此，我们的模型能够泛化任意拓扑、路由方案和流量强度。在我们的评估中，我们发现RouteNet能够准确地预测训练中看不到的拓扑、路由和流量的时延分布(平均时延和抖动)和损失(最坏情况MRE = 15.4%)。此外，我们还展示了几个利用我们的GNN模型的KPI预测来实现高效路由优化和网络规划的用例。

## SDN Security

### Related work

#### [BloomStore: Dynamic Bloom-Filter-based Secure Rule-Space Management Scheme in SDN](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=7&cacheurlFromRightClick=no)

软件定义网络(SDN)通过将复杂和严格的计算任务转移到集中式控制器，提供了一种有效的管理流量负载的方法。它减轻了交换机的负担，交换机的任务是执行基于规则操作对的路由。但是，交换机的流表存储容量有限。它可能不得不面对性能瓶颈，而这又会导致严重的安全漏洞和性能下降。因此，本文提出了SDN中基于动态Bloom-Filter的安全规则空间管理方案BloomStore。BloomStore通过管理网络资源动态地处理数据流量。双重安全检查用于安全数据传输使用双重哈希，即两个独立的哈希函数被用来生成k哈希函数。此外，还提出了分区哈希在bloom数组的bucket中进行插入和查询的方法。结果分析表明，在各种性能参数方面，BloomStore的性能都优于竞争对手的变体。

#### [An Efficient Approach to Robust SDN Controller Placement for Security](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=14&cacheurlFromRightClick=no)

安全是传统网络的关键问题之一。软件定义网络(SDN)通过分离网络的控制平面和数据平面，提高了网络的安全性。为了提高SDN网络的性能，研究人员设计了许多先进的控制器原型，并考虑了控制器的放置问题。然而，链路失效是网络中非常重要的安全问题，极大地影响了SDN网络的安全。在今天，对于链路失败的控制器放置问题仍然是一个挑战。在本文中，我们分别研究了单链路和多链路失效情况下的SDN控制器配置问题。对于单链路故障，我们开发了一种启发式算法来解决控制器配置问题。对于多链路故障，我们引入蒙特卡罗模拟来减少计算开销。我们对真实网络拓扑进行了实验，仿真结果表明启发式算法在取得良好性能的同时，比最优算法节省了更多的时间。

## Load balancing

#### [An energy-efficient load distribution framework for SDN controllers](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=18&cacheurlFromRightClick=no)

软件定义网络(SDN)由于能够根据不同的需求动态配置网络，已经发展成为未来Internet的一个有效平台。我们观察到，随着通信网络的增长，SDN设备的负载和能量需求显著增加。因此，需要对SDN控制器进行高效的建模，平衡负载，优化设备能耗。在本文中，我们提出了一个节能的负荷分配框架和有效的负荷分配和客观上优化网络能耗的流量路由控制器系统模型。该模型通过引入高效节能的路由算法选择过程，实现了基于异构流量需求的负载均衡，降低了能耗。负载均衡方案采用多控制器同步切换迁移技术，而高效路由的新颖之处在于网络设备的休眠和主动模式。为了提高网络的性能，我们提出了负载均衡和节能路由之间的相互作用。大量的仿真结果表明，我们所提出的控制器系统模型的有效性得到了证明，能耗降低了约25%，性能提高了约20%。该模型适用于满足绿色通信标准的现实网络环境。

#### [A trust management framework for software-defined network applications](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=22)

(评估框架)

SDN (software-defined network, SDN)的出现给现有网络带来了前所未有的创新。SDN的两个最显著的特性是解耦性和可编程性。解耦使得网络管理集中在一个控制平面上。同时，得益于SDN的可编程特性，可以很容易地实现新的组网功能。然而，这些特性也给SDN带来了新的安全问题。通过SDN提供的编程接口，软件工程师可以轻松开发网络应用程序，生成SDN控制平面的组网策略，以指导网络路由。然而，这些新应用的安全性和质量很难保证。恶意或低质量的应用程序可能会破坏整个网络。为了解决这个问题，本文提出了一种新的SDN应用信任管理框架。它可以根据应用程序对网络性能(如时延、丢包率、吞吐量等)的影响来评估应用程序的信任值。这些信任值对SDN中应用程序的管理和选择起到了决定性的作用。我们通过一个基于泛光灯控制器的原型系统来评估该框架的性能。实验结果表明了设计的正确性和有效性。

[ArchSDN: a reinforcement learning-based autonomous OpenFlow controller with distributed management properties](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=23)

网络控制器的屏蔽能力对网络运营商有信心继续扩展通信网络至关重要。今天的网络可以使用该软件定义网络(SDN)概念分离控制和转换层面，并使用自主自治特性快速应对任何网络活动,同时为网络提供必要的灵活性,支持自定义服务级别协议(SLA)的客户,同时降低资本和运营开支。SDN和自主管理的概念都有一个集中式的架构，需要一个单一的实体管理网络的方方面面，这使得在庞大的物联网世界中管理整个网络变得困难，在这些网络中服务往往是靠近用户，基于边缘的方法是支持5G和超越服务的关键。要支持这种动态和灵活的网络，需要一种方法，通过多个自主的OpenFlow控制器(我们称之为ArchSDN控制器)来分配网络管理职责。通过将OpenFlow交换机分配给不同的ArchSDN控制器，将网络划分为不同的扇区，每个扇区由一个ArchSDN控制器独占控制。这些控制者协调他们的行动，并使用基于强化学习的决策机制来探索、学习和实现接近最佳的端到端通信路径。评估结果表明，所提出的决策系统能够找到接近最优的解决方案，从已获得的结果中学习以改进未来的服务激活结果，并能在不到100毫秒的时间内响应，使网络快速适应通信链路的丢失。

#### [Self-healing and SDN: bridging the gap](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=4&SID=6DsnaK11My1YwUPVlix&page=1&doc=27&cacheurlFromRightClick=no)

随着分布式交互应用的普及，为了获得良好的参与者交互体验，必须考虑网络资源的高效和公平分配。在软件定义的网络中，中央控制器的存在为交互式应用程序部署可定制的路由提供了一种新颖的解决方案，它允许对DIAs进行细粒度的资源分配，以实现参与者之间的公平性。但是机遇总是伴随着挑战，广泛分布的用户位置往往需要分配控制器来满足应用的要求。因此，参与者之间的延迟直接受到控制器处理时间的影响。在此背景下，我们解决了DIAs在计算和链路负载方面的公平资源配置问题，目的是平衡SDN网络中多流之间的可实现请求率和公平性。首先，我们将问题表示为控制器加载和路由优化的组合。然后，提出了基于深度学习的主动分配控制器算法和公平路径分配算法来共享瓶颈环节。与目前最先进的贪婪分配算法和优先级分配算法相比，通过跟踪驱动仿真验证了该算法在控制器和链路负载方面具有更好的公平性。 

#### [Multi-domain virtual network embedding with dynamic flow migration in software-defined networks](https://apps-webofknowledge-com.webvpn.las.ac.cn/full_record.do?product=UA&search_mode=AdvancedSearch&qid=3&SID=5FUOQZPzuNrS9dmyBmq&page=1&doc=34&cacheurlFromRightClick=no)

软件定义网络(SDN)解耦了网络的控制面和数据面，并使用一个中央控制器来提供有效使用网络资源和方便的服务提供。SDN中的虚拟化使虚拟SDN网络能够共享公共的物理网络基础设施，从而使它们能够提供通用的服务。在这种设置下，SDN hypervisor支持多个虚拟SDN网络(vSDN)，其中每个vSDN网络都有自己的控制器。为了建立虚拟网络，实现物理网络资源的优化共享，设计了虚拟网络嵌入算法。单域虚拟虚拟机是虚拟虚拟机研究的一个热点问题。然而，在大多数实际场景中，VNs是跨属于不同基础设施提供者(InPs)的异构管理域供应的。

本文利用不规则细胞学习自动机(ICLA)提出了一种用于多域SDN网络的VNE算法，即vSDN-CLA。我们考虑了两个方面——虚拟节点和链路在多域基片网络中的最优映射，以及SDN控制器在吞吐量和端到端延迟方面的最优配置。我们通过考虑动态流迁移来扩展vSDN-CLA，以实现资源最优路由。我们使用Mininet评估了提议的方案，我们观察到提议的方案在吞吐量、端到端延迟和虚拟网络请求接受率方面优于现有的基准方案在单域和多域环境下。