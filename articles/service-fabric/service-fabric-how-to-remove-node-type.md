---
title: 删除 Azure 中的节点类型 Service Fabric
description: 了解如何从在 Azure 中运行的 Service Fabric 群集删除节点类型。
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f3dc3210fdb436038174bb8d9347424f14d3faa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464507"
---
# <a name="remove-a-service-fabric-node-type"></a>删除 Service Fabric 节点类型
本文介绍如何通过删除群集的现有节点类型来缩放 Azure Service Fabric 群集。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集之后，可以通过删除节点类型（虚拟机规模集）及其所有节点来水平缩放群集。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用[AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype)删除 Service Fabric 节点类型。

调用 AzServiceFabricNodeType 时，会发生三个操作：
1.  删除节点类型后的虚拟机规模集。
2.  从群集中删除节点类型。
3.  对于该节点类型中的每个节点，将从系统中删除该节点的整个状态。 如果该节点上有服务，则首先将服务移出到另一个节点。 如果群集管理器找不到副本/服务的节点，则会延迟/阻止该操作。

> [!WARNING]
> 建议不要频繁使用 Remove-AzServiceFabricNodeType 从生产群集中删除节点类型。 这是一个非常危险的命令，因为它会删除节点类型后的虚拟机规模集资源。 

## <a name="durability-characteristics"></a>持续性特征
使用 AzServiceFabricNodeType 时，安全性的优先级高于速度。 节点类型必须为银级或金级[持续性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)，原因如下：
- 铜级未提供任何关于保存状态信息的保证。
- 银级和金级持续性阻止对规模集的任何更改。
- 金级还可控制规模集下的 Azure 更新。

Service Fabric 会“协调”基础更改和更新，以便数据不会丢失。 但是，当删除具有铜级持续性的节点时，可能会丢失状态信息。 如果要删除主节点类型且应用程序是无状态，则可以接受铜级。 在生产环境中运行有状态工作负载时，最低配置应为银级。 同样，对于生产方案，主节点类型应始终为银级或金级。

### <a name="more-about-bronze-durability"></a>有关铜级持续性的详细信息

删除铜级的节点类型时，节点类型中的所有节点都会立即关闭。 Service Fabric 不会阻止任何铜级节点的规模集更新，因此所有 VM 都会立即关闭。 如果这些节点上有任何有状态的内容，则数据将丢失。 现在，即使是无状态，Service Fabric 中的所有节点也会参与该更新，因此整个邻域可能会丢失，这可能会影响群集本身。

## <a name="recommended-node-type-removal-process"></a>推荐的节点类型删除过程

若要删除节点类型，请运行 [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet。  该 cmdlet 需要一些时间才能完成操作。  所有 Vm 都丢失（表示为 "关闭"）后，fabric：/System/InfrastructureService/[nodetype name] 将显示错误状态。

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

然后，你可以更新群集资源以删除节点类型。 你可以使用 ARM 模板部署，或通过[Azure 资源管理器](https://resources.azure.com)编辑群集资源。 这会启动群集升级，这将删除处于错误状态的 fabric：/System/InfrastructureService/[nodetype name] 服务。

在 Service Fabric Explorer 中，仍会看到节点处于 "关闭" 状态。 在要删除的每个节点上运行 [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。


```powershell
$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>后续步骤
- 了解有关群集[持续性特征](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)的更多信息。
- 了解有关[节点类型和虚拟机规模集](service-fabric-cluster-nodetypes.md)的更多信息。
- 了解有关 [Service Fabric 群集缩放](service-fabric-cluster-scaling.md)的更多信息。
