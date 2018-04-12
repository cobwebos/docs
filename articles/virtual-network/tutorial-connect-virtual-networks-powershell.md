---
title: 使用虚拟网络对等互连连接虚拟网络 - PowerShell | Microsoft Docs
description: 在本文中，你将学习如何使用 Azure PowerShell 通过虚拟网络对等互连来连接虚拟网络。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2490b96716519ef749dd1e3a1fbe6846c6b5d999
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>通过 PowerShell 使用虚拟网络对等互连连接虚拟网络

可以使用虚拟网络对等互连将虚拟网络互相连接。 将虚拟网络对等互连后，两个虚拟网络中的资源将能够以相同的延迟和带宽相互通信，就像这些资源位于同一个虚拟网络中一样。 在本文中，学习如何：

* 创建两个虚拟网络
* 使用虚拟网络对等互连连接两个虚拟网络。
* 将虚拟机 (VM) 部署到每个虚拟网络
* VM 之间进行通信

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="create-virtual-networks"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络。 以下示例创建地址前缀为 *10.0.0.0/16* 且名为 *myVirtualNetwork1* 的虚拟网络。

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建子网配置。 以下示例创建地址前缀为 10.0.0.0/24 的子网配置：

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

使用 [Set-azurermvirtualnetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 将子网配置写入虚拟网络，从而创建子网：

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

创建地址前缀为 10.1.0.0/16 的虚拟网络和一个子网：

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>将虚拟网络对等互连

使用 [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) 创建对等互连。 以下示例将 *myVirtualNetwork1* 对等互连到 *myVirtualNetwork2*。

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

在上一个命令执行后返回的输出中，可以看到 **PeeringState** 为 *Initiated*。 对等互连将保持 *Initiated* 状态，直到你创建从 *myVirtualNetwork2* 到 *myVirtualNetwork1* 的对等互连。 创建从 *myVirtualNetwork2* 到 *myVirtualNetwork1* 的对等互连。 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

在上一个命令执行后返回的输出中，可以看到 **peeringState** 为 *Connected*。 Azure 还将 *myVirtualNetwork1-myVirtualNetwork2* 对等互连的对等互连状态更改为 *Connected*。 使用 [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) 确认 *myVirtualNetwork1-myVirtualNetwork2* 对等互连的对等互连状态是否已更改为 *Connected*。

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

在两个虚拟网络中的对等互连的 **PeeringState** 为 *Connected* 之前，在一个虚拟网络中的资源无法与另一个虚拟网络中的资源通信。 

## <a name="create-virtual-machines"></a>创建虚拟机

在稍后的步骤中，会在每个虚拟网络中创建一个 VM，以便可以在它们之间进行通信。

### <a name="create-the-first-vm"></a>创建第一个 VM

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 以下示例在 *myVirtualNetwork1* 虚拟网络中创建一个名为 *myVm1* 的 VM。 `-AsJob` 选项会在后台创建 VM，因此可继续执行下一步。 系统提示时，请输入想要用来登录到 VM 的用户名和密码。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>创建第二个 VM

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

创建 VM 需要几分钟时间。 在 Azure 创建 VM 并将输出返回到 PowerShell 之前，不要继续执行后续步骤。

## <a name="communicate-between-vms"></a>VM 之间进行通信

可以从 Internet 连接到 VM 的公用 IP 地址。 使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 返回 VM 的公共 IP 地址。 以下示例返回 myVm1 VM 的公共 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

从本地计算机使用以下命令创建与 myVm1 VM 的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机，同时打开该文件。 输入用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建 VM 时输入的凭据），然后单击“确定”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。

在 *myVm1* VM 上，允许 Internet 控制消息协议 (ICMP) 通过 Windows 防火墙，以便在稍后的步骤中使用 PowerShell 从 *myVm2* ping 此 VM：

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

虽然本文中使用 ping 在 VM 之间进行通信，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。

若要连接到 *myVm2* VM，请在 *myVm1* VM 上通过命令提示符输入以下命令：

```
mstsc /v:10.1.0.4
```

由于已在 *myVm1* 上启用了 ping，因此，现在可以在 *myVm2* VM 上通过命令提示符按 IP 地址对它执行 ping 操作：

```
ping 10.0.0.4
```

会收到四条回复。 断开到 *myVm1* 和 *myVm2* 的 RDP 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本文中，你已学习了如何使用虚拟网络对等互连来连接同一 Azure 区域中的两个网络。 还可以将不同[受支持的区域](virtual-network-manage-peering.md#cross-region)、[不同 Azure 订阅](create-peering-different-subscriptions.md#powershell)中的虚拟网络对等互连，并且可以使用对等互连创建[中心辐射型网络设计](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。 若要详细了解虚拟网络对等互连，请参阅[虚拟网络对等互连概述](virtual-network-peering-overview.md)和[管理虚拟网络对等互连](virtual-network-manage-peering.md)。

可以通过 VPN [将自己的计算机连接到虚拟网络](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，并可与虚拟网络或对等虚拟网络中的资源进行交互。 有关用来完成虚拟网络文章中涉及的许多任务的可重用脚本，请参阅[脚本示例](powershell-samples.md)。
