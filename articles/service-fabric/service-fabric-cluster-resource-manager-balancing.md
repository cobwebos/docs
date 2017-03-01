---
title: "均衡 Azure Service Fabric 群集 | Microsoft Docs"
description: "介绍如何使用 Azure Service Fabric 群集资源管理器平衡群集。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>均衡 Service Fabric 群集
Service Fabric 群集资源管理器支持动态负载更改、对添加或删除节点或服务做出反应、更正约束冲突和重新均衡群集。 但是，执行这些操作的频率是什么，以及如何触发这些操作呢？

与均衡相关的第一组控件是一组计时器。 这些计时器控制群集资源管理器针对需要处理的事项检查群集状态的频率。 共有三种不同的工作类别，每一种都有对应的计时器。 它们具有以下特点：

1. 放置 - 此阶段负责放置任何遗漏的有状态副本或无状态实例。 这包括两项新服务以及处理已失败的有状态副本或无状态实例。 删除和丢弃副本或实例的操作也在此阶段处理。
2. 约束检查 – 此阶段检查并更正系统中不同放置约束（规则）的违规情况。 规则示例：确保节点不超出容量、符合服务的放置约束。
3. 均衡 - 此阶段根据为不同指标配置的所需均衡级别检查是否需要主动重新均衡。 如果需要，将尝试在群集中查找更均衡的排列方式。

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>配置群集资源管理器的步骤和计时器
群集资源管理器可进行的这些不同类型的修正由管理其频率的不同计时器控制。 激发每个计时器时，将会计划任务。 默认情况下，资源管理器将：

* 每 1/10 秒扫描其状态并应用更新（如记录某节点处于关闭状态）
* 每秒设置放置和约束检查标志
* 每五秒设置均衡标志。

这些操作反映在以下配置信息中：

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

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
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

现在，群集资源管理器仅按顺序一次执行这些操作中的一个（这些计时器因此称为“最小间隔”）。 例如，群集资源管理器先处理挂起的创建服务的请求，再均衡群集。 通过指定的默认时间间隔，群集资源管理器扫描并检查需要频繁执行的任何操作，因此在每个步骤结束时进行的更改集通常较小。 通过频繁的小更改，群集资源管理器可以迅速响应群集中发生的事件。 因为许多相同类型的事件往往同时发生，所以默认计时器会提供一些批处理。 默认情况下，群集资源管理器不会扫描群集中数小时的更改，也不会尝试同时处理所有更改。 这会导致大量改动。

群集资源管理器还需要一些其他信息以确定群集是否不均衡。 为此，我们设置了其他两项配置：*均衡阈值*和*活动阈值*。

## <a name="balancing-thresholds"></a>均衡阈值
平衡阈值是触发主动式重新平衡的主要控件。 MinLoadBalancingInterval 计时器仅用于群集资源管理器应执行检查的频率，不代表发生了任何事件。 均衡阈值以某个特定指标来定义群集的不均衡程度，使资源管理器能够考虑它是否不均衡并触发均衡操作。

均衡阈值根据每个指标定义为群集定义的一部分。 有关指标的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-metrics.md)。

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

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

指标的平衡阈值是一个比率。 如果负载最重的节点的负载量除以负载最轻的节点的负载量超过这个数字，群集将被视为不均衡。 因此，群集资源管理器下次检查时会触发均衡。

<center>
![均衡阈值示例][Image1]
</center>

在此示例中，每个服务使用一个单位的指标。 在最上面的示例中，节点的负载上限为&5;，下限为&2;。 假设此指标的均衡阈值为&3;。 由于群集中的比率是 5/2 = 2.5，小于指定的均衡阈值 3，所以群集是均衡的。 群集资源管理器执行检查时，不会触发均衡。

在最下面的示例中，节点的负载上限为&10;，下限为&2;，因此比率为&5;。 5 大于该指标的指定均衡阈值&3;。 因此，下一次引发均衡计时器时，将计划运行重新均衡。 在此情况下，一些负载几乎一定会分配到 Node3。 因为 Service Fabric 群集资源管理器不使用贪婪方法，所以一些负载也可能分配到 Node2。 这样会导致节点间的总体差异最小化，这也是群集资源管理器的目标之一。

<center>
![均衡阈值示例操作][Image2]
</center>

低于均衡阈值不是显示目标。 均衡阈值只是触发器，它告诉群集资源管理器应该查看群集，以确定可以做出哪些改进（如果有）。 实际上，启动均衡搜索并不意味着任何移动。有时群集虽然不均衡，但无法改善此情况。

## <a name="activity-thresholds"></a>活动阈值
有时，虽然节点相对不均衡，但群集中的负载*总量*很低。 缺少负载可能是由于暂时下降，或由于群集是新的且刚刚启动。 在任一情况下，无需花时间均衡群集，因为效果微乎其微。 如果均衡群集，将耗费网络和计算资源进行移动操作，却不会产生任何绝对差异。 为了避免这种情况，可使用名为“活动阈值”的另一种控件。 通过活动阈值，可指定活动的一些绝对下限。 如果没有节点超过此阈值，即使满足均衡阈值，也不会触发均衡。

有关示例，请看下图。 同时假设此指标的均衡阈值保持为 3，且现在的活动阈值为 1536。 在第一种情况下，虽然根据均衡阈值，群集为不均衡状态，但没有任何节点满足活动阈值，因此不会发生任何事件。 在底部的示例中，Node1 远远超过活动阈值。 由于同时超过了指标的均衡阈值和活动阈值，所以计划进行均衡。

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

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

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

均衡阈值和活动阈值都与特定的指标绑定，只有在同时超过同一个指标的均衡阙值和活动阈值时，才触发均衡。

## <a name="balancing-services-together"></a>一起平衡服务
有意思的一点是，群集是否不均衡是群集范围内的决策。 但是我们对其进行修复的方式是移动单个服务副本和实例。 这种说法很合理，是吗？ 如果内存叠加在一个节点上，多个副本或实例可能会对其造成影响。 修复这种不均衡可能需要移动使用不均衡指标的任何有状态副本或无状态实例。

但偶尔也会移动均衡的服务。 在有其他不均衡情况的时候，如果该服务的所有指标都已均衡（已经非常完美了），为何还会发生服务四处移动的情况？ 让我们看一看！

假设有四个服务：Service1、Service2、Service3 及 Service4。 Service1 针对 Metric1 和 Metric2 指标进行报告、Service2 针对 Metric2 和 Metric3 指标进行报告、Service3 针对 Metric3 和 Metric4 指标进行报告，Service4 针对 Metric99 指标进行报告。 你可以看到此处的运行情况。 这里形成了一个链条！ 我们没有&4; 个独立的服务，我们拥有多个相关的服务（Service1、Service2 和 Service3）以及&1; 个独立的服务。

<center>
![一起均衡服务][Image4]
</center>

因此，Metric1 不均衡可能会导致属于 Service3（不报告 Metric1）的副本或实例移动。 这种移动通常是有限的，但是移动幅度可能变大，这取决于 Metric1 究竟有多不均衡以及需要在群集中进行哪些更改才能更正问题。 指标 1、2 或 3 中的不均衡一定不会在 Service4 中引起移动。 因为移动属于 Service4 的副本或实例绝对不会影响指标 1、2 或 3 的均衡，所以这一点毋庸置疑。

群集资源管理器会自动找出相关的服务，因为可能已添加、删除服务，或者服务的指标配置已发生更改。 例如，在两次运行均衡之间，Service2 可能已经重新配置为删除 Metric2。 此更改会中断 Service1 和 Service2 之间的链。 现在有三组相关服务，而不是两组：

<center>
![一起均衡服务][Image5]
</center>

## <a name="next-steps"></a>后续步骤
* 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 若要详细了解指标及其配置方式，请查看[此文](service-fabric-cluster-resource-manager-metrics.md)
* 移动成本是向群集资源管理器发出信号，表示移动某些服务比移动其他服务会产生更高成本的方式之一。 若要了解有关移动成本的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-movement-cost.md)
* 群集资源管理器提供多个限制机制，你可以配置这些限制机制，以减慢群集中的流动。 这些限制通常不是必要的，但如果需要，可以在[此处](service-fabric-cluster-resource-manager-advanced-throttling.md)了解其相关信息

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


