---
title: 为迁移到 Azure 的工作负荷设置网络的最佳做法 | Microsoft Docs
description: 迁移到 Azure 后，获取为迁移的工作负荷设置网络的最佳做法。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 1493eb6978b00771aa8ed4d8cfc28c37a9dde5b6
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139735"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>为迁移到 Azure 的工作负荷设置网络的最佳做法

在计划和设计迁移时，除迁移本身外，最重要的一个步骤是 Azure 网络的设计和实现。 本文介绍迁移到 Azure 中的 IaaS 和 PaaS 实施时，针对网络的最佳做法。

> [!IMPORTANT]
> 本文介绍的最佳做法和意见以撰写本文时可用的 Azure 平台和服务功能为基础。 各种特性和功能随时间而变化。 可能并非所有建议都适用于你的部署，请选择合适的建议。


## <a name="design-virtual-networks"></a>设计虚拟网络

Azure 提供了虚拟网络 (VNet)：
- Azure 资源以私密、直接且安全的方式与 VNet 通信。
- 可在 VNet 上为需要 Internet 通信的 VM 和服务配置终结点连接。
- VNet 是对专用于订阅的 Azure 云进行的逻辑隔离。
- 可在每个 Azure 订阅和 Azure 区域中实现多个 VNet。
- 每个 VNet 与其他 VNet 隔离。
- VNet 可包含 [RFC 1918](https://tools.ietf.org/html/rfc1918) 中定义的专用和公共 IP 地址，以 CIDR 表示法表示。 无法从 Internet 直接访问公用 IP 地址。
- VNet 可使用 VNet 对等互连相互连接。 连接的 VNet 可位于相同区域，也可位于不同区域。 因此，一个 VNet 中的资源可连接到其他 VNet 中的资源。
- 默认情况下，Azure 在 VNet 中的子网、连接的 VNet、本地网络以及 Internet 之间路由流量。


计划 VNet 拓扑时，需考虑许多事宜，包括如何安排 IP 地址空间，如何实施中心辐射型网络，如何将 VNet 划分为子网，设置 DNS 以及实施 Azure 可用性区域。

## <a name="best-practice-plan-ip-addressing"></a>最佳做法：计划 IP 寻址

在迁移过程中创建 VNet 时，计划 VNet IP 地址空间非常重要。

- 应为每个 VNet 分配一个不超过范围为 /16 的 CIDR 的地址空间。 通过 VNet 可使用 65536 个 IP 地址，且若分配的前缀小于 /16，则会导致 IP 地址丢失。 请勿浪费 IP 地址，即使是 RFC 1918 定义的专有范围内的 IP 地址。
- VNet 地址空间不应与本地网络范围重叠。
- 不应使用网络地址转换 (NAT)。
- 地址重叠可能导致无法连接到网络，且路由无法正常进行。 如果网络重叠，则需重新设计网络或使用网络地址转换 (NAT)。

**了解更多：**

- [概要了解](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure VNet。
- [查看](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)网络的常见问题解答。
- [了解](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)网络限制。


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>最佳做法：实现中心辐射型网络拓扑

中心辐射型网络拓扑在隔离工作负荷的同时共享标识和安全性等服务。
- 该中心为 Azure VNet，充当中心连接点。
- 辐射的是 VNet，使用 VNet 对等互连连接到中心 VNet。
- 共享服务部署在中心，而各个工作负荷则以辐射的形式部署。 

请注意以下几点：
- 在 Azure 中实现中心和辐射拓扑时，可集中管理常见服务，如与本地网络的连接、防火墙以及 VNet 之间的隔离。 中心 VNet 提供了本地网络的中心连接点，以及用于托管在辐射VNet 中托管的工作负荷使用的服务的位置。
- 中心和辐射配置通常由大型企业使用。 较小的网络可能会考虑采用更简单的设计，以节省成本，降低复杂性。
- 辐射 VNet 可用于隔离工作负荷，各辐射独立于其他辐射单独进行管理。 每个工作负荷可包括多个层，以及与 Azure 负载均衡器连接的多个子网。
- 中心和辐射可以在不同的资源组中实现，甚至可以在不同的订阅中实现。 如果对等虚拟网络位于不同的订阅中，两个订阅可关联到同一个或不同的 Azure Active Directory (AD) 租户。 这样，可以对各个工作负荷进行非集中管理，同时在中心网络内维护共享服务。

![变更管理](./media/migrate-best-practices-networking/hub-spoke.png)
中心和辐射拓扑**

**了解更多：**

- [阅读有关](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)中心和辐射拓扑的信息。
- 获取有关运行 Azure [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm#network-recommendations) 和 [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm#network-recommendations) VM 的网络建议。
- [了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) VNet 对等互连。


## <a name="best-practice-design-subnets"></a>最佳做法：设计子网

要在 VNet 中进行隔离，请将其细分为一个或多个子网，并向每个子网分配一部分 VNet 地址空间。
- 可在每个 VNet 中创建多个子网。
- 默认情况下，Azure 在 VNet 中的所有子网之间路由流量。
- 子网决策基于技术和组织要求。  
- 可使用 CIDR 表示法创建子网。
- 确定子网的网络范围时，请务必注意 Azure 保留了每个子网中无法使用的五个 IP 地址。 例如，如果创建 /29 的最小可用子网（具有八个 IP 地址），则 Azure 将保留五个地址，因此只有三个可分配给子网上主机的可用地址。
- 在大多数情况下，建议使用 /28 作为最小子网。

### <a name="example"></a>示例

该表显示了一个 VNet 的示例，其地址空间 10.245.16.0/20 已划分为多个子网，用于计划的迁移。

**子网** | **CIDR** | **地址** | **使用**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | 前端/Web 层 VM
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | 应用层 VM
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | 数据库 VM

**了解更多：**
- [了解有关](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation)设计子网的信息。
- [了解](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure)虚拟公司 (Contoso) 如何为迁移准备其网络基础结构。


## <a name="best-practice-set-up-a-dns-server"></a>最佳做法：设置 DNS 服务器

在部署 VNet 时，Azure 会默认添加 DNS 服务器。 因此可快速构建 VNet 并部署资源。 但此 DNS 服务器仅为该 VNet 上的资源提供服务。 若要将多个 VNet 连接在一起，或从 VNet 连接到本地服务器，则需其他名称解析功能。 例如，可能需要使用 Active Directory 解析虚拟网络间的 DNS 名称。 为此，需在 Azure 中部署自己的自定义 DNS 服务器。

- VNet 中的 DNS 服务器可将 DNS 查询转发到 Azure 的递归解析程序。 这样，便可以解析该 VNet 中的主机名。 例如，在 Azure 中运行的域控制器可以响应自身域的 DNS 查询，而将所有其他查询转发到 Azure。
- 通过 DNS 转发，VM 可通过域控制器查看本地资源，并使用转发器查看 Azure 提供的主机名。 可使用虚拟 IP 地址 168.63.129.16 访问 Azure 中的递归解析程序。
- DNS 转发还可用于在 VNet 之间进行 DNS 解析，可通过本地计算机来解析 Azure 提供的主机名。
    - 若要解析 VM 主机名，DNS 服务器 VM 必须驻留在同一 VNet 中，并且必须配置为将主机名查询转发到 Azure。
    - 由于 DNS 后缀在每个 VNet 中是不同的，因此可使用条件性转发规则将 DNS 查询发送到正确的 VNet 进行解析。
- 使用自己的 DNS 服务器时，可为每个 VNet 指定多个 DNS 服务器。 也可以针对每个网络接口（适用于 Azure 资源管理器）或云服务（适用于经典部署模型）指定多个 DNS 服务器。
- 为网络接口或云服务指定的 DNS 服务器的优先级高于为 VNet 指定的 DNS 服务器。
- 在 Azure 资源管理器部署模型中，可为 VNet 和网络接口指定 DNS 服务器，但最好仅在 VNet 上使用该设置。

    ![DNS 服务器](./media/migrate-best-practices-networking/dns2.png)VNet 的 DNS 服务器

**了解更多：**
- [了解](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure)使用自己的 DNS 服务器时的名称解析。
- [了解](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) DNS 命名规则和限制。


## <a name="best-practice-set-up-availability-zones"></a>最佳做法：设置可用性区域

数据中心发生故障时，可用性区域可提高高可用性，保护应用和数据。

- 可用性区域是 Azure 区域中独特的物理位置。
- 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。
- 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。
- 数据中心发生故障时，区域中的可用性区域的物理隔离可保护应用程序和数据。
- 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。 - - Azure 凭借可用性区域提供 VM 运行时间达 99.99% 的 SLA。

    ![可用性区域](./media/migrate-best-practices-networking/availability-zone.png)可用性区域

- 通过将计算、存储、网络和数据资源共置在一个区域，并将其复制到其他区域，在迁移体系结构中计划并内置高可用性。 支持可用性区域的 Azure 服务分为两类：
    - 区域性服务：将资源与特定区域关联。 如 VM、托管磁盘、IP 地址。
    - 区域冗余服务：自动跨区域复制资源。 如区域冗余存储、Azure SQL 数据库。
- 可部署与面向 Internet 的工作负荷或应用层平衡的标准 Azure 负荷，以提供区域容错。

    ![负载均衡器](./media/migrate-best-practices-networking/load-balancer.png)*负载均衡器*

**了解更多：**
-   [概要了解](https://docs.microsoft.com/azure/availability-zones/az-overview)可用性区域。



## <a name="design-hybrid-cloud-networking"></a>设计混合云网络

要成功迁移，将本地企业网络连接到 Azure 至关重要。 连接后可创建名为混合云网络的始终可用的连接，其中服务从 Azure 云提供给企业用户。 可通过两种方式创建此类网络：

- **站点到站点 VPN：** 在兼容的本地 VPN 设备和 VNet 中部署的 Azure VPN 网关之间建立站点到站点的连接。 所有经授权的本地资源都可访问 VNet。 通过 Internet 经加密隧道发送站点到站点的通信。 
- **Azure ExpressRoute：** 通过 ExpressRoute 合作伙伴在本地网络和 Azure 之间建立 Azure ExpressRoute 连接。 此连接为专用连接，且流量不会通过 Internet。

**了解更多：**

- [详细了解](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn)混合云网络。

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>最佳做法：实现高度可用的站点到站点 VPN

若要实现站点到站点 VPN，请在 Azure 中设置 VPN 网关。
- VPN 网关是特定类型的 VNet 网关，用于跨公共 Internet 在 Azure VNet 和本地位置之间发送加密流量。
- 也可使用 VPN 网关在基于 Microsoft 网络的 Azure VNet 之间发送加密流量。
- 每个 VNet 只能有一个 VPN 网关。
- 可针对相同 VPN 网关创建多个连接。 创建多个连接时，所有 VPN 隧道共享可用的网关带宽。
- 每个 Azure VPN 网关由两个采用主动-待机配置的实例组成。
    - 活动实例发生计划内维护或计划外中断时，会出现故障转移，且会由备用实例自动接替，并恢复站点到站点的连接或 VNet 到 VNet 的连接。 
    - 转换时会导致短暂的中断。
    - 发生计划内维护时，应可在 10 到 15 秒内恢复连接。
    - 发生计划外的问题时，恢复连接所需的时间更长，在最糟的情况下大约需要 1 到 1.5 分钟。
    - 点到站点 (P2S) VPN 客户端与网关的连接将会断开，且用户需从客户端计算机重新连接。

设置站点到站点 VPN 时，请执行以下操作：
 - 需要一个 VNet，其地址范围不会与 VPN 将连接到的本地网络重叠。
 - 在网络中创建网关子网。
 - 创建 VPN 网关，指定网关类型 (VPN) 以及网关是基于策略，还是基于路由。 建议使用 RouteBased VPN，因为该 VPN 更受支持，也更不易过时。
 - 在本地创建本地网络网关，并配置本地 VPN 设备。 
 - 在 VNet 网关和本地设备间创建故障转移站点到站点 VPN 连接。 通过基于路由的 VPN，可实现到 Azure 的主动 - 被动或主动 - 主动连接。 基于路由的 VPN 还同时支持站点到站点的（从任何计算机）连接和点到站点的（从单个计算机）连接。
 - 指定要使用的网关 SKU。 这取决于工作负荷要求、吞吐量、功能和 SLA。
 - 边界网关协议 (BGP) 是一项可选功能，可将其与 Azure ExpressRoute 和基于路由的 VPN 网关一起使用，从而将本地 BGP 路由传播到 VNet。

![VPN](./media/migrate-best-practices-networking/vpn.png)
站点到站点 VPN**
 
**了解更多：**

- [查看](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)兼容的本地 VPN 设备。
- [概要了解](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateway) VPN 网关。
- [了解](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)高可用 VPN 连接。
- [了解有关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)计划和设计 VPN 网关的信息。
- [查看](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) VPN 网关设置。
- [查看](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)网关 SKU。
- [阅读有关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)通过 Azure VPN 网关设置 BGP 的信息。


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>最佳做法：配置 VPN 网关的网关

在 Azure 中创建 VPN 网关时，必须使用名为 GatewaySubnet 的特殊子网。 创建此子网时注意以下这些最佳做法：

- 网关子网的前缀长度最大为 29（例如，10.119.255.248/29）。 目前推荐使用的前缀长度为 27（例如，10.119.255.224/27）。
- 定义网关子网的地址空间时，请使用 VNet 地址空间的最后一部分。
- 使用 Azure GatewaySubnet 时，请勿将任何 VM 或其他设备（如应用程序网关）部署到网关子网。
- 请勿将网络安全组 (NSG) 分配给此子网。 这样会使网关停止运行。

**了解更多：**
- [使用此工具](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed)确定 IP 地址空间。

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>最佳做法：为分支机构实现 Azure 虚拟 WAN

使用多个 VPN 连接时，Azure 虚拟 WAN 可充当网络服务，通过 Azure 提供经优化的、自动的分支到分支连接。
- 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 可手动完成此操作，也可通过虚拟 WAN 合作伙伴使用首选的提供商设备完成此操作。
- 使用首选的提供商设备可降低操作难度、简化连接和进行配置管理。
- Azure WAN 的内置仪表板提供即时故障排除见解，可节省时间，便于跟踪大规模的站点到站点连接。 

**了解详细信息：**
[了解](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) Azure 虚拟 WAN。

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>最佳做法：为任务关键型连接实现 ExpressRoute

借助 Azure ExpressRoute 服务，可通过在虚拟 Azure 数据中心和本地网络间创建专用连接，将本地基础结构扩展到 Microsoft 云。
- ExpressRoute 连接可通过任意位置间的 (IP VPN) 网络，点对点以太网网络或连接提供商。 该连接不会通过公共 Internet。
- ExpressRoute 连接提供更高的安全性、可靠性、速度（最高可达 10 Gbps）和一致的延迟。
- 由于 ExpressRoute 客户可以从与专用连接相关的符合性规则受益，因此 ExpressRoute 对虚拟数据中心十分有用。
- 若需要较高的带宽，则可使用 ExpressRoute Direct 以 100Gbps 的速率直接连接到 Microsoft 路由器。
- ExpressRoute 使用 BGP 在本地网络、Azure 实例和 Microsoft 公共地址之间交换路由。

部署 ExpressRoute 连接通常要与 ExpressRoute 服务提供商合作。 若要快速开始通信，往往需要先使用站点到站点 VPN 在虚拟数据中心与本地资源之间建立连接，然后与服务提供商建立物理互连后迁移到 ExpressRoute 连接。

**了解更多：**
- [概要了解](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ExpressRoute。
- [了解](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) ExpressRoute Direct。

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>最佳做法：优化 ExpressRoute 与 BGP 社区的路由

有多个 ExpressRoute 线路时，可以通过多个路径连接到 Microsoft。 这样一来，所采用的路由并不理想，流量可能会经历较长的路径才能到达 Microsoft，而对于 Microsoft 到网络的流量也同样如此。 网络路径越长，延迟越严重。 延迟对应用性能和用户体验有直接影响。

#### <a name="example"></a>示例

我们来举例说明。

- 你在美国有两个办公室，一个在洛杉矶，一个在纽约。
- 办公室通过 WAN 进行连接，该 WAN 可能是自己的主干网络，也可能是你服务提供商的 IP VPN。
- 有两个 ExpressRoute 线路，一个在美国西部，一个在美国东部，也通过 WAN 连接。 显然，可以通过两个路径连接到 Microsoft 网络。


 
**问题**：现在，假设你在美国西部和美国东部都有 Azure 部署（例如 Azure 应用服务）。
- 你希望每个办公室的用户都访问离他们最近的 Azure 服务以获得最佳体验。
- 因此，你希望将洛杉矶的用户连接到 Azure 美国西部，将纽约的用户连接到 Azure 美国东部。
- 这适用于东海岸的用户，但不适用于西部用户。 存在如下问题：
    - 在每个 ExpressRoute 线路上，我们会播发 Azure 美国东部的前缀 (23.100.0.0/16) 和 Azure 美国西部的前缀 (13.100.0.0/16)。
    - 在不知道哪个前缀来自哪个区域的情况下，不会区别对待各前缀。
    - 你的 WAN 网络会假设相比美国西部，两个前缀更靠近美国东部，从而将两个办公室的用户路由到美国东部的 ExpressRoute 线路，让洛杉矶办公室的用户体验不佳。

![VPN](./media/migrate-best-practices-networking/bgp1.png)
BGP 社区未优化的连接**

**解决方案**

要优化两个办公室的用户的路由，需要知道哪个前缀来自 Azure 美国西部，哪个前缀来自 Azure 美国东部。 可使用 BGP 社区值对此信息进行编码。
- 向每个 Azure 区域分配唯一的 BGP 社区值。 例如，为美国东部分配 12076:51004，为美国西部分配 12076:51006。
- 现在弄清前缀所属的 Azure 区域后，即可配置首选 ExpressRoute 线路。
- 由于使用 BGP 来交换路由信息，因此可使用 BGP 的本地首选项来影响路由。
- 在本示例中，相比美国东部，在美国西部为 13.100.0.0/16 分配的本地优先级值更高，同样，对于 23.100.0.0/16，在美国东部分配的本地优先级值高于美国西部。 
- 此配置可确保两条 Microsoft 路径都可用时，洛杉矶的用户将使用西部线路连接到 Azure 美国西部，而纽约用户则使用东部线路连接到 Azure 美国东部。 两边的路由都获得了优化。

![VPN](./media/migrate-best-practices-networking/bgp2.png)
BGP 社区优化的连接**


**了解更多：**
- [了解](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing)优化路由

## <a name="securing-vnets"></a>保护 Vnet

保护 VNet 是 Microsoft 与你共同的责任。 Microsoft 提供了许多网络功能，以及有助于保护资源的服务。 设计针对 VNet 的安全措施时，应遵循许多最佳做法，包括实现外围网络，使用筛选和安全组，保护资源和 IP 地址的访问权限以及实施攻击保护。

**了解更多：**

- [概要了解](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)保护网络的最佳做法。
- [了解如何](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security)设计安全的网络。

## <a name="best-practice-implement-an-azure-perimeter-network"></a>最佳做法：实现 Azure 外围网络

尽管 Microsoft 为保护云基础结构投入了大量资金，但你仍必须保护自己的云服务和资源组。 安全的多层方案提供最佳的防御措施。 采用外围网络是该防御策略的一个重要部分。

- 外围网络可防止不受信任的网络访问内部网络资源。 
- 这是暴露在 Internet 上的最外层网络。 一般位于 Internet 和企业基础结构之间，通常为双方提供某种形式的保护。 
- 在典型的企业网络拓扑中，核心基础结构的周边有多层的安全设备严加保护。 每一层的边界由设备和策略实施点组成。
- 每一层都可能包括网络安全解决方案的组合，其中包括防火墙、拒绝服务 (DoS) 预防、入侵检测/入侵保护系统 (IDS/IPS)，以及 VPN 设备。
- 可使用防火墙策略、访问控制列表 (ACL) 或特定路由在外围网络上实施策略。
- Internet 传入流量到达时，防御解决方案组合会拦截并处理该流量，阻止攻击和有害流量，同时允许合法请求进入网络。
- 传入流量可直接路由到外围网络中的资源。 随后，外围网络资源可与网络中更深处的其他资源通信，验证流量后，将流量向前移动到网络中。

下图显示了企业网络中的单子网外围网络示例，其中有两个安全边界。

![VPN](./media/migrate-best-practices-networking/perimeter.png)
外围网络部署**

**了解更多：**
- [了解有关](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)在 Azure 和本地数据中心间部署外围网络的信息。


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>最佳做法：使用 NSG 筛选 VNet 流量

网络安全组 (NSG) 包含多个入站和出站安全规则，可筛选进出资源的流量。 可按源和目标 IP 地址、端口和协议进行筛选。 
- NSG 包含安全规则，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 可以为每项规则指定源和目标、端口以及协议。
- 将使用 5 元组信息（源、源端口、目标、目标端口和协议）按优先级对 NSG 规则进行评估来允许或拒绝流量。
- 将为现有连接创建流记录。 是允许还是拒绝通信取决于流记录的连接状态。
- 通过流记录，可使 NSG 处于不同状态。 例如，如果通过端口 80 为任何地址指定出站安全规则，则不需要入站安全规则来响应出站流量。 如果通信是从外部发起的，则只需指定入站安全规则。
- 反之亦然。 如果允许通过某个端口发送入站流量，则不需要指定出站安全规则来响应通过该端口发送的流量。
- 删除启用了流的安全规则时，现有连接不会中断。 当连接停止，且至少几分钟内在任一方向都没有流量流过时，流量流会中断。
- 创建 NSG 时，请尽量减少创建的数量，但必须尽可能创建所需 NSG。

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>最佳做法：保护南/北和东/西流量

保护 VNet 时，考虑攻击途径很重要。
- 仅使用子网 NSG 可简化环境，但只能保护进入子网的流量。 这被称为南/北流量。
- 同一子网上的 VM 之间的流量称为东/西流量。
- 务必利用这两种形式的保护，这样如果黑客从外部获得了访问权限，那么在尝试连接位于同一子网中的计算机时，会遭到拦截。

### <a name="use-service-tags-on-nsgs"></a>在 NSG 上使用服务标记

服务标记表示一组 IP 地址前缀。 使用服务标记有助于在创建 NSG 规则时最大限度地降低复杂性。
- 创建规则时，可使用服务标记代替特定的 IP 地址。
- Microsoft 会管理与服务标记相关的地址前缀，并会在地址发生更改时自动更新服务标记。
- 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。

采用服务标记后，无需手动将规则分配到 Azure 服务组。 例如，如果要允许包含 Web 服务器的 VNet 子网访问 Azure SQL 数据库，则可为端口 1433 创建出站规则，并使用 Sql 服务标记。
- 此 Sql 标记表示 Azure SQL 数据库和 Azure SQL 数据仓库服务的地址前缀。
- 如果指定 Sql 作为值，则会允许或拒绝发往 Sql 的流量。
- 如果只想允许对某个特定区域中的 Sql 进行访问，可以指定该区域。 例如，如果希望只允许访问美国东部区域中的 Azure SQL 数据库，则可指定 Sql.EastUS 作为服务标记。
- 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。
- 由此标记表示的所有地址前缀也由 **Internet** 标记表示。


**了解更多：**

- [阅读有关](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview) NSG 的信息。
- [查看](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) NSG 的可用服务标记。


## <a name="best-practice-use-application-security-groups"></a>最佳做法：使用应用程序安全组

通过应用程序安全组，可将网络安全配置为应用结构的自然扩展。

- 可根据应用程序安全组对 VM 进行分组并确定网络安全策略。
- 通过应用程序安全组，可大量重复使用安全策略，而无需手动维护显式 IP 地址。
- 应用程序安全组会处理显式 IP 地址和多个规则集存在的复杂性，让你专注于业务逻辑。 

### <a name="example"></a>示例

![应用程序安全组](./media/migrate-best-practices-networking/asg.png)
应用程序安全组示例**

**网络接口** | **应用程序安全组**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- 在本示例中，每个网络接口仅属于一个应用程序安全组，但实际上，根据 Azure 限制，一个接口可属于多个组。
- 这些网络接口都没有关联的 NSG。 NSG1 关联到两个子网，包含以下规则。

    **规则名称** | **用途** | **详细信息**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | 让流量从 Internet 流到 Web 服务器。 来自 Internet 的入站流量被 DenyAllInbound 默认安全规则拒绝，因此 AsgLogic 或 AsgDb 应用程序安全组不需其他规则。 | 优先级：100<br/><br/> 源：Internet<br/><br/> 源端口：*<br/><br/> 目标：AsgWeb<br/><br/> 目标端口：80<br/><br/> 协议：TCP<br/><br/> 访问：允许。
    Deny-Database-All | AllowVNetInBound 默认安全规则允许在同一 VNet 中的资源之间进行的所有通信，需使用此规则来拒绝来自所有资源的流量。 | 优先级：120<br/><br/> 源：*<br/><br/> 源端口：*<br/><br/> 目标：AsgDb<br/><br/> 目标端口：1433<br/><br/> 协议：All<br/><br/> 访问：拒绝。
    Allow-Database-BusinessLogic | 允许从 AsgLogic 应用程序安全组到 AsgDb 应用程序安全组的流量。 此规则的优先级高于 Deny-Database-All 规则，会优先处理此规则，这样系统就会允许来自 AsgLogic 应用程序安全组的流量，而阻止所有其他流量。 | 优先级：110<br/><br/> 源：AsgLogic<br/><br/> 源端口：*<br/><br/> 目标：AsgDb<br/><br/> 目标端口：1433<br/><br/> 协议：TCP<br/><br/> 访问：允许。

- 将应用程序安全组指定为源或目标的规则只会应用到属于应用程序安全组成员的网络接口。 如果网络接口不是应用程序安全组的成员，则规则不会应用到网络接口，即使网络安全组关联到子网。

**了解更多：**

- [了解](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)应用程序安全组。


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>最佳做法：使用 VNet 服务终结点安全访问 PaaS

VNet 服务终结点可通过直接连接将 VNet 专有地址空间和标识扩展到 Azure 服务。

- 使用终结点可确保仅自己的 VNet 可访问关键的 Azure 服务资源。 从 VNet 发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。
- VNet 专用地址空间可能重叠，因此不能用于唯一标识源自 VNet 的流量。
- 在 VNet 中启用服务终结点后，可通过将 VNet 规则添加到服务资源来保护 Azure 服务资源。 这完全消除了通过公共 Internet 对资源进行访问的可能性，并仅允许来自自己 VNet 的流量，从而提高了安全性。

![服务终结点](./media/migrate-best-practices-networking/endpoint.png)
服务终结点**

**了解更多：**

- [了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) VNet 服务终结点。


## <a name="best-practice-control-public-ip-addresses"></a>最佳做法：控制公共 IP 地址

Azure 中的公共 IP 地址可与 VM、负载均衡器、应用程序网关和 VPN 网关相关联。

- 公共 IP 地址允许 Internet 资源以入站方式与 Azure 资源通信，并允许 Azure 资源以出站方式与 Internet 通信。
- 使用基本或标准 SKU 创建公共 IP 地址，地址间存在许多差异。 可将标准 SKU 分配给任何服务，但它通常配置在 VM、负载均衡器和应用程序网关上。
- 请务必注意，基本公共 IP 地址不会自动配置 NSG。 需自行配置并分配规则来控制访问。 会默认为标准 SKU IP 地址分配 NSG 和规则。
- 最好不为 VM 配置公共 IP 地址。
    - 如需打开的端口，则只应是针对 Web 服务的端口，如端口 80 或 443 等。
    - 应使用 NSG 将标准远程管理端口（如 SSH (22) 和 RDP (3389)）与其他所有端口一起设置为拒绝。
- 更好的做法是将 VM 置于 Azure 负载均衡器或应用程序网关后。 然后，如需访问远程管理端口，则可在 Azure 安全中心中使用实时 VM 访问。

**了解更多：**

- [了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) Azure 中的公共 IP 地址。
- [详细了解](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) Azure 安全中心中的实时 VM 访问。


## <a name="leverage-azure-security-features-for-networking"></a>利用针对网络的 Azure 安全功能

Azure 具有易于使用的平台安全功能，并提供多种针对常见网络攻击的对策。 其中包括 Azure 防火墙、Web 应用程序防火墙和网络观察程序。

## <a name="best-practice-deploy-azure-firewall"></a>最佳做法：部署 Azure 防火墙

Azure 防火墙是托管的、基于云的网络安全服务，可保护 VNet 资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。

![服务终结点](./media/migrate-best-practices-networking/firewall.png)
Azure 防火墙**

- 在 Azure 防火墙中，可跨订阅和 VNet 集中创建、实施和记录应用程序与网络连接策略。
- Azure 防火墙对 VNet 资源使用静态公共 IP 地址，使外部防火墙能够识别来自你的 VNet 的流量。
- Azure 防火墙与用于日志记录和分析的 Azure Monitor 完全集成。
- 创建 Azure 防火墙规则时，最佳做法是使用 FQDN 标记创建规则。
    - FQDN 标记表示与已知的 Microsoft 服务关联的一组 FQDN。
    - 可使用 FQDN 标记，以允许所需出站网络流量通过防火墙。
- 例如，若要手动允许 Windows 更新网络流量通过防火墙，需要创建多个应用程序规则。 使用 FQDN 标记可创建应用程序规则，并在其中包括 Windows 更新标记。 采用此规则后，Microsoft Windows 更新终结点的网络流量可流经防火墙。

**了解更多：**

- [概要了解](https://docs.microsoft.com/azure/firewall/overview) Azure 防火墙。
- [了解](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 标记。


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>最佳做法：部署 Azure Web 应用程序防火墙 (WAF)

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 这些攻击包括 SQL 注入攻击和跨站点脚本攻击。 防止应用程序代码遭受此类攻击颇具挑战性，并且需要对应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全管理，并帮助应用管理员抵卸威胁或入侵。 相较保护每个单独的 Web 应用程序，Web 应用防火墙可通过在中央位置修补已知漏洞，更快地响应安全威胁。 可将现有应用程序网关轻松转换为支持 Web 应用程序防火墙的应用程序网关。

Azure Web 应用程序防火墙 (WAF) 是 Azure 应用程序网关的一项功能。
- WAF 可在出现常见攻击和漏洞时为 Web 应用程序提供集中保护。
- WAF 提供保护时不会修改后端代码。
- 它可在应用程序网关背后同时保护多个 Web 应用
- WAF 已与 Azure 安全中心集成。
- 您可自定义 WAF 规则和规则组，满足应用程序的要求。
- 最好应在所有面向 Web 的应用上使用 WAF，包括 Azure VM 上的应用程序或 Azure 应用服务。

**了解更多：**
- [了解](https://docs.microsoft.com/azure/application-gateway/waf-overview) WAF。
- [查看](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration)有关 WAF 限制和例外的信息。


## <a name="best-practice-implement-azure-network-watcher"></a>最佳做法：实现 Azure 网络观察程序

Azure 网络观察程序提供了用于监视 Azure VNet 中的资源和通信的工具。 例如，可监视 VM 与终结点（如另一 VM 或 FQDN）间的通信，查看 VNet 中的资源和资源关系，或诊断网络流量问题。

![网络观察程序](./media/migrate-best-practices-networking/network-watcher.png)
网络观察程序**

- 借助网络观察程序无需登录 VM 即可监视和诊断网络问题。
- 可通过设置警报触发数据包捕获，并获取数据包级别上的实时性能信息访问权限。 如果遇到问题，可进行详细调查。
- 最好应使用网络观察程序来查看 NSG 流日志。
    - 网络观察程序中的 NSG 流日志可用于查看有关通过 NSG 的入口和出口 IP 流量的信息。
    - 流日志以 JSON 格式编写。
    - 流日志基于每个规则显示出站和入站流、流所适用的网络接口 (NIC)、有关流的 5 元组信息（源/目标 IP 地址、源/目标端口和协议），以及是允许还是拒绝流量。

**了解更多：**

- [概要了解](https://docs.microsoft.com/azure/network-watcher)网络观察程序。
- [详细了解](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) NSG 流日志。

## <a name="use-partner-tools-in-the-azure-marketplace"></a>在 Azure 市场中使用合作伙伴工具

对于更复杂的网络拓扑，可使用 Microsoft 合作伙伴的安全产品，尤其是网络虚拟设备 (NVA)。

- NVA 是可执行网络功能（例如防火墙、WAN 优化等）的 VM。
- NVA 可为 VNet 安全和网络功能提供支持。 可部署 NVA，将其用于高可用性防火墙、入侵防御、入侵检测、Web 应用程序防火墙 (WAF)、WAN 优化、路由、负载均衡、VPN、证书管理、Active Directory 和多因素身份验证。
- 可从  [Azure 市场](https://azuremarketplace.microsoft.com/)中的众多供应商处获得 NVA。 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>最佳做法：在中心网络中实施防火墙和 NVA

在中心内，可访问 Internet 的外围网络通常是通过 Azure 防火墙、防火墙场或 Web 应用程序防火墙 (WAF) 管理的。 请考虑以下对比情况。

**防火墙类型** | **详细信息**
--- | ---
WAF | Web 应用十分常见，容易出现漏洞和遭受潜在攻击。<br/><br/> WAF 旨在检测针对 Web 应用程序 (HTTP/HTTPS) 的攻击，较通用防火墙更为具体。<br/><br/> 与传统防火墙技术相比，WAF 拥有一组用于保护内部 Web 服务器免受威胁的特定功能。
Azure 防火墙 | Azure 防火墙与 NVA 防火墙场类似，防火墙使用通用管理机制，以及一组安全规则来保护辐射网络中托管的工作负荷，并控制对本地网络的访问。<br/><br/> Azure 防火墙内置了可扩展性。
NVA 防火墙 | NVA 防火墙场与 Azure 防火墙类似，防火墙场具有通用管理机制，以及一组安全规则，可保护辐射网络中托管的工作负荷，并控制对本地网络的访问。<br/><br/> 可在负载均衡器后手动缩放 NVA 防火墙。<br/><br/> 与 WAF 相比，尽管 NVA 防火墙场的专用软件更少，但应用范围广泛，可以筛选和检查流出和流入的任何流量类型。<br/><br/> 若要使用 NVA，可在 Azure 市场中获取。

建议对源自 Internet 的流量使用一组 Azure 防火墙（或 NVA），对源自本地的流量使用另一组 Azure 防火墙。
- 对两组网络流量使用一组防火墙会导致安全风险，因为它无法在两组网络流量之间提供安全外围。
- 使用不同防火墙层可以降低检查安全规则的复杂性，并指明哪项规则与哪项传入网络请求对应。

**了解更多：**
- [了解有关](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)在 Azure VNet 中使用 Nva 的信息。

## <a name="next-steps"></a>后续步骤 

查看其他最佳做法：

- 迁移后安全和管理的[最佳做法](migrate-best-practices-security-management.md)。
- 有关迁移后的成本管理的[最佳做法](migrate-best-practices-costs.md)。
