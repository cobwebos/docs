---
title: Azure Service Fabric 容量规划和缩放最佳做法 | Microsoft Docs
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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471530"
---
# <a name="capacity-planning-and-scaling"></a>容量规划和缩放

在创建任何 Azure Service Fabric 群集或缩放托管群集的计算资源之前，必须做好容量规划。 有关规划容量的详细信息，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 除了考虑节点类型和群集特征以外，还要规划好生产环境中需要花费一小时以上才能完成（不管要添加多少个 VM）的缩放操作。

## <a name="auto-scaling"></a>自动缩放
应该通过 Azure 资源模板部署执行缩放操作，因为最佳做法是[将资源配置视为代码]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)，而使用虚拟机规模集自动缩放会导致版本受控的资源管理器模板不准确地定义虚拟机规模集实例计数，并增加将来的部署导致意外缩放操作的风险。一般而言，如果存在以下情况，则应该使用自动缩放：

* 使用相应的声明容量部署资源管理器模板无法为用例提供支持。
  * 除了手动缩放以外，还可以[使用 Azure 资源组部署项目在 Azure DevOps 服务中配置持续集成和交付管道]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)，该管道通常由某个逻辑应用触发，而该应用利用从 [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) 查询的虚拟机性能指标；基于所需的任意指标进行有效自动缩放，同时针对 Azure 资源管理器进行优化可以增大价值。
* 每次只需水平缩放 1 个虚拟机规模集节点。
  * 若要一次性横向扩展 3 个或更多个节点，应该[通过添加虚拟机规模集来横向扩展 Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)。最安全的做法是每次横向扩展或缩减虚拟机规模集的 1 个节点。
* 对于 Service Fabric 群集，可以实现银级可靠性；对于配置了自动缩放规则的任何规模集，可以实现银级或更高的持久性。
  * 自动缩放规则容量的 [minimum] 必须大于或等于 5 个虚拟机实例，并且必须大于或等于主节点类型的最低可靠性层。

> [!NOTE]
> Azure Service Fabric 有状态服务 fabric:/System/InfastructureService/<NODE_TYPE_NAME> 在具有银级或更高持久性的每个节点类型上运行，它是唯一支持在 Azure 中任何群集节点类型上运行的系统服务。 

## <a name="vertical-scaling-considerations"></a>垂直缩放注意事项

[垂直缩放](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) Azure Service Fabric 中的节点类型需要执行许多步骤并考虑多种因素。 例如：
* 在缩放之前，群集必须处于正常状态。 否则，只会进一步破坏群集的稳定性。
* 托管有状态服务的所有 Service Fabric 群集节点类型需要**银级或更高级别的持久性**。

> [!NOTE]
> 托管有状态 Service Fabric 系统服务的主节点类型必须具有银级或更高级别的持久性。 启用银级持久性后，升级、添加或删除节点等群集操作将会变慢，因为系统优化的目标是保护数据安全，而不是加快操作速度。

垂直缩放虚拟机规模集是破坏性的操作。 通过添加具有所需 SKU 的新规模集来水平缩放群集，然后将服务迁移到所需的 SKU，可以安全完成垂直缩放操作。 更改虚拟机规模集资源 SKU 是破坏性的操作，因为需要重置主机映像，而这会删除所有本地持久化状态。

群集使用 Service Fabric [节点属性和位置约束](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)来确定要将应用程序服务托管在哪个位置。 垂直缩放主节点类型时，请为虚拟机规模集 Service Fabric 扩展中的 `"nodeTypeRef"` 声明相同的属性值。 资源管理器模板的以下代码片段显示了要声明的属性，其值与要缩放到的新预配规模集的属性值相同，仅支持用作群集的临时有状态属性：

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 请不要让群集与使用相同 `nodeTypeRef` 属性值的多个规模集一起运行的时间超过成功完成垂直缩放操作所需的时间。
> 在尝试进行生产环境更改之前，始终在测试环境中验证操作。 默认情况下，Service Fabric 群集系统服务提供仅针对主节点类型的位置约束。

声明节点属性和位置约束后，每次在一个 VM 实例上执行以下步骤。 这样，可以在其他位置创建新副本时，让系统服务（以及有状态服务）在要删除的 VM 实例上正常关闭。
1. 在 PowerShell 中，结合意图“RemoveNode”运行 `Disable-ServiceFabricNode`，以禁用要删除的节点。 删除编号最大的节点类型。 例如，如果你有一个六节点群集，请删除“MyNodeType_5”虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 对于每个节点，此过程可能需要花费几个小时。 在节点转换为禁用状态之前，请不要继续操作。
3. 将该节点类型的 VM 数目减少一个。 现在，将会删除编号最大的 VM 实例。
4. 根据需要重复步骤 1 到 3，但切勿将主节点类型的实例数目缩减到少于可靠性层所需的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

## <a name="horizontal-scaling"></a>水平扩展

可以[手动](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down)或[以编程方式](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)水平缩减 Service Fabric。

> [!NOTE]
> 如果缩放具有银级或金级持久性的节点类型，则缩放速度将很缓慢。

### <a name="scaling-out"></a>扩大

通过增加特定虚拟机规模集的实例计数来横向扩展 Service Fabric 群集。 可以使用 AzureClient 和所需规模集的 ID 以编程方式进行横向扩展，以增加容量。

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

若要手动横向扩展，请在所需[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源的 SKU 属性中更新容量。
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>缩减

横向缩减的考虑因素比横向扩展要多一些。例如：
* Service Fabric 系统服务在群集的主节点类型中运行。 切勿关闭该节点类型的实例，或者将其实例数目缩减到少于可靠性层保证的数目。 
* 对于有状态服务，需要一些始终启动的节点来保持可用性，以及保持服务的状态。 至少需要与分区/服务的目标副本集计数相等的节点数目。

若要手动横向缩减，请执行以下步骤：

1. 在 PowerShell 中，结合意图“RemoveNode”运行 `Disable-ServiceFabricNode`，以禁用要删除的节点。 删除编号最大的节点类型。 例如，如果你有一个六节点群集，请删除“MyNodeType_5”虚拟机实例。
2. 运行 `Get-ServiceFabricNode` 以确保该节点已转换为禁用状态。 如果没有，请等到节点已禁用。 对于每个节点，此过程可能需要花费几个小时。 在节点转换为禁用状态之前，请不要继续操作。
3. 将该节点类型的 VM 数目减少一个。 现在，将会删除编号最大的 VM 实例。
4. 根据需要重复步骤 1 到 3，但切勿将主节点类型的实例数目缩减到少于可靠性层所需的数目。 有关建议实例的列表，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

若要手动横向缩减，请在所需[虚拟机规模集](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)资源的 SKU 属性中更新容量。

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. 重复步骤 1 到 3，直到预配了所需的容量。 请勿将主节点类型的实例数目缩减到少于可靠性层保证的数目。 有关可靠性层及其所需实例数目的详细信息，请参阅[规划 Service Fabric 群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。

必须准备好要关闭的节点才能以编程方式进行横向缩减。 这涉及到查找要删除的节点（编号最大的实例节点），然后将其停用。 例如：

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

识别要删除的节点后，使用上述代码中所用的相同 `FabricClient` 实例（在本例中为 `client`）和节点实例名称（在本例中为 `instanceIdString`）停用并删除该节点：

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 缩放时缩减您群集会显示在 Service Fabric Explorer 中的不正常状态的已删除的节点 /VM 实例。 此行为的说明，请参阅[您可能会在 Service Fabric Explorer 中观察到的行为](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)。
> 
> 可以：
> * 调用[Remove-servicefabricnodestate cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)具有相应的节点名称。
> * 部署[service fabric 自动缩放帮助器应用程序](https://github.com/Azure/service-fabric-autoscale-helper/)这样可以确保节点下的缩放群集上清除从 Service Fabric Explorer。

## <a name="reliability-levels"></a>可靠性级别

[可靠性级别](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)是 Service Fabric 群集资源的一个属性，对于各个节点类型，此属性的配置必须相同。 该属性控制群集系统服务的复制因子，是群集资源级别的设置。 可靠性级别决定了主节点类型必须具有的节点数下限。 可靠性层可以采用以下值：
* 白金 - 运行包含 7 个目标副本集和 9 个种子节点的系统服务。
* 金 - 运行包含 7 个目标副本集和 7 个种子节点的系统服务。
* 银 - 运行包含 5 个目标副本集和 5 个种子节点的系统服务。
* 铜 - 运行包含 3 个目标副本集和 3 个种子节点的系统服务。

建议的最低可靠性级别为“银”级。

可靠性级别在 [Microsoft.ServiceFabric/clusters 资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)的 properties 节中设置，如下所示：

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>持久性级别

> [!WARNING]
> 以青铜级持续性运行的节点类型不具有任何特权。 这意味着，不会停止或延迟对无状态工作负荷产生影响的基础结构作业，这可能影响工作负荷。 铜级持久性仅用于运行无状态工作负荷的节点类型。 对于生产工作负荷，运行银级或更高级别可确保状态一致性。 请根据[容量规划文档](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)中的指导选择适当的可靠性级别。

必须在两个资源中设置持久性级别。 [虚拟机规模集资源](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)的扩展配置文件：

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

[Microsoft.ServiceFabric/clusters 资源](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)中的 `nodeTypes` 下 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
