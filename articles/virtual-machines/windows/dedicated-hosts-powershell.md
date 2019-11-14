---
title: 使用 Azure PowerShell 部署 Azure 专用主机
description: 使用 Azure PowerShell 将 Vm 部署到专用主机。
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 92dca6f4f41ff426aebcb8e580653afaa71afff8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033367"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>预览：使用 Azure PowerShell 将 Vm 部署到专用主机

本文介绍如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机（vm）。 

请确保已安装 Azure PowerShell 版本 2.4.2 sections 或更高版本，并且已使用 `Connect-AzAccount`登录到 Azure 帐户。 若要安装版本 2.4.2 sections，请打开 PowerShell 提示符，然后键入：

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrerelease
```

需要至少1.6.0 版本的 PowerShellGet 模块才能在 PowerShell 中启用预览模块功能。 最新版本的 PowerShell Core 自动内置此项，但对于较早版本的 PowerShell，你可以运行以下命令以更新到最新版本：

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Azure 专用主机目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> **已知预览版限制**
> - 虚拟机规模集目前在专用主机上不受支持。
> - 预览版初始版本支持以下 VM 系列： DSv3 和 ESv3。 



## <a name="create-a-host-group"></a>创建主机组

**主机组**是代表专用主机集合的资源。 在区域和可用性区域中创建主机组，并向其添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用： 
- 跨多个可用性区域。 在这种情况下，你需要在要使用的每个区域中都有一个主机组。
- 跨多个容错域，这些容错域映射到物理机架。 
 
在任一情况下，都需要提供主机组的容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数1。 

您还可以决定使用可用性区域和容错域。 此示例将在区域1中创建具有2个容错域的主机组。 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>创建主机

现在，让我们在主机组中创建一个专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件生成。  在预览期间，我们将支持以下主机 SKU 值： DSv3_Type1 和 ESv3_Type1。


有关主机 Sku 和定价的详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

如果为主机组设置了容错域计数，系统会要求你为主机指定容错域。 在此示例中，我们将主机的容错域设置为1。


```powershell
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

如果在创建主机组时指定了可用性区域，则需要在创建虚拟机时使用同一区域。 在此示例中，由于我们的主机组在区域1中，因此需要在区域1中创建 VM。  


```powershell
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
> 如果在没有足够资源的主机上创建虚拟机，则虚拟机将创建为失败状态。 

## <a name="check-the-status-of-the-host"></a>检查主机的状态

你可以使用[GetAzHost](/powershell/module/az.compute/get-azhost)和 `-InstanceView` 参数检查主机运行状况状态，以及你仍可以将多个虚拟机部署到主机。

```
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

## <a name="clean-up"></a>清理

即使部署了虚拟机，也需要为你的专用主机付费。 应删除当前未使用的任何主机以节省成本。  

只有在没有任何更长的虚拟机使用它时，才能删除该主机。 使用[new-azvm](/powershell/module/az.compute/remove-azvm)删除 vm。

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

删除 Vm 后，可以使用[AzHost](/powershell/module/az.compute/remove-azhost)删除该主机。

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

删除所有主机后，可以使用[AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)删除该主机组。 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

还可以使用[AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)在单个命令中删除整个资源组。 这会删除在组中创建的所有资源，包括所有 Vm、主机和主机组。
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>后续步骤

- [这里](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板，它使用区域和容错域实现了区域中的最大复原能力。

- 你还可以使用[Azure 门户](dedicated-hosts-portal.md)部署专用主机。
