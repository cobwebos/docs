---
title: "Azure 虚拟网络 | Microsoft Docs"
description: "了解 Azure 虚拟网络概念和功能。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jdial
ms.openlocfilehash: 892aa03bd058b50fc4868a225dfe602624ff19ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="azure-virtual-network"></a>Azure 虚拟网络

Microsoft Azure 虚拟网络服务可以使 Azure 资源与虚拟网络中的其他每项资源进行安全通信。 虚拟网络是对专用于订阅的 Azure 云进行的逻辑隔离。 可将虚拟网络连接到其他虚拟网络，或本地网络。 下图显示了 Azure 虚拟网络服务的部分功能：

![网络示意图](./media/virtual-networks-overview/virtual-network-overview.png)

若要了解有关以下 Azure 虚拟网络功能的详细信息，请单击功能：
- **[隔离：](#isolation)**虚拟网络之间彼此隔离。 可以为使用相同 CIDR（例如 10.0.0.0/0）地址块的开发、测试和生产创建单独的虚拟网络。 相反地，也可以创建使用不同 CIDR 地址的多个虚拟网络并将网络连接在一起。 可将一个虚拟网络分为多个子网。 Azure 为部署在虚拟网络中的资源提供内部名称解析。 如有必要，可将虚拟网络配置为使用自己的 DNS 服务器，而不使用 Azure 内部名称解析。
- **[Internet 通信：](#internet)**默认情况下，部署在虚拟网络中的虚拟机等资源具有 Internet 访问权限。 根据需要，还可对特定资源启用入站访问。
- **[Azure 资源通信：](#within-vnet)**部署在虚拟网络中的 Azure 资源可使用专用 IP 地址相互通信，即使资源部署在不同的子网中，也是如此。 Azure 提供子网、连接的虚拟网络和本地网络之间的默认路由，因此无需配置和管理路由。 如果需要，可以自定义 Azure 的路由。
- **[虚拟网络连接：](#connect-vnets)**虚拟网络可以互相连接，使任何虚拟网络中的资源可以与任何其他虚拟网络中的资源进行通信。
- **[本地连接：](#connect-on-premises)**虚拟网络可连接到本地网络，使资源能够相互通信。
- **[流量筛选：](#filtering)**可按源 IP 地址和端口、目标 IP 地址和端口以及协议来筛选传入和传出虚拟网络中的资源的网络流量。
- **[路由：](#routing)**可选择通过配置自己的路由或通过网关传播 BGP 路由来替代 Azure 默认路由。

## <a name = "isolation"></a>网络隔离和细分

可在每个 Azure [订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和 Azure [区域](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)中实现多个虚拟网络。 每个虚拟网络与其他虚拟网络相互隔离。 可以对每个虚拟网络执行以下操作：
- 使用公共和专用 (RFC 1918) 地址指定自定义专用 IP 地址空间。 Azure 从分配的地址空间中向虚拟网络中的资源分配一个专用 IP 地址。
- 将虚拟网络细分为一个或多个子网，并向每个子网分配一部分虚拟网络地址空间。
- 使用 Azure 提供的名称解析或指定自己的 DNS 服务器以供虚拟网络中的资源使用。 要详细了解虚拟网络中的名称解析，请参阅[虚拟网络中资源的名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)一文。

## <a name = "internet"></a>Internet 通信
虚拟网络中的所有资源都可以与 Internet 进行出站通信。 默认情况下，资源的专用 IP 地址是源网络地址 (SNAT)，该地址被转换为 Azure 基础结构选择的公共 IP 地址。 若要了解出站网络连接的详细信息，请阅读[了解 Azure 中的出站连接](..\load-balancer\load-balancer-outbound-connections.md)一文。 若要阻止出站 Internet 连接，可以自定义路由或筛选流量。

若要从 Internet 入站通信到 Azure 资源或出站通信到不具 SNAT 的 Internet，则必须向资源分配一个公共 IP 地址。 若要详细了解公共 IP 地址，请阅读 [公共 IP 地址](virtual-network-public-ip-address.md)一文。

## <a name="within-vnet"></a>Azure 资源之间的安全通信

可以在虚拟网络中部署虚拟机。 虚拟机通过网络接口与虚拟网络中的其他资源进行通信。 若要了解有关网络接口的详细信息，请阅读[网络接口](virtual-network-network-interface.md)。

还可以将其他类型的 Azure 资源部署到虚拟网络，如 Azure 应用服务环境和 Azure 虚拟机规模集。 有关可部署到虚拟网络的 Azure 资源完整列表，请参阅 [Azure 服务的虚拟网络服务集成](virtual-network-for-azure-services.md)。

某些资源不能部署到虚拟网络，但可将资源通信限制在虚拟网络中。 要详细了解如何限制对资源的访问，请参阅[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)。 

## <a name="connect-vnets"></a>连接虚拟网络

可以互相连接虚拟网络，使虚拟网络中的资源能够通过虚拟网络对等互连相互进行通信。 不同虚拟网络中的资源之间的带宽和延迟与同一虚拟网络中的资源之间的带宽和延迟相同。 若要了解对等互连的详细信息，请阅读[虚拟网络对等互连](virtual-network-peering-overview.md)一文。

## <a name="connect-on-premises"></a>连接到本地网络

可组合使用以下任何选项将本地网络连接到虚拟网络：
- **点到站点虚拟专用网 (VPN)：**在虚拟网络和网络中的单台电脑之间建立连接。 要与虚拟网络建立连接的每台电脑必须单独配置其连接。 这种连接类型适用于刚开始使用 Azure 的人员或开发人员，因为该连接类型仅需对现有网络作出极少更改或不做任何更改。 此连接使用 SSTP 协议在电脑和虚拟网络之间通过 Internet 提供加密通信。 由于流量遍历 Internet，因此点到站点 VPN 的延迟不可预测。
- **站点到站点 VPN：**在 VPN 设备和虚拟网络中部署的 Azure VPN 网关之间建立连接。 此连接类型可使授权的任何本地资源访问虚拟网络。 此连接是一个 IPSec/IKE VPN，该 VPN 通过 Internet 在本地设备和 Azure VPN 网关之间提供加密通信。 由于流量遍历 Internet，因此站点到站点连接的延迟不可预测。
- **Azure ExpressRoute：**通过 ExpressRoute 合作伙伴在网络和 Azure 之间建立连接。 此连接是专用连接。 流量不会遍历 Internet。 由于流量未遍历 Internet，因此 ExpressRoute 连接的延迟可预测。

要详细了解以前的所有连接选项，请参阅[连接拓扑图](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)。

## <a name="filtering"></a>筛选网络流量
可使用以下两个选项中任意一个或同时使用这两个方案筛选子网之间的网络流量：
- **网络安全组：**每个网络安全组可包含多个入站和出站安全规则，通过这些规则可按源和目标 IP 地址、端口和协议筛选流量。 可以将网络安全组应用到虚拟机的每个网络接口。 此外，还可以将网络安全组应用到网络接口或其他 Azure 资源所在的子网。 若要深入了解网络安全组，请参阅[网络安全组](security-overview.md#network-security-groups)。
- **虚拟网络设备：**虚拟网络设备是运行软件的虚拟机，软件可执行网络功能，例如防火墙。 可在 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) 中查看可用的网络虚拟设备列表。 网络虚拟设备也可用于提供 WAN 优化和其他网络流量功能。 网络虚拟设备通常与用户定义路由或 BGP 路由配合使用。 还可以使用网络虚拟设备来筛选虚拟网络之间的流量。

## <a name="routing"></a>路由网络流量

默认情况下，Azure 会创建路由表，这些路由表可使连接到虚拟网络中任何子网的资源相互进行通信并与 Internet 通信。 可使用以下两个选项中任意一个或同时使用二者替代 Azure 创建的默认路由：
- **用户定义路由：**可创建自定义路由表，其中包含可对每个子网控制流量路由位置的路由。 若要深入了解用户定义的路由，请参阅[用户定义的路由](virtual-networks-udr-overview.md#user-defined)。
- **BGP 路由：**如果使用 Azure VPN 网关或 ExpressRoute 连接将虚拟网络连接到本地网络，则可将 BGP 路由传播到虚拟网络。

## <a name="pricing"></a>定价

虚拟网络、子网、路由表或网络安全组无收费。 出站 Internet 带宽使用、公共 IP 地址、虚拟网络对等互连、VPN 网关和 ExpressRoute 各有其定价结构。 相关详细信息，请查看[虚拟网络](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN 网关](https://azure.microsoft.com/pricing/details/vpn-gateway)和 [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) 定价页面。

## <a name="faq"></a>常见问题

若要查看关于 Azure 虚拟网络的常见问题解答，请参阅[虚拟网络常见问题解答](virtual-networks-faq.md)一文。

## <a name="next-steps"></a>后续步骤

- 完成[创建首个虚拟网络](quick-create-portal.md)中的步骤，创建自己的首个虚拟网络，并将几个虚拟机部署到此网络。
- 完成[配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤，为虚拟网络创建一个点到站点连接。
- 了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
