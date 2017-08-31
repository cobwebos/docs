---
title: "Service Fabric 群集 Resource Manager 中的限制 | Microsoft 文档"
description: "了解如何配置 Service Fabric 群集 Resource Manager 提供的限制。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/19/2017

---

# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>限制 Service Fabric 群集资源管理器
即使已正确配置群集 Resource Manager，但群集有时也会中断。 例如，可能同时发生节点和容错域故障 - 升级时如果发生这种情况会怎么样？ 群集资源管理器始终尝试修复所有问题，同时占用群集的资源，尝试重新整理和修复群集。 限制有助于提供一个停止机制，让群集可以使用资源进行稳定 - 节点恢复正常、网络分区修复、部署已更正的部分。

为了帮助实现这些目的，Service Fabric 群集 Resource Manager 提供了多个限制机制。 这些限制都是相当大的约束。 通常未经仔细地规划和测试，不应更改。

如果更改群集资源管理器的限制，则应将其调整到预期的实际负载。 你可能会需要让某些限制到位，即使这意味着群集需要更长时间才能在一些情况下达到稳定状态。 确定限制的正确值时，需要进行测试。 限制需要足够高，群集才能在合理的时间内对更改做出响应，需要足够低才能实际防止占用过多资源。 

大多数情况下，我们已了解客户使用限制，因为这些限制已在资源受限环境中。 一些例子包括各个节点中受限制的网络带宽或由于吞吐量限制不能并行生成多个有状态副本的磁盘。 如果没有限制，操作会占用大量的这些资源，导致操作失败或很慢。 在这些情况下，客户使用限制并知道他们正在延长使群集达到稳定状态所需的时间量。 客户还了解，在受到限制时，群集最终会在总体可靠性较低的状态下运行。


## <a name="configuring-the-throttles"></a>配置限制

Service Fabric 具有两种机制用于限制副本移动数。 Service Fabric 5.7 之前已存在的默认机制将限制以允许移动的绝对数表示。 这不适用于所有大小的群集。 具体而言，对于大型群集，该默认值太小，均衡速度明显变慢（即使是必需的），但对较小的群集又不起作用。 基于百分比的限制已取代这项旧机制，该机制在服务数和节点数定期更改的动态群集中能够更好地缩放。

限制是基于群集中副本数的百分比进行的。 例如，利用基于百分比的限制可以表达规则：“不要在 10 分钟时间间隔内移动 10% 以上的副本”。

基于百分比的限制的配置设置如下：

  - GlobalMovementThrottleThresholdPercentage - 任何时候群集中允许的最大移动数，以群集中副本总数的百分比表示。 0 表示没有限制。 默认值为 0。 如果指定了此设置和 GlobalMovementThrottleThreshold，则使用更保守的限制。
  - GlobalMovementThrottleThresholdPercentageForPlacement - 放置阶段允许的最大移动数，以群集中副本总数的百分比表示。 0 表示没有限制。 默认值为 0。 如果指定了此设置和 GlobalMovementThrottleThresholdForPlacement，则使用更保守的限制。
  - GlobalMovementThrottleThresholdPercentageForBalancing - 均衡阶段允许的最大移动数，以群集中副本总数的百分比表示。 0 表示没有限制。 默认值为 0。 如果指定了此设置和 GlobalMovementThrottleThresholdForBalancing，则使用更保守的限制。

指定限制百分比时，将 5% 指定为 0.05。 控制这些限制的间隔为 GlobalMovementThrottleCountingInterval，以秒为单位进行指定。


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>基于默认值计数的限制
如果具有较旧的群集或仍将这些配置保留在之后已升级的群集中，则提供此信息。 一般情况下，建议将这些限制替换为以上基于百分比的限制。 由于基于百分比的限制默认是禁用的，这些限制保持为群集的默认限制，直到被禁用并替换为基于百分比的限制。 

  - GlobalMovementThrottleThreshold - 此设置控制一段时间内群集中移动的总数。 时间量指定为 GlobalMovementThrottleCountingInterval，以秒为单位。 GlobalMovementThrottleThreshold 的默认值为 1000，GlobalMovementThrottleCountingInterval 的默认值为 600。
  - MovementPerPartitionThrottleThreshold - 此设置控制一段时间内针对任何服务分区的移动总数。 时间量指定为 MovementPerPartitionThrottleCountingInterval，以秒为单位。 MovementPerPartitionThrottleThreshold 的默认值为 50，MovementPerPartitionThrottleCountingInterval 的默认值为 600。

这些限制的配置遵循与基于百分比的限制相同的模式。

## <a name="next-steps"></a>后续步骤
- 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
- 群集 Resource Manager 提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇[介绍 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章

