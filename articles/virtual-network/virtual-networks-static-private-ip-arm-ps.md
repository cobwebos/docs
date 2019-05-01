---
title: 创建具有静态专用 IP 地址-Azure PowerShell 的 VM |Microsoft Docs
description: 了解如何使用专用 IP 地址使用 PowerShell 创建虚拟机。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 9115386b0543e1ac840aec29fc7f57e7c98c03bb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685335"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>使用静态专用 IP 地址使用 PowerShell 创建虚拟机

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

具有静态专用 IP 地址，可以创建虚拟机 (VM)。 如果你想要选择从子网的地址分配给 VM，分配静态专用 IP 地址，而不是动态地址。 详细了解如何[静态专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要更改在动态与静态的分配到现有 VM 的专用 IP 地址或使用公共 IP 地址，请参阅[添加、 更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以从本地计算机或通过使用 Azure Cloud Shell 完成以下步骤。 若要使用本地计算机，请确保[安装了 Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，请在接下来出现的任何命令框的右上角选择“试用”。 Cloud Shell 会将你登录到 Azure。

1. 如果使用 Cloud Shell，请跳到步骤 2。 打开命令会话并使用 `Connect-AzAccount` 登录到 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. 创建子网配置和使用虚拟网络[新建 AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)并[新建 AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)命令：

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. 在虚拟网络中创建网络接口并将从子网的专用 IP 地址分配给网络接口[新建 AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig)并[新建 AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)命令：

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. 创建 VM 配置与[新建 AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)，然后创建与 VM [New-azvm](/powershell/module/az.Compute/New-azVM)。 出现提示时，提供用户名和密码以用作 VM 登录凭据：

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> 尽管可以将专用 IP 地址设置添加到操作系统，我们建议不执行此操作在读取之前[将专用 IP 地址添加到操作系统](virtual-network-network-interface-addresses.md#private)。
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> 若要从 internet 访问 VM，必须将公共 IP 地址分配到 VM。 您还可以更改为静态分配的动态专用 IP 地址分配。 有关详细信息，请参阅[添加或更改 IP 地址](virtual-network-network-interface-addresses.md)。 此外，建议通过将相关联的网络接口，创建在中，网络接口的子网和 / 或网络安全组将网络流量限制到 VM。 有关详细信息，请参阅[管理网络安全组](manage-network-security-group.md)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)并分配[静态专用 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)到 Azure 虚拟机。
- 详细了解如何创建[Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)并[Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)虚拟机。
