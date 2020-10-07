---
title: 关于 Azure VPN 网关
description: 了解 VPN 网关的定义，以及如何使用 VPN 网关连接到 IPsec IKE 站点到站点、VNet 到 V-VNet 以及点到点 VPN 虚拟网络。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperfq1
ms.openlocfilehash: 23d8d28a03217b1359462332da736f852cfaf8ea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89015384"
---
# <a name="what-is-vpn-gateway"></a>什么是 VPN 网关？

VPN 网关是特定类型的虚拟网关，用于跨公共 Internet 在 Azure 虚拟网络和本地位置之间发送加密的流量。 也可使用 VPN 网关在基于 Microsoft 网络的 Azure 虚拟网络之间发送加密流量。 每个虚拟网络只能有一个 VPN 网关。 但是，可以创建连接到相同 VPN 网关的多个连接。 与同一个 VPN 网关建立多个连接时，所有 VPN 隧道共享可用的网关带宽。

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>什么是虚拟网关？

虚拟网络网关由两个或多个 VM 组成，这些 VM 部署到所创建的名为“网关子网”的特定子网。 虚拟网络网关 VM 包含路由表，并运行特定的网关服务。 这些 VM 是在创建虚拟网络网关时创建的。 不能直接配置属于虚拟网络网关的 VM。

配置虚拟网络网关时，将配置用于指定网关类型的设置。 网关类型确定如何使用虚拟网络网关以及网关所采取的操作。 网关类型“Vpn”指定创建的虚拟网关类型为“VPN 网关”。 这将它与使用其他网关类型的 ExpressRoute 网关区分开来。 一个虚拟网络可以有两个虚拟网络网关：一个 VPN 网关和一个 ExpressRoute 网关。 有关详细信息，请参阅[网关类型](vpn-gateway-about-vpn-gateway-settings.md#gwtype)。

创建虚拟网关可能需要多达 45 分钟才能完成。 创建虚拟网关时，会将网关 VM 部署到网关子网，并使用指定的设置进行配置。 在创建 VPN 网关以后，即在一个 VPN 网关和另一个 VPN 网关之间（VNet 到 VNet）创建 IPsec/IKE VPN 隧道连接，或者在 VPN 网关和本地 VPN 设备（站点到站点）之间创建跨界 IPsec/IKE VPN 隧道连接。 也可创建点到站点 VPN 连接（基于 OpenVPN、IKEv2 或 SSTP 的 VPN），以便从远程位置（例如从会议室或家）连接到虚拟网络。

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>配置 VPN 网关

VPN 网关连接依赖于使用特定设置配置的多个资源。 大多数资源可以单独配置，虽然某些资源必须按特定顺序配置。

### <a name="design"></a><a name="diagrams"></a>设计

必须知道，VPN 网关连接可以使用不同的配置。 必须确定哪种配置最适合自己的需要。 例如，点到站点、站点到站点以及共存的 ExpressRoute/站点到站点连接都有不同的说明和配置要求。 要了解设计和查看连接拓扑图，请参阅[设计](design.md)。

### <a name="planning-table"></a><a name="planningtable"></a>规划表

下表可帮助选择最适合解决方案的连接选项。

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>设置

为每个资源选择的设置对于成功创建连接至关重要。 有关 VPN 网关的各个资源和设置的信息，请参阅 [关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md)。 本文包含的信息有助于了解网关类型、网关 SKU、VPN 类型、连接类型、网关子网、本地网关，以及可能需要考虑的其他各项资源设置。

### <a name="deployment-tools"></a><a name="tools"></a>部署工具

开始时可以使用一个配置工具（如 Azure 门户）创建和配置资源。 可在以后切换到另一个工具（如 PowerShell）来配置其他资源或修改现有资源（如果适用）。 目前，无法在 Azure 门户中配置每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。

## <a name="gateway-skus"></a><a name="gwsku"></a>网关 SKU

创建虚拟网络网关时，需指定要使用的网关 SKU。 请根据工作负荷、吞吐量、功能和 SLA 的类型，选择满足需求的 SKU。

* 有关网关 SKU 的详细信息（包括支持的功能、生产和开发测试以及配置步骤），请参阅 [VPN 网关设置 - 网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) 一文。
* 有关旧版 SKU 的信息，请参阅[使用旧版 SKU](vpn-gateway-about-skus-legacy.md)。

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>按隧道、连接和吞吐量列出的网关 SKU

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>可用性区域

VPN 网关可以部署在 Azure 可用性区域中。 这样可以提高虚拟网络网关的复原性、可伸缩性和可用性。 如果在 Azure 可用性区域中部署网关，可以在地理位置和逻辑上将区域内的网关分隔开来，同时还能保护本地网络与 Azure 的连接免受区域级故障的影响。 请参阅[关于 Azure 可用性区域中的区域冗余虚拟网络网关](about-zone-redundant-vnet-gateways.md)。

## <a name="pricing"></a><a name="pricing"></a>定价

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

有关用于 VPN 网关的网关 SKU 的详细信息，请参阅[网关 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

## <a name="faq"></a><a name="faq"></a>常见问题解答

有关 VPN 网关的常见问题，请参阅 [VPN 网关常见问题](vpn-gateway-vpn-faq.md)。

## <a name="whats-new"></a><a name="new"></a>新增功能

订阅 RSS 源，并在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway)页面上查看最新的 VPN 网关功能更新。

## <a name="next-steps"></a>后续步骤

- 有关更多信息，请查看 [VPN 网关常见问题](vpn-gateway-vpn-faq.md)。
- 查看[订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。
- 了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。
