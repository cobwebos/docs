---
title: 在 Azure 可用性区域中创建区域冗余虚拟网络网关 - 预览版 | Microsoft Docs
description: 在可用性区域中部署 VPN 网关和 ExpressRoute 网关 - 预览版。
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/25/2018
ms.author: cherylmc
ms.openlocfilehash: a08c0f772965ddb2b40ac1ced1f26ade4cba3197
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017026"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>在 Azure 可用性区域中创建区域冗余虚拟网络网关 - 预览版

可以在 [Azure 可用性区域](../availability-zones/az-overview.md)中部署 VPN 网关和 ExpressRoute 网关。 这样可以提高虚拟网络网关的复原性、可伸缩性和可用性。 如果在 Azure 可用性区域中部署网关，可以在地理位置和逻辑上将区域内的网关分隔开来，同时还能保护本地网络与 Azure 的连接免受区域级故障的影响。

与常规虚拟网络网关相比，区块和区域冗余网关发生了根本性的性能提升。 此外，创建区域冗余或区块虚拟网络网关比创建其他网关更快。 创建 ExpressRoute 网关大约需要 15 分钟，创建 VPN 网关需要 19 分钟，而创建其他网关则需要 45 分钟。

### <a name="zrgw"></a>区域冗余网关

若要跨可用性区域自动部署虚拟网络网关，可以使用区域冗余虚拟网络网关。 借助区域冗余网关，可以利用 GA 的 99.99% 运行时间 SLA，访问 Azure 上可缩放的任务关键服务。

<br>
<br>

![区域冗余网关图](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>区块网关

若要在特定区域中部署网关，请使用区块网关。 部署区块网关时，网关的两个实例都会部署在同一可用性区域中。

<br>
<br>

![区块网关图](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>网关 SKU

区域冗余网关和区块网关必须使用新增的网关 SKU。 [在预览版中自注册](#enroll)后，便会在所有 Azure 可用性区域中看到新增的虚拟网络网关 SKU。 这些 SKU 与 ExpressRoute 网关和 VPN 网关的相应 SKU 类似，不同之处在于它们是区域冗余网关和区块网关的专用 SKU。

新增的网关 SKU 包括：

### <a name="vpn-gateway"></a>VPN 网关

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>公共 IP SKU

区域冗余网关和区块网关都依赖 Azure 公共 IP 资源标准 SKU。 Azure 公共 IP 资源的配置决定了是部署区域冗余网关，还是部署区块网关。 如果使用基本 SKU 创建公共 IP 资源，网关不会有任何区域冗余，且网关资源具有区域性。

### <a name="pipzrg"></a>区域冗余网关

如果使用标准公共 IP SKU 创建公共 IP 地址，但未指定区域，行为因网关是 VPN 网关还是 ExpressRoute 网关而异。 

* 对于 VPN 网关，两个网关实例会部署到这三个区域中的任意两个区域，以实现区域冗余。 
* 对于 ExpressRoute 网关，由于可以有超过两个实例，因此网关可以跨所有这三个区域部署。

### <a name="pipzg"></a>区块网关

如果使用标准公共 IP SKU 创建公共 IP 地址，并指定区域（1、2 或 3），所有网关实例都会部署到同一区域中。

### <a name="piprg"></a>区域网关

如果使用基本公共 IP SKU 创建公共 IP 地址，网关会部署为区域网关，并且不会内置有任何区域冗余。

## <a name="before-you-begin"></a>开始之前

可以使用在计算机上本地安装的 PowerShell，也可以使用 Azure Cloud Shell。 如果选择在本地安装并使用 PowerShell，必须使用最新版 PowerShell 模块，才能使用此功能。

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>在本地使用 PowerShell 的具体步骤

若要在计算机上本地使用 PowerShell（而不是使用 Cloud Shell），必须安装 PowerShell 模块 6.1.1 或更高版本。 若要检查已安装 PowerShell 的版本，请运行下面的命令：

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1.在预览版中注册

必须先在预览版中自注册订阅，然后才能配置区域冗余网关或区块网关。 预配订阅后，便会开始在所有 Azure 可用性区域中看到新增的网关 SKU。 

请确保已登录 Azure 帐户，且使用的是要为此预览版列入白名单的订阅。 运行下面的示例进行注册：

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

运行下面的命令，以验证是否已向订阅注册“AllowVMSSVirtualNetworkGateway”功能：

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

结果如下面的示例所示：

![已预配](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2.声明变量

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

## <a name="configure"></a>3.创建虚拟网络

创建资源组。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

创建虚拟网络。

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4.添加网关子网

网关子网包含虚拟网络网关服务使用的保留 IP 地址。 运行下面的示例，以添加并设置网关子网：

添加网关子网。

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

设置虚拟网络的网关子网配置。

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5.请求公共 IP 地址
 
在这一步中，选择适用于要创建的网关的说明。 选择用于部署网关的区域取决于为公共 IP 地址指定的区域。

### <a name="ipzoneredundant"></a>对于区域冗余网关

使用标准 PublicIpaddress SKU 请求获取公共 IP 地址，但不指定任何区域。 在这种情况下，创建的标准公共 IP 地址是区域冗余公共 IP。   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard
```

### <a name="ipzonalgw"></a>对于区块网关

使用标准 PublicIpaddress SKU 请求获取公共 IP 地址。 指定区域（1、2 或 3）。 所有网关实例都会部署在此区域中。

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>对于区域网关

使用基本 PublicIpaddress SKU 请求获取公共 IP 地址。 在这种情况下，网关部署为区域网关，并且不会内置有任何区域冗余。 网关实例分别会在任意区域中创建。

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6.创建 IP 配置

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7.创建网关

创建虚拟网络网关。

>[!NOTE]
>暂无法指定网关 SKU。 ExpressRoute 网关和 VPN 网关的 SKU 分别自动默认为 ErGw1AZ 和 VpnGw1AZ。
>

### <a name="for-expressroute"></a>对于 ExpressRoute 网关

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>对于 VPN 网关

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>如何提供反馈

如能提供反馈，我们将不胜感激。 若对区域冗余网关和区块网关（包括 VPN 网关和 Express Route 网关）有任何疑问或反馈（无论是正面还是负面），请发送电子邮件至 aznetworkgateways@microsoft.com 告诉我们。 请在主题行中添加你的公司名称（用“[ ]”括起来）。 若要报告问题，还请添加订阅 ID。

## <a name="faq"></a>常见问题解答

### <a name="how-do-i-sign-up-for-the-preview"></a>我如何注册申请预览版？

可以运行本文中的 PowerShell 命令进行[自注册](#enroll)。

### <a name="what-will-change-when-i-enroll"></a>我注册后会有什么变化？

从你的角度来看，在预览版期间，可以部署区域冗余网关。 也就是说，所有网关实例都会跨 Azure 可用性区域部署，每个可用性区域都是不同的容错域和更新域。 这样可以提高网关的可靠性、可用性和区域故障复原性。

### <a name="what-regions-are-available-for-the-preview"></a>预览版在哪些区域推出？

区域冗余网关和区块网关适用于生产/Azure 公共区域。

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>我加入此预览版后是否需要支付费用？

在预览版期间，网关是不计费的。 不过，部署没有附加 SLA。 我们非常期待你的反馈。

> [!NOTE]
> 虽然 ExpressRoute 网关是不计费/不收费的， 但线路本身（而不是网关）是计费的。

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>我能在哪些区域试用此预览版？

公共预览版在美国中部和法国中部区域（可用性区域普遍适用的 Azure 区域）推出。 今后，我们将在其他 Azure 公共区域推出区域冗余网关。

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>我能否将现有虚拟网络网关更改为区域冗余网关或区块网关？

暂不支持将现有虚拟网络网关迁移到区域冗余网关或区块网关。 不过，可以删除现有网关，并重新创建区域冗余网关或区块网关。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>我能否在同一虚拟网络中同时部署 VPN 网关和 ExpressRoute 网关？

公共预览版期间，支持 VPN 网关和 ExpressRoute 网关同时共存于同一虚拟网络中。 不过，请注意以下要求和限制：

* 保留网关子网的 /27 IP 地址范围。
* 区域冗余/区块 Express Route 网关只能与区域冗余/区块 VPN 网关共存。
* 区域冗余/区块 ExpressRoute 网关的部署先于区域冗余/区块 VPN 网关。
* 区域冗余/区块 ExpressRoute 网关最多可连接到 4 条线路。

## <a name="next-steps"></a>后续步骤

如能提供反馈，我们将不胜感激。 若对区域冗余网关和区块网关（包括 VPN 网关和 Express Route 网关）有任何疑问或反馈（无论是正面还是负面），请发送电子邮件至 aznetworkgateways@microsoft.com 告诉我们。 请在主题行中添加你的公司名称（用“[ ]”括起来）。 若要报告问题，还请添加订阅 ID。