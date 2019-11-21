---
title: Connectivity set up from virtual network to SAP HANA on Azure (large instances) | Microsoft Docs
description: Connectivity set up from virtual network to use SAP HANA on Azure (large instances).
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
ms.openlocfilehash: 5fbcc6ace4923d8aa2d938cd9fffe7a16c4fc1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206746"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>将虚拟网络连接到 HANA 大型实例

创建 Azure 虚拟网络后，可将该网络连接到 Azure SAP HANA 大型实例。 在虚拟网络上创建 Azure ExpressRoute 网关。 This gateway enables you to link the virtual network to the ExpressRoute circuit that connects to the customer tenant on the HANA Large Instance stamp.

> [!NOTE] 
> 完成此步骤最多需要 30 分钟。 在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure 虚拟网络。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

如果网关已存在，请检查它是否为 ExpressRoute 网关。 If it is not an ExpressRoute gateway, delete the gateway, and re-create it as an ExpressRoute gateway. 如果已建立 ExpressRoute 网关，请参阅本文的以下部分“链接虚拟网络”。 

- 使用 [Azure 门户](https://portal.azure.com/)或 PowerShell 创建与虚拟网络连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户，请添加新的“虚拟网关”，再选择“ExpressRoute”作为网关类型。
  - 如果使用 PowerShell，请首先下载并使用最新版 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)。 
 
以下命令创建 ExpressRoute 网关。 The texts preceded by a _$_ are user-defined variables that should be updated with your specific information.

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

Azure 虚拟网络现已包含 ExpressRoute 网关。 Use the authorization information provided by Microsoft to connect the ExpressRoute gateway to the SAP HANA Large Instances ExpressRoute circuit. 可以使用 Azure 门户或 PowerShell 进行连接。 The PowerShell instructions are as follows. 

Run the following commands for each ExpressRoute gateway by using a different AuthGUID for each connection. 下面脚本中的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个虚拟网络及其网关。 If you want to add another Azure virtual network, you need to get another AuthID for your ExpressRoute circuit that connects HANA large instances into Azure from Microsoft. 

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
> The last parameter in the command New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** is a new parameter that enables ExpressRoute Fast Path. A functionality that reduces network latency between your HANA Large Instance units and Azure VMs. The functionality got added in May 2019. For more details, check the article [SAP HANA (Large Instances) network architecture](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Make sure that you are running the latest version of PowerShell cmdlets before running the commands.

若要将网关连接到与订阅关联的多个 ExpressRoute 线路，建议多次运行这一步骤。 例如，可能要将同一个虚拟网络网关连接到将虚拟网络连接到本地网络的 ExpressRoute 线路。

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Applying ExpressRoute Fast Path to existing HANA Large Instance ExpressRoute circuits
The documentation so far explained how to connect a new ExpressRoute circuit that got created with a HANA Large Instance deployment to an Azure ExpressRoute gateway of one of your Azure virtual networks. But many customers already have their ExpressRoute circuits setup already and have their virtual networks connected to HANA Large Instances already. As the new ExpressRoute Fast Path is reducing network latency, it is recommended that you apply the change to use this functionality. The commands to connect a new ExpreesRoute circuit and to change an existing ExpressRoute Circuit are the same. As a result you need to run this sequence of PowerShell commands to change an existing circuit to use 

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

It is important that you add the last parameter as displayed above to enable the ExpressRoute Fast Path functionality


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
As you want to enable Global Reach for one or both of the two scenarios:

 - HANA System Replication without any additional proxies or firewalls
 - Copying backups between HANA Large Instance units in two different regions to perform system copies or system refreshes

you need consider that:

- You need to provide an address space range of a /29 address space. That address range may not overlap with any of the other address space ranges that you used so far connecting HANA Large Instances to Azure and may not overlap with any of your IP address ranges you used somewhere else in Azure or on-premises.
- There is a limitation on the ASNs (Autonomous System Number) that can be used to advertise your on-premises routes to HANA Large Instances. Your on-premises must not advertise any routes with private ASNs in the range of 65000 – 65020 or 65515. 
- For the scenario of connecting on-premises direct access to HANA Large instances, you need to calculate a fee for the circuit that connects you to Azure. For prices, check the prices for [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

To get one or both of the scenarios applied to your deployment, open a support message with Azure as described in [Open a support request for HANA large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Data that is needed and keywords that you need to use for Microsoft to be able to route and execute on your request, looks like:

- Service: SAP HANA Large Instance
- Problem type: Configuration and Setup
- Problem subtype: My problem is not listed above
- Subject 'Modify my Network - add Global Reach'
- Details: 'Add Global Reach to HANA Large Instance to HANA Large Instance tenant or 'Add Global Reach to on-premises to HANA Large Instance tenant.
- Additional details for the HANA Large Instance to HANA Large Instance tenant case: You need to define the **two Azure regions** where the two tenants to connect are located **AND** you need to submit the **/29 IP address range**
- Additional details for the on-premises to HANA Large Instance tenant case: You need to define the **Azure Region** where the HANA Large Instance tenant is deployed you want to connect to directly. Additionally you need to provide the **Auth GUID** and **Circuit Peer ID** that you received when you established your ExpressRoute circuit between on-premises and Azure. Additionally, you need to name your **ASN**. The last deliverable is a **/29 IP address range** for ExpressRoute Global Reach.

> [!NOTE]
> If you want to have both cases handled, you need to supply two different /29 IP address ranges that do not overlap with any other IP address range used so far. 




## <a name="next-steps"></a>后续步骤

- [HLI 的其他网络要求](hana-additional-network-requirements.md)
