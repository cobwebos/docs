---
title: "Azure 中 Windows VM 的可用性集教程 | Microsoft Docs"
description: "了解 Azure 中 Windows VM 的可用性集。"
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>如何使用可用性集

本教程介绍如何通过将虚拟机 (VM) 放入名为可用性集的逻辑分组中来提高虚拟机可用性。 在可用性集中创建 VM 时，Azure 平台会跨底层基础结构分发 VM。 如果平台上存在硬件故障或计划维护，则使用可用性集可确保至少有一个 VM 保持运行。

可使用最新版 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) 模块完成本教程中的步骤。

## <a name="availability-set-overview"></a>可用性集概述

可在基础 Azure 数据中心跨硬件的逻辑分组创建虚拟机。 创建两个或多个 VM 时，跨硬件分发计算和存储资源，例如服务器、网络交换机和存储。 如果硬件组件正在进行维护，此分配将维持你的应用的可用性。 使用可用性集可定义此逻辑分组。

可用性集为 VM 提供高可用性。 此外，应确保应用程序可容忍故障或维护事件。

## <a name="create-an-availability-set"></a>创建可用性集

可以使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 创建一个可用性集。 在本示例中，将 myResourceGroupAvailability 资源组中名为 myAvailabilitySet 的可用性集的更新域数和容错域数均设置为 2。


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM

需要在可用性集内创建 VM，以确保其正确地分布在硬件中。 创建后，无法将现有 VM 添加到可用性集中。 

同一位置的硬件分为多个更新域和容错域。 更新域是一组可同时重启的 VM 和基础物理硬件。 同一个容错域内的 VM 共享公用存储，以及公用电源和网络交换机。 

使用 [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 通过配置创建 VM 时，可以使用 `-AvailabilitySetId` 参数指定可用性集，以指定该可用性集的 ID。

在可用性集中使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 2 个 VM。

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

创建和配置这两个 VM 需要几分钟的时间完成。 完成后，将拥有 2 个跨基础硬件分布的虚拟机。 

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小 

稍后可向可用性集添加更多 VM，但需了解在硬件上可用的 VM 大小。 使用 [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 列出可用性集的硬件群集上所有可用的大小。

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何创建虚拟机规模集。 请转到下一教程，了解虚拟机规模集。

[创建 VM 规模集](tutorial-create-vmss.md)



