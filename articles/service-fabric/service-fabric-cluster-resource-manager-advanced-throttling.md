---
title: "Service Fabric 群集资源管理器中的限制 | Microsoft 文档"
description: "了解如何配置 Service Fabric 群集资源管理器提供的限制。"
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>限制 Service Fabric 群集资源管理器的行为
即使已正确配置群集资源管理器，但群集有时也会中断。 例如，可能同时发生节点或容错域故障 - 升级时如果发生这种情况会怎么样？ 群集资源管理器将尝试修复所有内容，但这可能会在群集中引入改动。 限制有助于提供一个停止机制，让群集可以使用资源稳定自身 - 节点恢复正常、网络分区修复、部署已更正的部分。

为了帮助实现这些目的，Service Fabric 群集资源管理器提供了多个限制机制。 这些限制是相当大的约束。 除非已对群集可以并行执行的工作量仔细地进行数学计算，否则，不应更改这些设置的默认值。

限制具有 Service Fabric 团队已通过体验发现适用的默认值。 如果需要更改它们，应根据预期的实际负载对它们进行优化。 你可能会需要让某些限制到位，即使这意味着群集需要更长时间才能在主流情况下达到稳定状态。

## <a name="configuring-the-throttles"></a>配置限制
下面是默认包含的限制：

* GlobalMovementThrottleThreshold - 此设置控制一段时间内（已定义为 GlobalMovementThrottleCountingInterval，以秒为单位的值）群集中移动的总数
* MovementPerPartitionThrottleThreshold - 此设置控制一段时间内（MovementPerPartitionThrottleCountingInterval，以秒为单位的值）针对任何服务分区的移动总数

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

大多数情况下，我们已了解客户使用这些限制，因为这些限制已在资源受限环境中。 该环境的一些例子包括各个节点中受限制的网络带宽或由于吞吐量限制不能并行生成多个副本的磁盘。 此类限制意味着为响应故障而触发的操作将失败或执行速度很慢，甚至没有限制。 在这些情况下，客户知道他们正在延长使群集达到稳定状态所需的时间量。 客户还了解，在受到限制时，群集最终会在总体可靠性较低的状态下运行。

## <a name="next-steps"></a>后续步骤
* 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章



<!--HONumber=Jan17_HO1-->


