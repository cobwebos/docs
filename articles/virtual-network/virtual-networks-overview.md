---
title: Azure 虚拟网络 | Microsoft Docs
description: 了解 Azure 虚拟网络概念和功能。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/23/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 851c8c1eb13497355038ef4a8d5f1f9326c8c3bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781174"
---
# <a name="what-is-azure-virtual-network"></a>什么是 Azure 虚拟网络？

Azure 虚拟网络允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 Azure 虚拟网络提供以下重要功能：

## <a name="isolation-and-segmentation"></a>隔离和细分

可在每个 Azure [订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [区域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)中实现多个虚拟网络。 每个虚拟网络与其他虚拟网络相互隔离。 可以对每个虚拟网络执行以下操作：
- 使用公共和专用 (RFC 1918) 地址指定自定义专用 IP 地址空间。 Azure 从分配的地址空间中向虚拟网络中的资源分配一个专用 IP 地址。
- 将虚拟网络细分为一个或多个子网，并向每个子网分配一部分虚拟网络地址空间。
- 使用 Azure 提供的名称解析或指定自己的 DNS 服务器以供虚拟网络中的资源使用。

## <a name="communicate-with-the-internet"></a>与 Internet 通信

默认情况下，虚拟网络中的所有资源都可以与 Internet 进行出站通信。 将公共 IP 地址分配给某个资源即可与之进行入站通信。 有关详细信息，请参阅[公共 IP 地址](virtual-network-public-ip-address.md)。

## <a name="communicate-between-azure-resources"></a>Azure 资源之间的通信

Azure 资源采用下述某种方式安全地相互通信：

- **通过虚拟网络**：可以将 VM 和多个其他类型的 Azure 资源部署到虚拟网络，如 Azure 应用服务环境、Azure Kubernetes Service (AKS) 和 Azure 虚拟机规模集。 若要查看可部署到虚拟网络的 Azure 资源的完整列表，请参阅[虚拟网络服务集成](virtual-network-for-azure-services.md)。 
- **通过虚拟网络服务终结点**：通过直接连接将虚拟网络专用地址空间和虚拟网络的标识扩展到 Azure 服务资源，例如 Azure 存储帐户和 Azure SQL 数据库。 使用服务终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 有关详细信息，请参阅[虚拟网络服务终结点概述](virtual-network-service-endpoints-overview.md)。
 
## <a name="communicate-with-on-premises-resources"></a>与本地资源通信

可组合使用以下任何选项将本地计算机和网络连接到虚拟网络：

- **点到站点虚拟专用网络 (VPN)：** 在虚拟网络和网络中的单台计算机之间建立连接。 要与虚拟网络建立连接的每台计算机必须配置其连接。 这种连接类型适用于刚开始使用 Azure 的人员或开发人员，因为该连接类型仅需对现有网络作出极少更改或不做任何更改。 计算机与虚拟网络之间的通信经 Internet 通过加密的通道来发送。 若要了解更多信息，请参阅[点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S)。
- **站点到站点 VPN：** 在本地 VPN 设备和虚拟网络中部署的 Azure VPN 网关之间建立连接。 此连接类型可使授权的任何本地资源访问虚拟网络。 本地 VPN 设备和 Azure VPN 网关之间的通信经 Internet 通过加密的通道来发送。 若要了解更多信息，请参阅[站点到站点 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)。
- **Azure ExpressRoute：** 通过 ExpressRoute 合作伙伴在网络和 Azure 之间建立连接。 此连接是专用连接。 流量不经过 Internet。 若要了解详细信息，请参阅 [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute)。

## <a name="filter-network-traffic"></a>筛选网络流量
可使用以下两个选项中任意一个或同时使用这两个方案筛选子网之间的网络流量：
- **网络安全组：** 每个网络安全组可包含多个入站和出站安全规则，通过这些规则可按源和目标 IP 地址、端口和协议筛选出入资源的流量。 有关详细信息，请参阅[网络安全组](security-overview.md#network-security-groups)。
- **虚拟网络设备：** 虚拟网络设备是可执行网络功能（例如防火墙、WAN 优化等）的 VM。 若要查看可在虚拟网络中部署的网络虚拟设备，请参阅 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

## <a name="route-network-traffic"></a>路由网络流量

默认情况下，Azure 在子网、连接的虚拟网络、本地网络以及 Internet 之间路由流量。 可使用以下两个选项中任意一个或同时使用二者替代 Azure 创建的默认路由：
- **路由表：** 可创建自定义路由表，其中包含可对每个子网控制流量路由到位置的路由。 详细了解[路由表](virtual-networks-udr-overview.md#user-defined)。
- **边界网关协议 (BGP) 路由：** 如果使用 Azure VPN 网关或 ExpressRoute 连接将虚拟网络连接到本地网络，则可将本地 BGP 路由传播到虚拟网络。 详细了解如何将 BGP 与 [Azure VPN 网关](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)和 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) 配合使用。

## <a name="connect-virtual-networks"></a>连接虚拟网络

可以互相连接虚拟网络，使虚拟网络中的资源能够通过虚拟网络对等互连相互进行通信。 连接的虚拟网络可以在相同或不同的 Azure 区域中。 有关详细信息，请参阅[虚拟网络对等互连](virtual-network-peering-overview.md)。

## <a name="next-steps"></a>后续步骤

现在你已概要了解 Azure 虚拟网络。 若要使用虚拟网络来入门，请先创建一个虚拟网络，向其部署一些 VM，然后在 VM 之间通信。 有关详细信息，请参阅[创建虚拟网络](quick-create-portal.md)快速入门。