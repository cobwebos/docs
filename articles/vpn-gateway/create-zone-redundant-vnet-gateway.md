---
title: 在 Azure 可用性区域中创建区域冗余虚拟网络网关 | Microsoft Docs
description: 在可用性区域中部署 VPN 网关和 ExpressRoute 网关
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: f2b459ccfd7e3f513b9b6526864321ce247ae7aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405832"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>在 Azure 可用性区域中创建区域冗余虚拟网络网关

可以在 Azure 可用性区域中部署 VPN 网关和 ExpressRoute 网关。 这样可以提高虚拟网络网关的复原性、可伸缩性和可用性。 如果在 Azure 可用性区域中部署网关，可以在地理位置和逻辑上将区域内的网关分隔开来，同时还能保护本地网络与 Azure 的连接免受区域级故障的影响。 有关信息，请参阅[关于区域冗余虚拟网络网关](about-zone-redundant-vnet-gateways.md)和[关于 Azure 可用性区域](../availability-zones/az-overview.md)。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以使用在计算机上本地安装的 PowerShell，也可以使用 Azure Cloud Shell。 如果选择在本地安装并使用 PowerShell，必须使用最新版 PowerShell 模块，才能使用此功能。

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>在本地使用 PowerShell 的具体步骤

如果在计算机本地使用 PowerShell（而不使用 Cloud Shell），必须安装 PowerShell 模块 1.0.0 或更高版本。 若要检查已安装 PowerShell 的版本，请运行下面的命令：

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1.声明变量

下面列出了示例步骤中需要用到的值。 此外，一些示例步骤还使用已声明的变量。 若要在自己的环境中执行这些步骤，请务必将这些值替换为自己的值。 指定位置时，请确认指定的区域是否受支持。 有关详细信息，请参阅[常见问题](#faq)。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2.创建虚拟网络

创建资源组。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

创建虚拟网络。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3.添加网关子网

网关子网包含虚拟网络网关服务使用的保留 IP 地址。 运行下面的示例，以添加并设置网关子网：

添加网关子网。

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

设置虚拟网络的网关子网配置。

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4.请求公共 IP 地址
 
在这一步中，选择适用于要创建的网关的说明。 选择用于部署网关的区域取决于为公共 IP 地址指定的区域。

### <a name="ipzoneredundant"></a>对于区域冗余网关

使用标准 PublicIpaddress SKU 请求获取公共 IP 地址，但不指定任何区域。 在这种情况下，创建的标准公共 IP 地址是区域冗余公共 IP。   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>对于区块网关

使用标准 PublicIpaddress SKU 请求获取公共 IP 地址。 指定区域（1、2 或 3）。 所有网关实例都会部署在此区域中。

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>对于区域网关

使用基本 PublicIpaddress SKU 请求获取公共 IP 地址。 在这种情况下，网关部署为区域网关，并且不会内置有任何区域冗余。 网关实例分别会在任意区域中创建。

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5.创建 IP 配置

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6.创建网关

创建虚拟网络网关。

### <a name="for-expressroute"></a>对于 ExpressRoute 网关

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>对于 VPN 网关

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>常见问题解答

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>部署这些新 SKU 时会发生什么变化？

从你的角度来看，可以部署区域冗余网关。 也就是说，所有网关实例都会跨 Azure 可用性区域部署，每个可用性区域都是不同的容错域和更新域。 这样可以提高网关的可靠性、可用性和区域故障复原性。

### <a name="can-i-use-the-azure-portal"></a>是否可以使用 Azure 门户？

是的，可以使用 Azure 门户部署新 SKU。 但是，你将仅在具有 Azure 可用性区域的 Azure 区域中看到这些新 SKU。

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>我可以在哪些区域中使用新 SKU？

请参阅[可用性区域](../availability-zones/az-overview.md#services-support-by-region)有关可用区域的最新列表。

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>我能否将现有虚拟网络网关更改/迁移/升级为区域冗余网关或区域网关？

暂不支持将现有虚拟网络网关迁移到区域冗余网关或区块网关。 不过，可以删除现有网关，并重新创建区域冗余网关或区块网关。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>我能否在同一虚拟网络中同时部署 VPN 网关和 ExpressRoute 网关？

支持 VPN 网关和 ExpressRoute 网关同时共存于同一虚拟网络中。 但是，应为网关子网保留 /27 IP 地址范围。
