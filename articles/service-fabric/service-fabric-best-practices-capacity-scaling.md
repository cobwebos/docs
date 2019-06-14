---
title: 容量规划和缩放 Azure Service fabric |Microsoft Docs
description: 有关规划和缩放 Service Fabric 群集与应用程序的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 58c50eac60f1a8a47aac9a88125bc3e0132ec3db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059160"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>容量规划和缩放 Azure Service fabric

创建任何 Azure Service Fabric 群集或缩放计算资源托管你的群集之前，务必要规划容量。 有关规划容量的详细信息，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 有关更多群集可伸缩性的最佳实践指南，请参阅[Service Fabric 可扩展性考虑事项](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考虑节点类型和群集特征，应该在缩放操作花费的时间超过一小时才能完成适用于生产环境。 这种考虑因素是而不考虑你要添加的 Vm 数，则返回 true。

## <a name="autoscaling"></a>自动缩放
应执行通过 Azure 资源管理器模板的缩放操作，因为它是将处理的最佳做法[资源配置即代码]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)。 

使用通过虚拟机规模集自动缩放可使版本控制资源管理器模板不正确地定义为虚拟机规模集实例计数。 不准确的定义会增加以后进行部署时，将导致意外的缩放操作的风险。 一般情况下，如果应使用自动缩放：

* 使用相应的声明容量部署资源管理器模板无法为用例提供支持。
     
   除了手动缩放，还可以配置[持续集成和交付管道中 Azure DevOps 服务通过使用 Azure 资源组部署项目](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)。 此管道通常由逻辑应用使用的虚拟机从查询的性能指标触发[Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)。 管道有效地措施基于任意指标所需，同时优化资源管理器模板。
* 您需要水平缩放只有一个虚拟机规模集节点，一次。
   
   若要向外扩展由三个或多个节点一次，您应[横向扩展通过添加虚拟机规模集的 Service Fabric 群集](virtual-machine-scale-set-scale-node-type-scale-out.md)。 它是最安全的做法在中缩放和向外扩展虚拟机规模水平，设置一次一个节点。
* 具有银级的可靠性或更高版本的 Service Fabric 群集和银级持久性或更高版本上任何规模，其中配置自动缩放规则。
  
   自动缩放规则的最小容量必须等于或大于五个虚拟机实例。 此外必须等于或大于主节点类型在可靠性层的最小值。

> [!NOTE]
> Service Fabric 有状态服务 fabric: / System/InfastructureService/< NODE_TYPE_NAME > 具有银级或更高版本的持续性的每个节点类型上运行。 它是唯一的系统服务，支持在 Azure 中运行任何群集节点类型上。

## <a name="vertical-scaling-considerations"></a>垂直缩放注意事项

[垂直缩放](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)Azure Service Fabric 中的节点类型需要大量的步骤和注意事项。 例如：

* 在缩放之前，群集必须处于正常状态。 否则，你将使进一步的群集不稳定。
* 银级持续性级别或更高版本是必需的所有托管有状态服务的 Service Fabric 群集节点类型。

> [!NOTE]
> 承载有状态 Service Fabric 系统服务在主节点类型必须为银级持续性级别或更高版本。 启用银级持续性，例如升级，群集操作后添加或删除节点和等等的会变慢因为系统优化的数据安全性优先于操作的速度。

垂直缩放虚拟机规模集是破坏性操作。 相反，通过添加新的规模集与所需的 SKU 而水平缩放群集。 然后，将服务迁移到你所需的 SKU，若要完成一项安全垂直缩放操作。 更改虚拟机规模集资源 SKU 是破坏性操作，因为它进行映像重置你的主机，哪些会删除所有本地持久化状态。

群集使用 Service Fabric[节点属性和放置约束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)来决定在何处托管应用程序的服务。 您要垂直缩放时主节点类型，声明相同的属性值为`"nodeTypeRef"`。 虚拟机规模集，可以在 Service Fabric 扩展中找到这些值。 

资源管理器模板的以下代码片段显示了将声明的属性。 它具有要缩放，并支持仅用作群集的临时有状态服务的新预配的规模集的相同值。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 不要将群集与使用相同的多个规模集运行`nodeTypeRef`完成成功的垂直缩放操作所需的长度大于属性值。
>
> 你在尝试更改到生产环境之前，始终验证在测试环境中的操作。 默认情况下，Service Fabric 群集系统服务具有为目标主节点类型的放置约束。

声明节点属性和位置约束后，每次在一个 VM 实例上执行以下步骤。 这允许系统服务 （和有状态服务） 上要根据其他位置创建新副本中删除的 VM 实例正常关闭。

1. 从 PowerShell 中运行`Disable-ServiceFabricNode`意向`RemoveNode`禁用要删除的节点。 删除编号最大的节点类型。 例如，如果您有一个六节点群集，删除"MyNodeType_5"虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 这可能需要几个小时的每个节点。 在节点转换为禁用状态之前，请不要继续操作。
3. 减少由一个节点类型的 Vm 数。 现在，将会删除编号最大的 VM 实例。
4. 根据需要重复步骤 1 到 3，但切勿将主节点类型的实例数目缩减到少于可靠性层所需的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

### <a name="example-scenario"></a>示例方案
有关何时执行垂直缩放操作受支持的方案是： 你想要将你的 Service Fabric 群集和应用程序从非托管磁盘迁移到托管磁盘，而无需应用程序停机。 

可以预配新的虚拟机规模集使用托管磁盘，并执行与放置约束针对预配的容量的应用程序升级。 然后，Service Fabric 群集可以计划上预配的群集节点容量的推出而无需应用程序停机的升级域信任的工作负荷。 

后端池的终结点[Azure 负载均衡器基本 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)可以是在单个可用性集或虚拟机规模集虚拟机。 这意味着如果规模集，而不会导致临时无法访问你的 Service Fabric 群集管理终结点之间移动你的 Service Fabric 系统应用程序，则不能使用基本 SKU 负载均衡器。 即使在群集和它的应用程序仍在运行，也是如此。

执行基本 SKU 负载均衡器和标准 SKU 负载均衡器资源之间的虚拟 IP 地址 (VIP) 交换时，用户通常预配的标准 SKU 负载均衡器。 此方法限制为大约 30 秒 VIP 交换所需的任何将来无法访问。

## <a name="horizontal-scaling"></a>水平扩展

您可以执行操作可以水平缩放[手动](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或[以编程方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)。

> [!NOTE]
> 如果要缩放的节点类型的具有银级或是金级持久性，缩放将会很慢。

### <a name="scaling-out"></a>扩大

通过增加实例计数为特定虚拟机规模集横向扩展的 Service Fabric 群集。 您可以通过横向扩展以编程方式使用`AzureClient`和所需的规模集以增加容量的 ID。

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手动扩大，更新所需的 SKU 属性中的容量[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>缩减

横向缩减的考虑因素比横向扩展要多一些。例如：

* Service Fabric 系统服务在群集中的主节点类型中运行。 切勿关闭该节点类型的实例，或者将其实例数目缩减到少于可靠性层保证的数目。 
* 对于有状态服务，您需要一定数量的节点始终是最以维护可用性和保留你的服务的状态。 至少，您需要节点的数等于目标副本集计数的分区或服务。

若要手动横向缩减，请执行以下步骤：

1. 从 PowerShell 中运行`Disable-ServiceFabricNode`意向`RemoveNode`禁用要删除的节点。 删除编号最大的节点类型。 例如，如果您有一个六节点群集，删除"MyNodeType_5"虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 这可能需要几个小时的每个节点。 在节点转换为禁用状态之前，请不要继续操作。
3. 减少由一个节点类型的 Vm 数。 现在，将会删除编号最大的 VM 实例。
4. 重复步骤 1 至 3，根据需要之前预配所需的容量。 请勿将主节点类型的实例数目缩减到少于可靠性层保证的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手动缩放中，更新所需的 SKU 属性中的容量[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

您必须准备关闭以编程方式缩放中的节点。 查找要作为的节点删除 （最高实例节点）。 例如：

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

停用和删除节点使用相同的`FabricClient`实例 (`client`在这种情况下) 和节点实例 (`instanceIdString`这种情况下) 在上面的代码中使用：

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 时减少群集时，你将看到已删除的节点 /VM 实例显示在 Service Fabric Explorer 中的不正常状态。 此行为的说明，请参阅[您可能会在 Service Fabric Explorer 中观察到的行为](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)。 可以：
> * 调用[Remove-servicefabricnodestate 命令](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)具有相应的节点名称。
> * 部署[Service Fabric 自动缩放帮助应用程序](https://github.com/Azure/service-fabric-autoscale-helper/)在群集上。 此应用程序可确保按比例缩小的节点，会清除从 Service Fabric Explorer。

## <a name="reliability-levels"></a>可靠性级别

[可靠性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是 Service Fabric 群集资源的属性。 它不能以不同的方式配置的单个节点类型。 该属性控制群集系统服务的复制因子，是群集资源级别的设置。 

可靠性级别决定了主节点类型必须具有的节点数下限。 可靠性层可以采用以下值：

* 白金：使用七个和九个种子节点的目标副本集计数中运行的系统服务。
* 金级：运行系统服务使用七个和七个种子节点的目标副本集计数。
* silver:使用五个和五个种子节点的目标副本集计数中运行的系统服务。
* 铜卡：使用三个和三个种子节点的目标副本集计数中运行的系统服务。

建议的最低可靠性级别为“银”级。

可靠性级别在 [Microsoft.ServiceFabric/clusters 资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)的 properties 节中设置，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性级别

> [!WARNING]
> 以青铜级持续性运行的节点类型不具有任何特权  。 影响无状态工作负载的基础结构作业不会停止或延迟，这会影响工作负荷。 
>
> 铜级持久性仅用于运行无状态工作负荷的节点类型。 对于生产工作负荷运行银级或更高版本以确保状态一致性。 请根据[容量规划文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指导选择适当的可靠性级别。

必须在两个资源中设置持久性级别。 一个是扩展配置文件的[虚拟机规模集资源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

其他资源`nodeTypes`中[microsoft.servicefabric/clusters 资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)。
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)。
* 了解 [Service Fabric 支持选项](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
