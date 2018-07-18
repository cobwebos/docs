---
title: 使用 PowerShell 创建和管理 Azure VPN 网关 | Microsoft Docs
description: 教程 - 使用 Azure PowerShell 模块创建和管理 VPN 网关
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0f10384e7e21d65b3a16869a10f8294b9643c74c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705677"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>使用 Azure PowerShell 模块创建和管理 VPN 网关

Azure VPN 网关在客户本地与 Azure 之间提供跨界连接。 本教程介绍了基本的 Azure VPN 网关部署项目，例如创建和管理 VPN 网关。 学习如何：

> [!div class="checklist"]
> * 创建 VPN 网关
> * 调整 VPN 网关大小
> * 重置 VPN 网关

下图展示了本教程中创建的虚拟网络和 VPN 网关。

![VNet 和 VPN 网关](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell 和 Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="common-network-parameter-values"></a>通用网络参数值

根据环境和网络设置更改以下值。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须先创建资源组。 以下示例在*美国东部*区域中创建名为 *TestRG1* 的资源组：

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

Azure VPN 网关为虚拟网络提供跨界连接和 P2S VPN 服务器功能。 可以将 VPN 网关添加到现有虚拟网络，也可以创建新的虚拟网络和网关。 此示例使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 和 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建具有三个子网（Frontend、Backend 和 GatewaySubnet）的新虚拟网络：

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>为 VPN 网关请求一个公共 IP 地址

Azure VPN 网关通过 Internet 与本地 VPN 设备进行通信来执行 IKE（Internet 密钥交换）协商并建立 IPsec 隧道。 使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 和 [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) 创建一个公用 IP 地址并将其分配给 VPN 网关，如以下示例中所示：

> [!IMPORTANT]
> 目前，只能为网关使用“动态”公用 IP 地址。 Azure VPN 网关不支持静态 IP 地址。

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>创建 VPN 网关

创建 VPN 网关可能需要 45 分钟或更长时间。 完成网关创建后，可以在虚拟网络与另一个 VNet 之间创建连接。 或者，在虚拟网络与本地位置之间创建连接。 使用 [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway) cmdlet 创建 VPN 网关。

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

关键参数值：
* GatewayType：对于站点到站点和 VNet 到 VNet 连接，请使用 **Vpn**
* VpnType：使用 **RouteBased** 与更广范围的 VPN 设备和更多路由功能进行交互
* GatewaySku：默认值为 **VpnGw1**；如果需要更高的吞吐量或更多连接，请将其更改为 VpnGw2 或 VpnGw3。 有关详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

完成网关创建后，可以在虚拟网络与另一个 VNet 之间创建连接，或者在虚拟网络与本地位置之间创建连接。 还可以配置从客户端计算机到 VNet 的 P2S 连接。

## <a name="resize-vpn-gateway"></a>调整 VPN 网关大小

可以在创建网关之后更改 VPN 网关 SKU。 不同的网关 SKU 支持不同的规范，例如吞吐量、连接数，等等。以下示例使用 [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) 将网关的大小从 VpnGw1 调整为 VpnGw2。 有关详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

调整 VPN 网关大小也将花费大约 30 到 45 分钟，但是此操作“不会”中断或删除现有连接和配置。

## <a name="reset-vpn-gateway"></a>重置 VPN 网关

作为故障排除步骤的一部分，你可以重置 Azure VPN 网关来强制 VPN 网关重新启动 IPsec/IKE 隧道配置。 使用 [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) 重置网关。

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

有关详细信息，请参阅[重置 VPN 网关](vpn-gateway-resetgw-classic.md)。

## <a name="get-the-gateway-public-ip-address"></a>获取网关公用 IP 地址

如果知道公用 IP 地址的名称，可使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) 来显示分配给网关的公用 IP 地址。

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>删除 VPN 网关

除了 VPN 网关之外，跨界和 VNet 到 VNet 连接的完整配置还需要多个资源类型。 在删除 VPN 网关本身之前，请删除与其关联的连接。 在删除网关后，可以删除网关的公用 IP 地址。 有关详细步骤，请参阅[删除 VPN 网关](vpn-gateway-delete-vnet-gateway-powershell.md)。

如果网关是某个原型或概念验证部署的一部分，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令来删除资源组、VPN 网关和所有相关资源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解了 VPN 创建和管理基本知识，例如如何：

> [!div class="checklist"]
> * 创建 VPN 网关
> * 调整 VPN 网关大小
> * 重置 VPN 网关

若要了解 S2S 连接、VNet 到 VNet 连接和 P2S 连接，请转到以下教程。

> [!div class="nextstepaction"]
> * [创建 S2S 连接](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [创建 VNet 到 VNet 连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [创建 P2S 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
