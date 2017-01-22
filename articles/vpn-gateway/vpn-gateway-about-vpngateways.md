---
title: "关于 VPN 网关 | Microsoft Docs"
description: "了解 Azure 虚拟网络的 VPN 网关连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f74d531006a2c2cc4b12aac846c20c30317cc013


---
# <a name="about-vpn-gateway"></a>关于 VPN 网关
虚拟网络网关用于在 Azure 虚拟网络和本地位置之间以及 Azure 中的虚拟网络之间（VNet 到 VNet）发送网络流量。 配置 VPN 网关时，必须创建并配置虚拟网络网关和虚拟网络网关连接。

在 Resource Manager 部署模型中，创建虚拟网络网关资源时，可以指定多个设置。 必需的设置之一是“-GatewayType”。 有两种虚拟网络网关类型：Vpn 和 ExpressRoute。 

通过专用连接发送网络流量时，使用的网关类型是“ExpressRout”。 这也称为 ExpressRoute 网关。 通过公共连接发送加密的网络流量时，使用的网关类型是“Vpn”。 这称为 VPN 网关。 站点到站点连接、点到站点连接和 VNet 到 VNet 连接都使用 VPN 网关。

对于每种网关类型，每个虚拟网络只能有一个虚拟网络网关。 例如，可以有一个使用 -GatewayType ExpressRoute 的虚拟网络网关和一个使用 -GatewayType Vpn 的虚拟网络网关。 本文重点介绍 VPN 网关。 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="pricing"></a>定价
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>网关 SKU
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

有关用于 VPN 网关的网关 SKU 的详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

### <a name="estimated-aggregate-throughput-by-sku"></a>按 SKU 列出的估计聚合吞吐量
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>配置 VPN 网关
配置 VPN 网关时，使用的说明取决于用于创建虚拟网络的部署模型。 例如，如果使用经典部署模型创建的 VNet，则使用经典部署模型的指导原则和说明来创建及配置 VPN 网关设置。 有关部署模型的详细信息，请参阅 [了解 Resource Manager 和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。

VPN 网关连接依赖于使用特定设置配置的多个资源。 大多数资源可以单独配置，虽然在某些情况下它们必须按特定顺序配置。 开始时可以使用一个配置工具（如 Azure 门户）创建和配置资源。 然后可以决定切换到另一个工具（如 PowerShell）配置其他资源或修改现有资源（如果适用）。 目前，无法在 Azure 门户中配置每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。 有关 VPN 网关的各个资源和设置的信息，请参阅 [关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md)。

以下部分包含的表列出了：

* 可用的部署模型
* 可用的配置工具
* 直接转到某篇文章的链接（如果可用）

使用关系图和说明来帮助选择符合要求的连接拓扑。 关系图显示了主要的基准拓扑，但可以使用关系图作为准则生成更复杂的配置。

## <a name="site-to-site-and-multi-site"></a>站点到站点和多站点
### <a name="site-to-site"></a>站点到站点
站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道建立的连接。 此类型的连接要求位于本地的 VPN 设备分配有一个公共 IP 地址，并且不位于 NAT 后面。 S2S 连接可以用于跨界和混合配置。   

![S2S 连接](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")

### <a name="multi-site"></a>多站点
可以在 VNet 与多个本地网络之间创建和配置 VPN 网关连接。 使用多个连接时，必须使用 RouteBased VPN 类型（适用于经典 VNet 的动态网关）。 由于一个 VNet 只能有一个 VPN 网关，因此通过网关的所有连接都共享可用带宽。 这通常称为“多站点”连接。

![多站点连接](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>适用于站点到站点和多站点的部署模型和方法
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnet-to-vnet"></a>VNet 到 VNet
将一个虚拟网络连接到另一个虚拟网络（VNet 到 VNet）类似于将 VNet 连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道。 甚至可以将 VNet 到 VNet 通信与多站点连接配置组合使用。 这样，便可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑。

连接的 VNet 可以：

* 在相同或不同区域中
* 在相同或不同订阅中 
* 在相同或不同部署模型中

![VNet 到 VNet 连接](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")

#### <a name="connections-between-deployment-models"></a>部署模型之间的连接
Azure 当前有两种部署模型：经典模型和 Resource Manager 模型。 如果 Azure 已经使用了一段时间，则您的 Azure VM 和实例角色可能是在经典 VNet 上运行。 而较新的 VM 和角色实例可能是在 Resource Manager 中创建的 VNet 上运行。 可以在 VNet 之间创建连接，以允许一个 VNet 中的资源直接与另一个 VNet 中的资源进行通信。

#### <a name="vnet-peering"></a>VNet 对等互连
可能能够使用 VNet 对等互连来创建连接，前提是虚拟网络符合特定要求。 VNet 对等互连不使用虚拟网络网关。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>适用于 VNet 到 VNet 的部署模型和方法
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="point-to-site"></a>点到站点
点到站点 (P2S) VPN 网关连接可让用户创建从单个客户端计算机到虚拟网络的安全连接。 P2S 是基于 SSTP（安全套接字隧道协议）的 VPN 连接。 P2S 连接不需要 VPN 设备或面向公众的 IP 地址即可运行。 从客户端计算机启动 VPN 连接即可建立 VPN 连接。 如果要从远程位置（例如，从家里或会议室）连接到 VNet，或者只有几个客户端需要连接到 VNet，此解决方案将非常有用。 可以通过同一 VPN 网关将 P2S 连接与 S2S 连接结合使用，前提是这两种连接的所有配置要求都兼容。

![点到站点连接](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### <a name="deployment-models-and-methods-for-point-to-site"></a>适用于点到站点的部署模型和方法
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>ExpressRoute
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

在 ExpressRoute 连接中，虚拟网络网关配置了网关类型“ExpressRoute”，而不是“Vpn”。 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="site-to-site-and-expressroute-coexisting-connections"></a>站点到站点和 ExpressRoute 的共存连接
ExpressRoute 是从 WAN （不通过公共 Internet）到 Microsoft 服务（包括 Azure）的直接专用连接。 站点到站点 VPN 流量以加密方式通过公共 Internet 传输。 能够为同一个虚拟网络配置站点到站点 VPN 和 ExpressRoute 连接有诸多优点。

可以将站点到站点 VPN 配置为 ExpressRoute 的安全故障转移路径，或者使用站点到站点 VPN 连接到不属于你的网络但却已通过 ExpressRoute 进行连接的站点。 请注意，这要求对于同一虚拟网络使用两个虚拟网络网关，一个网关使用 -GatewayType Vpn，和另一个网关使用 -GatewayType ExpressRoute。

![共存连接](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>适用于 S2S 和 ExpressRoute 的部署模型和方法
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>后续步骤
规划 VPN 网关配置。 请参阅 [VPN 网关规划和设计](vpn-gateway-plan-design.md)和[将本地网络连接到 Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md)。




<!--HONumber=Jan17_HO1-->


