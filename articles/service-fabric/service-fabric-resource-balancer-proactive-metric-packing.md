<properties
   pageTitle="主动指标打包 | Microsoft Azure"
   description="有关在资源平衡器中使用主动指标打包的概述"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 主动指标打包

Azure Service Fabric 资源平衡器的常见配置是为了让每个节点上的每个指标达到相等的使用率。资源平衡器也负责在新的服务请求出现时，为该服务寻找位置。如果没有足够的可用空间放置新的服务副本（所有服务分区的所有副本），资源平衡器会尝试移动现有的工作负荷，以便为其创建空间。虽然这非常正常，但视群集的饱和度和工作负荷拆分程度而定，此操作可能会很耗时。

例如，假设群集已充分利用，但客户想要添加具有较大默认负载（例如一或多个指标的最大节点容量）的新服务，资源平衡器可能需要移动许多副本，才能放置新服务。此外，如果是较大的有状态服务，因为需要复制数据，所以执行必要的移动操作可能需要一些时间。上述两种情况都可能会增加创建新服务的时间。尽管通常服务应会容忍偶尔出现的缓慢创建时间，但某些工作负荷容忍度较低，应尽可能快地进行创建。在稳定状态下，资源平衡器需要确保对群集进行了“碎片整理”，以便提升新工作负荷有足够空间可用的几率。

主动指标打包（又称碎片整理）机制在资源平衡器的平衡阶段运行。目标是通过将工作负荷打包到较少的节点上（而不是在平衡期间将其分散），将服务创建时间降至最低。为指标进行碎片整理配置时，资源平衡器的目标是要达到最大平均标准偏差，而不是用于平衡的最小平均标准偏差。

使用最大偏差时，资源平衡器会尝试尽可能在某些节点上放置最多的服务，并尽可能让最多节点保留为空。除此之外，其中一个放置新服务的基本约束是：副本不能位于同一个升级域或容错域中。由于目标是要能够快速地添加新服务，因此资源平衡器应将目标设为保持升级域和容错域间负载分布的最小标准偏差（每个升级域/容错域的服务负载总和）。这样一来，每个升级域/容错域的可用空间量都会相同。碎片整理也会遵守系统中的其他所有约束，比如相关性、放置约束和节点指标容量。

## 资源平衡器群集配置
在群集清单中，以下配置值可定义资源平衡器之下指标打包功能的整体行为。

### DefragmentationMetrics

碎片整理指标是资源平衡器进行主动打包/碎片整理时应考虑的指标。此列表中应指定所有已配置的指标（如同活动和平衡阈值列表）。如果将指标值指定为“true”，则会将其视为碎片整理指标。如果指定为“false”（或此列表中未指定），则不会将其视为碎片整理指标。

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

平衡阈值控制群集在资源平衡器运行平衡阶段（该阶段会执行指标打包逻辑）之前针对某个特定指标可能达到的零碎程度。如果将指标视为碎片整理指标，平衡阈值即为资源平衡器进行群集碎片整理之前，每个升级域/容错域允许存在的最常使用节点与最少使用节点的比率下限。如果任何升级域或容错域的这项比率小于阈值，碎片整理阶段将开始。

下图显示了两个示例，其中，指定指标的平衡阈值为 10。

![平衡阈值的示例][Image1]

请注意，在这个阶段，节点上的“使用率”并不考虑节点容量所决定的节点大小。它只考虑目前针对指定指标报告的节点绝对使用量。

如果未指定该指标，则默认值为 1。在此情况下，将执行碎片整理，直到群集中每个容错域和每个升级域中至少有一个空节点为止。

值 0 表示平衡阶段期间不考虑此指标，不论是否将其视为碎片整理指标都是如此。

以下代码示例显示，指标的平衡阈值通过群集清单内的 FabricSettings 元素，针对每个指标进行配置。

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息，请参阅：[资源平衡器体系结构](/documentation/articles/service-fabric-resource-balancer-architecture)

[Image1]: ./media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png
 
<!---HONumber=Mooncake_0321_2016-->