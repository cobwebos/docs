---
title: "使用虚拟网络对等互连连接虚拟网络 - PowerShell | Microsoft Docs"
description: "了解如何使用虚拟网络对等互连连接虚拟网络。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>通过 PowerShell 使用虚拟网络对等互连连接虚拟网络

可以使用虚拟网络对等互连将虚拟网络互相连接。 将虚拟网络对等互连后，两个虚拟网络中的资源将能够以相同的延迟和带宽相互通信，就像这些资源位于同一个虚拟网络中一样。 本文介绍了如何创建两个虚拟网络并将其对等互连。 学习如何：

> [!div class="checklist"]
> * 创建两个虚拟网络
> * 在虚拟网络之间创建对等互连
> * 测试对等互连

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

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

*myVirtualNetwork2* 虚拟网络的地址前缀与 *myVirtualNetwork1* 虚拟网络的地址前缀不重叠。 不能将具有重叠地址前缀的虚拟网络进行对等互连。

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

对等互连在两个虚拟网络之间进行，但不可传递。 因此，举例来说，如果还想要将 *myVirtualNetwork2* 对等互连到 *myVirtualNetwork3*，则需要另外在虚拟网络 *myVirtualNetwork2* 和 *myVirtualNetwork3* 之间创建对等互连。 即使 *myVirtualNetwork1* 已与 *myVirtualNetwork2* 对等互连，也仅当 *myVirtualNetwork1* 也与 *myVirtualNetwork3* 对等互连时，*myVirtualNetwork1* 中的资源才能访问 *myVirtualNetwork3* 中的资源。 

将生产虚拟网络对等互连之前，建议全面了解[对等互连概述](virtual-network-peering-overview.md)、[管理对等互连](virtual-network-manage-peering.md)和[虚拟网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 虽然本文以同一订阅和位置中两个虚拟网络之间的对等互连举例说明，但也可将[不同区域](#register)和[不同 Azure 订阅](create-peering-different-subscriptions.md#powershell)中的虚拟网络对等互连。 还可以使用对等互连创建[中心辐射型网络设计](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

## <a name="test-peering"></a>测试对等互连

若要通过对等互连测试不同虚拟网络中的虚拟机之间的网络通信，请将虚拟机部署到每个子网，然后在虚拟机之间进行通信。 

### <a name="create-virtual-machines"></a>创建虚拟机

在每个虚拟网络中创建虚拟机，以便在稍后的步骤中可以验证它们之间的通信。

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。 以下示例在 *myVirtualNetwork1* 虚拟网络中创建一个名为 *myVm1* 的虚拟机。 `-AsJob` 选项会在后台创建虚拟机，因此可继续执行下一步。 系统提示时，请输入需要用来登录到虚拟机的用户名和密码。

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

Azure 会自动分配 10.0.0.4 作为虚拟机的专用 IP 地址，因为 10.0.0.4 是 *myVirtualNetwork1* 的 *Subnet1* 中的第一个可用 IP 地址。 

在 *myVirtualNetwork2* 虚拟网络中创建虚拟机。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

创建虚拟机需花费几分钟的时间。 Azure 已分配 10.1.0.4（虽然不在返回的输出中）作为虚拟机的专用 IP 地址，因为 10.1.0.4 是 *myVirtualNetwork2* 的 *Subnet1* 中的第一个可用 IP 地址。 

在 Azure 创建虚拟机并将输出返回到 PowerShell 之前，不要继续执行后续步骤。

### <a name="test-virtual-machine-communication"></a>测试虚拟机通信

可以从 Internet 连接到虚拟机的公用 IP 地址。 使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 返回虚拟机的公用 IP 地址。 以下示例返回了 myVm1 虚拟机的公共 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

从本地计算机使用以下命令创建与 myVm1 虚拟机的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机，同时打开该文件。 输入用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建虚拟机时输入的凭据），然后单击“确定”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。

在命令提示符下，启用穿过 Windows 防火墙的 ping，以便在稍后的步骤中可以从 *myVm2* 对此虚拟机执行 ping 操作。

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

虽然本文中使用 ping 进行测试，但是对于生产部署，建议不要允许 ICMP 穿过 Windows 防火墙。

若要连接到 *myVm2* 虚拟机，请在 *myVm1* 虚拟机上通过命令提示符输入以下命令：

```
mstsc /v:10.1.0.4
```

由于你已在 *myVm1* 上启用了 ping，因此，现在可以在 *myVm2* 虚拟机上通过命令提示符按 IP 地址对它执行 ping 操作：

```
ping 10.0.0.4
```

将收到四个回复。 如果按虚拟机的名称 (*myVm1*) 而不是其 IP 地址进行 ping 操作，ping 操作会失败，因为 *myVm1* 是未知的主机名。 Azure 的默认名称解析在同一虚拟网络中的虚拟机之间可以正常工作，但在不同虚拟网络中的虚拟机之间无法正常工作。 若要跨虚拟网络解析名称，必须[部署自己的 DNS 服务器](virtual-networks-name-resolution-for-vms-and-role-instances.md)或使用 [Azure DNS 专用域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

断开到 *myVm1* 和 *myVm2* 的 RDP 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>注册全局虚拟网络对等互连（预览版）**

在同一区域中的虚拟网络之间建立对等互连的功能已推出正式版。 在不同区域的虚拟网络之间建立对等互连目前处于预览版状态。 有关可用区域，请参阅[虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)。 若要跨区域在虚拟网络之间建立对等互连，必须先通过完成以下步骤（在要对等互连的每个虚拟网络所在的订阅中执行）来注册预览版：

1. 通过输入以下命令，注册要对其建立对等互连的每个虚拟网络所在订阅的预览版：

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. 输入以下命令，确认已针对预览版进行了注册：

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    对于这两个订阅，在输入上一个命令后收到的 **RegistrationState** 输出为 **Registered** 之前，如果尝试将不同区域中的虚拟网络对等互连，则对等互连将失败。

## <a name="next-steps"></a>后续步骤

在本文中，你已学习了如何使用虚拟网络对等互连来连接两个网络。 可以通过 VPN [将自己的计算机连接到虚拟网络](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，并可与虚拟网络或对等虚拟网络中的资源进行交互。

请继续学习可重用脚本的脚本示例，以完成虚拟网络文章中涉及的许多任务。

> [!div class="nextstepaction"]
> [虚拟网络脚本示例](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
