---
title: "Azure 网络安全 | Microsoft Docs"
description: "了解基于云的计算服务，包括大量计算实例和服务，它们可根据应用程序或企业的需求自动增加或减少。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2017
ms.author: TomSh
ms.openlocfilehash: 6ab59dd02391287a1effc0b51502bb7eb90db319
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-security"></a>Azure 网络安全

我们知道，安全是云中的首要任务，及时找到有关 Azure 安全性的准确信息极其重要。 将 Azure 用于应用程序和服务的最合理原因之一是可以利用 Azure 的各种安全工具和功能。 这些工具和功能可帮助在 Azure 平台上创建安全的解决方案。

Microsoft Azure 提供具备保密性、完整性和可用性的客户数据，同时还能实现透明的问责制。 为帮助你从客户的角度更好地了解 Microsoft Azure 中实施的网络安全控件集合，特编写本文《Azure 网络安全》，以全面了解 Microsoft Azure 提供的网络安全控件。

本文旨在介绍各种网络控件，通过配置这些控件可增强 Azure 中部署的解决方案的安全性。 如果有兴趣了解 Microsoft 为保护 Azure 平台本身的网络结构而采取的措施，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/security/azure-security)的“Azure 安全性”部分。

## <a name="azure-platform"></a>Azure 平台

Azure 是一个公有云服务平台，支持极为广泛的操作系统、编程语言、框架、工具、数据库和设备选择。  它可运行与 Docker 集成的 Linux 容器；使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 生成应用；生成适用于 iOS、Android 和 Windows 设备的后端。 Azure 云服务支持数百万开发人员和 IT 专业人士依赖并信任的技术。

构建 IT 资产或将其迁移到公有云服务提供商处时，需要借助该组织的能力来保护应用程序和数据，并使用该组织提供的服务和控制机制来管理基于云的资产的安全性。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全要求。 此外，Azure 还提供广泛的可配置安全选项以及对这些选项进行控制的功能，方便用户自定义安全措施来满足组织部署的独特要求。

## <a name="abstract"></a>摘要

Microsoft 公有云服务提供超大规模的服务和基础结构、企业级的功能，以及许多混合连接选项。 可以选择通过 Internet 或 Azure ExpressRoute（提供专用网络连接）访问这些服务。 借助 Microsoft Azure 平台可将基础结构无缝扩展到云中并构建多层体系结构。 此外，第三方可以提供安全服务和虚拟设备，以启用增强的功能。

Azure 网络服务旨在最大程度提高灵活性、可用性、复原能力、安全性和完整性。 本白皮书详述 Azure 的网络功能，并介绍客户如何使用 Azure 的本机安全功能帮助保护自己的信息资产。

本白皮书的目标受众包括：

- 正在寻找 Azure 支持的可用安全解决方案的技术经理、网络管理员和开发人员。

-   想从较高层次大致了解与 Azure 公有云网络安全话题相关的 Azure 网络技术和服务的 SME 或业务流程行政人员。

## <a name="azure-networking-big-picture"></a>Azure 网络大图
Microsoft Azure 包括可靠的网络基础结构以支持应用程序和服务连接要求。 Azure 中的资源之间、本地资源与 Azure 托管的资源之间以及 Internet 与 Azure 之间都可能存在网络连接。

![Azure 网络大图](media/azure-network-security/azure-network-security-fig-1.png)

利用 [Azure 网络基础结构](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines)，可以安全地将 Azure 资源通过虚拟网络 (VNet) 相互连接。 VNet 是自己的网络在云中的表示形式。 VNet 是对专用于订阅的 Azure 云网络进行的逻辑隔离。 可将 VNet 连接到本地网络。

Azure 支持通过 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 使用专用 WAN 链路连接本地网络和 Azure 虚拟网络。 Azure 和站点之间的链接使用专用连接，不需要通过公共 Internet。 如果 Azure 应用程序在多个数据中心运行，则可以使用 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)智能地跨应用程序实例路由来自用户的请求。 如果可以通过 Internet 访问未在 Azure 中运行的服务，还可以将流量路由到这些服务。

## <a name="enterprise-view-of-azure-networking-components"></a>Azure 网络组件的企业视图
Azure 有许多与网络安全话题相关的网络组件。 我们将介绍这些网络组件，并重点说明与之相关的安全问题。

> [!Note]
> 我们并不会介绍 Azure 网络的所有组件，而只介绍在围绕 Azure 中部署的服务和应用程序规划和设计安全网络基础结构时发挥关键作用的组件。

本文涵盖以下 Azure 网络企业功能：

-   基本网络连接

-   混合连接

-   安全控制

-   网络验证

### <a name="basic-network-connectivity"></a>基本网络连接

利用 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)服务，可以安全地将 Azure 资源通过虚拟网络 (VNet) 相互连接。 VNet 是自己的网络在云中的表示形式。 VNet 是对专用于订阅的 Azure 网络基础结构进行的逻辑隔离。 还可以使用站点到站点 VPN 和专用 [WAN 链路](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)将 VNet 连接到 VNet 和本地网络。

![基本网络连接](media/azure-network-security/azure-network-security-fig-2.png)

虽然可以使用 VM 在 Azure 中托管服务器，但问题是如何将这些 VM 连接到网络。 答案就是将 VM 连接到 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

Azure 虚拟网络类似于有自己的虚拟化平台解决方案（例如 Microsoft Hyper-V 或 VMware）的本地虚拟网络。

#### <a name="intra-vnet-connectivity"></a>VNet 之间的连接

VNet 之间可相互连接，因此，连接到任意一个 VNet 的资源都可与 VNet 之间的每个资源进行通信。 可使用以下两个中任意一个选项或使用这两个选项相互连接 VNet：

- **对等互连：**使连接到相同 Azure 位置中不同 Azure VNet 的资源可相互进行通信。 VNet 之间的带宽和延迟相同，就像资源是连接到同一个 VNet 一样。 若要详细了解对等互连，请阅读[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

 ![对等互连](media/azure-network-security/azure-network-security-fig-3.png)

- **VNet 到 VNet连接：**可使资源连接到相同或不同 Azure 位置中的不同 Azure VNet。 与对等互连不同，VNet 之间的带宽有一定限制，因为流量必须流经 Azure VPN 网关。

![VNet 到 VNet 连接](media/azure-network-security/azure-network-security-fig-4.png)


若要详细了解如何通过 VNet 到 VNet 连接进行 VNet 连接，请阅读[配置 VNet 到 VNet 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。

#### <a name="azure-virtual-network-capabilities"></a>Azure 虚拟网络功能：

如用户所见，Azure 虚拟网络可以将虚拟机连接到网络，以便它们以安全的方式连接到其他网络资源。 但是，基本连接仅仅只是开始。 Azure 虚拟网络服务的以下功能揭示了 Azure 虚拟网络的安全特性：

-   隔离

-   Internet 连接

-   Azure 资源连接

-   VNet 连接

-   本地连接

-   流量筛选

-   路由

**隔离**

VNet 之间彼此[隔离](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 可以为使用相同 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 地址块的开发、测试和生产创建单独的 VNet。 相反地，也可以创建使用不同 CIDR 地址的多个 VNet 并将网络连接在一起。 可将一个 VNet 分为多个子网。

Azure 为连接到 VNet 的 VM 和[云服务](https://azure.microsoft.com/services/cloud-services/)角色实例提供内部名称解析。 可选择配置 VNet 来使用自己的 DNS 服务器，而不使用 Azure 内部名称解析。

可在每个 Azure [订阅](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)和 Azure [区域](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)中实现多个 VNet。 每个 VNet 与其他 VNet 隔离。 对于每个 VNet，可执行以下操作：

-   使用公共和专用 (RFC 1918) 地址指定自定义专用 IP 地址空间。 Azure 从分配的地址空间中向连接到 VNet 的资源分配一个专用 IP 地址。

-   将 VNet 细分为一个或多个子网，并向每个子网分配一部分 VNet 地址空间。

-   使用 Azure 提供的名称解析或指定自己的 DNS 服务器以供连接到 VNet 的资源使用。 若要详细了解 VNet 中的名称解析，请阅读 [VM 和云服务的名称解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)。

**Internet 连接**

默认情况下，连接到 VNet 的所有 [Azure 虚拟机 (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) 和云服务角色实例都具有 Internet 访问权限。 根据需要，还可对特定资源启用入站访问。 根据需要，还可对特定资源启用入站访问。

默认情况下，连接到 VNet 的所有资源都具有 Internet 出站连接。 资源的专用 IP 地址是由 Azure 基础结构转换到公共 IP 地址中的源网络地址 (SNAT)。 可通过实现自定义路由和流量筛选更改默认连接。 若要详细了解出站 Internet 连接，请阅读[了解 Azure 中的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json)。

若要从 Internet 入站通信到 Azure 资源或出站通信到不具 SNAT 的 Internet，则必须向资源分配一个公共 IP 地址。 若要详细了解公共 IP 地址，请阅读[公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)。

**Azure 资源连接**

云服务和 VM 等 [Azure 资源](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)可连接到同一 VNet。 即使资源在不同的子网中，也可使用专用 IP 地址连接彼此。 Azure 提供子网、VNet 和本地网络之间的默认路由，因此无需配置和管理路由。

可将多个 Azure 资源连接到 VNet，例如虚拟机 (VM)、云服务、应用服务环境和虚拟机规模集。 VM 通过网络接口 (NIC) 连接到 VNet 中的子网。 若要详细了解 NIC，请阅读[网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)。

**VNet 连接**

[VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 之间可相互连接，因此，连接到任何 VNet 的资源都可与任何其他 VNet 上的所有资源进行通信。

VNet 之间可相互连接，因此，连接到任意一个 VNet 的资源都可与 VNet 之间的每个资源进行通信。 可使用以下两个中任意一个选项或使用这两个选项相互连接 VNet：

- **对等互连：**使连接到相同 Azure 位置中不同 Azure VNet 的资源可相互进行通信。 VNet 之间的带宽和延迟相同，就像资源是连接到同一个 VNet 一样。若要详细了解对等互连，请阅读[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。

- **VNet 到 VNet连接：**可使资源连接到相同或不同 Azure 位置中的不同 Azure VNet。 与对等互连不同，VNet 之间的带宽有一定限制，因为流量必须流经 Azure VPN 网关。 若要详细了解如何通过 VNet 到 VNet 连接进行 VNet 连接， 请阅读[配置 VNet 到 VNet 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)。

**本地连接**

可通过网络和 Azure 之间的专用网络连接或基于 Internet 的站点到站点 VPN 连接将 VNet 连接到[本地](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)网络。

可组合使用以下任何选项将本地网络连接到 VNet：

- **点到站点虚拟网络 (VPN)：**在连接到网络的单台电脑和 VNet 之间建立连接。 这种连接类型适用于刚开始使用 Azure 的人员或开发人员，因为该连接类型仅需对现有网络作出极少更改或不做任何更改。 此连接使用 SSTP 协议在电脑和 VNet 之间通过 Internet 提供加密通信。 由于流量遍历 Internet，因此点到站点 VPN 的延迟不可预测。

- **站点到站点 VPN：**在 VPN 设备和 Azure VPN 网关之间建立连接。 此连接类型可使授权的任何本地资源访问 VNet。 此连接是一个 IPsec/IKE VPN，该 VPN 通过 Internet 在本地设备和 Azure VPN 网关之间提供加密通信。 由于流量遍历 Internet，因此站点到站点连接的延迟不可预测。

- **Azure ExpressRoute：**通过 ExpressRoute 合作伙伴在网络和 Azure 之间建立连接。 此连接是专用连接。 流量不会遍历 Internet。 由于流量未遍历 Internet，因此 ExpressRoute 连接的延迟可预测。 若要详细了解以前的所有连接选项，请阅读[连接拓扑图](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)。

**流量筛选**

可以按源 IP 地址和端口、目标 IP 地址和端口以及协议，对 VM 和云服务角色实例[网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)进行入站和出站筛选。

可使用以下两个选项中任意一个或同时使用这两个方案筛选子网之间的网络流量：

- **网络安全组 (NSG)：**每个 NSG 可包含多个入站和出站安全规则，通过这些规则可按源和目标 IP 地址、端口和协议筛选流量。 可将 NSG 应用到一个 VM 中的每个 NIC。 也可将 NSG 应用到 NIC 或其他 Azure 资源所连接的子网。 若要详细了解 NSG，请阅读[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。

- **虚拟网络设备：**虚拟网络设备是运行软件的 VM，软件可执行网络功能，例如防火墙。 请查看 Azure 应用商店中的可用 NVA 列表。 NVA 也可用于提供 WAN 优化和其他网络流量功能。 NVA 通常与用户定义路由或 BGP 路由配合使用。 还可使用 NVA 筛选 VNet 之间的流量。

**路由**

可选择通过配置自己的路由或通过网关使用 BGP 路由来替代 Azure 默认路由。

默认情况下，Azure 会创建路由表，这些路由表可使连接到 VNet 中任何子网的资源相互进行通信。 可使用以下两个选项中任意一个或同时使用二者替代 Azure 创建的默认路由：

- **用户定义路由：**可创建自定义路由表，其中包含可对每个子网控制流量路由位置的路由。 若要详细了解用户定义路由，请阅读[用户定义路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

- **BGP 路由：**如果使用 Azure VPN 网关或 ExpressRoute 连接将 VNet 连接到本地网络，则可将 BGP 路由传播到 VNet。

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>混合 Internet 连接：连接到本地网络
可组合使用以下任何选项将本地网络连接到 VNet：

-   Internet 连接

-   点到站点 VPN (P2S VPN)

-   站点到站点 VPN (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Internet 连接

顾名思义，Internet 连接允许用户向虚拟网络中的工作负荷公开各种公共终结点，以便能够从 Internet 访问这些工作负荷。 可以通过以下方式公开这些工作负荷：使用[面向 Internet 的负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)，或者只需向 VM 分配公共 IP 地址。 这样一来，Internet 上的任何内容都有可能访问该虚拟机，当然，前提是主机防火墙、[网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 和[用户定义路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)允许这样做。

在此方案中，可以公开需要向 Internet 公开并且能够从任意位置或特定位置（具体取决于工作负荷的配置）连接到 Internet 的应用程序。

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>点到站点 VPN 或站点到站点 VPN
这两种 VPN 属于同一类别。 它们都要求 VNet 具有 VPN 网关，用户可以使用工作站的 VPN 客户端（作为[点到站点配置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)的一部分）连接到该网关，也可以配置本地 [VPN 设备](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)，使其能够终止站点到站点 VPN。 这样一来，本地设备就可以连接到 VNet 中的资源。

通过点到站点 (P2S) 配置，可以使单台客户端计算机与虚拟网络建立安全的连接。 P2S 是基于 SSTP（安全套接字隧道协议）的 VPN 连接。

![点到站点 VPN](media/azure-network-security/azure-network-security-fig-5.png)

如果要从远程位置（例如，从家里或会议中心）连接到 VNet，或者只有少数几个客户端需要连接到虚拟网络，点到站点连接将非常有用。

P2S 连接不需要 VPN 设备或面向公众的 IP 地址。 可从客户端计算机建立 VPN 连接。 因此，如果需要建立从多个本地设备和计算机到 Azure 网络的持久化连接，建议不要以 P2S 的方式连接到 Azure。

![站点到站点 VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> 有关点到站点连接的详细信息，请参阅[点到站点常见问题解答](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)。

使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。

此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 此连接通过 Internet 进行，可让你在网络与 Azure 之间的加密链接内“输送”信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 使用 [IPsec 隧道模式](https://technet.microsoft.com/library/cc786385.aspx)可执行隧道加密。

尽管站点到站点 VPN 是可信、可靠且经过证实的技术，但隧道中的流量将遍历 Internet。 此外，最大带宽相对受限于大约 200 Mbps。

如果希望跨界连接具有突出的安全或性能级别，建议对跨界连接使用 Azure ExpressRoute。 ExpressRoute 是你本地位置与 Exchange 托管提供商之间专用的 WAN 链接。 因为这是电信运营商连接，所以数据不会通过 Internet 传输，也不会出现 Internet 通信固有的潜在风险。

> [!Note]
> 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。

#### <a name="dedicated-wan-link"></a>专用 WAN 链路
使用 Microsoft Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Azure。

ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。

![ 专用 WAN 链路](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> 有关如何使用 ExpressRoute 将网络连接到 Microsoft 的信息，请参阅 [ExpressRoute 连接模型](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)和 [ExpressRoute 技术概述](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

与站点到站点 VPN 选项一样，ExpressRoute 也允许连接到不止一个 VNet 中的资源。 事实上，根据 SKU，可以连接到 10 个 VNet。 如果有[高级外接程序](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)，可以根据带宽连接到最多 100 个 VNet。 若要详细了解这些连接类型的外观，请阅读[连接拓扑图](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="security-controls"></a>安全控件
Azure 虚拟网络提供一种与其他虚拟网络隔离的安全逻辑网络，并支持本地网络上使用的多种安全控件。 客户使用子网创建自己的结构：他们使用自己的专用 IP 地址范围，配置路由表、网络安全组、访问控制列表 (ACL)、网关和虚拟设备，以在云中运行其工作负荷。

下面是可以在 Azure 虚拟网络上使用的安全控件：

-   网络访问控件

-   用户定义路由

-   网络安全设备

-   应用程序网关

-   Azure Web 应用程序防火墙

-   网络可用性控件

#### <a name="network-access-controls"></a>网络访问控件
Azure 虚拟网络 (VNet) 是 Azure 网络模型的基础，可提供隔离和保护，而[网络安全组 (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) 则是用于在网络级别实施和控制网络流量规则的主要工具。

![ 网络访问控件](media/azure-network-security/azure-network-security-fig-8.png)


可以从客户网络上的系统，通过跨界连接或直接 Internet 通信允许或拒绝虚拟网络内工作负荷之间的通信，从而控制访问。

在图中，VNet 和 NSG 位于 Azure 总体安全堆栈的特定层，在该层，可以使用 NSG、UDR 和网络虚拟设备创建安全边界，保护受保护网络中的应用程序部署。

NSG 使用 5 元组信息评估流量（并且用于为 NSG 配置的规则）：

-   [源 IP 地址和目标 IP 地址](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [源端口和目标端口](https://technet.microsoft.com/library/dd197515)

-   协议：[传输控制协议 (TCP)](https://technet.microsoft.com/library/cc940037.aspx) 或[用户数据报协议 (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

这意味着可以控制单个 VM 与一组 VM 之间、单个 VM 到其他单个 VM 或整个子网之间的访问。 同样，请记住，这是简单的有状态数据包筛选，不是完整数据包检查。 网络安全组中没有协议验证或者网络级别 IDS 或 IPS 功能。

NSG 附带了一些应当注意的内置规则。 其中包括：

-   **允许特定虚拟网络中的所有流量：**同一 Azure 虚拟网络上的所有 VM 可以相互通信。

-   **允许 Azure 负载均衡到入站流量：** 此规则允许 Azure 负载均衡器从任何源地址流向任何目标地址的流量。

-   **拒绝所有入站流量：** 此规则阻止源自已显式允许的 Internet 的所有流量。

-   **允许流向 Internet 的所有出站流量：**此规则允许 VM 启动到 Internet 的连接。 如果不希望启动这些连接，则需创建一个规则来阻止这些连接或实施强制隧道。

#### <a name="system-routes-and-user-defined-routes"></a>系统路由和用户定义路由

在 Azure 中将虚拟机 (VM) 添加到虚拟网络 (VNet) 时，用户会发现 VM 能够自动通过网络相互通信。 不需要指定网关，即使这些 VM 位于不同子网中。

存在从 Azure 到自己的数据中心的混合连接时，这同样适用于从 VM 到公共 Internet 甚至到本地网络的通信。

![系统路由](media/azure-network-security/azure-network-security-fig-9.png)

之所以允许这种通信流，是因为 Azure 使用一系列系统路由来定义 IP 流量的流动方式。 系统路由通过以下方案来控制通信流：

-   从同一子网内。

-   在 VNet 中从一个子网到另一个子网。

-   从 VM 到 Internet。

-   通过 VPN 网关从一个 VNet 到另一个 VNet。

-   通过 VNet 对等互连（[服务链](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)）从一个 VNet 到另一个 VNet。

-   通过 VPN 网关从 VNet 到本地网络。

许多企业有严格的安全性和合规性要求，要求在本地检查所有网络数据包，以实施特定策略。 Azure 提供一种称为[强制隧道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling)的机制，该机制通过创建自定义路由或通过[边界网关协议 (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) 播发，

在 Azure 中，可通过虚拟网络用户定义路由 (UDR) 配置强制隧道。 将流量重定向到本地站点，这是 Azure VPN 网关的默认路由。

以下部分列出了 Azure 虚拟网络路由和路由表的当前限制：

-   每个虚拟网络子网具有内置的系统路由表。 系统路由表具有以下三组路由：

 -  **本地 VNet 路由：**直接路由到同一个虚拟网络中的目标虚拟机

 - **本地路由：**路由到 Azure VPN 网关

 -  **默认路由：**直接路由到 Internet。 如果要将数据包发送到不包含在前面两个路由中的专用 IP 地址，数据包会被删除。

-   在发布的用户定义路由中，可以创建路由表来添加默认路由，然后将路由表关联到 VNet 子网，在这些子网上启用强制隧道。

-   需要在连接到虚拟网络的跨界本地站点中，设置一个“默认站点”。

-   强制隧道必须关联到具有动态路由 VPN 网关的 VNet，不能是静态网关。

- ExpressRoute 强制隧道不是通过此机制配置的，而是通过 ExpressRoute BGP 对等会话播发默认路由来启用的。

> [!Note]
> 有关详细信息，请参阅 [ExpressRoute 文档](https://azure.microsoft.com/documentation/services/expressroute/)。

#### <a name="network-security-appliances"></a>网络安全设备
尽管网络安全组和用户定义路由可以在 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的网络层和传输层提供特定网络安全度量，但在某些情况下，用户想要或需要启用更高网络堆栈级别的安全性。 在此类情况下，建议部署 Azure 合作伙伴所提供的虚拟网络安全设备。

![网络安全设备](./media/azure-network-security/azure-network-security-fig-10.png)

Azure 网络安全设备可增强 VNet 安全性和网络功能。众多供应商通过 [Azure 应用商店](https://azuremarketplace.microsoft.com)提供了这类设备。 通过部署这些虚拟安全设备，可以提供：

-   高度可用的防火墙

-   入侵防护

-   入侵检测

-   Web 应用程序防火墙 (WAF)

-   WAN 优化

-   路由

-   负载均衡

-   VPN

-   证书管理

-   Active Directory

-   多重身份验证

#### <a name="application-gateway"></a>应用程序网关

[Microsoft Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)是以服务形式提供应用程序传递控制器 (ADC) 的专用虚拟设备。

 ![应用程序网关](./media/azure-network-security/azure-network-security-fig-11.png)

使用应用程序网关可以将 CPU 密集型 SSL 终端的负载卸载到应用程序网关（SSL 卸载），从而优化 Web 场的性能和可用性。 它还提供第 7 层的其他路由功能，包括：

-   传入流量的轮循机制分布

-   基于 Cookie 的会话关联

-   基于 URL 路径的路由

-   能够在单个应用程序网关后面托管多个网站


[Web 应用程序防火墙 (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 也作为应用程序网关的一部分提供。 它为 Web 应用程序提供保护，帮助抵御常见 Web 漏洞和攻击。 可以将应用程序网关配置为面向 Internet 的网关、仅限内部的网关或这两者的组合。

可以在检测或保护模式下运行应用程序网关 WAF。 常见的用例是让管理员在检测模式下运行，以观察流量是否存在恶意模式。 一旦检测到潜在攻击，就转向保护模式，阻止可疑的传入流量。

 ![应用程序网关](./media/azure-network-security/azure-network-security-fig-12.png)

此外，应用程序网关 WAF 还可以使用与 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) 和 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成的实时 WAF 日志来跟踪 WAF 警报和轻松监视趋势，帮助监视 Web 应用程序免受攻击。

JSON 格式化的日志直接进入客户的存储帐户。 你对这些日志具有完全控制权，并且可以应用自己的保留策略。

还可以使用 [Azure 日志集成](https://aka.ms/AzLog)将这些日志引入自己的分析系统。 WAF 日志还与 [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite) 集成，因此你可以使用 OMS Log Analytics 执行复杂的细化查询。

#### <a name="azure-web-application-firewall-waf"></a>Azure Web 应用程序防火墙 (WAF)

越来越多 Web 应用程序成为恶意攻击的目标，这些攻击利用常见的已知漏洞，如 SQL 注入、跨站点脚本攻击以及 [OWASP top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) 中出现的其他攻击。 为了防止应用程序遭受此类攻击，需要对应用程序拓扑的多个层进行严格的维护、修补和监视。

 ![Azure Web 应用程序防火墙 (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

集中式 [Web 应用程序防火墙 (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 可以抵御 Web 攻击，并简化安全管理，无需进行任何应用程序更改。

相较保护每个单独的 Web 应用程序，WAF 解决方案还可通过在中央位置修补已知漏洞，更快地响应安全威胁。 可将现有应用程序网关轻松转换为支持 Web 应用程序防火墙的应用程序网关。

#### <a name="network-availability-controls"></a>网络可用性控件

使用 Microsoft Azure 分配网络流量有不同的选项。 这些选项的工作方式彼此不同，具有不同的功能集，并支持不同的方案。 这些选项每个都能单独使用，也可以组合使用。

下面是一些网络可用性控件：

-   Azure 负载均衡器

-   应用程序网关

-   流量管理器

**Azure 负载均衡器**

提高应用程序的可用性和网络性能。 它是第 4 层（TCP、UDP）类型的负载均衡器，可在负载均衡集中定义的运行状况良好的服务实例之间分配传入流量。

 ![Azure 负载均衡器](media/azure-network-security/azure-network-security-fig-14.png)


可以将 Azure 负载均衡器配置为：

-   对传入到虚拟机的 Internet 流量进行负载均衡。 此配置称为[面向 Internet 的负载均衡](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)。

-   对虚拟网络中虚拟机之间的流量、云服务中虚拟机之间的流量或本地计算机和跨界虚拟网络中虚拟机之间的流量进行负载均衡。 此配置称为[内部负载均衡](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)。

-   将外部流量转发到特定的虚拟机。

云中的所有资源都需要通过公共 IP 地址从 Internet 进行访问。 Azure 中的云基础结构对其资源使用不可路由的 IP 地址。 为了与 Internet 通信，Azure 对公共 IP 地址使用网络地址转换 (NAT)。

 **应用程序网关**

 应用程序网关在应用层（OSI 网络参考堆栈中的第 7 层）工作。 它充当反向代理服务，终止客户端连接，并将请求转发到后端终结点。

 **流量管理器**

使用 Microsoft Azure 流量管理器，可以控制用户流量在不同数据中心内的服务终结点上的分布。 流量管理器支持的服务终结点包括 Azure VM、Web 应用和云服务。 也可将流量管理器用于外部的非 Azure 终结点。

流量管理器根据[流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)和终结点的运行状况，使用域名系统 (DNS) 将客户端请求定向到最合适的终结点。 流量管理器提供多种流量路由方法来满足不同的应用程序需求、终结点运行状况[监视](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)和自动故障转移。 流量管理器能够灵活应对故障，包括整个 Azure 区域的故障。

使用 Azure 流量管理器可以控制流量在应用程序终结点之间的分布。 终结点可以是托管在 Azure 内部或外部的任何面向 Internet 的服务。

流量管理器具有两大优势：

-   根据某个[流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)对流量进行分布。

-   [连续监视终结点运行状况](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)，在终结点发生故障时自动进行故障转移。

当客户端尝试连接到某个服务时，必须先将该服务的 DNS 名称解析成 IP 地址。 然后，客户端就可以连接到该 IP 地址以访问相关服务。 流量管理器根据流量路由方法的规则，使用 DNS 将客户端导向到特定的服务终结点。 客户端直接连接到选定的终结点。 流量管理器不是代理或网关。 流量管理器看不到流量在客户端与服务之间传递。

### <a name="azure-network-validation"></a>Azure 网络验证

Azure 网络验证旨在确保 Azure 网络如配置的那样正常运行，并且可以使用可用于监视网络的服务和功能执行验证。 使用 Azure 网络观察程序可以访问大量日志记录和诊断功能，便于深入了解网络性能和运行状况。 这些功能可通过门户、Power Shell、CLI、Rest API 和 SDK 访问。

Azure 操作安全性是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。 Azure 操作安全性建立在一个框架上，该框架融合了通过 Microsoft 独有的各种功能获得的知识，包括 Microsoft 安全开发生命周期 (SDL)、Microsoft 安全响应中心计划以及对网络安全威胁形态的深刻认识。

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure 网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

操作 Microsoft Azure 的人员和进程可能是平台最重要的安全功能。 本部分介绍 Microsoft 全球数据中心基础结构的功能，这些功能可帮助增强和维护安全性、连续性和隐私。

应用程序的基础结构通常由许多组件构成，其中可能包括虚拟机、存储帐户、虚拟网络、Web 应用、数据库、数据库服务器和第三方服务。 这些组件不会以独立的实体出现，而是以单个实体的相关部件和依赖部件出现。 如果希望以组的方式部署、管理和监视这些这些组件， 那么，可以使用 Azure Resource Manager 以组的方式处理解决方案中的资源。

可以通过一个协调的操作为解决方案部署、更新或删除所有资源。 可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 Resource Manager 提供安全、审核和标记功能，以帮助你在部署后管理资源。

**使用 Resource Manager 的优势**

Resource Manager 提供多种优势：

-   可以以组的形式部署、管理和监视解决方案的所有资源，而不是单独处理这些资源。

-   可以在整个开发生命周期内重复部署解决方案，并确保以一致的状态部署资源。

-   可以通过声明性模板而非脚本来管理基础结构。

-   可以定义各资源之间的依赖关系，使其按正确的顺序进行部署。

-   可以将访问控制应用到资源组中的所有服务，因为基于角色的访问控制 (RBAC) 已在本机集成到管理平台。

-   可以将标记应用到资源，以逻辑方式组织订阅中的所有资源。

-   可以通过查看一组共享标记的资源的成本来理清组织的帐单。

> [!Note]
> Resource Manager 提供了一种新方法来部署和管理解决方案。 如果使用早期的部署模型并想了解这些更改，请参阅[了解 Resource Manager 部署和经典部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)。

## <a name="azure-network-logging-and-monitoring"></a>Azure 网络日志记录和监视

Azure 提供许多用于监视、防止、检测和响应网络安全事件的工具。 这方面最强大的可用工具（一部分）包括：

-   网络观察程序

-   网络资源级监视

-   Log Analytics

### <a name="network-watcher"></a>网络观察程序

[网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)：网络观察程序中的功能提供基于方案的监视。 此服务包括数据包捕获、下一跃点、IP 流验证、安全组视图和 NSG 流日志。 与单独网络资源的监视不同，方案级监视提供网络资源的端到端视图。

 ![网络观察程序](./media/azure-network-security/azure-network-security-fig-15.png)

网络观察程序是一个区域性的服务，可用于在网络级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。

网络观察程序目前提供以下功能：

#### <a name="topology"></a>拓扑

[拓扑](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview)返回虚拟网络中的网络资源图。 此图描绘了资源之间的相互关系以表示端到端网络连接。 在门户中，拓扑基于每个虚拟网络返回资源对象。 网络观察程序区域外部资源之间的关系用线条绘出（即使这些关系不会显示在资源组中）。 在门户视图中返回的资源是绘制的网络组件的子集。 若要查看网络资源的完整列表，可以使用 [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) 或 [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest)。

返回资源时，会在两种关系下为资源之间的连接建模。

- **包含**：虚拟网络包含子网，子网包含 NIC。

- **关联**：NIC 与 VM 相关联。

#### <a name="variable-packet-capture"></a>可变数据包捕获

使用网络观察程序[可变数据包捕获](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)，可以创建数据包捕获会话，跟踪进出虚拟机的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。

数据包捕获是通过网络观察程序远程启动的虚拟机扩展。 此功能可减轻手动在所需虚拟机上运行数据包捕获的负担，从而可节省宝贵的时间。 可以通过门户、PowerShell、CLI 或 REST API 触发数据包捕获。 触发数据包捕获方式的一个示例是使用虚拟机警报。

#### <a name="ip-flow-verify"></a>IP 流验证

[IP 流验证](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)基于 5 元组信息检查是允许还是拒绝进出虚拟机的数据包。 这些信息包括方向、协议、本地 IP、远程 IP、本地端口和远程端口。 如果数据包被安全组拒绝，则返回拒绝数据包的规则的名称。 虽然可以选择任何源或目标 IP，此功能可帮助管理员快速诊断与 Internet 的连接问题以及与本地环境的连接问题。

“IP 流验证”针对虚拟机的网络接口。 然后，将基于网络接口的配置设置验证流量流。 此功能可用于确认网络安全组中的规则是否正在阻止进出虚拟机的入口或出口流量。

#### <a name="next-hop"></a>下一跃点

确定 Azure 网络结构中路由的数据包的[下一跃点](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)，以便诊断任何错误配置的用户定义路由。 来自 VM 的流量将基于与 NIC 关联的有效路由发送到目标。 “下一跃点”功能可获取来自特定虚拟机和 NIC 的数据包的下一跃点类型和 IP 地址。 这可帮助确定是将数据包定向到目标，还是将流量定向到黑洞。

“下一跃点”功能也返回与下一跃点关联的路由表。 查询下一跃点时，如果路由定义为用户定义的路由，则将返回该路由。 否则，“下一跃点”功能返回“系统路由”。

#### <a name="security-group-view"></a>安全组视图

获取在 VM 上应用的有效安全规则。 网络安全组可以在子网级别或 NIC 级别关联。 在子网级别关联时，网络安全组将应用于子网中的所有 VM 实例。 网络[安全组视图](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview)针对虚拟机返回在 NIC 和子网级别关联的所有已配置 NSG 和规则，以供深入了解配置。 此外，VM 中的每个 NIC 将返回有效的安全规则。 使用网络安全组视图，可以评估网络漏洞的 VM，如打开端口。 还可以基于[已配置规则和有效安全规则之间的比较](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell)，验证网络安全组是否按预期方式工作。

#### <a name="nsg-flow-logging"></a>NSG 流日志记录

 使用网络安全组的流日志可以捕获被组中的安全规则允许或拒绝的流量的相关日志。 流由 5 元组信息（源 IP、目标 IP、源端口、目标端口和协议）定义。

[网络安全组流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)是网络观察程序的一项功能，可用于查看有关通过网络安全组的入口和出口 IP 流量的信息。

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>虚拟网络网关和连接故障排除

网络观察程序提供了许多功能，因为它关系到了解 Azure 中的网络资源。 其中一项功能就是资源故障排除。 可以通过 PowerShell、CLI 或 REST API 调用[资源故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)。 调用后，网络观察程序会检查虚拟网络网关或连接的运行状况，并返回查找结果。

本部分逐步讲解如何完成当前可用于资源故障排除的各种管理任务。

-   [排查虚拟网络网关问题](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [排查连接问题](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>网络订阅限制

[网络订阅限制](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)提供区域中订阅内每个网络资源的用量详细信息，以及可用资源数上限。

#### <a name="configuring-diagnostics-log"></a>配置诊断日志

网络观察程序提供[诊断日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)视图。 此视图包含所有支持诊断日志记录的网络资源。 从此视图中，可以快速方便地启用和禁用网络资源。

### <a name="network-resource-level-monitoring"></a>网络资源级监视

提供以下功能进行资源级监视：

#### <a name="audit-log"></a>审核日志

记录网络配置过程中执行的操作。 这些审核日志对于制定各种合规性策略至关重要。 可在 Azure 门户中查看，或者使用 Power BI 等 Microsoft 工具或第三方工具检索这些日志。 可通过门户、PowerShell、CLI 和 REST API 获取审核日志。

> [!Note]
> 有关审核日志的详细信息，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
针对所有网络资源执行的操作都有相应的审核日志。


#### <a name="metrics"></a>度量值

指标是在一段时间内收集的性能度量值与计数器。 指标目前适用于应用程序网关。 使用指标可以基于阈值触发警报。 默认情况下，Azure 应用程序网关会监视其后端池中所有资源的运行状况，并自动从池中删除任何被视为不正常的资源。 应用程序网关持续监视不正常的实例，一旦这些实例恢复可用状态并能响应运行状况探测，应用程序网关就会将它们添加回到正常的后端池中。 应用程序网关发送的运行状况探测所针对的端口与后端 HTTP 设置中定义的端口相同。 此配置可确保探测所测试的端口即是客户用来连接到后端的端口。

> [!Note]
> 请参阅 [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)（应用程序网关诊断），了解如何使用指标创建警报。

#### <a name="diagnostic-logs"></a>诊断日志

定期和自发性事件由网络资源创建，记录在存储帐户中并发送到事件中心或 Log Analytics。 这些日志提供资源运行状况的见解。 可在 Power BI 和 Log Analytics 等工具中查看这些日志。 若要了解如何查看诊断日志，请访问 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)。

诊断日志适用于[负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)、[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)、路由和[应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。

网络观察程序提供诊断日志视图。 此视图包含所有支持诊断日志记录的网络资源。 从此视图中，可以快速方便地启用和禁用网络资源。

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 是 [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 中的一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。

Log Analytics 提供了用来监视网络的以下解决方案：

-   网络性能监视器 (NPM)

-   Azure 应用程序网关分析

-   Azure 网络安全组分析

#### <a name="network-performance-monitor-npm"></a>网络性能监视器 (NPM)
[网络性能监视器](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)管理解决方案是一个网络监视解决方案，它监视网络的运行状况、可用性和可访问性。

它用来监视以下项之间的连接：

-   公有云与本地

-   数据中心和用户位置（分支机构）

-   托管着多层应用程序的各个层次的子网。


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Log Analytics 中的 Azure 应用程序网关分析

应用程序网关支持以下日志：

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

应用程序网关支持以下指标：

-   5 分钟吞吐量

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Log Analytics 中的 Azure 网络安全组分析

[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)支持以下日志：

- **NetworkSecurityGroupEvent：**包含根据 MAC 地址向 VM 和实例角色应用的 NSG 规则条目。 每隔 60 秒收集一次这些规则的状态。

- **NetworkSecurityGroupRuleCounter：**包含应用每个 NSG 规则以拒绝或允许流量的次数的条目。

## <a name="next-steps"></a>后续步骤
阅读一些深度安全性主题，了解有关安全性的更多信息：

-   [网络安全组 (NSG) 的 Log Analytics](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [推动云瓦解的网络创新](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC：支持 Microsoft 全球云的网络交换机软件](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Microsoft 如何构建快速可靠的全球网络](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [照亮网络创新之路](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
