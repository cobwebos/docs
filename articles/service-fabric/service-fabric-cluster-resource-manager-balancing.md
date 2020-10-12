---
title: 均衡 Azure Service Fabric 群集
description: 介绍如何使用 Azure Service Fabric 群集 Resource Manager 平衡群集。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416266"
---
# <a name="balancing-your-service-fabric-cluster"></a>均衡 Service Fabric 群集
Service Fabric 群集资源管理器支持动态负载更改、对添加或删除节点或服务做出反应。 还会自动更正约束冲突和主动重新均衡群集。 但这些操作的执行频率是多少，又是什么触发了这些操作？

群集资源管理器可以执行三种不同类别的工作。 它们具有以下特点：

1. 放置 - 此阶段负责放置任何遗漏的有状态副本或无状态实例。 放置既包括新服务也包括处理已失败的有状态副本或无状态实例。 可在此进行删除和丢弃副本或实例。
2. 约束检查 – 此阶段检查并更正系统中不同放置约束（规则）的违规情况。 规则示例：确保节点不超出容量、符合服务的放置约束。
3. 均衡 - 此阶段根据为不同指标配置的所需均衡级别检查是否需要重新均衡。 如果需要，则尝试查找群集中更均衡的排列方式。

## <a name="configuring-cluster-resource-manager-timers"></a>配置群集资源管理器计时器
与均衡相关的第一组控件是一组计时器。 这些计时器控制群集资源管理器检查群集并采取纠正措施的频率。

群集 Resource Manager 可以进行的每种不同类型的修复都由不同的计时器控制，控管修复频率。 激发每个计时器时，会计划任务。 默认情况下，Resource Manager：

* 每 1/10 秒扫描其状态并应用更新（如记录某节点处于关闭状态）
* 每秒设置放置检查标志
* 每秒设置约束检查标志
* 每隔 5 秒设置一次均衡标志

下面是用于管理这些计时器的配置示例：

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

通过用于独立部署的 ClusterConfig.json 或用于 Azure 托管群集的 Template.json：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

现在，群集资源管理器仅按顺序一次执行这些操作中的一个。 因此将这些计时器称为“最小间隔”，将计时器停止时可采取的操作称为“设置标志”。 例如，群集 Resource Manager 处理挂起的请求，以在均衡群集之前创建服务。 可以看到，群集资源管理器根据指定的默认时间间隔扫描它需要频繁执行的任何操作。 通常这意味着在每个步骤中所采取的变更集很小。 通过频繁的小更改，群集资源管理器可以在群集中发生事件时迅速做出响应。 许多相同类型的事件往往同时发生，因此默认计时器可进行某种批处理。 

例如，节点出现故障时，它们可以一次性地对整个容错域执行这样的操作。 会在 *PLBRefreshGap* 后的下一个状态更新过程中捕获所有这些故障。 在以下放置、约束检查和均衡运行的过程中，确定要修正的内容。 默认情况下，群集 Resource Manager 不扫描群集中数小时内进行的更改或尝试一次处理所有更改。 这样会导致大量改动。

群集 Resource Manager 还需要一些其他信息来确定群集是否不均衡。 为此，我们还提供了另外两个配置：*BalancingThresholds* 和 *ActivityThresholds*。

## <a name="balancing-thresholds"></a>均衡阈值
均衡阈值是触发重新均衡的主要控件。 指标的均衡阈值是一个 _比率_。 如果负载最重的节点上某个指标的负载除以负载最轻的节点的负载量超过指标的 *BalancingThreshold*，群集是不均衡的。 因此群集 Resource Manager 进行下一次检查时将触发均衡。 *MinLoadBalancingInterval* 计时器定义群集资源管理器应检查是否需要重新均衡的频率。 检查并不代表发生任何事件。 

均衡阈值根据每个指标定义为群集定义的一部分。 有关指标的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-metrics.md)。

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

通过用于独立部署的 ClusterConfig.json 或用于 Azure 托管群集的 Template.json：

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![均衡阈值示例][Image1]
</center>

在此示例中，每个服务使用一个单位的指标。 在最上面的示例中，节点的负载上限为 5，下限为 2。 假设此指标的均衡阈值为 3。 群集中的比率为 5/2 = 2.5，这小于指定的均衡阈值 3，因此群集被视为均衡。 群集 Resource Manager 进行检查时不会触发均衡。

在底部的示例中，节点的最大负载为 10，最小负载为 2，因此比率为 5。 5 大于该指标的指定均衡阈值 3。 因此，下一次引发均衡计时器时，将计划运行重新均衡。 在此类似情况下，一些负载通常会分配到 Node3。 因为 Service Fabric 群集资源管理器不使用贪婪方法，所以一些负载也可能分配到 Node2。 

<center>

![均衡阈值示例操作][Image2]
</center>

> [!NOTE]
> “均衡”会处理两种不同的策略，管理群集中的负载。 群集资源管理器使用的默认策略是在群集的节点间分发负载。 另一个策略是[碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。 在同一均衡运行的过程中，执行碎片整理。 均衡和碎片整理策略可以用于同一群集中的不同指标。 一个服务可具有均衡和碎片整理两个指标。 对于碎片整理指标，群集中负载的比率低于均衡阈值时，会触发重新均衡。 
>

低于均衡阈值不是直接目标。 均衡阈值只是一个触发器。 均衡运行时，群集资源管理器会确定它可进行哪些改进（如有）。 因为仅仅启动均衡搜索并不意味着会移动任何内容。 有时群集是不均衡的，但约束过度，就无法修正。 或者，改进需要[成本高昂](service-fabric-cluster-resource-manager-movement-cost.md)的移动）。

## <a name="activity-thresholds"></a>活动阈值
有时，虽然节点相当不均衡，但群集中的负载 *总量* 很低。 负载缺乏可能是暂时性的下降，或是因为群集是新的并且刚刚开始引导。 在任一情况下，无需花时间均衡群集，因为效果微乎其微。 如果群集耗费平衡，则需要花费网络和计算资源来移动东西，而不会产生任何大的 *绝对* 差异。 为了避免不必要的移动，可使用名为“活动阈值”的另一种控件。 活动阈值可以指定活动的绝对下限。 如果没有节点高于此阈值，即使达到均衡阈值，也不触发均衡。

假设我们为此指标保留三个均衡阈值。 另外假设具有 1536 个活动阈值。 在第一种情况下，根据均衡阈值，群集为不均衡状态，但没有节点符合活动阈值，因此保持现状。 在底部的示例中，Node1 超过活动阈值。 由于同时超过了指标的均衡阈值和活动阈值，所以计划进行均衡。 有关示例，请看下图： 

<center>

![活动阈值示例][Image3]
</center>

如同平衡阈值，活动阈值通过群集定义根据每个指标进行定义：

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

通过用于独立部署的 ClusterConfig.json 或用于 Azure 托管群集的 Template.json：

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

均衡和活动阈值都绑定到具体指标，只有在同一个指标的均衡阈值和活动阈值都超过时才触发均衡。

> [!NOTE]
> 如未指定，则指标的均衡阈值为 1，活动阈值为 0。 这表示对于任何给定的负载，群集资源管理器将尝试使该指标保持完美平衡。 如果正在使用自定义指标，则建议显式定义指标的均衡和活动阈值。 
>

## <a name="balancing-services-together"></a>一起平衡服务
群集是否非均衡是从整个群集来看。 但解决这种情况的方法是移动单个服务副本和实例。 这种说法很合理，是吗？ 如果内存堆积在某一个节点上，可能是由多个副本或实例造成的。 修复不均衡需要移动所有使用不均衡指标的有状态副本或无状态实例。

但本身不均衡的服务偶尔会移动（请记住之前有关局部权重和全局权重的讨论）。 为什么某服务的所有指标均衡时，该服务会移动？ 请看以下示例：

- 假设有四个服务：Service1、Service2、Service3 及 Service4。 
- Service1 报告指标 Metric1 和 Metric2。 
- Service2 报告指标 Metric2 和 Metric3。 
- Service3 报告指标 Metric3 和 Metric4。
- Service4 报告指标 Metric99。 

可以看到此处的运行情况：这里是一个链条！ 我们没有 4 个独立的服务，我们拥有 3 个相关的服务以及 1 个独立的服务。

<center>

![一起均衡服务][Image4]
</center>

由于此链条，指标 1-4 不均衡可能会导致属于服务 1-3 的副本或实例四处移动。 此外，指标 1、2 或 3 不均衡一定不会在 Service4 中引起移动。 因为移动属于 Service4 的副本或实例绝对不会影响指标 1-3 的均衡，所以这样做毫无意义。

群集资源管理器会自动计算出哪些服务是相关的。 添加、移除或更改服务的指标会影响服务间的关系。 例如，在两次运行均衡之间，Service2 可能已经更新为删除 Metric2。 这会中断 Service1 和 Service2 之间的链接。 现在有三组相关服务，而不是两组：

<center>

![一起均衡服务][Image5]
</center>

## <a name="next-steps"></a>后续步骤
* 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[此文](service-fabric-cluster-resource-manager-metrics.md)
* 移动成本是向群集 Resource Manager 发出信号，表示移动某些服务比移动其他服务会产生更高成本的方式之一。 若要详细了解移动成本，请参阅[此文](service-fabric-cluster-resource-manager-movement-cost.md)
* 群集 Resource Manager 提供多个限制机制，可以配置这些限制机制，以减慢群集中的流动。 这些限制通常不是必要的，但如果需要，可以在[此处](service-fabric-cluster-resource-manager-advanced-throttling.md)了解其相关信息
* 群集资源管理器可以识别并处理子群集（使用放置约束和均衡时有时会出现这种情况）。 若要了解子群集如何影响均衡以及如何处理它，请参阅[此文](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
