<properties
   pageTitle="节点缓冲区百分比 | Microsoft Azure"
   description="资源平衡器中节点缓冲区百分比角色的概述"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 节点缓冲区百分比概述

目前，客户可以将节点的容量限制指定为约束，而资源平衡器会根据约束优先级来遵守此约束。如果容量的约束优先级很高（不能违反节点容量）且群集节点的使用率也很高，有可能不会立即进行故障转移，或者可能违反节点容量。

如果包含主要副本的节点发生故障，且包含次要副本的节点快达到节点容量，则会进行问题扫描。在此情况下，如果主要负载大于次要负载，次要副本一定要过度使用节点或复制副本，才能立即升级。

运行主动打包逻辑时，较高的群集节点数量会接近节点容量限制。节点缓冲区百分比是一项功能，可让客户指定应保留可用的节点百分比，藉此避免在故障转移期间增加故障转移时间或过度使用节点的情况。不应将新服务的副本添加到节点缓冲区空间，但资源平衡器应能够使用总节点容量（包含缓冲区空间）进行故障转移并添加缺失的副本。

即使主动指标打包功能关闭，仍可使用此功能。

以下代码示例显示，指标的节点缓冲区百分比通过群集清单内的 FabricSettings 元素，针对每个指标进行配置。

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

名为“MetricName”的指标值 0.1 表示针对指标“MetricName”，每个节点容量都应保持 10% 的可用容量。

如果在此节中未指定值，则将使用 0 作为默认值。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息，请参阅：[资源平衡器体系结构](/documentation/articles/service-fabric-resource-balancer-architecture)

<!---HONumber=Mooncake_0321_2016-->