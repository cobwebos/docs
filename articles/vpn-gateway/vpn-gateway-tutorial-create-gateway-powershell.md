---
title: 使用 PowerShell 创建和管理 Azure VPN 网关 | Microsoft Docs
description: 教程 - 使用 Azure PowerShell 模块创建和管理 VPN 网关
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 790a8b74f437fe8fd7b8660c2ac9d208328b487f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445216"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>教程：使用 PowerShell 创建和管理 VPN 网关

Azure VPN 网关在客户本地与 Azure 之间提供跨界连接。 本教程介绍了基本的 Azure VPN 网关部署项目，例如创建和管理 VPN 网关。 学习如何：

> [!div class="checklist"]
> * 创建 VPN 网关
> * 查看公用 IP 地址
> * 调整 VPN 网关大小
> * 重置 VPN 网关

下图展示了本教程中创建的虚拟网络和 VPN 网关。

![VNet 和 VPN 网关](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell 和 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>通用网络参数值

根据你的环境和网络设置更改以下值，然后复制并粘贴以设置本教程的变量。 如果 Cloud Shell 会话超时，或者需要使用其他 PowerShell 窗口，请将变量复制并粘贴到新会话中，然后继续使用本教程。

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

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须先创建资源组。 以下示例在*美国东部*区域中创建名为 *TestRG1* 的资源组：

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

Azure VPN 网关为虚拟网络提供跨界连接和 P2S VPN 服务器功能。 可以将 VPN 网关添加到现有虚拟网络，也可以创建新的虚拟网络和网关。 此示例创建包含三个子网的全新虚拟网络：Frontend、Backend 和 GatewaySubnet，使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 和 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 进行创建：

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>为 VPN 网关请求一个公用 IP 地址

Azure VPN 网关通过 Internet 与本地 VPN 设备进行通信，执行 IKE（Internet 密钥交换）协商并建立 IPsec 隧道。 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 和 [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) 创建一个公共 IP 地址并将其分配给 VPN 网关，如以下示例中所示：

> [!IMPORTANT]
> 目前，只能为网关使用“动态”公用 IP 地址。 Azure VPN 网关不支持静态 IP 地址。

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>创建 VPN 网关

创建 VPN 网关可能需要 45 分钟或更长时间。 完成网关创建后，可以在虚拟网络与另一个 VNet 之间创建连接。 或者，在虚拟网络与本地位置之间创建连接。 使用 [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) cmdlet 创建 VPN 网关。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

关键参数值：
* GatewayType：对于站点到站点和 VNet 到 VNet 连接，请使用 **Vpn**
* VpnType：使用 **RouteBased** 与更广范围的 VPN 设备和更多路由功能进行交互
* GatewaySku：默认值为 **VpnGw1**；如果需要更高的吞吐量或更多连接，请将其更改为 VpnGw2 或 VpnGw3。 有关详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

如果使用的是 TryIt，你的会话可能会超时。没关系。 仍会创建网关。

完成网关创建后，可以在虚拟网络与另一个 VNet 之间创建连接，或者在虚拟网络与本地位置之间创建连接。 还可以配置从客户端计算机到 VNet 的 P2S 连接。

## <a name="view-the-gateway-public-ip-address"></a>查看网关公共 IP 地址

如果知道公共 IP 地址的名称，可使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 来显示分配给网关的公共 IP 地址。

如果会话超时，请将本教程开头的常用网络参数复制到新会话中，然后继续操作。

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>重设网关大小

可以在创建网关之后更改 VPN 网关 SKU。 不同的网关 SKU 支持不同的规范，例如吞吐量、连接数，等等。以下示例使用 [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) 将网关的大小从 VpnGw1 调整为 VpnGw2。 有关详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

调整 VPN 网关大小也将花费大约 30 到 45 分钟，但是此操作“不会”中断或删除现有连接和配置。

## <a name="reset-a-gateway"></a>重置网关

作为故障排除步骤的一部分，你可以重置 Azure VPN 网关来强制 VPN 网关重新启动 IPsec/IKE 隧道配置。 使用 [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) 重置网关。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

有关详细信息，请参阅[重置 VPN 网关](vpn-gateway-resetgw-classic.md)。

## <a name="clean-up-resources"></a>清理资源

如果要转到[下一个教程](vpn-gateway-tutorial-vpnconnection-powershell.md)，你将需要保留这些资源，因为它们是先决条件。

但是，如果网关是某个原型、测试或概念证明部署的一部分，则可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令来删除资源组、VPN 网关和所有相关资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解了 VPN 创建和管理基本知识，例如如何：

> [!div class="checklist"]
> * 创建 VPN 网关
> * 查看公用 IP 地址
> * 调整 VPN 网关大小
> * 重置 VPN 网关

转到以下教程来了解 S2S、VNet 到 VNet 和 P2S 连接。

> [!div class="nextstepaction"]
> * [创建 S2S 连接](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [创建 VNet 到 VNet 连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [创建 P2S 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
