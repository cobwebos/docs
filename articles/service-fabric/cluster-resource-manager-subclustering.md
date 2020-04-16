---
title: 子群集指标的平衡
description: 放置约束对平衡的影响及如何处理
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430638"
---
# <a name="balancing-of-subclustered-metrics"></a>子群集指标的平衡

## <a name="what-is-subclustering"></a>什么是子群集

当具有不同放置约束的服务具有通用指标并且它们都报告其负载时，就会发生子群集。 如果服务报告的负载差异很大，则节点上的总负载将具有较大的标准偏差，并且即使具有最佳平衡，群集看起来也不平衡。

## <a name="how-subclustering-affects-load-balancing"></a>子群集如何影响负载平衡

如果服务在不同节点上报告的负载差异很大，则可能看起来存在一个不存在的大不平衡。 此外，如果子聚类引起的假不平衡大于实际不平衡，则有可能混淆资源管理器平衡算法，并在群集中产生次优平衡。

例如，假设我们有四个服务，它们都报告指标公制1的负载：

* 服务 A = 具有放置约束"NodeType_Type1"，报告负载为 10
* 服务 B = 具有放置约束"NodeType_Type1"，报告负载为 10
* 服务 C = 具有放置约束"NodeType_Type2"，报告负载为 100
* 服务 D = 具有放置约束"NodeType_Type2"，报告负载为 100
* 我们有四个节点。 其中两个将 NodeType 设置为"类型 1"，另外两个设置为"类型 2"

我们有以下位置：

<center>

![子群集放置示例][Image1]
</center>

群集可能看起来不平衡，我们在节点 3 和 4 上负载较大，但在此情况下，此放置可以创造最佳平衡。

资源管理器可以识别子群集情况，在几乎所有情况下，它可以为给定的情况产生最佳平衡。

对于某些特殊情况，当资源管理器无法以最佳方式平衡子群集指标时，它仍将检测到子群集，并将生成运行状况报告，建议您解决问题。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>子群集的类型及其处理方式

子群集情况可分为三个不同的类别。 特定子群集情况的类别决定了资源管理器将如何处理这种情况。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>第一类 = 具有不相交节点组的平面子群集

此类别具有最简单的子群集形式，其中节点可以分隔成不同的组，并且每个服务只能放置在这些组中的节点上。 每个节点仅属于一个组和一个组。 上述情况属于此类别，大多数子群集情况都属于此类别。 

对于此类别中的情况，资源管理器可以产生最佳平衡，无需进一步干预。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>第二类 = 分层节点组的子群集

当一个服务允许的一组节点是另一个服务允许的节点组的子集时，就会发生这种情况。 这种情况的最常见示例是，某些服务定义了放置约束，而另一个服务没有放置约束，可以放置在任何节点上。

示例：

* 服务 A：无放置约束
* 服务 B：放置约束"节点类型=类型 1"
* 服务 C：放置约束"节点类型=类型 2"

此配置在不同服务的节点组之间创建子集超集关系。

<center>

![子集超级集子群集][Image2]
</center>

在这种情况下，有可能取得次优平衡。

资源管理器将识别这种情况并生成运行状况报告，建议您将服务 A 拆分为两个服务 - 可放置在 Type1 节点的服务 A1 和服务 A2 可放置在 Type2 节点上。 这将使我们回到第一类情况，可以优化平衡。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>第三类 – 节点集之间部分重叠的子群集

当节点集之间部分重叠时，就会发生这种情况，某些服务可以放在这些节点上。

例如，如果我们有一个节点属性称为 NodeColor，并且我们有三个节点：

* 节点 1：节点颜色=红色
* 节点 2：节点颜色*蓝色
* 节点 2：节点颜色=绿色

我们有两种服务：

* 服务 A：带放置约束"颜色=红色 |颜色=蓝色"
* 服务 B：带放置约束"颜色=蓝色 |颜色=绿色"

因此，服务 A 可以放置在节点 1 和 2 上，服务 B 可以放置在节点 2 和 3 上。

在这种情况下，有可能取得次优平衡。

资源管理器将认识到这种情况，并生成运行状况报告，建议您拆分某些服务。

对于这种情况，资源管理器无法给出如何拆分服务的建议，因为可以执行多个拆分，并且无法估计哪种方式是拆分服务的最佳方式。

## <a name="configuring-subclustering"></a>配置子群集

可以通过修改以下配置参数来修改资源管理器有关子群集的行为：
* 子群集启用 - 参数确定资源管理器在进行负载平衡时是否会考虑子群集。 如果关闭此参数，资源管理器将忽略子群集，并尝试在全局级别上实现最佳平衡。 此参数的默认值为 false。
* 子群集报告策略 - 确定资源管理器如何为分层和部分重叠子群集发出运行状况报告。 值为零意味着关闭有关子群集的运行状况报告，"1"表示将针对次优次群集情况生成警告运行状况报告，并且值"2"将生成"确定"运行状况报告。 此参数的默认值为"1"。

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
* 要了解如何将服务限制为仅放置在某些节点上，请参阅[Node 属性和放置约束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
