---
title: "关于用于连接到 Azure VPN 网关的第三方 VPN 设备配置 | Microsoft Docs"
description: "本文提供用于连接到 Azure VPN 网关的第三方 VPN 设备配置的概述。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017


---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>第三方 VPN 设备配置概述
本文提供用于连接到 Azure VPN 网关的本地 VPN 设备配置的概述。 示例 Azure 虚拟网络和 VPN 网关设置用于连接到具有相同参数的不同本地 VPN 设备。

## <a name="device-requirements"></a>设备要求
Azure VPN 网关使用标准 IPsec/IKE 协议套件建立 S2S VPN 隧道。 请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)，了解 Azure VPN 网关的详细 IPsec/IKE 协议参数和默认加密算法。 （可选）可以根据[关于加密要求](vpn-gateway-about-compliance-crypto.md)中所述，为特定的连接准确指定加密算法与密钥长度的组合。

## <a name ="singletunnel"></a>单一 VPN 隧道
第一个拓扑由 Azure VPN 网关与本地 VPN 设备之间的单个 S2S VPN 隧道构成。 （可选）可以配置跨 VPN 隧道的 BGP。

![单一隧道](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

有关详细的分步指南，请参阅[配置站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 以下部分列出参数，并提供入门示例 PowerShell 脚本。

### <a name="network-and-vpn-gateway-information"></a>网络和 VPN 网关信息
本部分列出上述示例的参数。

| **Parameter**                | **值**                    |
| ---                          | ---                          |
| VNet 地址前缀        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 网关 IP         | Azure VPN 网关 IP         |
| 本地地址前缀 | 10.51.0.0/16<br>10.52.0.0/16 |
| 本地 VPN 设备 IP    | 本地 VPN 设备 IP    |
| * VNet BGP ASN                | 65010                        |
| *Azure BGP 对等 IP           | 10.12.255.30                 |
| *本地 BGP ASN         | 65050                        |
| *本地 BGP 对等 IP     | 10.52.255.254                |

* (*) 仅适用于 BGP 的可选参数

### <a name="sample-powershell-script"></a>示例 PowerShell 脚本
[使用 PowerShell 创建 S2S VPN 连接](vpn-gateway-create-site-to-site-rm-powershell.md)中提供了详细说明。 本部分提供入门示例脚本。

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>[可选] 结合“UsePolicyBasedTrafficSelectors”使用自定义 IPsec/IKE 策略
如果 VPN 设备不支持“任意到任意”流量选择器（基于路由/基于 VTI 的配置），则需要根据[此文](vpn-gateway-connect-multiple-policybased-rm-ps.md)中所述，创建自定义 IPsec/IKE 策略并配置“UsePolicyBasedTrafficSelectors”选项。

> [!IMPORTANT]
> 需创建 IPsec/IKE 策略，才能对连接启用“UsePolicyBasedTrafficSelectors”选项。

下方示例脚本使用以下算法和参数创建 IPsec/IKE 策略：
* IKEv2：AES256、SHA384、DHGroup24
* IPsec：AES256、SHA1、PFS24、SA 生存期 7200 秒和 20480000KB (20GB)

然后它会应用策略，并在连接中启用“UesPolicyBasedTrafficSelectors”。

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[可选] 在 S2S VPN 连接中使用 BGP
可以选择性地在连接中使用 BGP。 请参阅 [VPN 网关的 BGP](vpn-gateway-bgp-resource-manager-ps.md)。 有两个区别：

本地地址前缀可以是单个主机地址，即本地 BGP 对等 IP 地址：

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

创建连接时，必须将“-EnableBGP”设置为 $True：

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>后续步骤
有关配置主动-主动跨界连接和 VNet 到 VNet 连接的步骤，请参阅[为跨界连接和 VNet 到 VNet 连接配置主动-主动 VPN 网关](vpn-gateway-activeactive-rm-powershell.md)。


