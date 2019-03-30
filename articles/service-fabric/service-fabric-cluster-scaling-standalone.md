---
title: Azure Service Fabric 独立群集缩放 | Microsoft Docs
description: 了解如何横向或纵向扩展、放大或缩减 Service Fabric 独立群集。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 05049b9b08b4630c4299a6d3054c7815b082af52
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663377"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>缩放 Service Fabric 独立群集
Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 群集可以包含数千个节点。 创建 Service Fabric 群集后，可以群集横向缩放（更改节点数）或纵向缩放（更改节点资源）该群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

为何缩放群集？ 应用程序的需求会不断变化。  可能需要增加群集资源来满足更多的应用程序工作负荷或网络流量，或者在需求下降时减少群集资源。

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>横向扩展和缩减
更改群集中的节点数。  新节点加入群集后，[群集资源管理器](service-fabric-cluster-resource-manager-introduction.md)会将服务移到其中，导致现有节点上的总负载减少。  此外，如果群集的资源未被有效利用，可以减少节点数量。  节点退出群集后，服务会移出这些节点，剩余节点上的负载会增大。  减少 Azure 中运行的群集的节点数可以节省资金，因为我们是根据 VM 的数量付费，而不是根据这些 VM 上的工作负荷付费。  

- 优点：理论上无限缩放。  如果应用程序采用可伸缩性设计，则可以通过添加更多节点来实现无限扩充。  使用云环境中的工具可以轻松添加或删除节点，因此可以方便地调整容量，并且只需为使用的资源付费。  
- 缺点：应用程序必须采用[可伸缩性设计](service-fabric-concepts-scalability.md)。  应用程序数据库和持久性可能需要更多的体系结构工作才能正常缩放。  但是，Service Fabric 有状态服务中的[可靠集合](service-fabric-reliable-services-reliable-collections.md)能够大大简化应用程序数据的缩放。

独立群集允许在本地或所选的云提供程序中部署 Service Fabric 群集。  节点类型由物理机或虚拟机构成，具体取决于部署。 与 Azure 中运行的群集相比，缩放独立群集的过程牵涉到的工作要略多一些。  必须手动更改群集中的节点数，然后运行群集配置升级。

删除可能启动多个升级的节点。 某些节点标有 `IsSeedNode=”true”` 标记，可使用 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) 通过查询群集清单进行标识。 在此类方案中，因为种子节点需要移动，因此删除此类节点相比其他节点需要较长时间。 群集必须至少维护三个主节点类型的节点。

> [!WARNING]
> 我们建议你不要将节点数降低到群集的[可靠性层的群集大小](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)以下。 这会干扰 Service Fabric 系统服务在群集中复制的能力，并且会破坏群集的稳定性或可能销毁群集。
>

缩放独立群集时，请记住以下准则：
- 应逐个节点地替换主节点，而不是批量删除再添加。
- 删除节点类型之前，请检查是否存在任何引用该节点类型的节点。 请在删除相应节点类型之前删除这些节点。 删除所有相应节点后，可从群集配置中删除节点类型并使用 [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) 开始配置升级。

有关详细信息，请参阅[缩放独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

## <a name="scaling-up-and-down-or-vertical-scaling"></a>纵向扩展和缩减 
更改群集中节点的资源（CPU、内存或存储）。
- 优点：软件和应用程序体系结构保持不变。
- 缺点：有限缩放，因为在单个节点上可以增加的资源量有限制。 会造成停机，因为需要使物理机或虚拟机脱机才能添加或删除资源。

## <a name="next-steps"></a>后续步骤
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

