---
title: Azure ExpressRoute：重置线路对等互连
description: 如何禁用和启用 ExpressRoute 线路的对等互连。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b14b8a9a2bc43c33095bf07e7fb7ebcc2d6c1ffa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769517"
---
# <a name="reset-expressroute-circuit-peerings"></a>重置 ExpressRoute 线路对等互连

本文介绍如何使用 PowerShell 禁用和启用 ExpressRoute 线路的对等互连。 禁用对等互连后，ExpressRoute 线路的主连接和辅助连接上的 BGP 会话都将关闭。 你将无法通过此对等互连连接到 Microsoft。 启用对等互连后，ExpressRoute 线路的主连接和辅助连接上的 BGP 会话都将启动。 你将能够通过此对等互连再次连接到 Microsoft。 可单独对 ExpressRoute 线路启用和禁用 Microsoft 对等互连和 Azure 专用对等互连。 在 ExpressRoute 线路上初次配置对等互连时，将默认启用对等互连。

存在几个有助于重置 ExpressRoute 对等互连的方案。
* 测试灾难恢复设计和实现。 例如，你有两条 ExpressRoute 线路。 可以禁用一条线路的对等互连，并强制网络流量故障转移到另一条线路。
* 对 ExpressRoute 线路的 Azure 专用对等互连启用双向转发检测 (BFD)。 如果 ExpressRoute 线路在 2018 年 8 月 1 日之后创建，将默认启用 BFD。 如果线路在此之前创建，则未启用 BFD。 可以通过禁用对等互连并重新启用它来启用 BFD。 应注意，BFD 仅在 Azure 专用对等互连上受支持。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>重置对等互连

1. 如果在本地运行 PowerShell，请使用提升的权限打开 PowerShell 控制台，然后连接到帐户。 使用下面的示例来帮助连接：

   ```azurepowershell
   Connect-AzAccount
   ```
2. 如果有多个 Azure 订阅，请查看该帐户的订阅。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. 指定要使用的订阅。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. 运行以下命令，检索 ExpressRoute 线路。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. 标识要禁用或启用的对等互连。 对等互连是一个数组。 在以下示例中，Peerings[0] 是 Azure 专用对等互连，而 Peerings[1] 是 Microsoft 对等互连。

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. 运行以下命令以更改对等互连状态。

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   对等互连应处于设定的某种状态。 

## <a name="next-steps"></a>后续步骤
如果需要帮助排查 ExpressRoute 问题，请查看以下文章：
* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [网络性能故障排除](expressroute-troubleshooting-network-performance.md)
