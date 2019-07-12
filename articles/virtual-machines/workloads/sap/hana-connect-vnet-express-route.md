---
title: 连接到 Azure （大型实例） 上的 SAP HANA 设置从虚拟网络 |Microsoft Docs
description: 设置从虚拟网络在 Azure （大型实例） 上使用 SAP HANA 的连接。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebe303b24c497fe8ac52ac90a236a23c279ea2e9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709671"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

创建 Azure 虚拟网络后，可将该网络连接到 Azure SAP HANA 大型实例。 在虚拟网络上创建 Azure ExpressRoute 网关。 此网关，可将虚拟网络链接到 ExpressRoute 线路，连接到 HANA 大型实例模具上的客户租户。

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure 虚拟网络。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果它不是 ExpressRoute 网关，删除网关，并重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请参阅本文的以下部分“链接虚拟网络”。 

- 使用 [Azure 门户](https://portal.azure.com/)或 PowerShell 创建与虚拟网络连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型   。
  - 如果使用 PowerShell，请首先下载并使用最新版 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
以下命令创建 ExpressRoute 网关。 文本前面 _$_ 是用户定义的变量，应使用您的特定信息更新。

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

此示例使用了 HighPerformance 网关 SKU。 选项包括 HighPerformance 或 UltraPerformance，因为 Azure 上的 SAP HANA（大型实例）仅支持这两个网关 SKU。

> [!IMPORTANT]
> 对于 II 类 SKU 的 HANA 大型实例，必须使用 UltraPerformance 网关 SKU。

## <a name="link-virtual-networks"></a>链接虚拟网络

Azure 虚拟网络现已包含 ExpressRoute 网关。 使用由 Microsoft 提供的授权信息连接到 SAP HANA 大型实例 ExpressRoute 线路的 ExpressRoute 网关。 可以使用 Azure 门户或 PowerShell 进行连接。 PowerShell 说明如下所示。 

为每个连接使用不同 AuthGUID 运行以下命令为每个 ExpressRoute 网关。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个虚拟网络及其网关。 如果你想要添加另一个 Azure 虚拟网络，需要为 ExpressRoute 线路连接到 Azure 中的 HANA 大型实例从 Microsoft 获取另一个 AuthID。 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> 最后一个参数在命令中新建-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass**是启用 ExpressRoute 快速路径的新参数。 减少了 HANA 大型实例单元与 Azure Vm 之间的网络延迟功能。 在 2019 年 5 中添加功能。 有关更多详细信息，查看文章[SAP HANA （大型实例） 网络体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)。 请确保在运行命令之前运行 PowerShell cmdlet 的最新版本。

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次运行这一步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>将 ExpressRoute 快速路径应用于现有 HANA 大型实例 ExpressRoute 线路
到目前为止的文档介绍了如何连接与 HANA 大型实例部署到 Azure ExpressRoute 网关的其中一个 Azure 虚拟网络创建的新 ExpressRoute 线路。 但很多客户已将其设置 ExpressRoute 线路已经和其虚拟网络连接到 HANA 大型实例已。 新 ExpressRoute 快速路径是减少网络延迟，建议应用使用此功能的更改。 连接新的 ExpreesRoute 线路并将更改现有的 ExpressRoute 线路的命令是相同的。 因此您需要运行 PowerShell 命令将更改为现有线路，若要使用此序列 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

务必按照上面的显示以启用 ExpressRoute 快速路径功能添加的最后一个参数


## <a name="expressroute-global-reach"></a>ExpressRoute 的全球性覆盖
如你想要启用全局访问的一个或两个两种方案：

 - HANA 系统复制，而无需任何其他代理或防火墙
 - 在两个不同区域中 HANA 大型实例单元，以执行系统副本或系统刷新之间的复制备份

您需要考虑：

- 您需要提供/29 的地址空间范围地址空间。 Azure 或本地中的其他某个位置的地址范围不能重叠的任何其他地址空间范围，使用到目前为止将 HANA 大型实例连接到 Azure 并不能重叠与任何 IP 地址范围与您的使用。
- 没有可用于你的本地路由播发到 HANA 大型实例的 Asn （自治系统编号） 上的限制。 使用专用 Asn 65000 – 65020 范围内的任何路由或 65515，必须不播发在本地。 
- 对于在本地直接访问连接到 HANA 大型实例的方案，您需要计算线路连接到 Azure 的费用。 有关价格，请为价格[全局访问的外接程序](https://azure.microsoft.com/pricing/details/expressroute/)。

若要获取一个或两个应用于你的部署方案，请打开支持消息与 Azure 中所述[为 HANA 大型实例打开支持请求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

所需的数据和关键字，您需要使用 Microsoft 能够路由和执行您的请求，如下所示：

- 服务：SAP HANA 大型实例
- 问题类型：配置和设置
- 问题子类型：上面未列出我的问题
- 使用者修改添加全球覆盖我的网络-
- 详细信息：将添加全球覆盖到 HANA 大型实例到 HANA 大型实例租户或添加全球覆盖以在本地与 HANA 大型实例租户。
- 为 HANA 大型实例租户的大小写的 HANA 大型实例的其他详细信息：您需要定义**两个 Azure 区域**来连接两个租户位于何处**AND**需要提交  **/29 IP 地址范围**
- 在本地与 HANA 大型实例租户情况下的其他详细信息：您需要定义**Azure 区域**部署 HANA 大型实例租户的位置，你想要直接连接。 此外还需要提供**身份验证 GUID**并**线路对等 ID**接收时建立的本地与 Azure 之间的 ExpressRoute 线路。 另外，您需要重命名你**ASN**。 最后一个可交付结果是 **/29 IP 地址范围**的 ExpressRoute 全球覆盖。

> [!NOTE]
> 如果你想要具有处理两种情况下，需要提供两个不同/29 IP 地址范围不重叠的与其他任何 IP 地址范围使用到目前为止。 




## <a name="next-steps"></a>后续步骤

- [HLI 的其他网络要求](hana-additional-network-requirements.md)
