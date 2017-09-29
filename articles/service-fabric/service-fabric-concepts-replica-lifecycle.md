---
title: "Azure Service Fabric 中的副本和实例 | Microsoft Docs"
description: "了解副本、实例及其功能和生命周期"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 794cbed0f5072bcc1c18343b9896aad464861c45
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="replicas-and-instances"></a>副本和实例 
本文概述了有状态服务副本和无状态服务实例的生命周期。

## <a name="instances-of-stateless-services"></a>无状态服务的实例
无状态服务的实例是在其中一个群集节点上运行的服务逻辑的副本。 分区内的实例由其 InstanceId 唯一标识。 实例的生命周期可由以下图表进行建模：

![实例生命周期](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
一旦群集资源管理器确定实例的放置，它将进入此生命周期状态。 此时，实例将在节点上启动（应用程序主机已启动，实例已先创建后打开）。 启动完成后，实例将转换为就绪状态。 

如果此实例的应用程序主机或节点发生故障，它将转换为已删除状态。

### <a name="ready-rd"></a>就绪 (RD)
在就绪状态下，实例已启动并且正在节点上运行。 如果这是可靠的服务，则已调用 RunAsync。 

如果此实例的应用程序主机或节点发生故障，它将转换为已删除状态。

### <a name="closing-cl"></a>关闭 (CL)
在关闭状态下，服务结构正在关闭此节点上的实例。 这可能由多种原因所致，例如应用程序升级、负载均衡或正在删除服务。 关闭完成后，它将转换为已删除状态。

### <a name="dropped-dd"></a>已删除 (DD)
在已删除状态下，实例不再在节点上运行。 此时，服务结构正在维护关于此实例的元数据（这将缓慢删除）。

> [!NOTE]
> 可通过使用 `Remove-ServiceFabricReplica` 上的 ForceRemove 选项实现从任意状态到已删除状态的转换。
>

## <a name="replicas-of-stateful-services"></a>有状态服务的副本
有状态服务的副本是在其中一个群集节点上运行的服务逻辑的副本。 此外，副本会保留该服务的状态的副本。 以下两个相关的概念介绍了有状态副本的生命周期和行为：
- 副本生命周期
- 副本角色

以下讨论介绍了持久的有状态服务。 对于临时的（或内存中）有状态服务，关闭状态和已删除状态等效。

![副本生命周期](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
InBuild 副本是正在创建或准备加入副本集的副本。 根据副本角色，IB 具有不同的语义。 

如果 InBuild 副本的应用程序主机或节点发生故障，它将转换为关闭状态。

####<a name="primary-inbuild-replicas"></a>主 InBuild 副本 
主 InBuild 副本是分区的第一个副本。 这通常发生在创建分区时。 当分区的所有副本均重启或删除时，主 InBuild 副本也会出现。

####<a name="idlesecondary-inbuild-replicas"></a>空闲辅助 InBuild 副本
这些副本可以是由群集资源管理器创建的新副本。 它们也可以是已关闭并需要添加回副本集的现有副本。 这些副本先由主要副本播种或生成，然后才能加入到副本集作为活动的次要副本并参与操作的仲裁确认。

####<a name="activesecondary-inbuild-replicas"></a>活动的辅助 InBuild 副本
在某些查询中可能会观察到此状态。 这是一种优化，其中副本集不会更改，但需要生成一个副本。 副本本身遵循常规状态的计算机转换（如副本角色部分所述）。

### <a name="ready-rd"></a>就绪 (RD)
就绪副本是参与复制和操作的仲裁确认的副本。 就绪状态适用于主要副本和活动的次要副本。

如果就绪副本的应用程序主机或节点发生故障，它将转换为关闭状态。

### <a name="closing-cl"></a>关闭 (CL)
在以下情况下，副本会进入关闭状态：

- **关闭副本代码**：服务结构可能需要关闭副本的运行代码。 这可能出于多种原因，例如应用程序、结构或基础结构升级或由于副本报告的错误等。副本关闭完成后，副本将转换为关闭状态。 与存储在磁盘上的此副本关联的持久状态未得到清理。

- **从群集中删除副本**：服务结构可能需要删除持久状态并关闭副本的运行代码。 这可能出于多种原因，例如负载均衡

### <a name="dropped-dd"></a>已删除 (DD)
在已删除状态下，实例不再在节点上运行。 节点上也没有保留任何状态。 此时，服务结构正在维护关于此实例的元数据（最终也将删除）。

### <a name="down-d"></a>关闭 (D)
在关闭状态下，副本代码未运行，但该副本的持久状态存在于该节点上。 副本可能会因多种原因而关闭，例如节点关闭、副本代码发生故障、应用程序升级、副本错误等。

关闭的节点可按需由服务结构打开，例如当完成节点上的升级时等。

副本角色在关闭状态下不相关。

### <a name="opening-op"></a>打开 (OP)
当服务结构需要再次备份副本时，关闭的副本将进入打开状态。 例如，这可能出现在应用程序的代码升级在节点上完成后。 

如果打开的副本的应用程序主机或节点发生故障，它将转换为关闭状态。

副本角色在打开状态下不相关。

### <a name="standby-sb"></a>备用 (SB)
备用副本是在关闭后打开的持久服务的副本。 如果需要将另一副本添加到副本集，此副本可供服务结构使用（因为它已经具有状态的某些部分，生成过程将更快）。 在 StandByReplicaKeepDuration 到期后，备用副本将丢弃。

如果备用副本的应用程序主机或节点发生故障，它将转换为关闭状态。

副本角色在备用状态下不相关。

> [!NOTE]
> 任何未关闭或删除的副本将视为可*启动*的副本。
>

> [!NOTE]
> 可通过使用 `Remove-ServiceFabricReplica` 上的 ForceRemove 选项实现从任意状态到已删除状态的转换。
>

## <a name="replica-role"></a>副本角色 
副本的角色确定其在副本集内的功能。

- **主要副本 (P)**：副本集有一个主要副本，该副本负责执行读取和写入操作。 
- **活动的次要副本 (S)**：此类副本用于从主要副本接收状态更新、应用这些更新并发送回确认。 副本集内有多个活动的次要副本，这些副本的数量确定服务可以处理的错误数量。
- **空闲的次要副本 (I)**：这些副本由主要副本生成，即它们在升级为活动的次要副本前从主要副本接收状态。 
- **无 (N)**：这些副本在副本集内没有任何职责。
- **未知 (U)**：在副本从服务结构接收任何 ChangeRole api 调用前，该角色为副本的初始角色。

以下部分介绍了副本角色转换以及它们可能会发生的一些示例方案：

![副本角色](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P：创建新的主要副本
- U -> I：创建新的空闲副本
- U -> N：删除备用副本
- I -> S：空闲的次要副本升级为活动的次要副本，以便其确认构成仲裁。
- I -> P：空闲的次要副本升级为主要副本。 当空闲的次要副本成为主要副本的正确候选项时，在特殊的重新配置下可能发生此情况。
- I -> N：删除空闲的次要副本。
- S -> P：活动的次要副本升级为主要副本。 这可能由于主要副本的故障转移或由群集资源管理器启动的主要副本移动，以响应应用程序升级或负载均衡等。
- S -> N：删除活动的次要副本。
- P -> S：主要副本降级。 这可能由于群集资源管理器启动的主要副本移动，以响应应用程序升级或负载均衡等。
- P -> N：删除主要副本

> [!NOTE]
> 对开发人员而言，更高级别的编程模型（如 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 和 [Reliable Services](service-fabric-reliable-services-introduction.md)）可以隐藏副本角色的概念。 在执行组件中，角色的概念是不必要的，而在服务中，大多数情况下都得到了很大程度的简化。
>

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

- [Reliable Services 生命周期 - C#](service-fabric-reliable-services-lifecycle.md)

