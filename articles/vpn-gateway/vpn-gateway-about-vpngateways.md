---
title: "VPN 网关概述：创建到 Azure 虚拟网络的跨界 VPN 连接 | Microsoft 文档"
description: "此 VPN 网关概述介绍了在使用基于 Internet 的 VPN 连接时，如何通过各种方式连接到 Azure 虚拟网络。 随附基本连接配置示意图。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: c24f7770e4d0721f9c584b80df9eb857442dfa0b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/22/2017


---
# 关于 VPN 网关
<a id="about-vpn-gateway" class="xliff"></a>

VPN 网关是一种虚拟网络网关，可通过公共连接将加密流量发送到本地位置。 也可使用 VPN 网关在基于 Microsoft 网络的 Azure 虚拟网络之间发送加密流量。 若要在 Azure 虚拟网络与本地站点之间发送加密网络流量，必须为虚拟网络创建 VPN 网关。

每个虚拟网络只能有一个 VPN 网关，但是，可以创建连接到相同 VPN 网关的多个连接。 此示例演示了一种多站点连接配置。 与同一个 VPN 网关建立多个连接时，所有 VPN 隧道（包括点到站点 VPN）将共享该网关可用的带宽。

### 什么是虚拟网络网关？
<a id="what-is-a-virtual-network-gateway" class="xliff"></a>

虚拟网络网关由两个或多个部署到名为 GatewaySubnet 的特定子网的虚拟机组成。 创建虚拟网络网关时，将创建位于 GatewaySubnet 的 VM。 虚拟网络网关 VM 配置为包含特定于该网关的路由表和网关服务。 无法直接配置属于虚拟网络网关的 VM，且绝不应该将其他资源部署到 GatewaySubnet。

如果使用网关类型“VPN”创建虚拟网络网关，则会创建可加密流量的特定类型的虚拟网络网关，即 VPN 网关。 VPN 网关可能需要长达 45 分钟才能创建完。 这是因为，适用于 VPN 网关的 VM 是部署到 GatewaySubnet 且使用指定的设置进行配置的。 所选网关 SKU 决定了 VM 功能的强大程度。

## <a name="gwsku"></a>网关 SKU

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

## 配置 VPN 网关
<a id="configuring-a-vpn-gateway" class="xliff"></a>

VPN 网关连接依赖于使用特定设置配置的多个资源。 大多数资源可以单独配置，虽然在某些情况下它们必须按特定顺序配置。

### 设置
<a id="settings" class="xliff"></a>

为每个资源选择的设置对于成功创建连接至关重要。 有关 VPN 网关的各个资源和设置的信息，请参阅 [关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md)。 可在借助该文章中的信息来了解网关类型、VPN 类型、连接类型、网关子网、本地网络网关，以及可能需要考虑的其他各项资源设置。

### 部署工具
<a id="deployment-tools" class="xliff"></a>

开始时可以使用一个配置工具（如 Azure 门户）创建和配置资源。 然后，可以切换到另一个工具（如 PowerShell）来配置其他资源或修改现有资源（如果适用）。 目前，无法在 Azure 门户中配置每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。 

### 部署模型
<a id="deployment-model" class="xliff"></a>

配置 VPN 网关时，采取的步骤取决于用于创建虚拟网络的部署模型。 例如，如果使用经典部署模型创建的 VNet，则使用经典部署模型的指导原则和说明来创建及配置 VPN 网关设置。 有关部署模型的详细信息，请参阅 [了解 Resource Manager 和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="diagrams"></a>连接拓扑示意图

必须知道，VPN 网关连接可以使用不同的配置。 必须确定哪种配置最适合自己的需要。 在下面的部分中，可以查看有关以下 VPN 网关连接的信息和拓扑示意图：以下部分包含一些表格，其中列出了：

* 可用的部署模型
* 可用的配置工具
* 直接转到某篇文章的链接（如果适用）

使用示意图和说明来帮助选择符合要求的连接拓扑。 示意图显示了主要的基准拓扑，但可以使用示意图作为准则生成更复杂的配置。

## 站点到站点和多站点（IPsec/IKE VPN 隧道）
<a id="site-to-site-and-multi-site-ipsecike-vpn-tunnel" class="xliff"></a>

### <a name="S2S"></a>站点到站点

站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道建立的连接。 此类型的连接要求位于本地的 VPN 设备分配有一个公共 IP 地址，并且不位于 NAT 后面。 S2S 连接可以用于跨界和混合配置。   

![Azure VPN 网关站点到站点连接示例](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>多站点

这种类型的连接是站点到站点连接的另一种形式。 可从虚拟网络网关创建多个 VPN 连接（通常是连接到多个本地站点）。 使用多个连接时，必须使用 RouteBased VPN 类型（使用经典 VNet 时称为动态网关）。 由于每个虚拟网络只能有一个 VPN 网关，因此通过网关的所有连接都共享可用带宽。 这通常称为“多站点”连接。

![Azure VPN 网关多站点连接示例](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### 适用于站点到站点和多站点的部署模型和方法
<a id="deployment-models-and-methods-for-site-to-site-and-multi-site" class="xliff"></a>

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>点到站点（基于 SSTP 的 VPN）

点到站点 (P2S) VPN 网关连接可让用户创建从单个客户端计算机到虚拟网络的安全连接。 P2S 是基于 SSTP（安全套接字隧道协议）的 VPN 连接。 与 S2S 连接不同，P2S 连接不需本地面向公众的 IP 地址或 VPN 设备。 从客户端计算机启动 VPN 连接即可建立 VPN 连接。 如果要从远程位置（例如，从家里或会议室）连接到 VNet，或者只有几个客户端需要连接到 VNet，此解决方案将非常有用。 可以通过同一 VPN 网关将 P2S 连接与 S2S 连接结合使用，前提是这两种连接的所有配置要求都兼容。

![Azure VPN 网关点到站点连接示例](./media/vpn-gateway-about-vpngateways/vpngateway-point-to-site-connection-diagram.png)

### 适用于点到站点的部署模型和方法
<a id="deployment-models-and-methods-for-point-to-site" class="xliff"></a>

[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>VNet 到 VNet 连接（IPsec/IKE VPN 隧道）

将一个虚拟网络连接到另一个虚拟网络（VNet 到 VNet）类似于将 VNet 连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道。 甚至可以将 VNet 到 VNet 通信与多站点连接配置组合使用。 这样，便可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑。

连接的 VNet 可以：

* 在相同或不同区域中
* 在相同或不同订阅中 
* 在相同或不同部署模型中

![Azure VPN 网关 VNet 到 VNet 连接示例](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### 部署模型之间的连接
<a id="connections-between-deployment-models" class="xliff"></a>

Azure 当前有两种部署模型：经典模型和 Resource Manager 模型。 如果 Azure 已经使用了一段时间，则您的 Azure VM 和实例角色可能是在经典 VNet 上运行。 而较新的 VM 和角色实例可能是在 Resource Manager 中创建的 VNet 上运行。 可以在 VNet 之间创建连接，以允许一个 VNet 中的资源直接与另一个 VNet 中的资源进行通信。

### VNet 对等互连
<a id="vnet-peering" class="xliff"></a>

可能能够使用 VNet 对等互连来创建连接，前提是虚拟网络符合特定要求。 VNet 对等互连不使用虚拟网络网关。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

### 适用于 VNet 到 VNet 的部署模型和方法
<a id="deployment-models-and-methods-for-vnet-to-vnet" class="xliff"></a>

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute（专用连接）

Microsoft Azure ExpressRoute 可让你通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 CRM Online 等 Microsoft 云服务建立连接。 可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在共置设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。

ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。

ExpressRoute 连接不使用 VPN 网关，不过，它确实会使用虚拟网络网关作为其所需配置的一部分。 在 ExpressRoute 连接中，虚拟网络网关配置了网关类型“ExpressRoute”，而不是“Vpn”。 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="coexisting"></a>站点到站点和 ExpressRoute 共存连接

ExpressRoute 是从 WAN （不通过公共 Internet）到 Microsoft 服务（包括 Azure）的直接专用连接。 站点到站点 VPN 流量以加密方式通过公共 Internet 传输。 能够为同一个虚拟网络配置站点到站点 VPN 和 ExpressRoute 连接有诸多优点。

可以将站点到站点 VPN 配置为 ExpressRoute 的安全故障转移路径，或者使用站点到站点 VPN 连接到不属于你的网络但却已通过 ExpressRoute 进行连接的站点。 请注意，此配置要求对同一虚拟网络使用两个虚拟网络网关，一个网关使用网关类型“Vpn”，另一个网关使用网关类型“ExpressRoute”。

![ExpressRoute 和 VPN 网关共存连接示例](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### 适用于 S2S 和 ExpressRoute 的部署模型和方法
<a id="deployment-models-and-methods-for-s2s-and-expressroute" class="xliff"></a>

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## 定价
<a id="pricing" class="xliff"></a>

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

有关用于 VPN 网关的网关 SKU 的详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

## 常见问题
<a id="faq" class="xliff"></a>

有关 VPN 网关的常见问题，请参阅 [VPN 网关常见问题](vpn-gateway-vpn-faq.md)。

## 后续步骤
<a id="next-steps" class="xliff"></a>

- 规划 VPN 网关配置。 请参阅 [VPN 网关规划和设计](vpn-gateway-plan-design.md)。
- 有关更多信息，请查看 [VPN 网关常见问题](vpn-gateway-vpn-faq.md)。
- 查看[订阅和服务限制](../azure-subscription-service-limits.md#networking-limits)。
