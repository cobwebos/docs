---
title: 从虚拟网络到 Azure SAP HANA（大型实例）的连接设置 | Microsoft Docs
description: 从虚拟网络进行连接设置以使用 Azure SAP HANA（大型实例）。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167575"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>将 VNet 连接到 HANA 大型实例 ExpressRoute

定义所有 IP 地址范围并从 Microsoft 获取数据后，可以开始将前面创建的 VNet 连接到 HANA 大型实例。 创建 Azure VNet 后，必须在 VNet 中创建 ExpressRoute 网关，以便将 VNet 链接到与大型实例戳记上的客户租户相连接的 ExpressRoute 线路。

> [!NOTE] 
> 此步骤可能需要长达 30 分钟才能完成，因为需要在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure VNet。

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果不是，则必须删除该网关，然后重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请转到下面的“链接 VNet”部分，因为 Azure VNet 已连接到用于建立本地连接的 ExpressRoute 线路。

- 使用（新的）[Azure 门户](https://portal.azure.com/)或 PowerShell 创建与 VNet 连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型。
  - 如果选择的是 PowerShell，请先下载并使用最新的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)，确保获得最佳体验。 以下命令创建 ExpressRoute 网关。 前面带有 $ 的文本是用户定义的变量，需要使用具体信息进行更新。

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

此示例使用了 HighPerformance 网关 SKU。 选项包括 HighPerformance 或 UltraPerformance，因为 Azure 上的 SAP HANA（大型实例）仅支持这两个网关 SKU。

> [!IMPORTANT]
> 对于 II 类 SKU 的 HANA 大型实例，必须使用 UltraPerformance 网关 SKU。

**链接 VNet**

在 Azure VNet 中创建 ExpressRoute 网关后，可以使用 Microsoft 提供的授权信息，将 ExpressRoute 网关连接到为此连接创建的“Azure 上的 SAP HANA（大型实例）”ExpressRoute 线路。 可以使用 Azure 门户或 PowerShell 执行这一步。 虽然建议使用门户，但下面介绍了 PowerShell 使用说明。 

- 请对每个连接使用不同的 AuthGUID，从而对每个 VNet 网关执行以下命令。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个 VNet 及其网关。 也就是说，如果要添加另一个 Azure VNet，需要获取将 HANA 大型实例连接到 Azure 的 ExpressRoute 线路的另一个 AuthID。 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次执行这一步。 例如，可能要将同一个 VNet 网关连接到将 VNet 连接到本地网络的 ExpressRoute 线路。

**后续步骤**

- 请参阅 [HLI 的其他网络要求](hana-additional-network-requirements.md)。