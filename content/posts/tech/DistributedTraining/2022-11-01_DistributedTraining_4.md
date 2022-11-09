---
title: "分布式训练 – 第4篇 - 分布式训练常用的网络结构及集合通信拓扑算法"
slug: ""
date: 2022-11-01T09:11:20+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: ["DistributedTraining"]
katex: true
mermaid: false
draft: false
---

ref:
[1]. https://www.changping.me/2022/04/10/ai-distributed-training-coll-topo/

## 1. 概述

在深度学习的分布式训练里，Ring AllReduce拓扑算法奠定了数据并行训练的集合通信基础，但集合通信拓扑不只是仅有Ring Allreduce，经典的集合通信拓扑算法还有2D-Ring/Hierarchical Ring AllReduce，halving and doubling AllReduce，Butterfly AllReduce，2D-Torus AllReduce，2D-Mesh AllReduce，double binary tree等。拓扑算法很多，但也不是所有的拓扑算法都能满足实际的生产需求的，这需要具体问题具体分析、具体场景具体设计。

集合通信的**难点**在于需要在固定的网络互联结构的约束下进行高效的通信，集合通信拓扑算法与物理网络互联结构强相关，为了发挥网络通信的效率，也不是说就能随意发挥通信拓扑算法，更多的是在**效率与成本**、**带宽与时延**、**客户要求与质量**、**创新与产品化**等之间进行合理取舍。

充分发挥训练加速卡与网络的效率是通信拓扑算法的初衷，但除了设计高效的集合通信拓扑算法外，分布式训练中需要解决的通信难题还有：网络是异构的，网络带宽是有限的，主机内PCIE SWITCH是有亲和性的，网络是会出故障的，节点是有落后者效应的，设备成本是需要考虑的，数据中心是有部署约束的，用户是有多租户要求的等，这些属于产品化的范畴不在本文阐述。

## 2. 网络互联结构

分布式训练的集合通信拓扑算法与物理的网络互联结构强相关，而网络互联结构又多种多样，因此，本文需要先对网络互联结构进行约束，依据生产中常用的、既定的互联结构设计集合通信算法，网络互联结构描述如下：

### 2.1 服务内网络互联结构

以一台集成了8张训练加速卡的服务器为例，如下图:

![volta-architecture](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_architecture.png#center)

这台服务器内的网络互联情况如下：

1）在这台服务器内，8张训练加速卡通过私有协议连接组成多个主机内的物理ring环，且可双工；

2）服务期内网络带宽 NVLINK>PCIE switch > QPI；

3）加速卡1、2、3、4之间两两全互联，加速卡5,、6、7、8之间两两全互联，2、5、3、8之间非全互联；

4）加速卡1、4与网卡NIC1 挂在同一个PCIE Switch上，具有亲和性，加速卡2、3与网卡NIC2挂在同一个PCIE Switch上，具有亲和性，而PCIE Switch之间也互联，因此 加速卡 1、2、3、4 与网卡NIC 1、NIC2具备亲和性，它们无需通过CPU的QPI线进行通信；

5）加速卡5、8与网卡NIC3 挂在同一个PCIE Switch上，具有亲和性，加速卡6、7与网卡NIC4挂在同一个PCIE Switch上，具有亲和性，而PCIE Switch之间也互联的，因此 加速卡 5、6、7、8 与网卡NIC 3、NIC4具备亲和性，它们也无需通过CPU的QPI线进行通信；

6）网卡可根据需要 选择 1张、2张、4张或8张，最多可以采用8张RDMA物理网卡；


### 2.2 服务器间网络互联结构

以一个训练加速卡集群为例，如下图是一个常用的CLOS互联架构方案:

![CLOS](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_CLOS.png#center)

在这个集群内，其网络互联情况如下：

1）集群内每台服务器自带高速RDMA网卡，通过RDMA 交换机在主机间两两全互联；

2）交换机组成CLOS架构，分为Spine与Leaf交换机，当然也可以是更为高端的Spine、Leaf合一的高端交换机；

3）RDMA网卡与Leaf交换机互联，每台服务器的RDMA网卡数量根据成本与性能考虑，可以是1张、2张+每卡虚拟化4卡、4张+每卡虚拟化2卡或8张；

### 2.3 高速网卡及其虚拟化使用

RDMA网卡是双工的且可虚拟化，在这里每台服务器可根据成本、性能的考虑选用1张、2张、4张或8张，且在服务器内左右对称，如下图：

![RDAM](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_RDAM.png#center)

从成本与效率的角度考虑，每台服务器内的网卡可以是以下配置：

- 1张物理RDMA网卡，不进行虚拟化，直接用双工通道，适合选用2D/Hierarchical Ring拓扑算法；
- 2张物理RDMA网卡，可以每张虚拟化出4个虚拟网卡，2X4共8卡，适合选用2D-MESH、2D-Torus拓扑算法；
- 4张物理RDMA网卡，可每张虚拟化出2个虚拟网卡，4X2共8卡，适合选用2D-MESH、2D-Torus拓扑算法；
- 8张物理RDMA网卡，不需要虚拟化，直接采用双工通道，适合选用2D-MESH、2D-Torus拓扑算法；

在实际的分布式训练生产集群中，集合通信算法也可以结合RDMA网卡端口（包括虚拟化的）的具体个数进行设计，而拓扑算法的选择也是需要根据成本与效率的进行合理取舍的。

### 2.4 网络结构抽象

网络根据连接情况可分为**ring结构**、**mesh结构**、 **torus 结构**以及**tree结构**，基于以上的服务器内网络互联结构、服务器间网络互联结构以及网卡的具体情况，可以抽象出一个网络结构，即**二维环面网络**：Torus 网络，而Torus网络横向与纵向都可以看成ring结构，因此相应的拓扑算法基本上就是Ring-Based 集合通信拓扑算法。如下图：

![TORUS](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_TORUS.png#center)

TORUS网络是常见的大规模并行计算机的互连网络，在上图这个Torus网络里：

1）横向：主机内8卡通过私有连接协议，比如CXL/CCIX/NVLINK等组成一个或多个ring，如上图的黄色连接线，横向8卡组成二维Torus的横向维度；

2）纵向：主机间通过RDMA（RoCE/IB）网卡、交换机互联组成1到8个ring，如上图的红色连接线，纵向采用RDMA网卡组成二维Torus的纵向维度；

3）根据物理网卡数量、网卡虚拟化以及PCIe Switch亲和性的实际情况：

- 每台服务器1张网卡可组成主机间一个ring，网卡与XPU0 挂载同一个PCIE switch上，依据最佳实践原则（比如性能、成本、客户要求等），适合选用2D/Hierarchical Ring拓扑算法；
- 两张网卡可组成主机间两个ring或者经过虚拟化组成8个ring，根据PCIE SWITCH亲和性原则，一张网卡与XPU0挂在同一个pcie switch，另一张网卡与XPU4挂在同一个pcie switch，依据最佳实践原则（比如性能、成本、客户要求等），适合选用2D-MESH、2D-Torus拓扑算法；
- 4张网卡、8张网卡以此类推，也是根据PCIE SWITCH亲和性原则进行连接，主机间RDMA物理网卡不够就虚拟化网口来凑，并且要服务器内的RDMA出口端口数左右平衡，依据最佳实践原则（比如性能、成本、客户要求等），也是适合选用2D-MESH、2D-Torus拓扑算法，这样才能发挥多张网卡以及XPU的算力优势。

4）更复杂的Torus网络组合关系还可以如下图，从横向只有 主机内的8卡纵向只有主机间的RDMA互联，扩展到 横向与纵向 主机内互联与主机间互联混合，但本文仅限于在横向8卡的二维Torus网络下进行拓扑算法选择与设计，因此不展开讲述。

![TORUS COMBINATION](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_TORUS_COMBINATION.png#center)

## 3. 常用的通信拓扑算法

Torus 网络结构可以解读本文中的物理网络互联结构的一切，而Torus网络的横向与纵向都可以看成ring结构，因此，相应的集合通信拓扑算法都可以看成是Ring-Based 集合通信拓扑算法。

### 3.1 Ring AllReduce

在分布式训练中，Ring 是最基础的互联结构，在本文中Ring AllReduce的应用场景是在服务器内将8张加速卡组环通信进行分布式训练。每个XPU都是这个主机内互联环上的一个计算节点，每个节点都有一个前向和一个后向，它只会向它的前向接收数据，并向它的右向发送数据，如下图所示，8张XPU 通过主机内的私有互联网络组成一个环，当然因为这些通信网络是双工的，这8张XPU训练加速卡也可以看成是通过多个逻辑环互联起来的，同时缺点是，如果这个ring太大，Ring Allreduce的效率也会变得很低。

![RING ARCHITECTURE](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_RING_ARCHITECTURE.png#center)

Ring Allreduce 有两种组合实现策略：
 1）先Reduce后broadcast；
 2）先ScatterReduce后AllGather，这两个策略执行后都会让每个XPU节点得到一样的平均梯度，如下图所示：


![RING ALLREDUCE](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_RING_ALLREDUCE.png#center)

#### 3.1.1 Reduce +broadcast

在Reduce + broadcast里，reduce先将8张卡的梯度reduce sum到master节点 XPU0 上，再通过broadcast将这个总的平均梯度复制给其他XPU，如下图：

![REDUCE BROADCAST](https://github.com/jianye0428/hello-hugo/raw/master/img/posts/tech/DistributedTraining/2022-11-01_DistributedTraining_4/2022-11-01_DistributedTraining_4_REDUCE_BROADCAST.png#center)

Reduce + broadcast这种策略有几个比较大的缺点：
 1）8张卡的数据都reduce sum到一张卡，假设每张卡的梯度是100MB，8张卡就是800MB，这可能存在XPU 0计算很久，而其他7张卡空闲的情况存在，整体效率不高；
 2）XPU0 的网络带宽可能会成为瓶颈，8张卡的数据都只能通过XPU0的互联网络进行reduce和broadcast，在数据量比较大的场景 XPU0的带宽成为瓶颈；
 3）8张XPU不都是两两全互联的，因此，要把8张卡的数据一次Reduce或broadcast，这一点受限于网络互联条件做不到，那么就需要采用 ring或tree的策略进行reduce或broadcast，这样效率也不高。

#### 3.1.2 ScatterReduce + AllGather

Ring AllReduce 的Ring ScatterReduce + Ring AllGather策略组合里，每个 XPU只会从前向接受数据，并发送数据给后向，其算法主要分为：

- ScatterReduce：这一步会先scatter拆分数据块再进行reduce，并且在执行完毕后，每张XPU都会包括一个完整的经过融合的同维梯度；
- AllGather：这一步会进行全局Gather同步，最后所有 XPU都会得到完整的大的整个梯度；

Ring ScatterReduce + Ring AllGather是效率比较高的 Ring AllReduce 组合策略，这个策略考虑到了XPU上的梯度可能很大的情况，比如一个梯度有400MB，在scatterreduce阶段就会先被拆分成 ring上XPU个数份，比如主机内XPU个数等于8，那么 这400MB 就会被 拆分成8份，每份50MB，从而减少了加速卡的计算量以及节约带宽。此外，scatterReduce通过将数据拆分成小块，同时并发进行scatterReduce，从而将通信时间隐藏在计算时间内进而提高Ring AllReduce的效率。

