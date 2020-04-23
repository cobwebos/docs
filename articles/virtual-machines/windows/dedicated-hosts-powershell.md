---
title: 使用 Azure PowerShell 部署 Azure 专用主机
description: 使用 Azure PowerShell 将 VM 部署到专用主机。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082843"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>使用 Azure PowerShell 将 VM 部署到专用主机

本文将指导您如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机 （VM）。 

请确保已安装 Azure PowerShell 版本 2.8.0 或更高版本，并且已登录到 中的`Connect-AzAccount`Azure 帐户。 

## <a name="limitations"></a>限制

- 专用主机当前不支持虚拟机缩放集。
- 专用主机可用的大小和硬件类型因地区而异。 请参阅主机[定价页面](https://aka.ms/ADHPricing)以了解更多信息。

## <a name="create-a-host-group"></a>创建主机组

**主机组**是表示专用主机集合的资源。 在区域和可用性区域中创建主机组，并将主机添加到其中。 在规划高可用性时，还有其他选项。 您可以将以下一个或两个选项用于专用主机： 
- 跨多个可用性区域的跨范围。 在这种情况下，您需要在每个要使用的区域中具有一个主机组。
- 跨映射到物理机架的多个容错域。 
 
在这两种情况下，都需要为主机组提供容错域计数。 如果不想跨越组中的容错域，请使用容错域计数 1。 

您还可以决定同时使用可用性区域和容错域。 本示例在区域 1 中创建一个主机组，其中有 2 个容错域。 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>创建主机

现在，让我们在主机组中创建一个专用主机。 除了主机的名称外，还需要为主机提供 SKU。 主机 SKU 捕获支持的 VM 系列以及专用主机的硬件生成。

有关主机 SKU 和定价的详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

如果为主机组设置容错域计数，系统将要求您为主机指定容错域。 在此示例中，我们将主机的容错域设置为 1。


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>创建 VM

在专用主机上创建虚拟机。 

如果在创建主机组时指定了可用性区域，则创建虚拟机时需要使用相同的区域。 对于此示例，由于我们的主机组位于区域 1 中，我们需要在区域 1 中创建 VM。  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 如果在没有足够的资源的主机上创建虚拟机，则虚拟机将以 FAILED 状态创建。 

## <a name="check-the-status-of-the-host"></a>检查主机的状态

您可以使用 使用 参数使用[GetAzHost](/powershell/module/az.compute/get-azhost)检查主机运行状况状态以及仍可部署到主机的`-InstanceView`虚拟机数。

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

输出与此类似：

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>添加现有 VM 

您可以将现有 VM 添加到专用主机，但 VM 必须首先处于"停止_交易"的位置。 在将 VM 移动到专用主机之前，请确保 VM 配置受支持：

- VM 大小必须与专用主机的大小相同。 例如，如果您的专用主机是 DSv3，则 VM 大小可以Standard_D4s_v3，但它不可能是Standard_A4_v2。 
- VM 需要与专用主机位于同一区域。
- VM 不能是邻近放置组的一部分。 在将 VM 移动到专用主机之前，请从接近放置组中删除 VM。 有关详细信息，请参阅将[VM 移出邻近放置组](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位于可用性集中。
- 如果 VM 位于可用性区域中，则它必须与主机组位于同一可用性区域。 VM 和主机组的可用性区域设置必须匹配。

将变量的值替换为您自己的信息。

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>清理

即使未部署虚拟机，也会为专用主机向您收费。 您应该删除当前不使用的任何主机以节省成本。  

仅当不再有虚拟机使用主机时，才能删除主机。 使用[Remove-AzVM](/powershell/module/az.compute/remove-azvm)删除 VM。

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

删除 VM 后，可以使用[Delete-AzHost](/powershell/module/az.compute/remove-azhost)删除主机。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

删除所有主机后，可以使用[删除 AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)删除主机组。 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

您还可以使用[删除-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)在单个命令中删除整个资源组。 这将删除组中创建的所有资源，包括所有 VM、主机和主机组。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>后续步骤

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例模板，它同时使用区域和容错域，以实现区域中的最大恢复能力。

- 您还可以使用[Azure 门户](dedicated-hosts-portal.md)部署专用主机。
