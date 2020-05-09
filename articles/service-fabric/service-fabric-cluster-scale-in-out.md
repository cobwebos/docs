---
title: 扩展或缩减 Service Fabric 群集
description: 通过为每个节点类型/虚拟机规模集设置自动缩放规则，根据需要扩展或缩减 Service Fabric 群集。 在 Service Fabric 群集中添加或删除节点
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 670e2a1fcccf80faaf652f1ce0c749b315c3aaec
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797261"
---
# <a name="scale-a-cluster-in-or-out"></a>缩小或扩大群集

> [!WARNING]
> 在缩放之前阅读此部分

要缩放计算资源来满足你的应用程序工作负载，需要实施有目的性的计划，此操作在生产环境中总是需要几乎一个多小时才能完成，而且你还要对你的工作负载和业务环境了如指掌。事实上，如果从未执行过此操作，建议在继续本文档其余部分之前，首先阅读并理解 [Service Fabric 群集容量规划注意事项](service-fabric-cluster-capacity.md)。 该项建议的目的是避免出现意外的实时站点问题；此外，建议成功测试你决定针对非生产环境执行的操作。 可随时[报告生产问题或请求 Azure 付费支持](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure)。 对于被分配根据适当情况执行这些操作的工程师，请参考本文了解缩放操作。但是，你必须决定并了解哪些操作适合你的情况，例如要缩放哪些资源（CPU、存储、内存）、按哪个方向进行缩放方向（垂直或水平），以及要执行哪些操作（资源模板部署、门户、PowerShell/CLI）。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>使用自动缩放规则或者手动来扩展和缩减 Service Fabric 群集
虚拟机规模集是一种 Azure 计算资源，可用于将一组 VM 作为一个集进行部署和管理。 在 Service Fabric 群集中定义的每个节点类型将设置为不同的虚拟机规模集。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 可在 [Service Fabric 节点类型](service-fabric-cluster-nodetypes.md)文档中了解有关详细信息。 由于群集中的 Service Fabric 节点类型由后端的虚拟机规模集构成，因此需要为每个节点类型/虚拟机规模集设置自动缩放规则。

> [!NOTE]
> 订阅必须有足够的核心用于添加构成此群集的新 VM。 当前没有模型验证，因此如果达到任何配额限制，会遇到部署时故障。 此外，对于单个节点类型，每个 VMSS 不能超过 100 个节点。 要实现目标缩放，可能需要添加 VMSS，且自动缩放不会自动添加 VMSS。 将 VMSS 就地添加到实时集群非常具有挑战性务，这通常会导致用户使用创建时预配的相应节点类型来预配新集群。请相应地[计划群集容量](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)。 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>选择要缩放的节点类型/虚拟机规模集
当前无法使用门户为虚拟机规模集指定自动缩放规则，以创建 Service Fabric 群集，因此我们来使用 Azure PowerShell (1.0+) 列出节点类型，并向它们添加自动缩放规则。

若要获取构成群集的虚拟机规模集的列表，请运行以下 cmdlet：

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>为节点类型/虚拟机规模集设置自动缩放规则
如果群集具有多个节点类型，请为要缩放（扩展或缩减）的每个节点类型/虚拟机规模集重复此操作。 在设置自动缩放之前请考虑必须具有的节点数。 对于主节点类型所必须具有的最小节点数受所选择的可靠性级别影响。 了解有关[可靠性级别](service-fabric-cluster-capacity.md)的详细信息。

> [!NOTE]
> 在主节点类型中缩放到小于最小数量会使群集不稳定，甚至会使群集不稳定。 这可能会对应用程序和系统服务造成数据丢失。
> 
> 

当前，自动缩放功能不受应用程序可能向 Service Fabric 报告的负荷所影响。 因此当前获得的自动缩放纯粹受每个虚拟机规模集实例发出的性能计数器所影响。  

按照以下说明[为每个虚拟机规模集设置自动缩放](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)。

> [!NOTE]
> 在方案中，除非节点类型具有 "黄金" 或 "白银"[持续性级别][durability]，否则需要使用相应的节点名称调用[remove-servicefabricnodestate cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) 。 对于铜牌持久性，不建议一次在多个节点中进行缩放。
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>手动将 VM 添加到节点类型/虚拟机规模集

横向扩展时，会添加更多虚拟机实例到规模集。 这些实例成为 Service Fabric 使用的节点。 Service Fabric 知道规模集什么时候添加了更多实例（通过扩大实现）并自动做出反应。 

> [!NOTE]
> 添加 VM 需要时间，请不要期待马上就有结果。 计划提前添加容量，允许在 VM 容量可用于副本/服务实例之前超过10分钟。
> 

### <a name="add-vms-using-a-template"></a>使用模板添加 VM
根据[快速入门模板库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)中的示例/说明更改每个节点类型的 VM 数目。 

### <a name="add-vms-using-powershell-or-cli-commands"></a>使用 PowerShell 或 CLI 命令添加 VM
以下代码按名称获取规模集，并使规模集的容量增加 1。****

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

此代码将容量设为 6。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>手动从节点类型/虚拟机规模集中删除 VM
按节点类型进行缩放时，将从规模集中删除 VM 实例。 如果节点类型为“铜”持续性级别，Service Fabric 将不知道发生了什么情况，因此会报告节点已丢失。 然后 Service Fabric 将报告群集的不正常状态。 若要防止状态错误，必须从群集中显式删除节点并删除节点状态。

Service Fabric 系统服务在群集中以主节点类型运行。 当在主节点类型中缩放时，绝不能将实例的数量缩小到小于[可靠性层](service-fabric-cluster-capacity.md)所保证的数目。 
 
对于有状态服务，需要一些始终启动的节点来保持可用性，以及保持服务的状态。 至少需要与分区/服务的目标副本集计数相等的节点数目。

### <a name="remove-the-service-fabric-node"></a>删除 Service Fabric 节点

手动删除节点状态的步骤仅适用于具有“铜”** 持续性层的节点类型。  对于 "*白银*" 和 "*黄金*" 持续性层，平台会自动完成这些步骤。 有关持续性的详细信息，请参阅 [Service Fabric 群集容量规划][durability]。

为了使群集节点均匀地分布在升级域和容错域中，从而使它们的利用均匀，应该首先删除最近创建的节点。 换句话说，应该按照创建节点的相反顺序删除节点。 最近创建的节点是具有最大 `virtual machine scale set InstanceId` 属性值的节点。 下面的代码示例返回最近创建的节点。

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric 群集需要了解此节点将被删除。 需要执行以下三个步骤：

1. 禁用节点，使其不再是数据复制。  
PowerShell：`Disable-ServiceFabricNode`  
sfctl：`sfctl node disable`

2. 停止节点，使 Service Fabric 运行时完全关闭且应用获取终止请求。  
PowerShell：`Start-ServiceFabricNodeTransition -Stop`  
sfctl：`sfctl node transition --node-transition-type Stop`

2. 从群集移除节点。  
PowerShell：`Remove-ServiceFabricNodeState`  
sfctl：`sfctl node remove-state`

对节点执行这三个步骤后，即可将其从规模集中移除。 如果使用除 [bronze][durability] 以外的任意持续性层，在移除规模集实例时会完成这些步骤。

以下代码块获取最近创建的节点，禁用、停止并将节点从群集中移除。

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

在下面的“sfctl”代码中，使用以下命令获取最近创建的节点 `sfctl node list --query "sort_by(items[*], &name)[-1].name"` 的 “node-name”值：********

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 使用以下“sfctl”查询检查每个步骤的状态。****
>
> **检查停用状态**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **检查停止状态**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>缩小规模集

将 Service Fabric 节点从群集中删除后，即可缩小虚拟机规模集。 在下面的示例中，规模集容量缩小了 1。

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

此代码将容量设为 5。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>可能会在 Service Fabric Explorer 中观察到的行为
横向扩展群集时，Service Fabric Explorer 将反映作为群集一部分的节点（虚拟机规模集实例）的数目。  但是，当你在中缩放群集时，将看到已删除的节点/VM 实例处于不正常状态，除非使用适当的节点名称调用[remove-servicefabricnodestate 命令](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate)。   

下面是此行为的说明。

Service Fabric Explorer 中列出的节点是 Service Fabric 系统服务（特别是 FM）在群集具有的节点数方面所了解的信息的反映。 当你在中缩放虚拟机规模集时，VM 已删除，而 FM 系统服务仍然认为节点（映射到已删除的 VM）会恢复。 因此 Service Fabric Explorer 会继续显示该节点（尽管运行状况状态可能是错误或未知）。

若要确保在删除 VM 时删除节点，有两个选项：

1. 为群集中的节点类型选择金级或银级持续性级别，这会提供基础结构集成。 这样，当你在中缩放时，将自动从我们的系统服务（FM）状态中删除节点。
在[此处](service-fabric-cluster-capacity.md)了解有关持续性级别的详细信息

2. 在中缩放 VM 实例后，需调用[remove-servicefabricnodestate cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate)。

> [!NOTE]
> Service Fabric 群集需要有一定数量的节点可随时启动，以保持可用性和状态 - 称为“维持仲裁”。 因此，除非已事先执行[状态的完整备份](service-fabric-reliable-services-backup-restore.md)，否则关闭群集中的所有计算机通常是不安全的做法。
> 
> 

## <a name="next-steps"></a>后续步骤
参阅以下文章以另外了解如何规划群集容量、升级群集以及对服务进行分区：

* [规划群集容量](service-fabric-cluster-capacity.md)
* [群集升级](service-fabric-cluster-upgrade.md)
* [为有状态服务分区以最大程度地实现缩放](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
