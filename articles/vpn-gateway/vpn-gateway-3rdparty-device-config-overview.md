---
title: "用于连接到 Azure VPN 网关的合作伙伴 VPN 设备配置 | Microsoft Docs"
description: "本文提供用于连接到 Azure VPN 网关的合作伙伴 VPN 设备配置的概述。"
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
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: b3806d16d3b78347e183ecbd2ab5a463a2142110
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="overview-of-partner-vpn-device-configurations"></a>合作伙伴 VPN 设备配置概述
本文提供有关配置用于连接到 Azure VPN 网关的本地 VPN 设备的概述。 示例 Azure 虚拟网络和 VPN 网关设置用于演示如何使用相同参数连接到不同的本地 VPN 设备配置。

## <a name="device-requirements"></a>设备要求
Azure VPN 网关使用标准 IPsec/IKE 协议套件建立站点到站点 (S2S) VPN 隧道。 若要获取 Azure VPN 网关的 IPsec/IKE 参数和加密算法列表，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。 还可以根据[关于加密要求](vpn-gateway-about-compliance-crypto.md)中所述，为特定的连接指定确切的算法与密钥长度。

## <a name ="singletunnel"></a>单一 VPN 隧道
示例中的首个配置由 Azure VPN 网关与本地 VPN 设备之间的单个 S2S VPN 隧道构成。 可选择性配置[跨 VPN 隧道的边界网关协议(BGP)](#bgp)。

![单一 S2S VPN 隧道图示](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

有关设置单一 VPN 隧道的分布说明，请参阅[配置站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 以下部分指定示例配置的连接参数，并提供 PowerShell 脚本来帮助读者入门。

### <a name="connection-parameters"></a>连接参数
本部分列出了前几部分中示例中所用的参数。

| **Parameter**                | **值**                    |
| ---                          | ---                          |
| 虚拟网络地址前缀        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 网关 IP         | Azure VPN 网关 IP         |
| 本地地址前缀 | 10.51.0.0/16<br>10.52.0.0/16 |
| 本地 VPN 设备 IP    | 本地 VPN 设备 IP    |
| * 虚拟网络 BGP ASN                | 65010                        |
| * Azure BGP 对等 IP           | 10.12.255.30                 |
| * 本地 BGP ASN         | 65050                        |
| * 本地 BGP 对等 IP     | 10.52.255.254                |

\* 仅适用于 BGP 的可选参数。

### <a name="sample-powershell-script"></a>示例 PowerShell 脚本
本部分提供入门示例脚本。 有关详细说明，请参阅[使用 PowerShell 创建 S2S VPN 连接](vpn-gateway-create-site-to-site-rm-powershell.md)。

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

### <a name ="policybased"></a>（可选）结合“UsePolicyBasedTrafficSelectors”使用自定义 IPsec/IKE 策略
如果 VPN 设备不支持“任意到任意”流量选择器（例如基于路由或基于 VTI 的配置），请创建自定义 IPsec/IKE 策略并配置[“UsePolicyBasedTrafficSelectors”](vpn-gateway-connect-multiple-policybased-rm-ps.md)选项。

> [!IMPORTANT]
> 需创建 IPsec/IKE 策略，才能对连接启用“UsePolicyBasedTrafficSelectors”选项。


示例脚本使用以下算法和参数创建 IPsec/IKE 策略：
* IKEv2：AES256、SHA384、DHGroup24
* IPsec：AES256、SHA1、PFS24、SA 生存期 7200 秒和 20480000KB (20GB)

该脚本应用 IPsec/IKE 策略，并对连接启用“UsePolicyBasedTrafficSelectors”选项。

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>（可选）在 S2S VPN 连接中使用 BGP
创建 S2S VPN 连接时，可选择性地使用[用于 VPN 网关的 BGP](vpn-gateway-bgp-resource-manager-ps.md)。 这种方法有两点不同：

* 本地地址前缀可以是单个主机地址。 本地 BGP 对等 IP 地址指定如下：

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* 在创建连接时，必须将“-EnableBGP”选项设置为 $True：

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>后续步骤
有关设置主动 - 主动 VPN 网关的分布说明，请参阅[为跨界连接和 VNet 到 VNet 连接配置主动 - 主动 VPN 网关](vpn-gateway-activeactive-rm-powershell.md)。


