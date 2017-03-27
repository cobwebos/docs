---
title: "Service Fabric 服务的可伸缩性 | Microsoft 文档"
description: "介绍如何缩放 Service Fabric 服务"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/30/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 52dcf341a34478bf4e800d8f1b9d44867b5feaf6


---
# <a name="scaling-service-fabric-applications"></a>缩放 Service Fabric 应用程序
Azure Service Fabric 通过管理服务、分区以及群集中所有节点上的副本，让可缩放应用程序的生成更简单。 这实现了资源利用率的最大化。

可以通过以下两种方式实现 Service Fabric 应用程序的高缩放性：

1. 在服务分区级别进行缩放
2. 在指定的服务实例级别进行缩放

## <a name="scaling-at-the-partition-level"></a>在分区级别进行缩放
Service Fabric 支持分区。 分区功能可将单个服务拆分为多个独立的分区，每个分区具有服务总体状态的某个部分。 [分区概述](service-fabric-concepts-partitioning.md)提供了有关受支持分区方案类型的信息。 每个分区的副本分布在群集中的各个节点上。 请考虑使用具有低键 0、高键 99 和 4 个分区的范围分区方案的服务。 在包含三个节点的群集中，该服务可能如此处所示，按四个副本共享每个节点上的资源的方式进行布局：

<center>
![三节点式分区布局](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

如果增加节点数目，Service Fabric 将通过移动一些现有副本到新节点来利用新节点上的资源。 通过将节点数目增加到四个，现在服务在每个节点（每个节点属于不同分区）上运行三个副本，从而获得更高的资源利用率和性能。

<center>
![四节点式分区布局](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-at-the-service-name-level"></a>在服务名称级别进行缩放
服务实例是应用程序名称和服务类型名称的一个特定实例（请参阅 [Service Fabric 应用程序生命周期](service-fabric-application-lifecycle.md)）。 在创建服务的过程中，指定要使用的分区方案（请参阅 [Service Fabric 服务分区](service-fabric-concepts-partitioning.md)）。

第一个缩放级别是按服务名称进行。 随着旧服务实例变得繁忙，你可以创建服务实例，并可选择赋予不同的分区级别。 这允许新服务使用者使用较不繁忙的服务实例而非较忙碌的实例。

增加容量的一种方法是使用新的分区方案创建新的服务实例。 不过，这会增加复杂性。 因为任何使用客户端需要知道何时以及如何使用命名不同的服务。 另一种替代方法是，管理或中介服务需要就哪个服务和分区应处理每个请求做出决定。

### <a name="example-scenario-embedded-dates"></a>示例方案：嵌入日期
一种可行的方案是将日期信息用作服务名称的一部分。 例如，可以使用如下服务实例：2013 年加入的所有客户具有一个特定名称，2014 年加入的客户具有另一个名称。 此命名方案允许根据日期以编程方式增加名称（随着 2014 年的临近，可按需创建 2014 年的服务实例）。

但是，此方法基于使用超出 Service Fabric 知识范围外的应用程序特定命名信息的客户端。

* *使用命名约定*：2013 年应用程序上线时，创建了一个名为 fabric:/app/service2013 的服务。 临近 2013 年第二季度时，你创建了另一个名为 fabric:/app/service2014 的服务。 这两个服务同为一种服务类型。 在这种方法中，你的客户端需要使用基于年度构建适当服务名称的逻辑。
* *使用查找服务*：另一种模式是提供辅助查找服务，此服务可为所需密钥提供服务的名称。 然后可以通过查找服务来创建新的服务实例。 查找服务本身不会保留任何应用程序数据，它仅保留有关自己创建的服务名称的数据。 因此，对于以上基于年度的示例，客户端将首先联系查找服务以找到处理给定年度数据的服务的名称。 然后，客户端会使用该服务名称执行实际操作。 首次查找的结果可进行缓存。

## <a name="putting-it-all-together"></a>汇总
让我们汇总已在此文中讨论的所有观点，然后讨论另一个方案。

请考虑以下示例：你想要生成一个充当通讯簿的服务，其中保存名称和联系信息。 你要设置多少个用户？ 每个用户将存储多少个联系人？ 想要在第一次构建服务时解决这所有问题确定很难。 选错分区计数的结果可能会导致你以后遇到缩放问题。 但为什么要尝试为所有用户选择出单个分区方案？

在此类情况下，请改为考虑以下模式：
1. 构建“管理器服务”，而不是尝试预先为每个人选择分区方案。
2. 管理器服务作业的目标是在客户注册服务时，查看客户信息。 然后根据该信息_只为该客户_创建_实际_的联系人存储服务的实例。 此类动态服务创建模式有许多好处：

    * 你不必预先猜测所有用户的正确分区计数
    * 数据分段，因为每个客户都有自己的服务副本
    * 每个客户服务的配置方式可以不同，并且可以根据需要基于客户预期的规模，增加或减少包含的分区或副本数。
      * 例如，假设客户支付“黄金”层级 - 他们就可以得到更多副本或更多分区
      * 或者假设客户提供的信息指示他们需要的联系人数为“Small”- 则会只得到几个分区。
    * 你在等待客户出现时，你未在运行一大堆服务实例或副本
    * 如果某个客户要离开，则从你的服务中删除其信息非常简单，就像让管理器删除它创建的服务一样

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

* [Service Fabric 服务的可用性](service-fabric-availability-services.md)
* [Service Fabric 服务分区](service-fabric-concepts-partitioning.md)
* [定义和管理状态](service-fabric-concepts-state.md)



<!--HONumber=Jan17_HO1-->


