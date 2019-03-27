---
title: 从虚拟网络进行连接设置以使用 Azure SAP HANA（大型实例）| Microsoft Docs
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
ms.openlocfilehash: 071db2d9aeda2373c85ae62c47bbef175dcb7678
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483397"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

创建 Azure 虚拟网络后，可将该网络连接到 Azure SAP HANA 大型实例。 在虚拟网络上创建 Azure ExpressRoute 网关。 通过此网关，可将虚拟网络链接到与大型实例标记上的客户租户相连接的 ExpressRoute 线路。

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure 虚拟网络。

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果不是，删除网关，并重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请参阅本文的以下部分“链接虚拟网络”。 

- 使用 [Azure 门户](https://portal.azure.com/)或 PowerShell 创建与虚拟网络连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型。
  - 如果使用 PowerShell，请首先下载并使用最新版 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 以下命令创建 ExpressRoute 网关。 前面带有 $ 的文本是用户定义的变量，应该使用具体信息进行更新。

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

## <a name="link-virtual-networks"></a>链接虚拟网络

Azure 虚拟网络现已包含 ExpressRoute 网关。 使用 Microsoft 提供的授权信息，将 ExpressRoute 网关连接到“Azure SAP HANA（大型实例）”ExpressRoute 线路。 可以使用 Azure 门户或 PowerShell 进行连接。 建议使用门户，但如果想要使用 PowerShell，请参阅如下说明。 

请对每个连接使用不同的 AuthGUID，从而对每个虚拟网络网关运行以下命令。 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个虚拟网络及其网关。 如果想要添加另一个 Azure 虚拟网络，则需要获取用于将 HANA 大型实例连接到 Azure 的 ExpressRoute 线路的另一个 AuthID。 

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
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次运行这一步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="next-steps"></a>后续步骤

- [HLI 的其他网络要求](hana-additional-network-requirements.md)