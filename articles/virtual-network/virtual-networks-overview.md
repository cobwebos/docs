---
title: Azure 虚拟网络 | Microsoft Docs
description: 了解 Azure 虚拟网络概念和功能。
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 22c1e3050915fc697a62862620ef492ef22f80b8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542839"
---
# <a name="what-is-azure-virtual-network"></a>什么是 Azure 虚拟网络？

Azure 虚拟网络 (VNet) 是 Azure 中专用网络的基本构建块。 VNet 允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 VNet 类似于在你在自己的数据中心运营的传统网络，但附带了 Azure 基础设施的其他优势，例如可伸缩性、可用性和隔离性。

## <a name="vnet-concepts"></a>VNet 概念

- **地址空间：** 创建 VNet 时，必须使用公共和专用 (RFC 1918) 地址指定自定义的专用 IP 地址空间。 Azure 从分配的地址空间中向虚拟网络中的资源分配一个专用 IP 地址。 例如，如果在地址空间为 10.0.0.0/16 的 VNet 中部署某个 VM，将为该 VM 分配类似于 10.0.0.4 的专用 IP。
- **子网：** 使用子网可将虚拟网络划分为一个或多个子网络，并向每个子网分配一部分虚拟网络地址空间。 然后，可以在特定的子网中部署 Azure 资源。 就像在传统网络中一样，使用子网可将 VNet 地址空间划分为适合组织内部网络的网段。 这还会提高地址分配效率。 可以使用网络安全组保护子网中的资源。 有关详细信息，请参阅[安全组](security-overview.md)。
- **区域**：VNet 局限于一个区域/位置；但是，可以使用虚拟网络对等互连将不同区域的多个虚拟网络连接起来。
- **订阅：** VNet 的范围限定为订阅。 可在每个 Azure [订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [区域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)中实现多个虚拟网络。

## <a name="best-practices"></a>最佳做法

在 Azure 中构建网络时，必须记住以下通用设计原则：

- 确保地址空间不重叠。 确保 VNet 地址空间（CIDR 块）不与组织的其他网络范围重叠。
- 子网不应涵盖 VNet 的整个地址空间。 提前规划，为将来留出一些地址空间。
- 建议使用少量的大型 VNet，而不要使用多个小型 VNet。 这可以防止出现管理开销。
- 使用网络完全组 (NSG) 保护 VNet。

## <a name="communicate-with-the-internet"></a>与 Internet 通信

默认情况下，VNet 中的所有资源都可以与 Internet 进行出站通信。 可以通过分配公共 IP 地址或公共负载均衡器来与资源进行入站通信。 还可以使用公共 IP 或公共负载均衡器来管理出站连接。  若要详细了解 Azure 中的出站连接，请参阅[出站连接](../load-balancer/load-balancer-outbound-connections.md)、[公共 IP 地址](virtual-network-public-ip-address.md)和[负载均衡器](../load-balancer/load-balancer-overview.md)。

>[!NOTE]
>仅使用内部[标准负载均衡器](../load-balancer/load-balancer-standard-overview.md)时，在定义[出站连接](../load-balancer/load-balancer-outbound-connections.md)如何与实例级公共 IP 或公共负载均衡器配合使用之前，出站连接不可用。

## <a name="communicate-between-azure-resources"></a>Azure 资源之间的通信

Azure 资源采用下述某种方式安全地相互通信：

- **通过虚拟网络**：可以将 VM 和多个其他类型的 Azure 资源部署到虚拟网络，如 Azure 应用服务环境、Azure Kubernetes 服务 (AKS) 和 Azure 虚拟机规模集。 若要查看可部署到虚拟网络的 Azure 资源的完整列表，请参阅[虚拟网络服务集成](virtual-network-for-azure-services.md)。
- **通过虚拟网络服务终结点**：通过直接连接将虚拟网络专用地址空间和虚拟网络的标识扩展到 Azure 服务资源，例如 Azure 存储帐户和 Azure SQL 数据库。 使用服务终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 有关详细信息，请参阅[虚拟网络服务终结点概述](virtual-network-service-endpoints-overview.md)。
- **通过 VNet 对等互连**：可以互相连接虚拟网络，使虚拟网络中的资源能够通过虚拟网络对等互连相互进行通信。 连接的虚拟网络可以在相同或不同的 Azure 区域中。 有关详细信息，请参阅[虚拟网络对等互连](virtual-network-peering-overview.md)。

## <a name="communicate-with-on-premises-resources"></a>与本地资源通信

可组合使用以下任何选项将本地计算机和网络连接到虚拟网络：

- **点到站点虚拟专用网络 (VPN)：** 在网络中的虚拟网络和单台计算机之间建立连接。 要与虚拟网络建立连接的每台计算机必须配置其连接。 这种连接类型适用于刚开始使用 Azure 的人员或开发人员，因为该连接类型仅需对现有网络作出极少更改或不做任何更改。 计算机与虚拟网络之间的通信经 Internet 通过加密的通道来发送。 若要了解更多信息，请参阅[点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S)。
- **站点到站点 VPN：** 在本地 VPN 设备和虚拟网络中部署的 Azure VPN 网关之间建立连接。 此连接类型可使授权的任何本地资源访问虚拟网络。 本地 VPN 设备和 Azure VPN 网关之间的通信经 Internet 通过加密的通道来发送。 若要了解更多信息，请参阅[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)。
- **Azure ExpressRoute：** 通过 ExpressRoute 合作伙伴在网络和 Azure 之间建立连接。 此连接是专用连接。 流量不经过 Internet。 若要了解详细信息，请参阅 [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute)。

## <a name="filter-network-traffic"></a>筛选网络流量

可使用以下两个选项中任意一个或同时使用这两个方案筛选子网之间的网络流量：

- **安全组：** 网络安全组和应用程序安全组可包含多个入站和出站安全规则，通过这些规则可按源和目标 IP 地址、端口和协议筛选出入资源的流量。 要了解详细信息，请参阅[网络安全组](security-overview.md#network-security-groups)或[应用程序安全组](security-overview.md#application-security-groups)。
- **网络虚拟设备：** 虚拟网络设备是可执行网络功能（例如防火墙、WAN 优化等）的 VM。 若要查看可在虚拟网络中部署的网络虚拟设备，请参阅 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

## <a name="route-network-traffic"></a>路由网络流量

默认情况下，Azure 在子网、连接的虚拟网络、本地网络以及 Internet 之间路由流量。 可使用以下两个选项中任意一个或同时使用二者替代 Azure 创建的默认路由：

- **路由表：** 可创建自定义路由表，其中包含可对每个子网控制流量路由到位置的路由。 详细了解[路由表](virtual-networks-udr-overview.md#user-defined)。
- **边界网关协议 (BGP) 路由：** 如果使用 Azure VPN 网关或 ExpressRoute 连接将虚拟网络连接到本地网络，则可将本地 BGP 路由传播到虚拟网络。 详细了解如何将 BGP 与 [Azure VPN 网关](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) 配合使用。

## <a name="azure-vnet-limits"></a>Azure VNet 的限制

可部署的 Azure 资源数存在一定的限制。 大多数 Azure 网络限制设置在最大值。 但是，你可以根据 [VNet 限制页](../azure-subscription-service-limits.md#networking-limits)中的指定，[提高某些网络限制](../azure-supportability/networking-quota-requests.md)。 

## <a name="pricing"></a>定价

使用 Azure VNet 不会产生费用，它是免费的。 标准费率适用于虚拟机 (VM) 等资源和其他产品。 有关详细信息，请参阅 [VNet 定价](https://azure.microsoft.com/pricing/details/virtual-network/)和 Azure [定价计算器](https://azure.microsoft.com/pricing/calculator/)。

## <a name="next-steps"></a>后续步骤

 若要使用虚拟网络来入门，请先创建一个虚拟网络，向其部署一些 VM，然后在 VM 之间通信。 有关详细信息，请参阅[创建虚拟网络](quick-create-portal.md)快速入门。
