---
title: 如何在 Azure Service Fabric 中删除节点类型 | Microsoft Docs
description: 了解如何在 Azure Service Fabric 中删除节点类型
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981398"
---
# <a name="remove-a-service-fabric-node-type"></a>删除 Service Fabric 节点类型

使用 [Remove-AzureRmServiceFabricNodeType ](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) 删除 Service Fabric 节点类型。

调用 Remove-AzureRmServiceFabricNodeType 时发生的两个操作是：
1.  删除节点类型后面的虚拟机规模集 (VMSS)。
2.  对于该节点类型中的所有节点，将从系统中删除该节点的整个状态。 如果该节点上有服务，则首先将服务移出到另一个节点。 如果群集管理器找不到副本/服务的节点，则会延迟/阻止该操作。

> [!NOTE]
> 建议不要频繁使用 Remove-AzureRmServiceFabricNodeType 从生产群集中删除节点类型。 在这种情况下，它是一个非常危险的命令，因为它基本上删除了节点类型后面的虚拟机规模集资源。 当调用 Remove-AzureRmServiceFabricNodeType 时，系统无法知道你是否关心删除安全与否。 

## <a name="durability-characteristics"></a>持续性特征
使用 Remove-AzureRmServiceFabricNodeType 时，安全性优先于速度。 建议使用银级或金级[持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，因为：
- 铜级未提供任何关于保存状态信息的保证。
- 银级和金级的持续性可以阻止任何对 VMSS 的更改。
- 金级还可控制 VMSS 下的 Azure 更新。

Service Fabric 会“协调”基础更改和更新，以便数据不会丢失。 但是，当删除具有铜级持续性的节点时，可能会丢失状态信息。 如果要删除主节点类型且应用程序是无状态，则可以接受铜级。 在生产环境中运行有状态工作负载时，最低配置应为银级。 同样，对于生产方案，主节点类型应始终为银级或金级。

### <a name="more-about-bronze-durability"></a>有关铜级持续性的详细信息

删除铜级的节点类型时，节点类型中的所有节点都会立即关闭。 Service Fabric 不会捕获任何铜级节点的 VMSS 更新，因此所有 VM 都会立即关闭。 如果这些节点上有任何有状态的内容，则数据将丢失。 现在，即使是无状态，Service Fabric 中的所有节点也会参与该更新，因此整个邻域可能会丢失，这可能会影响群集本身。

与删除单个节点不同，因为理论上，你可以一次删除一个节点，等待副本和服务移动，等待系统达到稳定，然后再删除另一个节点，依此类推。  但是，如果同时删除多个节点，则群集可能会关闭（因为 Service Fabric 不会捕获任何具有铜级持续性的 VMSS 更新）。

## <a name="recommended-node-type-removal-process"></a>推荐的节点类型删除过程

以最安全和最快速的方式删除节点类型：
1.  如果使用铜级持续性或者不希望系统移动包含将作为节点类型删除的一部分丢失的状态信息的应用程序，那么首先要从将受节点类型删除影响的节点中删除空的有状态数据。
2.  在要删除的每个节点上运行 [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。
3.  对将受节点类型删除影响的 VM 运行 [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set)。
4. 运行 [Remove-AzureRmServiceFabricNodeType ](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) 以删除节点类型。

## <a name="next-steps"></a>后续步骤
- 了解有关群集[持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)的更多信息。
- 了解有关[节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)的更多信息。
- 了解有关 [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)的更多信息。