---
title: Subclustered 指标的均衡
description: 放置约束对平衡和如何处理的影响
author: nipavlo
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: nipavlo
ms.openlocfilehash: fe41f45399670d5ac003e5da6c8da0fb1e0847b2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081637"
---
# <a name="balancing-of-subclustered-metrics"></a>Subclustered 指标的均衡

## <a name="what-is-subclustering"></a>什么是 subclustering

如果具有不同放置约束的服务具有共同的指标并且它们都报告其负载，则会发生 Subclustering。 如果服务报告的负载差异很大，则节点上的总负载会有一个较大的标准偏差，并且看起来像是不均衡群集，即使它具有最佳平衡。

## <a name="how-subclustering-affects-load-balancing"></a>Subclustering 如何影响负载平衡

如果不同节点上的服务报告的负载差异很大，则可能会出现没有的不平衡。 此外，如果 subclustering 导致的虚假不平衡大于实际的不平衡，则可能会混淆资源管理器平衡算法，并在群集中产生完美的平衡。

例如，假设我们有四个服务，它们都报告指标 Metric1 的负载：

* 服务 A –具有放置约束 "NodeType = = Type1"，报告10的负载
* 服务 B-具有放置约束 "NodeType = = Type1"，报告10的负载
* 服务 C-具有放置约束 "NodeType = = Type2"，报告100的负载
* Service D –具有放置约束 "NodeType = = Type2"，报告100的负载
* 而且，我们有四个节点。 其中两个节点的 NodeType 设置为 "Type1"，而另两个设置为 "Type2"

我们有以下放置：

<center>

![Subclustered 放置示例][Image1]
</center>

群集可能看起来不均衡，节点3和4上的负载较大，但在这种情况下，这种情况会产生最佳的平衡。

资源管理器可以识别 subclustering 情况，几乎所有情况下，它可以在给定的情况下产生最佳平衡。

在某些特殊情况下，如果资源管理器无法以最佳方式平衡 subclustered 指标，它仍将检测 subclustering，并将生成运行状况报告，以建议你解决问题。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Subclustering 类型及其处理方式

Subclustering 情况可以分为三个不同的类别。 特定 subclustering 情况的类别决定了资源管理器的处理方式。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>第一个类别–具有非连续节点组的平面 subclustering

此类别具有最简单的 subclustering 形式，其中的节点可以分为不同的组，并且每个服务只能放置在这些组中的一个节点上。 每个节点仅属于一个组和一个组。 上述情况在大多数 subclustering 情况下属于此类别。 

在此类别中的情况下，资源管理器可以产生最佳平衡，无需进一步的干预。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>第二个类别–具有分层节点组的 subclustering

如果一项服务允许的一组节点是另一服务允许的节点组的子集，则会发生这种情况。 这种情况最常见的示例是，一些服务定义了放置约束，而另一个服务没有放置约束，并且可以放置在任何节点上。

示例：

* 服务 A：无放置约束
* 服务 B：放置约束 "NodeType = = Type1"
* 服务 C：放置约束 "NodeType = = Type2"

此配置将在不同服务的节点组之间创建子集间关系。

<center>

![子集超集 subclusters][Image2]
</center>

在这种情况下，有可能会产生不理想的平衡。

资源管理器将识别这种情况，并生成运行状况报告，通知你将服务 A 拆分为两个服务-服务 A1，可以放置在 Type1 节点上，也可以放置在 Type2 节点上。 这将使我们返回到第一类可以最佳平衡的情况。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>第三个类别–节点集之间具有部分重叠的 subclustering

如果可以将某些服务放置到的节点集之间存在部分重叠，则会发生这种情况。

例如，如果有一个名为 NodeColor 的节点属性，并且有三个节点：

* 节点1： NodeColor = 红色
* 节点2： NodeColor = Blue
* 节点2： NodeColor = 绿色

我们有两个服务：

* 服务 A：使用放置约束 "Color = = Red | |Color = = Blue "
* 服务 B：具有放置约束 "Color = = Blue | |Color = = 绿色

因此，可以将服务 A 放置在节点1和2中，服务 B 可放置在节点2和3上。

在这种情况下，有可能会产生不理想的平衡。

资源管理器将识别这种情况，并生成运行状况报告，建议你拆分一些服务。

对于这种情况，资源管理器不能为提议提供如何拆分服务，因为可以完成多个拆分，并且无法估计哪种方法是拆分服务的最佳方式。

## <a name="configuring-subclustering"></a>配置 subclustering

可以通过修改以下配置参数来修改有关 subclustering 资源管理器的行为：
* SubclusteringEnabled-参数确定在执行负载平衡时资源管理器是否需要 subclustering。 如果此参数处于关闭状态，资源管理器将忽略 subclustering，并尝试在全局级别实现最佳平衡。 此参数的默认值为 false。
* SubclusteringReportingPolicy-确定资源管理器将如何发出用于分层和部分重叠 subclustering 的运行状况报告。 如果值为零，则表示有关 subclustering 的运行状况报告已关闭，"1" 表示将为不理想的 subclustering 情况生成警告运行状况报告，值 "2" 将生成 "正常" 运行状况报告。 此参数的默认值为 "1"。

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

通过 ClusterConfig.json 进行独立部署或将 Template.json 用于 Azure 托管群集：

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤
* 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
* 若要了解如何将服务约束为仅置于特定节点上，请参阅[节点属性和放置约束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/service-fabric-cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/service-fabric-cluster-resource-manager-subclustering/subset-superset-nodes.png
