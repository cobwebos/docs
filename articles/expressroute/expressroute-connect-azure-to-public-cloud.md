---
title: 将 Azure 连接到公有云 | Microsoft Docs
description: 介绍如何通过各种方式将 Azure 连接到其他公有云
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889629"
---
# <a name="connecting-azure-with-public-clouds"></a>将 Azure 与公有云进行连接

许多企业因业务目标和技术目标而追求一种多云的策略。 其中包括成本、灵活性、功能可用性、冗余、数据主权等。此策略可帮助他们充分利用这两个云。 

此方法也对企业提出了网络和应用程序体系结构方面的挑战。 这些挑战包括延迟和数据吞吐量。 为了应对这些挑战，客户希望能够直接连接到多个云。 一些服务提供商提供了一种为客户连接多个云提供商的解决方案。 在其他情况下，客户可以通过部署自己的路由器来连接多个公有云。
## <a name="connectivity-via-expressroute"></a>通过 ExpressRoute 进行连接
客户可以使用 ExpressRoute，通过某个连接服务提供商提供的专用连接将本地网络扩展到 Microsoft 云。 客户可以使用 ExpressRoute 与 Microsoft 云服务建立连接。

可以使用三种方式通过 ExpressRoute 进行连接。

1. Layer3 提供商
2. Layer2 提供商
3. 直接连接

### <a name="layer3-provider"></a>Layer3 提供商

Layer3 提供商通常称为 IP VPN 或 MPLS VPN 提供商。 客户利用这些提供商在其数据中心、分支和云之间进行多点连接。 客户通过 BGP 或静态默认路由连接到 L3 提供商。 服务提供商在客户站点、事件中心和公有云之间播发路由。 
 
通过 Layer3 提供商进行连接时，Microsoft 会通过 BGP 将客户 VNET 路由播发给服务提供商。 提供商可以进行两种不同的实现。

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

提供商可能会将每个云提供商置于单独的 VRF 中，前提是来自所有云提供商的流量会到达客户路由器。 如果客户通过服务提供商运行 BGP，则默认情况下，这些路由会重新播发到其他云提供商。 

如果服务提供商将所有云提供商置于同一 VRF 中，则会将路由从服务提供商处直接播发到其他云提供商。 这将采用标准的 BGP 操作，其中的 eBGP 路由默认播发到其他 eBGP 邻居。

每个公有云有不同的前缀限制，因此服务提供商在分发路由时应谨慎。

### <a name="layer2-provider-and-direct-connection"></a>Layer2 提供商和直接连接

虽然两种模型中的物理连接不同，但在 layer3 中，BGP 是在 MSEE 和客户路由器之间直接建立的。 就 ExpressRoute Direct 来说，客户直接连接到 MSEE。 而在使用 Layer2 时，服务提供商将 VLAN 从客户本地扩展到云。 客户在 layer2 网络之上运行 BGP，将其 DC 连接到云。
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
在两种情况下，客户将通过点到点方式连接到每个公有云。 客户会建立单独的 BGP 连接来连接到每个公有云。 默认情况下，一个云提供商接收的路由会播发到其他云提供商。 每个云提供商都有不同的前缀限制，因此客户在播发路由时应注意这些限制。 客户可以在播发其他公有云提供的路由时，与 Microsoft 一起使用常规 BGP 设置。

## <a name="direct-connection-with-expressroute"></a>通过 ExpressRoute 进行的直接连接

客户可以选择将 ExpressRoute 直接连接到云提供商的直接连接产品/服务。 两个云提供商将进行背靠背连接，并会在其路由器之间直接建立 BGP。 目前，此类型的连接可以通过 Oracle 获得。

## <a name="site-to-site-vpn"></a>站点到站点 VPN

客户可以利用 Internet 将其在 Azure 中的实例与其他公有云连接。 几乎所有云提供商都提供站点到站点 VPN 功能。 但是，由于缺少某些变体，可能会存在不兼容现象。 例如，某些云提供商仅支持 IKEv1，因此在该云中，必须有一个 VPN 终止终结点。 对于那些支持 IKEv2 的云提供商，可以在两个云提供商的 VPN 网关之间建立直接隧道。

站点到站点 VPN 不属于具有高吞吐量和低延迟特性的解决方案。 但是，可以将它用作物理连接的备份。

## <a name="next-steps"></a>后续步骤
如果遇到 ExpressRoute 和虚拟网络连接的其他任何问题，请参阅 [ExpressRoute 常见问题解答][ER-FAQ]。

请参阅在 Azure 和 oracle[云之间设置直接连接][ER-OCI]以连接 Azure 和 oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



