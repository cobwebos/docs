---
title: 在 Azure（大型实例）上从虚拟网络连接到 SAP HANA 的连接 |微软文档
description: 从虚拟网络设置的连接，在 Azure 上使用 SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617189"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

创建 Azure 虚拟网络后，可将该网络连接到 Azure SAP HANA 大型实例。 在虚拟网络上创建 Azure ExpressRoute 网关。 此网关使您能够将虚拟网络链接到连接到 HANA 大型实例戳上的客户租户的 ExpressRoute 电路。

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure 虚拟网络。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果它不是 ExpressRoute 网关，请删除网关，并将其重新创建为 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请参阅本文的以下部分“链接虚拟网络”。 

- 使用 [Azure 门户](https://portal.azure.com/)或 PowerShell 创建与虚拟网络连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新**的虚拟网络网关**，然后选择**ExpressRoute**作为网关类型。
  - 如果使用 PowerShell，请首先下载并使用最新版 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
以下命令创建 ExpressRoute 网关。 前面带有的文本_$_ 是用户定义的变量，应使用您的特定信息进行更新。

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

Azure 虚拟网络现已包含 ExpressRoute 网关。 使用 Microsoft 提供的授权信息将 ExpressRoute 网关连接到 SAP HANA 大型实例 ExpressRoute 电路。 可以使用 Azure 门户或 PowerShell 进行连接。 PowerShell 说明如下。 

为每个快速路由网关使用不同的 AuthGUID 运行以下命令。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个虚拟网络及其网关。 如果要添加另一个 Azure 虚拟网络，则需要为 ExpressRoute 电路获取另一个 AuthID，该电路从 Microsoft 将 HANA 大型实例连接到 Azure。 

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
> 命令"新建-Az虚拟网络网关连接"中的最后一个参数**是**启用 ExpressRoute 快速路径的新参数。 一种功能，可减少 HANA 大型实例单元和 Azure VM 之间的网络延迟。 该功能于 2019 年 5 月添加。 有关详细信息，请查看文章 SAP [HANA（大型实例）网络体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)。 在运行命令之前，请确保正在运行最新版本的 PowerShell cmdlet。

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次运行这一步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>将快速路由快速路径应用于现有的 HANA 大型实例快速路由电路
到目前为止，文档说明了如何将使用 HANA 大型实例部署创建的新 ExpressRoute 电路连接到其中一个 Azure 虚拟网络的 Azure ExpressRoute 网关。 但许多客户已经设置了其 ExpressRoute 电路，并且已经将其虚拟网络连接到 HANA 大型实例。 由于新的 ExpressRoute 快速路径正在减少网络延迟，因此建议您应用更改来使用此功能。 连接新的 ExpreesRoute 电路和更改现有 ExpressRoute 电路的命令是相同的。 因此，您需要运行此系列 PowerShell 命令，以更改要使用的现有电路 

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

请务必添加上面显示的最后一个参数，以启用 ExpressRoute 快速路径功能


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
您希望为两种方案中的一种或两种方案启用全局覆盖：

 - HANA 系统复制，无需任何其他代理或防火墙
 - 在两个不同区域的 HANA 大型实例单元之间复制备份以执行系统副本或系统刷新

您需要考虑：

- 您需要提供 /29 地址空间的地址空间范围。 该地址范围可能与到目前为止用于将 HANA 大型实例连接到 Azure 的任何其他地址空间范围重叠，并且不得与您在 Azure 或本地其他地方使用的任何 IP 地址范围重叠。
- ASN（自治系统编号）有一个限制，可用于将本地路由通告给 HANA 大型实例。 您的本地不得通告任何具有 65000 + 65020 或 65515 范围内的专用 ASN 路由。 
- 对于连接本地直接访问 HANA 大型实例的情况，您需要计算将您连接到 Azure 的电路的费用。 有关价格，请查看[全局覆盖附加组件](https://azure.microsoft.com/pricing/details/expressroute/)的价格。

要将其中一个或两个方案应用于部署，请打开 Azure 的支持消息，如打开[HANA 大型实例的支持请求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)中所述

需要的数据以及 Microsoft 需要使用的关键字才能根据您的请求进行路由和执行，如下所示：

- 服务：SAP HANA 大型实例
- 问题类型：配置和设置
- 问题子类型：上面未列出我的问题
- 主题"修改我的网络 - 添加全球覆盖"
- 详细信息："将全局覆盖到 HANA 大型实例添加到 HANA 大型实例租户"或"将全局覆盖添加到本地到 HANA 大型实例租户"。
- HANA 大型实例到 HANA 大型实例租户案例的其他详细信息：您需要定义两个要连接的租户所在的**Azure 区域****，** 并且需要提交 **/29 IP 地址范围**
- 本地到 HANA 大型实例租户案例的其他详细信息：您需要定义要直接连接到的 HANA 大型实例租户部署的**Azure 区域**。 此外，您需要提供在本地和 Azure 之间建立 ExpressRoute 电路时收到的**Auth GUID**和**电路对等体 ID。** 此外，您需要命名**ASN**。 最后一个交付结果是 ExpressRoute 全球覆盖的 **/29 IP 地址范围**。

> [!NOTE]
> 如果要处理这两种情况，则需要提供两个不同的 /29 IP 地址范围，这些范围与迄今为止使用的任何其他 IP 地址范围不重叠。 




## <a name="next-steps"></a>后续步骤

- [HLI 的其他网络要求](hana-additional-network-requirements.md)
