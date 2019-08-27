---
title: 将 Azure 连接到公有云 |Microsoft Docs
description: 描述将 Azure 连接到其他公有云的各种方式
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: 681a99d31e7ededda74aa186ff36cc837125bbda
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052207"
---
# <a name="connecting-azure-with-public-clouds"></a>通过公有云连接 Azure

许多企业正在寻求多云策略, 因为业务和技术目标。 其中包括成本、灵活性、功能可用性、冗余、数据主权等。此策略可帮助他们充分利用这两个云。 

此方法还在网络和应用程序体系结构方面面临企业的挑战。 其中一些挑战是延迟和数据吞吐量。 为了解决这些难题, 客户希望直接连接到多个云。 某些服务提供商提供了一种为客户连接多个云提供商的解决方案。 在其他情况下, 客户可以部署自己的路由器来连接多个公有云。
## <a name="connectivity-via-expressroute"></a>通过 ExpressRoute 连接
ExpressRoute 允许客户通过连接服务提供商所提供的专用连接将本地网络扩展到 Microsoft 云。 通过 ExpressRoute, 客户可以与 Microsoft 云服务建立连接。

有三种方法可通过 ExpressRoute 进行连接。

1. 第3层提供程序
2. L2 提供程序
3. 直接连接

### <a name="layer3-provider"></a>第3层提供程序

第3层提供程序通常称为 IP VPN 或 MPLS VPN 提供程序。 客户对其数据中心、分支和云之间的 multipoint 连接使用这些提供程序。 客户通过 BGP 或通过静态默认路由连接到 L3 提供程序。 服务提供商在客户站点、数据中心和公有云之间公布路由。 
 
通过第3层提供程序进行连接时, Microsoft 将通过 BGP 将客户 VNET 路由到服务提供商。 提供程序可以有两个不同的实现。

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

提供商可以在单独的 VRF 中登陆每个云提供商。 如果来自所有云提供商的流量将进入客户路由器。 如果客户正在使用服务提供程序运行 BGP, 则默认情况下, 这些路由将被重新播发到其他云提供程序。 

如果服务提供商在同一 VRF 中登陆所有云提供商, 则路由将直接从服务提供商播发到其他云提供商。 这是假设标准 BGP 操作, 默认情况下, eBGP 路由将播发到其他 eBGP 邻居。

每个公有云都具有不同的前缀限制, 因此, 在分发路由服务提供程序时, 应谨慎处理路由。

### <a name="layer2-provider-and-direct-connection"></a>L2 提供程序和直接连接

尽管两个模型中的物理连接是不同的, 但在第3层 BGP 是直接在 MSEE 和客户路由器之间建立的。 对于 ExpressRoute 直接客户, 直接连接到 MSEE。 在 L2 的情况下, 服务提供商将 VLAN 从客户本地扩展到云。 客户在第2层网络顶部运行 BGP, 以将其 Dc 连接到云。
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
在这两种情况下, 客户将具有与每个公有云的点到点连接。 客户将为每个公有云建立单独的 BGP 连接。 默认情况下, 一个云提供商接收的路由将播发到其他云提供商。 每个云提供商具有不同的前缀限制, 因此, 在播发路由时, 客户应考虑这些限制。 客户可以在从其他公有云播发路由的同时, 将常规的 BGP 旋钮与 Microsoft 一起使用。

## <a name="direct-connection-with-expressroute"></a>与 ExpressRoute 的直接连接

客户可以选择将 ExpressRoute 直接连接到云提供商的直接连接产品。 两个云提供程序将连接回 back, 并将在其路由器之间直接建立 BGP。 目前, Oracle 提供此类连接。

## <a name="site-to-site-vpn"></a>站点到站点 VPN

客户可利用 Internet 将其实例与其他公有云连接在 Azure 中。 几乎所有云提供商提供了站点到站点 VPN 功能。 但是, 由于缺少某些变体, 可能会出现不兼容问题。 例如, 某些云提供商仅支持 IKEv1, 因此该云中需要一个 VPN 终止终结点。 对于支持 IKEv2 的云提供商, 可以在两个云提供商之间的 VPN 网关之间建立直接隧道。

站点到站点 VPN 不被视为高吞吐量和低延迟的解决方案。 但是, 可以将其用作物理连接的备份。

## <a name="next-steps"></a>后续步骤
如果遇到 ExpressRoute 和虚拟网络连接的其他任何问题，请参阅 [ExpressRoute 常见问题解答][ER-FAQ]。

请参阅在 Azure 和 oracle[云之间设置直接连接][ER-OCI]以连接 Azure 和 oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



