---
title: Azure Service Fabric 的容量规划和扩展
description: 有关规划和缩放 Service Fabric 群集与应用程序的最佳做法。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75377457"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric 的容量规划和扩展

在创建托管群集的任何 Azure Service Fabric 群集或缩放计算资源之前，请务必规划容量。 有关规划容量的详细信息，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 有关群集可伸缩性的更好的最佳实践指南，请参阅[Service Fabric 可伸缩性注意事项](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考虑节点类型和群集特征以外，还应预计规模操作要花费超过一小时才能完成生产环境。 无论要添加多少个 Vm，都需要考虑这一点。

## <a name="autoscaling"></a>自动扩展
应通过 Azure 资源管理器模板执行缩放操作，因为这是将[资源配置视为代码]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)的最佳做法。 

通过虚拟机规模集使用自动缩放会使版本化资源管理器模板不准确地定义虚拟机规模集的实例计数。 不准确的定义增加了将来的部署将导致非预期的缩放操作的风险。 通常，在下列情况下应使用自动缩放：

* 使用相应的声明容量部署资源管理器模板无法为用例提供支持。
     
   除手动缩放外，还可以[使用 Azure 资源组部署项目在 Azure DevOps Services 中配置持续集成和传递管道](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)。 此管道通常由使用从[Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)查询的虚拟机性能指标的逻辑应用触发。 管道可根据所需的任何指标有效地措施，同时优化资源管理器模板。
* 一次只能横向缩放一个虚拟机规模集节点。
   
   若要一次横向扩展三个或更多节点，应[通过添加虚拟机规模集来横向扩展 Service Fabric 的群集](virtual-machine-scale-set-scale-node-type-scale-out.md)。 最安全的方法是，横向扩展和横向扩展虚拟机规模集，一次一个节点。
* 你的 Service Fabric 群集具有白银个可靠性或更高的可靠性，在你配置自动缩放规则的任何规模上都有银色或更高。
  
   自动缩放规则的最小容量必须等于或大于五个虚拟机实例。 它还必须等于或大于主节点类型的最小可靠性层。

> [!NOTE]
> Service Fabric 有状态 Service Fabric：/System/InfastructureService/< NODE_TYPE_NAME > 在每个具有白银或更高持久性的节点类型上运行。 这是唯一一种支持在 Azure 中的任何群集节点类型上运行的系统服务。

## <a name="vertical-scaling-considerations"></a>垂直缩放注意事项

[垂直缩放](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)Azure 中的节点类型 Service Fabric 需要执行许多步骤和注意事项。 例如：

* 在缩放之前，群集必须处于正常状态。 否则，你将进一步不稳定群集。
* 承载有状态服务的所有 Service Fabric 群集节点类型都需要 "白银" 持续性级别或更高级别。

> [!NOTE]
> 承载有状态 Service Fabric 系统服务的主节点类型必须是 "白银" 持续性级别或更高级别。 启用银持续性后，群集操作（如升级、添加或删除节点等）会变慢，因为系统会对数据安全进行优化，使其速度比操作速度快。

垂直缩放虚拟机规模集是一种破坏性操作。 相反，通过使用所需 SKU 添加新的规模集来横向缩放群集。 然后，将服务迁移到所需的 SKU，以完成安全的垂直缩放操作。 更改虚拟机规模集资源 SKU 是一种破坏性操作，因为它重置映像主机，这会删除所有本地保存的状态。

群集使用 Service Fabric[节点属性和放置约束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)来确定应用程序服务的托管位置。 垂直缩放主节点类型时，为 `"nodeTypeRef"`声明相同的属性值。 可以在虚拟机规模集的 Service Fabric 扩展中找到这些值。 

以下资源管理器模板片段显示了你将声明的属性。 它与新预配的规模集具有相同的值，它仅作为群集的临时有状态服务提供支持。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 不要让群集运行的多个规模集使用的 `nodeTypeRef` 属性值超过所需的时间，才能完成成功的垂直缩放操作。
>
> 请始终在测试环境中验证操作，然后再尝试更改生产环境。 默认情况下，Service Fabric 群集系统服务只有目标主节点类型具有放置约束。

声明节点属性和位置约束后，每次在一个 VM 实例上执行以下步骤。 这允许在要删除的 VM 实例上正常关闭系统服务（以及有状态服务），因为在其他位置创建新副本。

1. 在 PowerShell 中，运行具有意向 `RemoveNode` `Disable-ServiceFabricNode`，禁用要删除的节点。 删除编号最大的节点类型。 例如，如果你有一个6节点群集，请删除 "MyNodeType_5" 虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 对于每个节点，这可能需要几个小时。 在节点转换为禁用状态之前，请不要继续操作。
3. 减少此节点类型中 Vm 的数量。 现在，将会删除编号最大的 VM 实例。
4. 根据需要重复步骤 1 到 3，但切勿将主节点类型的实例数目缩减到少于可靠性层所需的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。
5. 所有 Vm 都丢失（表示为 "关闭"）后，fabric：/System/InfrastructureService/[node name] 将显示错误状态。 然后，你可以更新群集资源以删除节点类型。 你可以使用 ARM 模板部署，或通过[Azure 资源管理器](https://resources.azure.com)编辑群集资源。 这会启动群集升级，这将删除处于错误状态的 fabric：/System/InfrastructureService/[node type] 服务。
 6. 之后，你可以选择删除 VMScaleSet，但仍会从 Service Fabric Explorer 视图中看到节点 "Down"。 最后一步是清理 `Remove-ServiceFabricNodeState` 命令。

## <a name="horizontal-scaling"></a>水平扩展

可以[手动](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或[以编程方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)进行水平缩放。

> [!NOTE]
> 如果要缩放的节点类型具有银色或黄金持久性，缩放速度会很慢。

### <a name="scaling-out"></a>扩大

通过增加特定虚拟机规模集的实例计数来横向扩展 Service Fabric 群集。 你可以通过使用 `AzureClient` 和所需规模集的 ID 以编程方式扩展以增加容量。

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手动横向扩展，请更新所需的[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源的 "SKU" 属性中的容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>缩减

缩小时需要考虑更多的考虑因素。例如：

* Service Fabric 系统服务在群集中的主节点类型上运行。 切勿关闭该节点类型的实例，或者将其实例数目缩减到少于可靠性层保证的数目。 
* 对于有状态服务，需要一定数量的节点，这些节点始终保持可用状态并保留服务的状态。 至少需要有多个节点与分区或服务的目标副本集计数相等。

若要手动横向缩减，请执行以下步骤：

1. 在 PowerShell 中，运行具有意向 `RemoveNode` `Disable-ServiceFabricNode`，禁用要删除的节点。 删除编号最大的节点类型。 例如，如果你有一个6节点群集，请删除 "MyNodeType_5" 虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 对于每个节点，这可能需要几个小时。 在节点转换为禁用状态之前，请不要继续操作。
3. 减少此节点类型中 Vm 的数量。 现在，将会删除编号最大的 VM 实例。
4. 根据需要重复步骤1到步骤3，直到你预配所需的容量。 请勿将主节点类型的实例数目缩减到少于可靠性层保证的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手动缩放，请在所需的[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源的 "SKU" 属性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

您必须准备要以编程方式进行关闭的节点。 查找要删除的节点（最高实例节点）。 例如：

```csharp
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

使用在前面的代码中使用的同一 `FabricClient` 实例（在本例中为 "`client`）" 和 "节点实例" （在本例中为`instanceIdString`）来停用和删除节点：

```csharp
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
> 当你向下缩放群集时，将在 Service Fabric Explorer 中看到以不正常状态显示的已删除节点/VM 实例。 有关此行为的说明，请参阅[Service Fabric Explorer 中所述的行为](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)。 你可以：
> * 用适当的节点名称调用[remove-servicefabricnodestate 命令](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。
> * 在群集上部署[Service Fabric 自动缩放帮助程序应用程序](https://github.com/Azure/service-fabric-autoscale-helper/)。 此应用程序可确保从 Service Fabric Explorer 中清除缩小节点。

## <a name="reliability-levels"></a>可靠性级别

[可靠性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是 Service Fabric 群集资源的属性。 对于个别节点类型，不能以不同的方式进行配置。 该属性控制群集系统服务的复制因子，是群集资源级别的设置。 

可靠性级别决定了主节点类型必须具有的节点数下限。 可靠性层可以采用以下值：

* 白金：运行系统服务，其目标副本集计数为7个和9个种子节点。
* 金牌：运行系统服务，其目标副本集计数为七到7个种子节点。
* 银：运行系统服务，其目标副本集计数为5个和5个种子节点。
* 青铜：运行系统服务，其目标副本集计数为三个和三个种子节点。

建议的最低可靠性级别为“银”级。

可靠性级别在 [Microsoft.ServiceFabric/clusters 资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)的 properties 节中设置，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性级别

> [!WARNING]
> 以青铜级持续性运行的节点类型不具有任何特权。 影响无状态工作负荷的基础结构作业不会停止或延迟，这可能会影响工作负荷。 
>
> 铜级持久性仅用于运行无状态工作负荷的节点类型。 对于生产工作负荷，请运行 "白银" 或更高版本以确保状态一致。 请根据[容量规划文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指导选择适当的可靠性级别。

必须在两个资源中设置持久性级别。 其中一个是[虚拟机规模集资源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)的扩展配置文件：

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

其他资源位于[ServiceFabric/群集资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)中 `nodeTypes` 下： 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 Vm 或计算机上创建群集：[为 Windows server Service Fabric 群集创建](service-fabric-cluster-creation-for-windows-server.md)。
* 在运行 Linux 的 Vm 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)。
* 了解 [Service Fabric 支持选项](service-fabric-support.md)。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
