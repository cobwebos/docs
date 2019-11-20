---
title: 创建具有静态专用 IP 地址的 VM-Azure PowerShell
description: 了解如何使用 PowerShell 创建具有专用 IP 地址的虚拟机。
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
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196601"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>使用 PowerShell 创建具有静态专用 IP 地址的虚拟机

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以创建具有静态专用 IP 地址的虚拟机 (VM)。 若要从子网中选择分配给 VM 的具体地址，请分配静态专用 IP 地址而非动态地址。 详细了解[静态专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#allocation-method)。 若要将分配给现有 VM 的专用 IP 地址从动态更改为静态，或者要使用公共 IP 地址，请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以从本地计算机或通过使用 Azure Cloud Shell 完成以下步骤。 若要使用本地计算机，请确保[安装了 Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要使用 Azure Cloud Shell，请在接下来出现的任何命令框的右上角选择“试用”。 Cloud Shell 会将你登录到 Azure。

1. 如果使用 Cloud Shell，请跳到步骤 2。 打开命令会话并使用 `Connect-AzAccount` 登录到 Azure。
2. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. 使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 和 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 命令创建子网配置和虚拟网络：

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

4. 使用 [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) 和 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 命令在虚拟网络中创建一个网络接口，并将专用 IP 地址从子网分配给该网络接口：

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

5. 使用 [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig) 创建 VM 配置，然后使用 [New-AzVM](/powershell/module/az.Compute/New-azVM) 创建 VM。 出现提示时，请提供用作 VM 登录凭据的用户名和密码：

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> 虽然可以向操作系统添加专用 IP 地址设置，但建议不要在阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之前这样做。
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> 若要从 Internet 访问 VM，必须为该 VM 分配公共 IP 地址。 也可将动态专用 IP 地址分配更改为静态分配。 有关详细信息，请参阅[添加或更改 IP 地址](virtual-network-network-interface-addresses.md)。 另外，建议将一个网络安全组关联到网络接口和/或创建网络接口时所在的子网，限制从网络到 VM 的流量。 有关详细信息，请参阅[管理网络安全组](manage-network-security-group.md)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解[专用 IP 地址](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)。
- 详细了解如何创建 [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机。
