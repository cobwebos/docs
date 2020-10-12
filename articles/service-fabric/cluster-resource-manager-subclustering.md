---
title: 子聚类化指标的均衡
description: 放置约束对均衡的影响以及如何处理这种影响
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183104"
---
# <a name="balancing-of-subclustered-metrics"></a>子聚类化指标的均衡

## <a name="what-is-subclustering"></a>什么是子聚类化

当具有不同放置约束的服务具有共同的指标并且都报告自身的负载时，就会发生子聚类化。 如果各个服务报告的负载存在明显的差异，则节点上的总负载会出现较大的标准偏差，使群集看似不均衡，即使它保持了可能情况下的最佳均衡。

## <a name="how-subclustering-affects-load-balancing"></a>子聚类化对负载均衡造成何种影响

如果不同节点上的服务报告的负载存在明显的差异，则看似可能存在很大的不均衡性，但实际上并没有。 此外，如果子聚类化导致的虚假不均衡性超过了实际的不均衡性，可能会给资源管理器均衡算法造成混淆，使群集中的均衡性欠佳。

例如，假设有四个服务，它们全都报告负载的指标 Metric1：

* 服务 A –具有放置约束 "NodeType = = 前端"，报告10的负载
* 服务 B-具有放置约束 "NodeType = = 前端"，报告10的负载
* 服务 C-具有放置约束 "NodeType = = 后端"，报告100的负载
* Service D-具有放置约束 "NodeType = = 后端"，报告100的负载
* 另外，我们有四个节点。 其中两个节点的 NodeType 设置为“Frontend”，另两个节点的 NodeType 设置为“Backend”

放置约束如下：

<center>

![Subclustered 放置示例][Image1]
</center>

群集可能看似不均衡，节点 3 和 4 上的负载较大，但在此情况下，此放置约束会产生可能最佳的均衡。

资源管理器可以识别子聚类化情况，在几乎所有的情况下，它都能够为给定的场合产生最佳均衡。

在某些特殊情况下，如果资源管理器无法为子聚类化指标提供最佳均衡，它仍会检测子聚类化，并生成运行状况报告，为你提供解决问题的建议。

## <a name="types-of-subclustering-and-how-they-are-handled"></a>子聚类化的类型及其处理方式

子聚类化情况可以分为三种不同的类别。 特定子聚类化情况的类别决定了资源管理器对它的处理方式。

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>第一种类别 – 包含分离节点组的平缓子聚类化

此类别是最简单的子聚类化形式，其中的节点可以划分为不同的组，并且每个服务只能放置到其中一个组中的节点上。 每个节点仅属于一个组。 上述情况就是属于此类别，而大多数子聚类化情况也都属于此类别。 

对于此类别中的情况，资源管理器可以产生最佳均衡，且无需进一步的干预。

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>第二种类别 – 包含分层节点组的子聚类化

如果一个服务允许的节点组是另一个服务允许的节点组的子集，则会发生这种情况。 这种情况最常见的示例是，某个服务有一个定义的放置约束，但另一个服务没有放置约束，因此后者可以放置在任何节点上。

示例：

* 服务 A：无放置约束
* 服务 B：放置约束“NodeType==Frontend”
* 服务 C：放置约束“NodeType==Backend”

此配置将在不同服务的节点组之间创建子集-超集关系。

<center>

![子集超集 subclusters][Image2]
</center>

在此情况下，有可能会产生欠佳的均衡。

资源管理器将识别这种情况，并生成运行状况报告，通知你将服务 A 拆分为两个服务-服务 A1，可以放置在前端节点上，并可以放置在后端节点上。 这样，我们就会重新遇到能够实现最佳均衡的第一种类别的情况。

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>第三个类别–节点集之间具有部分重叠的 subclustering

如果可以放置某些服务的节点集之间存在部分重叠，则会发生这种情况。

例如，如果存在名为 NodeColor 的节点属性，并且存在三个节点：

* 节点 1：NodeColor=Red
* 节点 2：NodeColor=Blue
* 节点 2：NodeColor=Green

此外有两个服务：

* 服务 A：放置约束为“Color==Red || Color==Blue”
* 服务 B：放置约束为“Color==Blue || Color==Green”

因此，服务 A 可以放置在节点 1 和 2 上，服务 B 可以放置在节点 2 和 3 上。

在此情况下，有可能会产生欠佳的均衡。

资源管理器会识别这种情况，并生成运行状况报告，建议拆分某些服务。

对于这种情况，资源管理器无法建议如何拆分服务，因为可以执行多次拆分，并且无法评估哪种服务拆分方式是最佳的。

## <a name="configuring-subclustering"></a>配置子聚类化

可以通过修改以下配置参数，来修改资源管理器在子聚类化方面的行为：
* SubclusteringEnabled - 此参数决定了资源管理器在执行负载均衡时是否会考虑子聚类化。 如果禁用此参数，则资源管理器会忽略子聚类化，并尝试在全局级别实现最佳均衡。 此参数的默认值为 false。
* SubclusteringReportingPolicy - 确定资源管理器如何发出有关分层和部分重叠子聚类化的运行状况报告。 值为“0”表示禁用有关子聚类化的运行状况报告，值为“1”表示将为欠佳子聚类化情况生成警告运行状况报告，值为“2”表示将生成“正常”运行状况报告。 此参数的默认值为“1”。

ClusterManifest.xml：

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

通过用于独立部署的 ClusterConfig.json 或用于 Azure 托管群集的 Template.json：

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
* 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[均衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
* 若要了解如何将服务约束为仅放置在特定的节点上，请参阅[节点属性和放置约束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
