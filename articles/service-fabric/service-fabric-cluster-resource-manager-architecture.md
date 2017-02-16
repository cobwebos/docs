---
title: "资源管理器体系结构 | Microsoft 文档"
description: "Service Fabric 群集资源管理器的体系结构概述。"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 67f07bad8f6f89d9e5e68326f0cc194d920e841b


---
# <a name="cluster-resource-manager-architecture-overview"></a>群集资源管理器体系结构概述
为了管理群集中的资源，Service Fabric 群集资源管理器必须包含一些相关的信息。 它必须了解目前存在哪些服务，以及这些服务正在使用的资源的当前（默认）数量。 若要跟踪群集中的可用资源，它必须知道群集中各个节点的容量以及每个节点占用的资源数量。 给定服务的资源消耗量可随时间更改，服务通常关注多种类型的资源。 在不同服务中，可能同时存在实际物理资源和所度量的物理资源。 服务可能会跟踪内存占用率和磁盘使用量等物理指标。 更普遍的是，服务可能会关注“WorkQueueDepth”或“TotalRequests”等逻辑指标。 逻辑和实际指标可能跨许多不同类型的服务使用，或可能仅特定对几项服务使用。

## <a name="other-considerations"></a>其他注意事项
有时，群集的所有者和操作员与服务作者不同，或至少是戴着不同帽子的相同人员。 例如，开发服务时你知道一些有关服务在资源方面的要求以及在理想情况下应如何部署不同组件的信息。 但是，处理该服务在生产环境中的实时站点事件的人员需要执行不同的作业，并且需要不同的工具。 此外，群集和服务都不是在静止状态下配置的：

* 群集中的节点数可以增加和缩减
* 不同大小和类型的节点可以变化不定
* 可以创建、删除服务和更改其所需的资源分配
* 升级或其他管理操作可以在整个群集中运行，操作随时可能失败。

## <a name="cluster-resource-manager-components-and-data-flow"></a>群集资源管理器组件和数据流
群集资源管理器必须跟踪各个服务的需求以及构成这些服务的单个服务对象的资源消耗量。 为了实现此目的，群集资源管理器具有两个概念部件：在每个节点上运行的代理和容错服务。 每个节点上的代理跟踪服务的负载报告、聚合这些报告，并定期报告它们。 群集资源管理器服务从本地代理聚合所有信息，并基于其当前配置进行响应。

请看以下示意图：

<center>
![资源平衡器体系结构][Image1]
</center>

在运行时，有很多更改可能会发生。 例如，假设某些服务使用的资源量发生更改，某些服务出现故障，某些节点加入和离开群集。 节点上的所有更改进行汇总，并定期发送到群集资源管理器服务（1，2），它们在其中再次聚合、分析和存储。 每隔几秒钟，服务就查看更改，并确定是否需要任何操作 (3)。 例如，它可能注意到某些空节点已添加到群集，并确定要将某些服务移到这些节点。 群集资源管理器可能还注意到特定节点已超载，或者某些服务已失败（或删除），在其他位置释放资源。

让我们看看下图，了解接下来发生的情况。 假设群集资源管理器确定需要更改。 它与其他系统服务（尤其是故障转移管理器）进行协调，以进行必要的更改。 然后将所需命令发送到相应节点 (4)。 在此方案中，我们假设资源管理器注意到节点 5 已超载，因此确定要将服务 B 从 N5 移到 N4。 重新配置 (5) 结束时，群集看起来像这样：

<center>
![资源平衡器体系结构][Image2]
</center>

## <a name="next-steps"></a>后续步骤
* 群集资源管理器提供许多用于描述群集的选项。 若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](service-fabric-cluster-resource-manager-cluster-description.md)的文章

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png



<!--HONumber=Jan17_HO1-->


