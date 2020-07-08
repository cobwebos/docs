---
title: Azure ExpressRoute：验证连接 - 故障排除指南
description: 本页说明如何对 ExpressRoute 线路的端到端连接进行故障排除和验证。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 827d68a5f0f35e42acae1fa225646eb509f69c89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84729313"
---
# <a name="verifying-expressroute-connectivity"></a>验证 ExpressRoute 连接
本文可帮助验证 ExpressRoute 连接并对其进行故障排除。 ExpressRoute 可以通过往往已由连接服务提供商优化的专用连接，将本地网络扩展到 Microsoft 云中。 在传统上，ExpressRoute 连接涉及到三个不同的网络区域，如下所述：

-   客户网络
-   提供商网络
-   Microsoft 数据中心

> [!NOTE]
> 在 ExpressRoute 直接连接模型（10/100 Gbps 带宽提供）中，客户可以直接连接到 Microsoft 企业边缘（MSEE）路由器的端口。 因此，直接连接模型中只有客户和 Microsoft 网络区域。
>


本文档旨在帮助用户确定是否存在连接问题，以及出现问题的位置。 因此，本文档可为相应的团队提供支持，帮助他们解决问题。 如果需要 Microsoft 的支持才能解决问题，请向 [Microsoft 支持部门][Support]提交支持票证。

> [!IMPORTANT]
> 本文档旨在帮助用户诊断和修复简单问题。 它不是为了替代 Microsoft 支持部门。 如果无法通过本文档提供的指导解决问题，请向 [Microsoft 支持部门][Support]提交支持票证。
>
>

## <a name="overview"></a>概述
下图显示了客户网络通过 ExpressRoute 连接到 Microsoft 网络时的逻辑连接。
[![1]][1]

在上图中，数字表示关键网络点。 本文中不时提到的“网络点”将按其关联的编号来表示。 网络点 3 和 4 可以是交换机（第 2 层设备）或路由器（第 3 层设备），具体取决于 ExpressRoute 连接模型 -- 云交换归置、点到点以太网连接，或任意两点之间的连接 (IPVPN)。 直接连接模型中没有网络点 3 和 4；CE (2) 通过暗光纤直接连接到 MSEE。 上述关键网络点详述如下：

1.  客户计算设备（例如服务器或电脑）
2.  CE：客户边缘路由器 
3.  PE（面向 CE）：提供商边缘路由器/交换机，面向客户边缘路由器。 本文档中称为“PE-CE”。
4.  PE（面向 MSEE）：提供商边缘路由器/交换机，面向 MSEE。 本文档中称为“PE-MSEE”。
5.  MSEE：Microsoft 企业边缘 (MSEE) ExpressRoute 路由器
6.  虚拟网络 (VNet) 网关
7.  Azure VNet 上的计算设备

如果使用云交换归置、点到点以太网或直接连接模型，则 CE (2) 将与 MSEE (5) 建立 BGP 对等互连。 

如果使用任意两点之间的连接 (IPVPN) 模型，则 PE-MSEE (4) 将与 MSEE (5) 建立 BGP 对等互连。 PE-MSEE 通过 IPVPN 服务提供商网络将从 Microsoft 收到的路由传播回到客户网络。

> [!NOTE]
>为了实现高可用性，Microsoft 将在 MSEE (5) 和 PE-MSEE (4) 对之间建立完全冗余的并行连接。 另外，我们建议在客户网络和 PE-CE 对之间建立完全冗余的并行网络路径。 有关高可用性的详细信息，请参阅[使用 ExpressRoute 进行高可用性设计][HA]一文
>
>

下面是排查 ExpressRoute 线路问题的逻辑步骤：

* [验证线路预配和状态](#verify-circuit-provisioning-and-state)
  
* [验证对等互连配置](#validate-peering-configuration)
  
* [验证 ARP](#validate-arp)
  
* [验证 BGP 以及 MSEE 上的路由](#validate-bgp-and-routes-on-the-msee)
  
* [确认流量流](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>验证线路预配和状态
预配 ExpressRoute 线路可在 CE/PE-MSEE (2)/(4) 与 MSEE (5) 之间建立冗余的第 2 层连接。 若要详细了解如何创建、修改、预配和验证 ExpressRoute 线路，请参阅[创建和修改 ExpressRoute 线路][CreateCircuit]一文。

>[!TIP]
>服务密钥可以唯一地标识 ExpressRoute 线路。 如果需要 Microsoft 或 ExpressRoute 合作伙伴的帮助来排查 ExpressRoute 问题，请提供服务密钥以识别线路。
>
>

### <a name="verification-via-the-azure-portal"></a>通过 Azure 门户进行验证
在 Azure 门户中打开 ExpressRoute 线路边栏选项卡。 该边栏选项卡的“3”部分列出了 ExpressRoute 概要，如以下屏幕截图所示：![][3]

![4][4]    

在 ExpressRoute 的“概要”中，“线路状态”表示 Microsoft 这一侧线路的状态  。 “提供商状态”表示线路在服务提供商这一侧的状态是“已预配”还是“未预配”   。 

若要确保 ExpressRoute 线路正常运行，“线路状态”必须为“已启用”，“提供商状态”必须为“已预配”     。

> [!NOTE]
> 配置 ExpressRoute 线路后，如果“线路状态”停滞在“未启用”状态，请联系 [Microsoft 支持部门][Support]。  另一方面，如果“提供商状态”停滞在“未预配”状态，请联系服务提供商。 
>
>

### <a name="verification-via-powershell"></a>通过 PowerShell 进行验证
若要列出资源组中的所有 ExpressRoute 线路，请使用以下命令：

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>查找资源组的名称时，可以使用命令 *Get-AzResourceGroup* 列出订阅中的所有资源组，然后即可获取该名称。
>


若要选择资源组中的特定 ExpressRoute 线路，请使用以下命令：

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

示例响应如下：

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

若要确认 ExpressRoute 线路是否正常运行，请特别注意以下字段：

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> 配置 ExpressRoute 线路后，如果“线路状态”停滞在“未启用”状态，请联系 [Microsoft 支持部门][Support]。  另一方面，如果“提供商状态”停滞在“未预配”状态，请联系服务提供商。 
>
>

## <a name="validate-peering-configuration"></a>验证对等互连配置
在服务提供商完成 ExpressRoute 线路的预配后，可以通过 CE/MSEE-PE (2)/(4) 与 MSEE (5) 之间的 ExpressRoute 线路创建多个基于 eBGP 的路由配置。 每条 ExpressRoute 线路可以使用：Azure 专用对等互连（将流量发送到 Azure 中的专用虚拟网络），和/或 Microsoft 对等互连（将流量发送到 PaaS 和 SaaS 的公共终结点）。 有关如何创建和修改路由配置的详细信息，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]一文。

### <a name="verification-via-the-azure-portal"></a>通过 Azure 门户进行验证

> [!NOTE]
> 在 IPVPN 连接模型中，服务提供商将负责处理对等互连（第 3 层服务）的配置。 在此类模型中，如果在服务提供商配置对等互连后，对等互连在门户中是空白的，请尝试使用门户上的刷新按钮刷新线路配置。 此操作会从线路中提取当前路由配置。 
>

可以在 Azure 门户中的 ExpressRoute 线路边栏选项卡下检查 ExpressRoute 线路对等互连的状态。 该边栏选项卡的“3”部分将会列出 ExpressRoute 对等互连，如以下屏幕截图所示：![][3]

![5][5]

在前面的示例中，可以注意到已预配 Azure 专用对等互连，但尚未预配 Azure 公共对等互连和 Microsoft 对等互连。 成功预配的对等互连上下文还会列出主要和辅助的点到点子网。 /30 子网用于 MSEE 和 CE/PE-MSEE 的接口 IP 地址。 对于已预配的对等互连，列表中还会指示上次修改配置的用户。 

> [!NOTE]
> 如果启用对等互连失败，请检查分配的主要子网和辅助子网是否与链接的 CE/PE-MSEE 上的配置相匹配。 另请检查是否在 MSEE 上使用了正确的 *VlanId*、*AzureASN* 和 *PeerASN*，以及这些值是否映射到链接的 CE/PE-MSEE 上使用的对应项。 如果选择了 MD5 哈希，则 MSEE 和 PE-MSEE/CE 对上的共享密钥应相同。 出于安全原因，不会显示以前配置的共享密钥。 如果需要在 MSEE 路由器上更改其中的任何配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]。  
>

> [!NOTE]
> 在为接口分配的 /30 子网中，Microsoft 将为 MSEE 接口选择该子网的第二个可用 IP 地址。 因此，请确保已在对等互连的 CE/PE-MSEE 上分配该子网的第一个可用 IP 地址。
>


### <a name="verification-via-powershell"></a>通过 PowerShell 进行验证
若要获取 Azure 专用对等互连配置详细信息，请使用以下命令：

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

已成功配置的专用对等互连的示例响应如下：

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 成功启用的对等互连上下文会列出主要的和辅助的地址前缀。 /30 子网用于 MSEE 和 CE/PE-MSEE 的接口 IP 地址。

若要获取 Azure 公共对等互连配置详细信息，请使用以下命令：

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

若要获取 Microsoft 对等互连配置详细信息，请使用以下命令：

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

如果未配置对等互连，则会出现错误消息。 当所述对等互连（本示例中为 Azure 公共对等互连）未在线路中配置时，示例的响应如下：

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> 如果启用对等互连失败，请检查分配的主要子网和辅助子网是否与链接的 CE/PE-MSEE 上的配置相匹配。 另请检查是否在 MSEE 上使用了正确的 *VlanId*、*AzureASN* 和 *PeerASN*，以及这些值是否映射到链接的 CE/PE-MSEE 上使用的对应项。 如果选择了 MD5 哈希，则 MSEE 和 PE-MSEE/CE 对上的共享密钥应相同。 出于安全原因，不会显示以前配置的共享密钥。 如果需要在 MSEE 路由器上更改其中的任何配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]。  
>
>

> [!NOTE]
> 在为接口分配的 /30 子网中，Microsoft 将为 MSEE 接口选择该子网的第二个可用 IP 地址。 因此，请确保已在对等互连的 CE/PE-MSEE 上分配该子网的第一个可用 IP 地址。
>

## <a name="validate-arp"></a>验证 ARP

ARP 表为特定的对等互连提供 IP 地址和 MAC 地址的映射。 用于 ExpressRoute 线路对等互连的 ARP 表为每个接口（主接口和辅助接口）提供以下信息：
* 将本地路由器接口 IP 地址映射到 MAC 地址
* 将 ExpressRoute 路由器接口 IP 地址映射到 MAC 地址
* 映射 ARP 表的期限可帮助验证第 2 层配置，并可帮助排查第 2 层基本连接的问题。


请参阅[在资源管理器部署模型中获取 ARP 表][ARP]文档，了解如何查看 ExpressRoute 对等互连的 ARP 表，以及如何使用该信息排查第 2 层连接问题。


## <a name="validate-bgp-and-routes-on-the-msee"></a>验证 BGP 以及 MSEE 上的路由

对于“专用”路由上下文，若要获取“主要”路径上的 MSEE 的路由表，请使用以下命令：  

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

示例响应如下：

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> 如果 MSEE 与 CE/PE-MSEE 之间的 eBGP 对等互连状态为“活动”或“空闲”，请检查分配的主要和辅助对等子网是否与链接的 CE/PE-MSEE 上的配置相匹配。 另请检查是否在 MSEE 上使用了正确的 *VlanId*、*AzureAsn* 和 *PeerAsn*，以及这些值是否映射到链接的 PE-MSEE/CE 上使用的对应项。 如果选择了 MD5 哈希，则 MSEE 和 CE/PE-MSEE 对上的共享密钥应相同。 如果需要在 MSEE 路由器上更改其中的任何配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]。
>


> [!NOTE]
> 如果无法通过某个对等互连访问某些目标，请检查相应对等互连上下文的 MSEE 的路由表。 如果路由表中存在匹配的前缀（可能是 NAT 处理后的 IP），请检查路径中是否存在阻止流量的防火墙/NSG/ACL。
>


以下示例显示某个对等互连的命令响应不存在：

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>确认流量流
若要获取对等互连上下文在主要路径和辅助路径上的综合流量统计信息（出入字节数），请使用以下命令：

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

该命令的示例输出如下：

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

对于不存在的对等互连，该命令的示例输出如下：

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>后续步骤
有关详细信息或帮助，请查看以下链接：

- [Microsoft 支持部门][Support]
- [创建和修改 ExpressRoute 线路][CreateCircuit]
- [创建和修改 ExpressRoute 线路的路由][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "逻辑 ExpressRoute 连接"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "所有资源图标"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "概述图标"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute 概要示例屏幕截图"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute 概要示例屏幕截图"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





