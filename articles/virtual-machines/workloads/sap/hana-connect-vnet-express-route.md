---
title: 通过虚拟网络将连接设置为 Azure 上的 SAP HANA (大型实例) |Microsoft Docs
description: 从虚拟网络设置的连接, 用于在 Azure 上使用 SAP HANA (大型实例)。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 547640ab1a6dd948cf5d17279d784e1b4a37b35e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101238"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

创建 Azure 虚拟网络后，可将该网络连接到 Azure SAP HANA 大型实例。 在虚拟网络上创建 Azure ExpressRoute 网关。 通过此网关, 你可以将虚拟网络链接到 ExpressRoute 线路, 该线路连接到 HANA 大型实例标记上的客户租户。

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure 虚拟网络。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果它不是 ExpressRoute 网关, 请删除该网关, 并将其重新创建为 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请参阅本文的以下部分“链接虚拟网络”。 

- 使用 [Azure 门户](https://portal.azure.com/)或 PowerShell 创建与虚拟网络连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型。
  - 如果使用 PowerShell，请首先下载并使用最新版 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
以下命令创建 ExpressRoute 网关。 前面带有的 _$_ 文本是用户定义的变量, 应使用特定信息进行更新。

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

Azure 虚拟网络现已包含 ExpressRoute 网关。 使用 Microsoft 提供的授权信息将 ExpressRoute 网关连接到 SAP HANA 大型实例 ExpressRoute 线路。 可以使用 Azure 门户或 PowerShell 进行连接。 PowerShell 说明如下所示。 

针对每个连接使用不同的 AuthGUID, 针对每个 ExpressRoute 网关运行以下命令。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个虚拟网络及其网关。 如果要添加另一个 Azure 虚拟网络, 则需要获取 ExpressRoute 线路的另一个 AuthID, 该线路将 HANA 大型实例连接到 Azure 中的 Microsoft。 

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
> 命令 AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass**中的最后一个参数是启用 ExpressRoute 快速路径的新参数。 此功能减少了 HANA 大型实例单元和 Azure Vm 之间的网络延迟。 此功能已在5月2019中添加。 有关更多详细信息, 请查看[SAP HANA (大型实例) 网络体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)一文。 运行命令之前, 请确保运行最新版本的 PowerShell cmdlet。

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次运行这一步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>将 ExpressRoute 快速路径应用到现有 HANA 大型实例 ExpressRoute 线路
目前为止, 本文档介绍了如何将使用 HANA 大型实例部署创建的新 ExpressRoute 线路连接到某个 Azure 虚拟网络的 Azure ExpressRoute 网关。 但许多客户已经有了 ExpressRoute 线路设置, 并且其虚拟网络已连接到 HANA 大型实例。 由于新的 ExpressRoute 快速路径降低了网络延迟, 因此建议应用更改以使用此功能。 用于连接新 ExpreesRoute 线路和更改现有 ExpressRoute 线路的命令相同。 因此, 需要运行此 PowerShell 命令序列, 以更改要使用的现有线路 

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

务必添加如上所示的最后一个参数, 以启用 ExpressRoute 快速路径功能


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
如果要对这两种方案中的一个或两个方案启用 Global Reach:

 - HANA 系统复制, 无需任何其他代理或防火墙
 - 在两个不同的区域中的 HANA 大型实例单元之间复制备份, 以执行系统副本或系统刷新

需要考虑以下事项:

- 需要提供一个/29 地址空间的地址空间范围。 该地址范围不能与你目前在将 HANA 大型实例连接到 Azure 时所使用的任何其他地址空间范围重叠, 并且可能不会与 Azure 或本地的其他任何位置使用的任何 IP 地址范围重叠。
- Asn (自治系统编号) 存在限制, 可用于将本地路由播发到 HANA 大型实例。 在65000–65020或65515范围内, 你的本地不能播发具有 private Asn 的任何路由。 
- 对于将本地直接访问连接到 HANA 大型实例的方案, 需要为连接到 Azure 的线路计算费用。 对于价格, 请查看[Global Reach 外接程序](https://azure.microsoft.com/pricing/details/expressroute/)的价格。

若要获取应用于部署的一个或两个方案, 请使用 Azure 打开支持消息, 如为[HANA 大型实例提供支持请求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)中所述。

所需的数据和需要用于按请求进行路由和执行的关键字, 如下所示:

- 服务：SAP HANA 大型实例
- 问题类型:配置和设置
- 问题子类型：上面未列出我的问题
- 使用者 "修改我的网络-添加 Global Reach"
- 详细信息：' 将 Global Reach 添加到 HANA 大型实例到 HANA 大型实例租户, 或将 Global Reach 添加到 hana 大型实例租户。
- HANA 大型实例到 HANA 大型实例租户的其他详细信息:需要定义两个要连接的租户所在的**Azure 区域** **, 并且**需要提交 **/29 IP 地址范围**
- 本地到 HANA 大型实例租户案例的其他详细信息:需要定义要在其中部署 HANA 大型实例租户的**Azure 区域**, 以便直接连接到该租户。 此外, 还需要提供在本地与 Azure 之间建立 ExpressRoute 线路时收到的**身份验证 GUID**和**线路对等 ID** 。 此外, 还需要命名**ASN**。 最后一个可交付结果是 ExpressRoute Global Reach 的 **/29 IP 地址范围**。

> [!NOTE]
> 如果要同时处理两个事例, 则需要提供两个不同的/29 IP 地址范围, 它们不会与目前使用的任何其他 IP 地址范围重叠。 




## <a name="next-steps"></a>后续步骤

- [HLI 的其他网络要求](hana-additional-network-requirements.md)
