---
title: Azure Service Fabric 的容量规划和缩放
description: 有关规划和缩放 Service Fabric 群集与应用程序的最佳做法。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: be0f0a48e2fd334e2000c8a4b8c2e0101b291cef
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791861"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Azure Service Fabric 的容量规划和缩放

在创建任何 Azure Service Fabric 群集或缩放托管群集的计算资源之前，必须做好容量规划。 有关规划容量的详细信息，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 有关群集可伸缩性的更好的最佳实践指南，请参阅[Service Fabric 可伸缩性注意事项](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)。

除了考虑节点类型和群集特征以外，还要规划好生产环境中需要花费一小时以上才能完成的缩放操作。 不管要添加多少个 VM，都要考虑到这种情况。

## <a name="autoscaling"></a>自动缩放
应该通过 Azure 资源管理器模板执行缩放操作，因为最佳做法是[将资源配置视为代码]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)。 

使用虚拟机规模集自动缩放会导致版本受控的资源管理器模板不准确地定义虚拟机规模集实例计数。 不准确的定义会增加将来的部署导致意外缩放操作的风险。 一般而言，如果存在以下情况，则应该使用自动缩放：

* 使用相应的声明容量部署资源管理器模板无法为用例提供支持。
     
   除了手动缩放以外，还可以[使用 Azure 资源组部署项目在 Azure DevOps 服务中配置持续集成和交付管道](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)。 此管道通常由某个逻辑应用触发，而该应用利用从 [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) 查询的虚拟机性能指标。 该管道基于所需的任意指标进行有效自动缩放，同时针对 Azure 资源管理器进行优化可以增大价值。
* 每次只需水平缩放一个虚拟机规模集节点。
   
   若要一次性横向扩展三个或更多个节点，应该[通过添加虚拟机规模集来横向扩展 Service Fabric 群集](virtual-machine-scale-set-scale-node-type-scale-out.md)。 最安全的做法是每次横向扩展或缩减虚拟机规模集的一个节点。
* 对于 Service Fabric 群集，可以实现银级可靠性；对于配置了自动缩放规则的任何规模集，可以实现银级或更高的持久性。
  
   自动缩放规则的最小容量必须大于或等于五个虚拟机实例。 此外，它必须大于或等于主节点类型的最低可靠性层。

> [!NOTE]
> Service Fabric 有状态服务 fabric:/System/InfastructureService/<节点类型名称> 在具有银级或更高持久性的每个节点类型上运行。 它是唯一支持在 Azure 中任何群集节点类型上运行的系统服务。

## <a name="vertical-scaling-considerations"></a>垂直缩放注意事项

[垂直缩放](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)Azure 中的节点类型 Service Fabric 需要执行许多步骤和注意事项。 例如：

* 在缩放之前，群集必须处于正常状态。 否则，会进一步破坏群集的稳定性。
* 托管有状态服务的所有 Service Fabric 群集节点类型需要银级或更高级别的持久性。

> [!NOTE]
> 托管有状态 Service Fabric 系统服务的主节点类型必须具有银级或更高级别的持久性。 启用银级持久性后，升级、添加或删除节点等群集操作将会变慢，因为系统优化的目标是保护数据安全，而不是加快操作速度。

垂直缩放虚拟机规模集是破坏性的操作。 应该通过添加具有所需 SKU 的新规模集来水平缩放群集。 然后将服务迁移到所需的 SKU，以安全完成垂直缩放操作。 更改虚拟机规模集资源 SKU 是破坏性的操作，因为需要重置主机映像，而这会删除所有本地持久化状态。

群集使用 Service Fabric [节点属性和位置约束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)来确定要将应用程序服务托管在哪个位置。 垂直缩放主节点类型时，请为 `"nodeTypeRef"` 声明相同的属性值。 可在虚拟机规模集的 Service Fabric 扩展中的找到这些值。 

资源管理器模板的以下代码片段显示了要声明的属性。 其中的值与要缩放到的新预配规模集的属性值相同，仅支持用作群集的临时有状态服务。

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 请不要让群集与使用相同 `nodeTypeRef` 属性值的多个规模集一起运行的时间超过成功完成垂直缩放操作所需的时间。
>
> 在尝试进行生产环境更改之前，始终在测试环境中验证操作。 默认情况下，Service Fabric 群集系统服务提供仅针对主节点类型的位置约束。

声明节点属性和位置约束后，每次在一个 VM 实例上执行以下步骤。 这样，可以在其他位置创建新副本时，让系统服务（以及有状态服务）在要删除的 VM 实例上正常关闭。

1. 在 PowerShell 中， `Disable-ServiceFabricNode`运行并`RemoveNode`通过意向禁用要删除的节点。 删除编号最大的节点类型。 例如，如果你有一个六节点群集，请删除“MyNodeType_5”虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 对于每个节点，此过程可能需要花费几个小时。 在节点转换为禁用状态之前，请不要继续操作。
3. 将该节点类型的 VM 数目减少一个。 现在，将会删除编号最大的 VM 实例。
4. 根据需要重复步骤1到3，但绝不会在主节点类型的实例数小于可靠性层所需的数目范围内进行缩放。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。
5. 所有 VM 都消失（表示为“关闭”）后，fabric:/System/InfrastructureService/[node name] 将显示错误状态。 然后，可以更新群集资源以删除节点类型。 可以使用 ARM 模板部署，也可以通过 [Azure 资源管理器](https://resources.azure.com)编辑群集资源。 这将启动群集升级，从而删除处于错误状态的 fabric:/System/InfrastructureService/[node type] 服务。
 6. 之后，你可以选择删除 VMScaleSet，但仍会从 Service Fabric Explorer 视图中看到节点 "Down"。 最后一步是使用 `Remove-ServiceFabricNodeState` 命令清除它们。

## <a name="horizontal-scaling"></a>水平扩展

可以[手动](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-in-out)或[以编程方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)执行水平缩放。

> [!NOTE]
> 如果缩放具有银级或金级持久性的节点类型，则缩放速度将很缓慢。

### <a name="scaling-out"></a>扩大

通过增加特定虚拟机规模集的实例计数来横向扩展 Service Fabric 群集。 可以使用 `AzureClient` 和所需规模集的 ID 以编程方式进行横向扩展，以增加容量。

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

* Service Fabric 系统服务在群集的主节点类型中运行。 切勿关闭或缩放该节点类型的实例数，以便获得的实例数少于可靠性层所需的数目。 
* 对于有状态服务，需要一些始终启动的节点来保持可用性，以及保持服务的状态。 至少需要与分区或服务的目标副本集计数相等的节点数目。

若要手动横向缩减，请执行以下步骤：

1. 在 PowerShell 中， `Disable-ServiceFabricNode`运行并`RemoveNode`通过意向禁用要删除的节点。 删除编号最大的节点类型。 例如，如果你有一个六节点群集，请删除“MyNodeType_5”虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 对于每个节点，此过程可能需要花费几个小时。 在节点转换为禁用状态之前，请不要继续操作。
3. 将该节点类型的 VM 数目减少一个。 现在，将会删除编号最大的 VM 实例。
4. 视需要重复步骤 1 到 3，直到预配了所需的容量。 不要将主节点类型中的实例数扩展到小于可靠性层所保证的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手动缩放，请在所需的[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源的 "SKU" 属性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

必须准备好要关闭的节点才能以编程方式进行横向缩减。 查找要删除的节点（编号最大的实例节点）。 例如：

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

使用相同的 `FabricClient` 实例（在本例中为 `client`）和节点实例名称（在本例中为 `instanceIdString`）停用并删除该节点：

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
> 在群集中缩放时，将在 Service Fabric Explorer 中看到以不正常状态显示的已删除节点/VM 实例。 有关此行为的说明，请参阅[可能会在 Service Fabric Explorer 中观察到的行为](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-in-out#behaviors-you-may-observe-in-service-fabric-explorer)。 你可以：
> * 结合相应的节点名称调用 [Remove-ServiceFabricNodeState 命令](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)。
> * 在群集上部署 [Service Fabric 自动缩放帮助应用程序](https://github.com/Azure/service-fabric-autoscale-helper/)。 此应用程序可确保从 Service Fabric Explorer 中清除已缩减的节点。

## <a name="reliability-levels"></a>可靠性级别

[可靠性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是 Service Fabric 群集资源的一个属性。 对于各个节点类型，此属性的配置必须相同。 该属性控制群集系统服务的复制因子，是群集资源级别的设置。 

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
> 以青铜级持续性运行的节点类型不具有任何特权__。 不会停止或延迟对无状态工作负荷产生影响的基础结构作业，这可能影响工作负荷。 
>
> 铜级持久性仅用于运行无状态工作负荷的节点类型。 对于生产工作负荷，运行银级或更高级别可确保状态一致性。 请根据[容量规划文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指导选择适当的可靠性级别。

必须在两个资源中设置持久性级别。 [虚拟机规模集资源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)的一个扩展配置文件：

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

另一个资源位于 [Microsoft.ServiceFabric/clusters 资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)中的 `nodeTypes` 下： 

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
