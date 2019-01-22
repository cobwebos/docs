---
title: Azure ExpressRoute Global Reach 应用场景 | Microsoft Docs
description: 本页提供 Global Reach 的应用场景。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333020"
---
# <a name="expressroute-global-reach-application-scenario"></a>ExpressRoute Global Reach 应用场景

若要详细了解 ExpressRoute Global Reach，请参阅 [ExpressRoute Global Reach][Global Reach]。 本文逐步讲解一个应用场景、将 ExpressRoute Global Reach 解决方案与其他几种解决方案进行比较、为示例场景配置 Global Reach，然后验证连接。 

##<a name="application-scenario"></a>应用场景

Fabrikam Inc. 的实际经营场所众多，并在美国东部部署了 Azure。 Fabrikam 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。 Contoso Ltd. 也有一定数量的经营场所，并在美国西部部署了 Azure。 Contoso 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。  

Fabrikam Inc. 并购了 Contoso ltd.并购后，Fabrikam 希望建立网络互连。 下图演示了该场景：

 [![1]][1]

上图中间的虚线箭头表示所需的网络互连。 下表显示了并购之前，Contoso Ltd. 的 ExpressRoute 主要专用对等互连的路由表。

[![2]][2]

下表显示了并购之前，Fabrikam Inc. 的 ExpressRoute 主要专用对等互连的路由表。

[![3]][3]

## <a name="traditional-solutions"></a>传统解决方案

### <a name="option-1-interconnect-on-premises-networks"></a>选项 1：与本地网络互连

下图演示了此选项。 如图所示，可以使用站点到站点 VPN 或其他宽带连接选项来互连两个本地网络，并管理两个实体之间的所有路由。 

[![4]][4]

此选项具有以下缺点：

- 对于部署，需要强制通过欠佳的路由进行区域间的 Azure 通信。
- 无法享受 Microsoft 主干网络为区域间通信提供的高可用性优势。
- 客户需要为区域间通信承担所有路由责任。

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>选项 2：ExpressRoute 交叉连接和本地网络互连

下图演示了此选项。

[![5]][5]

如上图所示，可以在 ExpressRoute 线路与跨区域 VNet 之间额外建立连接。 VNet 与 ExpressRoute 线路之间的跨区域连接可实现以下通信：

- 美国西部/美国东部 VNet 可分别与 Fabrikam/Contoso 本地网络通信。
- 美国西部 VNet 可与美国东部 VNet 通信。

要使本地网络彼此通信，仍需在两个本地网络之间建立互连。

此选项可让专用部署通过 Microsoft 主干网络进行区域间的 Azure 通信，并通过外部网络在本地网络之间进行通信。 但是，该解决方案的主要缺点是需要建立多个跨区域连接，使维护和故障排除变得复杂。 此外，该选项不允许利用高可用性 Microsoft 全球主干网络在两个本地网络之间进行通信。

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>选项 3：VNet 对等互连和本地网络互连

下图演示了此选项。 该选项使用 VNet 对等互连进行区域间 VNet 通信。 如图所示，该选项是不完整的，因为它不能解决跨区域 VNet 与本地通信的需求（中间两条带箭头的虚线指示了这一点）。 使用本地到本地连接（如“选项 1”中所述）或 ExpressRoute 交叉连接（如“选项 2”中所述）在跨区域 VNet 与本地之间进行通信。

[![6]][6]

此选项确实能够为区域间 VNet 通信提供最佳路由。 但是，如果 Fabrikam 或 Contoso 采用较复杂的 Azure 部署（例如中心辐射 VNet 模型），则此选项就存在弱点。 此外，与前两个选项一样，此选项不允许利用高可用性 Microsoft 全球主干网络在两个本地网络之间进行通信。

## <a name="global-reach"></a>Global Reach

ExpressRoute Global Reach 可以链接 ExpressRoute 线路的专用对等互连，如下图所示：

[![7]][7]

在两条 ExpressRoute 线路之间配置 Global Reach 可在两个本地网络与 Azure 部署（在两个区域中）之间实现专用通信。 因此，Global Reach 能够满足通过 Microsoft 主干网络实现各种通信的需求（本文的第一张插图中的虚线指示了这一点）。

### <a name="configure-global-reach"></a>配置 Global Reach

若要了解如何配置 ExpressRoute Global Reach，请参阅[配置 Global Reach][Configure Global Reach]。 

由于 Fabrikam Inc. 和 Contoso Ltd. 以独立公司的形式采购了 Azure，这两家公司的 ExpressRoute 线路位于两个不同的 Azure 订阅中。 若要创建跨订阅的 Global Reach，需要在属于 Contoso Ltd. 的 ExpressRoute 线路中创建授权，并将该授权传递给 Fabrikam Inc.。ExpressRoute 线路。


若要为 Contoso 的 ExpressRoute 线路创建授权，请先登录到 Contoso 的 Azure 帐户，并选择相应的订阅（如果有多个订阅）。 这些步骤的 PowerShell 命令为：

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
下面列出了创建 ExpressRoute 线路授权的步骤：

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

“Set-AzureRmExpressRouteCircuit”的输出将列出 ExpressRouteCircuit。 请记下列表末尾处列出的专用对等互连 ID 和授权密钥。 参阅以下示例 PowerShell 输出片段：

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

使用该对等互连 ID 和授权密钥可以在 Fabrikam 的 ExpressRoute 线路中创建 Global Reach。 登录到 Fabrikam 的 Azure 帐户。 如果有多个订阅，请选择相应的订阅。

Global Reach 将在两个 MSEE 对之间创建一组冗余的点到点连接。 对于两个点到点连接，需要指定 /29 地址前缀（对于正在运行的示例，让我们使用 192.168.11.64/29）。 使用以下命令创建 Global Reach 连接：

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

执行上述命令后，花费几分钟创建冗余的 Global Reach 连接。

### <a name="verify-global-reach"></a>验证 Global Reach

下表显示了配置 Global Reach 之后，Contoso Ltd. 的 ExpressRoute 主要专用对等互连的路由表。

[![8]][8]

下表显示了配置 Global Reach 之后，Fabrikam Inc. 的 ExpressRoute 主要专用对等互连的路由表。

[![9]][9]

在上表中可以看到，列出了所有预期目标“网络”前缀和相应的“下一跃点”。

上面显示了可在 Azure 门户中 ExpressRoute 线路的“专用对等互连”下面访问的“获取路由表”边栏选项卡。 也可以使用以下 PowerShell 命令列出 ExpressRoute 路由表：

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

若要查看辅助 MSEE 的路由表，请将上述命令中的“primary”替换为关键字“secondary”。

另外，让我们通过从不同的网络 ping 不同的目标，来验证数据平面连接。 以下三个 ping 命令验证从 Fabrikam 本地网络到 Contoso 本地网络、Contoso Azure VNet 和 Fabrikam Azure VNet 的数据平面连接。


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


以下两个 ping 命令验证从 Contoso 本地网络到 Contoso Azure VNet 和 Fabrikam Azure VNet 的数据平面连接。

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

以下 ping 命令验证从 Fabrikam Azure VNet 到 Contoso Azure VNet 的数据平面连接。

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>针对延迟敏感型流量的优化

Global Reach 通过 Microsoft Edge 设备路由流量。 对于本文所述的特定场景，可以通过在两个 VNet 之间启用 VNet 对等互连，来进一步改善它们之间的路由。 同样，可以通过某家能够在站点之间提供更直接 WAN 连接的服务提供商，来进一步改善两个本地网络之间的路由。 在这种情况下，可以使用 Global Reach 路由作为这些连接的故障回复选项。 

## <a name="next-steps"></a>后续步骤

Global Reach 是按国家/地区推出的。 若要查看 Global Reach 是否在你所需的国家/地区推出，请参阅 [ExpressRoute Global Reach][Global Reach]。

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "应用场景"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "并购之前的 Contoso ExpressRoute 路由表"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "并购之前的 Fabrikam ExpressRoute 路由表"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "与本地网络互连"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "ExpressRoute 交叉连接"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet 对等互连"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "使用 Global Reach 的 Contoso ExpressRoute 路由表"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "使用 Global Reach 的 Fabrikam ExpressRoute 路由表"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





