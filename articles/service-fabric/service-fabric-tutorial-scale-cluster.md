---
title: 缩放 Azure Service Fabric 群集 | Microsoft Docs
description: 本教程介绍如何快速缩放 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/06/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: e80fad4d0bddff89ff4dda7feed90fc622369ee9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-scale-a-service-fabric-cluster"></a>教程：缩放 Service Fabric 群集

本教程是系列教程的第二部分，介绍如何扩大和缩小现有群集。 完成时，将知道如何缩放群集以及如何清理剩余的资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 读取群集节点计数
> * 添加群集节点（扩大）
> * 移除群集节点（缩小）

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 使用模板在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
> * 缩小或扩大群集
> * [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)
> * [部署 API 管理与 Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)或 [Azure CLI 2.0](/cli/azure/install-azure-cli)。
- 在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
- 如果部署 Windows 群集，请设置 Windows 开发环境。 安装 [Visual Studio 2017](http://www.visualstudio.com) 和 **Azure 开发**、**ASP.NET 和 Web 开发**以及 **.NET Core 跨平台开发**工作负荷。  然后设置 [.NET 开发环境](service-fabric-get-started.md)。
- 如果部署 Linux 群集，请在 [Linux](service-fabric-get-started-linux.md) 或 [MacOS](service-fabric-get-started-mac.md) 上设置一个 Java 开发环境。  安装 [Service Fabric CLI](service-fabric-cli.md)。 

## <a name="sign-in-to-azure"></a>登录 Azure
执行 Azure 命令之前，登录到你的 Azure 帐户并选择你的订阅。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>连接至群集

若要成功完成此部分教程，需要连接到 Service Fabric 群集和虚拟机规模集（用于托管群集）。 虚拟机规模集是在 Azure 上托管 Service Fabric 的 Azure 资源。

连接到群集时，可以进行信息查询。 可以使用群集了解群集识别的节点。 连接到以下代码中的群集使用在本系列的第一部分创建的相同证书。 请确保将 `$endpoint` 和 `$thumbprint` 变量设置为自己的值。

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

连接后，即可使用命令获取群集中每个节点的状态。 对于 PowerShell，请使用 `Get-ServiceFabricClusterHealth` 命令，而对于 sfctl，请使用 `sfctl cluster select` 命令。

## <a name="scale-out"></a>向外扩展

扩大时，添加更多虚拟机实例到规模集。 这些实例成为 Service Fabric 使用的节点。 Service Fabric 知道规模集什么时候添加了更多实例（通过扩大实现）并自动做出反应。 以下代码按名称获取规模集，并使规模集的容量增加 1。

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

此代码将容量设为 6。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>缩小

缩小和扩大相同，只是使用的是更低的容量值。 缩小规模集时，将从规模集中移除虚拟机实例。 通常情况下，Service Fabric 不能识别发生了什么，并且它认为节点已丢失。 然后 Service Fabric 将报告群集的不正常状态。 为防止错误状态，必须通知 Service Fabric 你希望节点消失。

### <a name="remove-the-service-fabric-node"></a>移除 Service Fabric 节点

> [!NOTE]
> 此部分仅应用于 Bronze 持续性层。 有关持续性的详细信息，请参阅 [Service Fabric 群集容量规划][durability]。

缩小虚拟机规模集时，规模集（大多情况下）会移除上次创建的虚拟机实例。 因此，需要找到上次创建的相应 Service Fabric 节点。 可以通过检查 Service Fabric 节点上最大 `NodeInstanceId` 属性值找到最近的节点。 下面的代码示例按节点实例排序并返回有最大 ID 值的实例的详细信息。 

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric 群集需要了解此节点将被移除。 需要执行以下三个步骤：

1. 禁用节点，使其不再是数据复制。  
PowerShell：`Disable-ServiceFabricNode`  
sfcli：`sfctl node disable`

2. 停止节点，使 Service Fabric 运行时完全关闭且应用获取终止请求。  
PowerShell：`Start-ServiceFabricNodeTransition -Stop`  
sfcli：`sfctl node transition --node-transition-type Stop`

2. 从群集移除节点。  
PowerShell：`Remove-ServiceFabricNodeState`  
sfcli：`sfctl node remove-state`

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

在下面的“sfctl”代码中，使用以下命令获取最近创建的节点 `sfctl node list --query "sort_by(items[*], &name)[-1].name"` 的 “node-name”值：

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 使用以下“sfctl”查询检查每个步骤的状态。
>
> **检查停用状态**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **检查停止状态**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>缩小规模集

将 Service Fabric 节点从群集中移除后，即可缩小虚拟机规模集。 在下面的示例中，规模集容量缩小了 1。

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

此代码将容量设为 5。

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 读取群集节点计数
> * 添加群集节点（扩大）
> * 移除群集节点（缩小）


接下来，请转到以下教程了解如何升级群集运行时。
> [!div class="nextstepaction"]
> [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
