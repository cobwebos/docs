---
title: "Azure Service Fabric 中的指标重整 | Microsoft 文档"
description: "概述如何对 Service Fabric 中的指标使用重整或打包作为策略"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 5ef6381f7d182c818171eca3e3d32a00bc30268e


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Service Fabric 中指标和负载的重整
Service Fabric 群集资源管理器主要与分布负载方面的平衡有关 - 确保均衡使用群集中的节点。 若要幸免于故障，让工作负荷分布是最安全的布局，因为它可确保某个故障不会导致给定的工作负荷大部分失效。 Service Fabric 群集资源管理器还支持另一种策略 - 重整。 重整通常意味着我们应实际尝试合并指标，而不是尝试将指标的使用分布到群集中。 合并是我们一般策略的幸运反转 - 群集资源管理器的目标是增加偏差，而不是将指标负载的平均标准偏差最小化。 但我们为什么要使用此策略？

如果你已在群集中的节点之间平均分散负载，则已消耗了节点必须提供的某些资源。 但是，某些工作负荷会创建使用大多数节点的极大服务。 假若这样，节点的 75% 到 95% 的资源最终可能会专用于单个服务对象。 大型工作负荷不是问题。 群集资源管理器在创建服务时就确定它需要重新组织群集，以便为此大型工作负荷腾出空间。 但是，在此期间该工作负荷必须等待在群集中进行计划。

如果有许多服务和状态来回移动，则可能需要很长时间才能将大型工作负荷放置在群集中。 这可能是，群集中的其他工作负荷很大，因此来回移动需要较长时间。 Service Fabric 团队在模拟此情况时测量了创建时间。 我们发现，如果服务足够大，并且群集的利用率很高，则这些大型服务的创建会很慢。 为了处理这种情况，我们引入了重整作为一种平衡策略。 我们发现，对于大型工作负荷，尤其是创建时间很重要的大型工作负荷，重整确实可帮助这些新的工作负荷在群集中获得计划。

可以配置重整指标，让群集资源管理器主动尝试将服务负载紧缩到更少的节点上。 这有助于确保即使是对于大型服务，也（几乎）始终有空间。 这允许在必要时快速创建此类服务。

大多数人无需重整。 服务通常应很小，因此不难在群集中为它们找到空间。 但是，如果需要快速创建大型服务（并愿意接受其他折衷方案），则重整策略适合你。

那么，什么是折衷方案？ 大部分重整可能会增加失败的影响力（因为更多服务在发生故障的节点上运行）。 此外，重整可确保群集中的某些资源在等待工作负荷进行计划时未被利用。

下图提供了两个不同群集的直观表示，其中一个已经过重整，另一个则没有经过重整。 在平衡的情况下，请考虑放置其中一个最大的服务对象所需的移动数。 将该数目与经过重整的群集进行比较，在经过重整的群集中大型工作负荷可以立即放置在四个或五个节点中。

<center>
![比较平衡的群集与经过重整的群集][Image1]
</center>

## <a name="defragmentation-pros-and-cons"></a>重整的优点和缺点
从概念上讲，重整有其他哪些利弊呢？ 我们建议你先彻底度量工作负荷，然后再启用重整指标。 下面是要注意的事项一览表：

| 重整的优点 | 重整的缺点 |
| --- | --- |
| 能够更快地创建大型服务 |将负载集中到更少的节点，增大资源争用 |
| 在创建期间实现较少的数据移动 |故障可能影响更多服务，并导致更多的服务流动 |
| 能够丰富描述要求和空间的回收 |更复杂的整体资源管理配置 |

可以混合同一群集中的重整指标和标准指标。 群集资源管理器会尝试尽可能多地合并重整指标，同时分配出其他指标。 如果没有服务共享这些指标，则结果可以为好。 确切结果取决于平衡指标数量与重整指标数量的比、两者重叠多少、其权重、当前负载等因素。 需要进行试验来确定所需的准确配置。

## <a name="configuring-defragmentation-metrics"></a>配置重整指标
配置重整指标是群集中的全局决策，你可以选择单个指标进行重整：

ClusterManifest.xml：

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Disk",
          "value": "true"
      },
      {
          "name": "CPU",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>后续步骤
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章
* 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[此文](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Jan17_HO1-->


