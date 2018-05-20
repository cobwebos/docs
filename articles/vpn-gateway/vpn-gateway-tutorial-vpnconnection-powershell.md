---
title: 使用 PowerShell 创建和管理 Azure S2S VPN 连接 | Microsoft Docs
description: 教程 - 使用 Azure PowerShell 模块创建和管理 S2S VPN 连接
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
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>使用 Azure PowerShell 模块创建和管理 S2S VPN 连接

Azure S2S VPN 连接提供客户本地和 Azure 间的安全跨界连接。 本教程详细介绍 IPsec S2S VPN 连接的生命周期，例如创建和管理 S2S VPN 连接。 学习如何：

> [!div class="checklist"]
> * 创建 S2S VPN 连接
> * 更新连接属性：预共享密钥、BGP、IPsec/IKE 策略
> * 添加更多的 VPN 连接
> * 删除 VPN 连接

下图显示了本教程的拓扑：

![站点到站点 VPN 连接图](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="requirements"></a>要求

完成第一个教程：[使用 Azure PowerShell 创建 VPN 网关](vpn-gateway-tutorial-create-gateway-powershell.md)，以便创建以下资源：

1. 资源组 (TestRG1)、虚拟网络 (VNet1) 和 GatewaySubnet
2. VPN 网关 (VNet1GW)

下面列出的是虚拟网络参数值。 请注意本地网关用来表示本地网络的其他值。 可以根据环境和网络设置更改这些值。

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

用于创建 S2S VPN 连接的工作流非常简单：

1. 创建用来代表本地网络的本地网关
2. 在 Azure VPN 网关与本地网关之间创建连接

## <a name="create-a-local-network-gateway"></a>创建本地网络网关

本地网关代表本地网络。 可以在本地网关中指定本地网络的属性，其中包括：

* VPN 设备的公共 IP 地址
* 本地地址空间
* （可选）BGP 属性（BGP 对等机 IP 地址和 AS 编号）

使用 [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway) 命令创建本地网关。

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>创建 S2S VPN 连接

接下来，请使用 [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection) 在虚拟网关和 VPN 设备之间创建站点到站点 VPN 连接。 请注意，站点到站点 VPN 的“-ConnectionType”为 *IPsec*。

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

若要使用 BGP，请添加可选的“**-EnableBGP $True**”属性，以便为连接启用 BGP。 此项默认禁用。

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>更新 VPN 连接预共享密钥、BGP 和 IPsec/IKE 策略

### <a name="view-and-update-your-pre-shared-key"></a>查看和更新预共享密钥

Azure S2S VPN 连接使用预共享密钥（机密）在本地 VPN 设备和 Azure VPN 网关之间进行身份验证。 可以使用 [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) 和 [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey) 查看和更新连接的预共享密钥。

> [!IMPORTANT]
> 预共享密钥是一个字符串，其中包含的**可打印 ASCII 字符**在长度上不得超出 128 个字符。

以下命令显示连接的预共享密钥：

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

执行上面的示例时，输出将为“**Azure@!b2C3**”。 使用以下命令可以将预共享密钥值更改为“**Azure@!_b2=C3**”：

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>在 VPN 连接上启用 BGP

Azure VPN 网关支持 BGP 动态路由协议。 可以在每个单独的连接上启用 BGP，具体取决于你是否在本地网络和设备上使用 BGP。 请指定以下 BGP 属性，然后再在连接上启用 BGP：

* Azure VPN ASN（自治系统编号）
* 本地网关 ASN
* 本地网关 BGP 对等机 IP 地址

如果尚未配置 BGP 属性，请使用以下命令将这些属性添加到 VPN 网关和本地网关：[Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) 和 [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway)。

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

使用 [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection) 启用 BGP。

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

将“-EnableBGP”属性值更改为 **$False** 即可禁用 BGP。 请参阅 [Azure VPN 网关上的 BGP](vpn-gateway-bgp-resource-manager-ps.md)，获取有关 Azure VPN 网关上的 BGP 的更多详细说明。

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>在连接上应用自定义 IPsec/IKE 策略

可以应用可选的 IPsec/IKE 策略，以便在连接上指定 IPsec/IKE 加密算法和密钥强度的具体组合，不必使用[默认建议](vpn-gateway-about-vpn-devices.md#ipsec)。 下方示例脚本使用以下算法和参数创建其他 IPsec/IKE 策略：

* IKEv2：AES256、SHA256、DHGroup14
* IPsec：AES128、SHA1、PFS14、SA 生存期 14,400 秒和 102,400,000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

请参阅[适用于 S2S 或 VNet 到 VNet 连接的 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)，以获取算法和说明的完整列表。

## <a name="add-another-s2s-vpn-connection"></a>添加另一 S2S VPN 连接

若要添加另一个到同一 VPN 网关的 S2S VPN 连接，请创建另一本地网关，然后在新的本地网关和 VPN 网关之间创建新连接。 请执行本文中的示例。

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

现在有两个到 Azure VPN 网关的 S2S VPN 连接。

![多站点 VPN 连接](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>删除 S2S VPN 连接

使用 [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection) 删除 S2S VPN 连接。

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

在不再需要的情况下删除本地网关。 如果有其他连接与本地网关相关联，则不能删除本地网关。

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建和管理 S2S VPN 连接，例如，如何：

> [!div class="checklist"]
> * 创建 S2S VPN 连接
> * 更新连接属性：预共享密钥、BGP、IPsec/IKE 策略
> * 添加更多的 VPN 连接
> * 删除 VPN 连接

若要了解 S2S 连接、VNet 到 VNet 连接和 P2S 连接，请转到以下教程。

> [!div class="nextstepaction"]
> * [创建 VNet 到 VNet 连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [创建 P2S 连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
