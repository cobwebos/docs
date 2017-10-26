---
title: "将本地网络连接到 Azure 虚拟网络：站点到站点 VPN：PowerShell | Microsoft 文档"
description: "通过公共 Internet 创建从本地网络到 Azure 虚拟网络的 IPsec 连接的步骤。 这些步骤将帮助你使用 PowerShell 创建跨界站点到站点 VPN 网关连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: cherylmc
ms.openlocfilehash: 489c922ae83af7146164500a1494532e95877aa4
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>使用 PowerShell 创建具有站点到站点 VPN 连接的 VNet

本文介绍如何使用 PowerShell 创建站点到站点 VPN 网关连接，以便从本地网络连接到 VNet。 本文中的步骤适用于 Resource Manager 部署模型。 也可使用不同的部署工具或部署模型来创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure 门户（经典）](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

![站点到站点 VPN 网关跨界连接示意图](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>准备工作

在开始配置之前，请验证你是否符合以下条件：

* 确保有一台兼容的 VPN 设备，并且可对其进行配置。 有关兼容的 VPN 设备和设备配置的详细信息，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。
* 确认 VPN 设备有一个面向外部的公共 IPv4 地址。 此 IP 地址不得位于 NAT 之后。
* 如果不熟悉本地网络配置中的 IP 地址范围，则需咨询能够提供此类详细信息的人员。 创建此配置时，必须指定 IP 地址范围前缀，Azure 会将该前缀路由到本地位置。 本地网络的任何子网都不得与要连接到的虚拟网络子网重叠。
* 安装最新版本的 Azure Resource Manager PowerShell cmdlet。 PowerShell cmdlet 经常会更新，因此，你通常需要更新 PowerShell cmdlet 才能获取最新的功能。 如果未更新 PowerShell cmdlet，指定的值可能会失败。 有关下载和安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a name="example"></a>示例值

本文中的示例使用以下值。 可使用这些值创建测试环境，或参考这些值以更好地理解本文中的示例。

```
#Example values

VnetName                = TestVNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.1.0/28 
GatewaySubnet           = 10.11.0.0/27
LocalNetworkGatewayName = Site2
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24, 20.0.0.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2

```


## <a name="Login"></a>1.连接到订阅

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2.创建虚拟网络和网关子网

如果还没有虚拟网络，请创建。 创建虚拟网络时，请确保指定的地址空间不与本地网络的任一个地址空间相重叠。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>创建虚拟网络和网关子网

此示例创建虚拟网络和网关子网。 如果已经有一个虚拟网络且需要向其添加网关子网，请参阅[向已创建的虚拟网络添加网关子网](#gatewaysubnet)。

创建资源组：

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

创建虚拟网络。

1. 设置变量。

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix 10.11.1.0/28
  ```
2. 创建 VNet。

  ```powershell
  New-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.11.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>将网关子网添加到已创建的虚拟网络

1. 设置变量。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. 创建网关子网。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27 -VirtualNetwork $vnet
  ```
3. 设置配置。

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3.<a name="localnet"></a>创建本地网关

本地网络网关通常是指本地位置。 可以为站点提供一个名称供 Azure 引用，并指定本地 VPN 设备的 IP 地址，以便创建一个连接来连接到该设备。 此外还可指定 IP 地址前缀，以便通过 VPN 网关将其路由到 VPN 设备。 指定的地址前缀是位于本地网络的前缀。 如果本地网络出现变化，可以轻松更新这些前缀。

使用以下值：

* *GatewayIPAddress* 是本地 VPN 设备的 IP 地址。 VPN 设备不能位于 NAT 之后。
* *AddressPrefix* 是本地地址空间。

若要添加具有单个地址前缀的局域网网关：

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

若要添加具有多个地址前缀的局域网网关：

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

若要为本地网关修改 IP 地址前缀：<br>
有时局域网网关前缀会有变化。 修改 IP 地址前缀时采取的步骤取决于是否已创建 VPN 网关连接。 请参阅本文的 [修改本地网关的 IP 地址前缀](#modify) 部分。

## <a name="PublicIP"></a>4.请求公共 IP 地址

VPN 网关必须具有公共 IP 地址。 请先请求 IP 地址资源，然后在创建虚拟网关时参阅该资源。 创建 VPN 网关时，IP 地址是动态分配给资源的。 VPN 网关当前仅支持动态公共 IP 地址分配。 不能请求静态公共 IP 地址分配。 但这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 该地址不会因为 VPN 网关大小调整、重置或其他内部维护/升级而更改。

请求一个公共 IP 地址，该地址将分配给虚拟网络 VPN 网关。

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5.创建网关 IP 寻址配置

网关配置定义要使用的子网和公共 IP 地址。 使用以下示例创建网关配置：

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6.创建 VPN 网关

创建虚拟网络 VPN 网关。 创建 VPN 网关可能需要长达 45 分钟或更长时间才能完成。

使用以下值：

* 站点到站点配置的 *-GatewayType* 为 *Vpn*。 网关类型永远是你要实现的配置的特定类型。 例如，其他网关配置可能需要 -GatewayType ExpressRoute。
* *-VpnType* 可以是 *RouteBased*（在某些文档中称为动态网关）或 *PolicyBased*（在某些文档中称为静态网关）。 有关 VPN 网关类型的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。
* 选择要使用的网关 SKU。 某些 SKU 存在配置限制。 有关详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。 如果创建 VPN 网关时出错（不管 -GatewaySku 是什么），请检查是否已安装最新版本的 PowerShell cmdlet。

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7.配置 VPN 设备

通过站点到站点连接连接到本地网络需要 VPN 设备。 在此步骤中，请配置 VPN 设备。 配置 VPN 设备时，需要以下项：

- 共享密钥。 此共享密钥就是在创建站点到站点 VPN 连接时指定的共享密钥。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
- 虚拟网关的“公共 IP 地址”。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要使用 PowerShell 查找虚拟网关的公共 IP 地址，请使用以下示例：

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8.创建 VPN 连接

接下来，会在虚拟网络网关和 VPN 设备之间创建站点到站点 VPN 连接。 请务必替换成自己的值。 共享密钥必须与用于 VPN 设备配置的值匹配。 请注意，站点到站点的“-ConnectionType”为 *IPsec*。

1. 设置变量。
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1
  ```

2. 创建连接。
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite2 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

在一小段时间后，将建立该连接。

## <a name="toverify"></a>9.验证 VPN 连接

VPN 连接有几种不同的验证方式。

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>连接到虚拟机

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>修改本地网关的 IP 地址前缀

如果需要路由到本地位置的 IP 地址前缀更改，则可修改本地网关。 提供了两套说明。 要选择哪套说明取决于是否创建了网关连接。

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>修改本地网关的 IP 地址

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>后续步骤

*  连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
* 有关 BGP 的信息，请参阅 [BGP 概述](vpn-gateway-bgp-overview.md)和[如何配置 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
* 有关使用 Azure 资源管理器模板创建站点到站点 VPN 连接的信息，请参阅[创建站点到站点 VPN 连接](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)。
* 有关使用 Azure 资源管理器模板创建 vnet 到 vnet VPN 连接的信息，请参阅[部署 HBase 异地复制](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)。
