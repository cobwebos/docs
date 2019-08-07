---
title: 在 Azure 澳大利亚中控制入站流量
description: 在 Azure 澳大利亚中控制流量的指南, 以满足澳大利亚政府对安全 Internet 网关的要求
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 3885b9fa346047a50a49c7b2f9b96b6a8f95e51f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779351"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>在 Azure 澳大利亚中控制入站流量

保护 ICT 系统的核心元素是控制网络流量。 应将流量限制为仅供系统使用, 以减少危害的可能性。

本指南提供了有关如何在 Azure 中使用入站 (入口) 网络流量的详细信息, 以及为连接 internet 的系统实施网络安全控制的建议。

网络控制与澳大利亚网络安全中心 (ACSC) 使用者指南以及 ACSC 的信息安全手册 (ISM) 的意图相符。

## <a name="requirements"></a>要求

在 ISM 中定义了英联邦系统的整体安全要求。 为了帮助实现网络安全方面的英联邦实体, ACSC 已[发布 ACSC 保护:实现网络分段和隔离](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm), 并帮助保护云环境中的系统 ACSC 已发布[适用于租户的云计算安全性](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf)。

这些指南概述了用于实现网络安全和控制流量的上下文, 并包括有关网络设计和配置的实用建议。

服务信任门户的 "澳大利亚" 页中的 "Microsoft[云计算安全性" Microsoft Azure](https://aka.ms/au-irap)指南中, 重点介绍了可用于满足 ACSC 发布中的建议的特定 Microsoft 技术。

ACSC 的发布中标识的以下关键要求对于控制 Azure 中的入口流量非常重要:

|描述|Source|
|---|---|
|**使用基于主机的防火墙和 CSP 的网络访问控制来实现网络分段和隔离, 例如, 使用基于主机的防火墙和 CSP 的网络访问控制, 将入站和出站 VM 网络连接限制为仅限所需端口/协议。**| _租户的云计算_|
|在租户 (包括租户的远程用户) 和云服务之间**实现充足的高带宽、低延迟、可靠网络连接**, 以满足租户的可用性要求  | _租户的云计算_|
|**不只是在网络层上应用技术**。 在可能的情况下, 应尽可能地对每个主机和网络进行分段和隔离。 在大多数情况下, 分段和隔离将从数据链路层应用到, 并包括应用程序层;但是, 在敏感环境下, 物理隔离可能适用。 基于主机和网络范围的度量值应以互补的方式进行部署并进行集中监视。 使用防火墙或安全设备是唯一的安全措施。 |_ACSC 保护:实现网络分段和隔离_|
|**使用最低权限原则, 并需要‐到‐** 。 如果主机、服务或网络不需要与另一台主机、服务或网络通信, 则不应允许这样做。 如果主机、服务或网络仅需要使用特定端口或协议与其他主机、服务或网络通信, 则应禁用任何其他端口或协议。 在网络中采用这些原则将补充用户权限的 minimisation, 并显著提高环境的总体安全状况。 |_ACSC 保护:实现网络分段和隔离_|
|**根据主机和网络的敏感性或关键性, 将它们分隔到业务运营**。 可以使用不同的硬件或平台来实现分离, 具体取决于不同的安全分类、安全域或某些主机或网络的可用性/完整性要求。 具体而言, 单独的管理网络, 并考虑对敏感环境进行物理隔离的带外管理网络。 |_ACSC 保护:实现网络分段和隔离_|
|**标识、身份验证和授权所有实体对所有其他实体的访问权限**。 所有用户、主机和服务都应将其访问权限限制为仅限完成其指定职责或功能所需的其他用户、主机和服务。 应禁用跳过或降级标识、身份验证和授权服务强度的所有旧或本地服务, 并应密切监视其使用情况。 |_ACSC 保护:实现网络分段和隔离_|
|**实现允许列表网络流量, 而不是拒绝列表**。 仅允许访问已知良好的网络通信 (即, 对其进行标识、身份验证和授权), 而不是拒绝对已知的不良网络通信 (例如, 阻止特定地址或服务) 的访问。 允许列表导致列入方块列表的高级安全策略, 并显著提高了组织的容量来检测和评估潜在的网络入侵。 |_ACSC 保护:实现网络分段和隔离_|
|

本文介绍了有关如何使用基础结构即服务 (IaaS) 和平台即服务 (PaaS) 在 Azure 中部署的系统如何满足这些要求的信息和建议。 还应阅读有关在[Azure 澳大利亚控制出口流量](gateway-egress-traffic.md)的文章, 以充分了解如何控制 azure 中的网络流量。

## <a name="architecture"></a>体系结构

如果涉及网络安全和入口流量控制的设计或实现, 则必须首先了解 IaaS 和 PaaS 中的网络流量在 Azure 中的工作原理。 本部分概述了可能的入口点, 其中网络流量可以到达 Azure 中托管的资源, 而安全控件可用于限制和控制该流量。 本指南的其余部分详细讨论了其中的每个组件。

### <a name="architecture-components"></a>体系结构组件

此处显示的体系结构关系图描述了网络流量可用于连接到 Azure 中托管的服务的可能路径。 这些组件分为 Azure、IaaS 入口、PaaS 入口和安全控制措施, 具体取决于为入口流量提供的功能。

![体系结构](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure 组件

|组件 | 描述|
|---|---|
|**DDoS 保护** | 分布式拒绝服务 (DDoS) 攻击尝试耗尽应用程序的资源, 使应用程序对于合法用户不可用。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。 Azure 通过 Azure 平台自动包含 DDoS 保护, 并提供额外的缓解功能, 可为特定应用程序启用该功能以实现更精细的控制。|
| **流量管理器** | Azure 流量管理器是基于域名系统 (DNS) 的流量负载均衡器, 它可以将流量以最佳方式分配到跨 Azure 区域的服务, 同时提供高可用性和响应能力。 流量管理器根据流量路由方法和终结点的运行状况, 使用 DNS 将客户端请求定向到最合适的终结点。|
| **ExpressRoute** | ExpressRoute 是用于使用 Microsoft 云服务的专用网络连接。 它通过连接提供商进行预配, 提供更高的可靠性、更快的速度、更低的延迟, 以及比通过 Internet 的典型连接更高的安全性。 ExpressRoute 线路表示通过连接提供商在本地基础结构与 Microsoft 云服务之间建立的逻辑连接。|
| **ExpressRoute 专用对等互连** | ExpressRoute 专用对等互连是本地环境与专用 Azure 虚拟网络之间的连接。 通过专用对等互连, 可以访问在虚拟网络中部署的 Azure 服务 (例如虚拟机)。 通过专用对等互连访问的资源和虚拟网络被视为组织核心网络的扩展。 专用对等互连使用专用 IP 地址在本地网络和 Azure 虚拟网络之间提供双向连接。|
| **ExpressRoute Microsoft 对等互连** | ExpressRoute Microsoft 对等互连是本地环境与 Microsoft 和 Azure 公共服务之间的连接。 这包括与 Office 365、Dynamics 365 和 Azure PaaS 服务的连接。 对等互连是通过企业或连接提供商拥有的公共 IP 地址建立的。 默认情况下, 不能通过 ExpressRoute Microsoft 对等互连访问任何服务, 组织必须选择加入所需的服务。 然后, 此过程提供与 Internet 上可用的相同终结点的连接。|
|

### <a name="iaas-ingress-components"></a>IaaS 入口组件

|组件 | 描述|
|---|---|
|**网络接口** | 网络接口是 Azure 中存在的资源。 它连接到虚拟机, 并为其关联的子网中的专用非 Internet 可路由 IP 地址分配。 此 IP 地址是通过 Azure 资源管理器动态或静态分配的。|
|**子网** | 子网是在 VNet 中创建的 IP 地址范围。 可以在 VNet 中创建多个子网, 以便进行网络分段。|
| **虚拟网络 (VNet)** | VNet 是 Azure 中的基础资源, 它提供用于部署资源和启用通信的平台和边界。 VNet 存在于 Azure 区域中, 并为 VNet 集成资源 (例如虚拟机) 定义 IP 地址空间和路由边界。|
| **VNet 对等互连** | VNet 对等互连是一种 Azure 配置选项, 可实现两个 Vnet 之间的直接通信, 无需虚拟网络网关。 对等互连后, 这两个 Vnet 可以直接通信, 而其他配置可控制虚拟网络网关和其他传输选项的使用。|
| **公共 IP** | 公共 IP 是一种资源, 用于保留 Microsoft 拥有的、可通过 Internet 路由的 IP 地址, 这些地址来自指定的区域, 可在虚拟网络中使用。 它可以与特定网络接口相关联, 这使资源可从 Internet、ExpressRoute 和 PaaS 系统进行访问。|
| **ExpressRoute 网关** | ExpressRoute 网关是虚拟网络中的一个对象, 它通过 ExpressRoute 线路上的专用对等互连, 提供从虚拟网络到本地网络的连接和路由。|
| **VPN 网关** | VPN 网关是虚拟网络中的一个对象, 它提供从虚拟网络到外部网络的加密隧道。 加密隧道可以是站点到站点的, 用于与本地环境、其他虚拟网络或云环境进行双向通信, 或使用点到站点来与单个终结点通信。|
| **PaaS VNet 集成** | 许多 PaaS 功能可部署到虚拟网络或与虚拟网络集成。 某些 PaaS 功能可以与 VNet 完全集成, 并且只能通过专用 IP 地址进行访问。 其他人 (如 Azure 负载均衡器和 Azure 应用程序网关) 可以具有一个具有公共 IP 地址的外部接口和一个在虚拟网络中具有专用 IP 地址的内部接口。 在此示例中, 流量可以通过 PaaS 功能进入虚拟网络。|
|

### <a name="paas-ingress-components"></a>PaaS 入口组件

|组件 | 描述|
|---|---|
|**主机名** | Azure PaaS 功能由创建资源时分配的唯一公共主机名标识。 然后, 将此主机名注册到公共 DNS 域, 可在其中将该主机名解析为公共 IP 地址。|
|**公共 IP** | 除非部署在 VNet 集成配置中, 否则可通过可通过 Internet 路由的公共 IP 地址访问 Azure PaaS 功能。 此地址可专用于特定资源 (如公共负载均衡器), 也可以与具有多个实例 (例如存储或 SQL) 的共享入口点的特定功能关联。 此公共 IP 地址可通过 Internet、ExpressRoute 或通过 Azure 主干网络从 IaaS 公共 IP 地址进行访问。|
|**服务终结点** | 服务终结点提供从虚拟网络到特定 PaaS 功能的直接专用连接。 服务终结点 (仅适用于 PaaS 功能的子集) 为访问 PaaS 的 VNet 中的资源提供了更高的性能和安全性。|
|

### <a name="security-controls"></a>安全控件

|组件 | 描述|
|---|---|
|**网络安全组 (Nsg)** | Nsg 控制进出 Azure 中的虚拟网络资源的流量。 Nsg 应用允许或拒绝的流量的规则, 其中包括 Azure 中的流量以及 Azure 和外部网络 (如本地或 Internet) 之间的流量。 Nsg 应用于虚拟网络中的子网或单个网络接口。|
|**PaaS 防火墙** | 许多 PaaS 功能 (如存储和 SQL) 都具有内置防火墙, 用于控制到特定资源的入口网络流量。 可以创建规则以允许或拒绝来自特定 IP 地址和/或虚拟网络的连接。|
|**PaaS 身份验证和访问控制** | 作为安全的分层方法的一部分, PaaS 功能提供多种机制来对用户进行身份验证以及控制权限和访问权限。|
|**Azure Policy** | Azure 策略是 Azure 中的一项服务, 可用于创建、分配和管理策略。 这些策略使用规则来控制可部署的资源类型以及这些资源的配置。 如果资源不满足要求或可用于监视以报告符合性状态, 则可以使用策略来强制实施符合性。|
|

## <a name="general-guidance"></a>通用指南

若要在 Azure 中设计和构建安全解决方案, 了解和控制网络流量至关重要, 以便只能进行标识的和授权的通信。 本指南的目的和后面几节中的特定组件指南旨在说明可以能否的工具和服务, 以应用_ACSC 保护中所述的原则:跨 Azure 工作负荷实施_网络分段和隔离。 这包括详细介绍如何创建虚拟体系结构来保护资源, 前提是不能应用在本地环境中可能存在的相同传统物理和网络控件。

### <a name="specific-focus-areas"></a>特定重点领域

* 将入口点的数量限制为虚拟网络
* 限制公共 IP 地址的数目
* 如 Microsoft 虚拟数据中心 (VDC) 文档中所述, 请考虑 utilising 虚拟网络的中心和辐射网络设计
* 为来自 Internet 的入站连接提供内置安全功能的利用产品 (例如, 应用程序网关、API 网关、网络虚拟设备)
* 将通信流限制为 PaaS 功能, 只允许系统功能必需的通信
* 在 VNet 集成配置中部署 PaaS 以增加隔离和控制
* 将系统配置为在 ACSC 使用者指南和 ISM 中使用加密机制
* 除了传统网络控制以外, 还可以使用基于标识的保护, 例如身份验证和基于角色的访问控制
* 实现 ExpressRoute 以连接到本地网络
* 实现管理通信的 Vpn 和与外部网络的集成
* 利用 Azure 策略, 以将区域和资源仅限于系统功能所必需的区域和资源
* 利用 Azure 策略, 为可访问 internet 的资源强制实施基准安全配置

### <a name="additional-resources"></a>其他资源

|Resource | 链接|
|---|---|
|澳大利亚法规和政策符合性文档, 包括使用者指南|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 虚拟数据中心|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC 网络分段|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|租户的 ACSC Cloud Security| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC 信息安全手册|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>组件指南

本部分提供有关与在 Azure 中部署的系统的流量相关的各个组件的更多指导。 每节描述特定组件的用途, 其中包含指向文档和配置指南的链接, 可用于帮助进行设计和生成活动。

## <a name="azure"></a>Azure

与 Azure 中的资源的所有通信都通过 Microsoft 维护的网络基础结构, 提供了连接和安全功能。 Microsoft 将自动就地保护一系列保护, 以保护 Azure 平台和网络基础结构, 并在 Azure 中提供附加功能作为服务来控制网络流量并建立网络分段和分离.

### <a name="ddos-protection"></a>DDoS 保护

可通过 Internet 访问的资源容易遭受 DDoS 攻击。 为了防范这些攻击, Azure 提供了一个基本和标准级别的 DDoS 保护。

基本功能作为 Azure 平台的一部分自动启用, 包括 always on 流量监视和常见网络级别攻击的实时缓解, 同时提供与 Microsoft 联机服务相同的 defences 能否。 整个 Azure 全球网络的规模可用于跨区域分散和缓解攻击流量。 为 IPv4 和 IPv6 Azure 公共 IP 地址提供保护

标准在专门针对 Azure 虚拟网络资源进行优化的基本服务层上提供其他缓解功能。 通过专用流量监控和机器学习算法优化保护策略。 为 IPv4 Azure 公共 IP 地址提供保护。

|Resource|链接|
|---|---|
|Azure DDoS 保护概述|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Azure DDoS 最佳实践|[https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices)|
|管理 DDoS 保护|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>通信管理器

流量管理器用于通过控制应用程序接收连接的终结点来管理入口流量。 为了防止因网络安全攻击而导致系统或应用程序的可用性丢失, 或在系统遭到破坏后恢复服务, 流量管理器可用于将流量重定向到正常运行的应用程序实例。

|Resource|链接|
|---|---|
|流量管理器概述 | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|使用 Azure DNS 和流量管理器指南进行灾难恢复 | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute 可用于建立从本地环境到托管在 Azure 中的系统的专用路径。 此连接可以提供更高的可靠性和保证的性能, 同时增强了网络通信的隐私性。 Express Route 允许多英实体控制来自本地环境的入站流量, 并定义特定于组织的专用地址, 以用于入站防火墙规则和访问控制列表。

|Resource | 链接|
|---|---|
|ExpressRoute 概述 | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|ExpressRoute 连接模型 | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>ExpressRoute 专用对等互连

专用对等互连提供一种机制, 用于仅使用专用 IP 地址将本地环境扩展到 Azure 中。 这使得可英的实体可将 Azure 虚拟网络和地址范围与现有的本地系统和服务相集成。 专用对等互连保证了 ExpressRoute 间的通信仅适用于组织授权的虚拟网络。 如果使用专用对等互连, 则无人需要的用户必须实施网络虚拟设备 (NVA) 而不是 Azure VPN 网关, 以根据 ACSC 使用者指南的要求, 建立与本地网络的安全 VPN 通信。

|Resource | 链接|
|---|---|
|ExpressRoute 专用对等互连概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute 专用对等互连操作指南 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute Microsoft 对等互连

Microsoft 对等互连提供与 Microsoft 公共服务的高速、低延迟连接, 无需遍历 Internet。 这为连接提供了更高的可靠性、性能和私密性。 通过使用路由筛选器, 英联邦实体可以将通信限制为仅限它们所需的 Azure 区域, 但这包括其他组织托管的服务, 并可能需要其他筛选或检查功能,本地环境和 Microsoft。

英联邦实体可以使用通过对等互连建立的专用公共 IP 地址来唯一标识本地环境, 以便在防火墙和 PaaS 功能内的访问控制列表中使用。

作为替代方法, 无英的实体可以使用 ExpressRoute Microsoft 对等互连作为是网络, 通过 Azure VPN 网关建立 VPN 连接。 在此模型中, 从内部本地网络到 Azure 公共服务的本地通信没有通过 ExpressRoute 进行的活动通信, 而是通过与 ACSC 使用者指南兼容来实现的安全连接。

|Resource | 链接|
|---|---|
|ExpressRoute Microsoft 对等互连概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute Microsoft 对等互连操作指南 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS 入口

本部分提供了用于控制向 IaaS 组件发送流量的组件指导。 IaaS 包含虚拟机和其他可在 Azure 网络流入量 Azure 中部署和管理的计算资源。 要使流量到达使用 IaaS 部署的系统, 它必须具有虚拟网络的入口点, 可以通过公共 IP 地址、虚拟网络网关或虚拟网络对等互连来建立此入口点。

### <a name="network-interface"></a>网络接口

网络接口是到虚拟机的所有流量的入口点。 网络接口启用 IP 寻址的配置, 可用于应用 Nsg 或通过网络虚拟设备路由流量。 虚拟机的网络接口应进行适当规划和配置, 以与总体网络分段和隔离目标保持一致。

|Resource | 链接|
|---|---|
|创建、更改或删除网络接口 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|网络接口 IP 寻址 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

子网是在 Azure 中进行网络分段和隔离的关键组件。 可以按类似方式使用子网来提供系统之间的分离。 Nsg 可应用于子网, 以将入口通信流限制为仅限系统功能所必需的通信流。 子网可以作为防火墙规则和访问控制列表的源地址和目标地址, 并可以针对服务终结点进行配置, 以提供与 PaaS 功能的连接。

|Resource | 链接|
|---|---|
|添加、更改或删除虚拟网络子网 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>虚拟网络 (VNet)

Vnet 是 Azure 中网络的基本构建基块之一。 虚拟网络定义要跨各种系统使用的 IP 地址空间和路由边界。 虚拟网络划分为子网, 虚拟网络中的所有子网都具有相互之间的直接网络路由。 通过使用虚拟网络网关 (ExpressRoute 或 VPN), 可以在本地和外部环境中访问虚拟网络内的系统。 了解虚拟网络和关联的配置参数和路由对于理解和控制入口网络流量至关重要。

|Resource | 链接|
|---|---|
|虚拟网络概述 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|规划虚拟网络操作指南 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
创建虚拟网络快速入门 | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNet 对等互连

VNet 对等互连用于在两个虚拟网络之间提供直接通信路径。 建立对等互连后, 一个虚拟网络中的主机会直接将高速路由路径直接路由到另一个虚拟网络中的主机。 Nsg 仍适用于正常流量, 并且高级配置参数可用于定义是否允许通过虚拟网络网关或来自其他外部系统的通信。

|Resource | 链接|
|---|---|
|虚拟网络对等互连概述 |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|创建、更改或删除虚拟网络对等互连 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>VNET 中的公共 IP

公共 IP 地址用于向虚拟网络中部署的服务提供入口通信路径。 无人处理的实体应该仔细规划公共 IP 地址的分配, 并仅将其分配给存在正版要求的资源。 一般而言, 公共 IP 地址应分配给具有内置安全功能 (例如应用程序网关或网络虚拟设备) 的资源, 以便为虚拟网络提供安全的受控公共入口点。

|Resource | 链接|
|---|---|
|公共 IP 地址概述 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|创建、更改或删除公共 IP 地址 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>ExpressRoute 网关

ExpressRoute 网关提供本地环境的入口点, 并应进行部署, 以满足安全性、可用性、财务和性能要求。 ExpressRoute 网关提供定义的网络带宽, 并在部署后产生使用成本。 虚拟网络只能有一个 ExpressRoute 网关, 但这可以连接到多个 ExpressRoute 线路, 并可由多个虚拟网络通过 VNet 对等互连利用, 允许多个虚拟网络共享带宽和连接性。 应注意使用 ExpressRoute 网关配置本地环境和虚拟网络之间的路由, 以确保使用已知的受控网络入口点进行端到端连接。 使用 ExpressRoute 网关的英联邦实体还必须部署网络虚拟设备, 以建立到本地环境的 VPN 连接, 以符合 ACSC 使用者指南。

|Resource | 链接|
|---|---|
|ExpressRoute 网关概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|配置 ExpressRoute 的虚拟网络网关 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN 网关

Azure VPN 网关提供外部网络的入口网络点, 以实现安全的站点到站点连接或点到站点连接。 VPN 网关提供定义的网络带宽, 并在部署后产生使用成本。 Utilising VPN 网关的英联邦实体应确保按照 ACSC 使用者指南进行配置。 虚拟网络只能有一个 VPN 网关, 但可以使用多个隧道对其进行配置, 并且可通过 VNet 对等互连将多个虚拟网络利用该网络, 允许多个虚拟网络共享带宽和连接。 可以通过 Internet 或通过 Microsoft 对等互连通过 ExpressRoute 建立 VPN 网关。

|Resource | 链接|
|---|---|
|VPN 网关概述 | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|规划和设计 VPN 网关 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|适用于澳大利亚政府机构的 VPN 网关配置|[澳大利亚政府机构所需的 IPSEC 配置](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS VNet 集成

利用 PaaS 可以提供增强的功能和可用性, 并减少管理开销, 但必须对其进行适当的保护。 若要提高控制、强制网络分段或为应用程序和服务提供安全入口点, 可以将许多 PaaS 功能与虚拟网络集成。

若要提供安全入口点, 可以为 PaaS 功能 (如应用程序网关) 配置一个面向公众的外部接口和一个用于与应用程序服务通信的内部专用接口。 这可以防止需要配置具有公共 IP 地址的应用程序服务器, 并将其公开给外部网络。

为了使用 PaaS 作为系统或应用程序体系结构的集成部分, Microsoft 提供了多种机制来将 PaaS 部署到虚拟网络。 部署方法会限制外部网络 (如 Internet) 的入站访问, 同时提供与内部系统和应用程序的连接和集成。 示例包括应用服务环境、SQL 托管实例等。

|Resource | 链接|
|---|---|
|Azure 服务的虚拟网络集成 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|将应用与 Azure 虚拟网络集成操作指南 | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS 入口

PaaS 功能为增强功能提供了机会, 简化了管理, 但为解决网络分段和隔离的需求带来了复杂性。 PaaS 功能通常配置有公共 IP 地址, 可以从 Internet 进行访问。  使用 PaaS 功能构建系统时, 应小心确定系统内组件之间的所有必要通信流, 以及创建的网络安全规则, 以便仅允许此通信。 作为安全性的深层防御方法的一部分, PaaS 功能应配置为具有加密、身份验证和适当的访问控制和权限。  

### <a name="hostname"></a>主机名

PaaS 功能由主机名唯一标识, 以允许同一服务的多个实例托管在同一公共 IP 地址上。 在 Microsoft 拥有的 DNS 域中创建并存在资源时, 指定唯一的主机名。 可在具有应用程序级筛选功能的安全工具内使用授权服务的特定主机名。 某些服务还可以根据需要配置自定义域。

|Resource | 链接|
|---|---|
|Azure 服务使用的许多公共命名空间都可以通过运行 Get-azurermenvironment 命令通过 Powershell 进行获取 | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|为 Azure 云服务配置自定义域名 | 应用服务和其他应用程序可以具有自定义域[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>PaaS 公共 IP

PaaS 功能的公共 IP 地址根据托管或部署服务的区域进行分配。 若要为网络分段构建适当的网络安全规则和路由拓扑, 以及涵盖 Azure 虚拟网络、PaaS 和 ExpressRoute 以及 Internet 连接的隔离, 请了解公共 IP 地址分配和区域必填。 Azure 会从分配给每个 Azure 区域的池分配 IP 地址。 Microsoft 使每个区域中使用的地址可供下载, 以常规且受控的方式进行更新。 每个区域中可用的服务也经常随着新服务的发布或部署更广泛而发生变化。 英联邦实体应定期检查这些资料, 并可利用自动化来根据需要维护系统。 可以通过联系 Microsoft 支持部门获取每个区域中托管的某些服务的特定 IP 地址。

|Resource | 链接|
|---|---|
|Microsoft Azure 数据中心 IP 范围 | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|每个区域的 Azure 服务 | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 澳大利亚中部、澳大利亚中部-2、澳大利亚-东部、澳大利亚-东南部 & products = 全部](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>服务终结点

虚拟网络服务终结点为虚拟网络中的子网提供高速的专用网络连接, 以使用特定的 PaaS 功能。 若要完成网络分段并分离 PaaS 功能, 必须将 PaaS 功能配置为仅接受来自必要虚拟网络的连接。 并非所有 PaaS 功能都支持包含服务终结点和传统的基于 IP 地址的规则的防火墙规则组合, 因此应小心了解应用程序功能和管理所需的通信流以便实现这些安全控件不会影响服务可用性。

|Resource | 链接|
|---|---|
|服务终结点概述 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|教程 |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>安全性

在 IaaS 和 PaaS 功能上实现网络分段和隔离控制是通过保护功能本身实现的, 通过从将与功能.

在 Azure 中设计和构建解决方案是创建逻辑体系结构的过程, 用于在整个 Azure 平台上了解、控制和监视网络资源。 此逻辑体系结构是在 Azure 平台中定义的软件, 取代了在传统网络环境中实施的物理网络拓扑。

创建的逻辑体系结构必须为可用性提供必要的功能, 但还必须提供安全和完整性所需的可见性和控制。

实现此结果基于实现必需的网络分段和隔离工具, 同时还保护和强制实施网络拓扑和这些工具的实现。

本指南中提供的信息可用于帮助标识需要允许的流入流量的来源, 以及可以进一步控制或约束流量的方式。

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

Nsg 用于指定子网或特定网络接口允许的入站和出站流量。 配置 Nsg 时, 无英实体应使用允许列表方法, 其中规则配置为允许具有默认规则的所需流量配置为拒绝不匹配特定允许语句的所有流量。 规划和配置 Nsg 时必须小心谨慎, 以确保所有必要的入站和出站流量得到适当的捕获。 这包括识别和了解 Azure 虚拟网络和本地环境中能否的所有专用 IP 地址范围, 以及 Azure 负载均衡器和 PaaS 管理要求等特定 Microsoft 服务。 设计和实现网络安全组所涉及的人员还应了解如何使用服务标记和应用程序安全组来创建细化、服务和特定于应用程序的安全规则。

|Resource | 链接|
|---|---|
|网络安全概述 | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|创建、更改或删除网络安全组 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>PaaS 防火墙

PaaS 防火墙是一种可应用于特定 PaaS 服务的网络访问控制功能。 它允许配置来自特定虚拟网络的 IP 地址筛选或筛选, 以限制向特定 PaaS 实例发送流量。 对于包含防火墙的 PaaS 功能, 应将网络访问控制策略配置为仅允许基于应用程序要求的必要入口流量。  

|Resource | 链接|
|---|---|
|Azure SQL 数据库和 SQL 数据仓库 IP 防火墙规则 | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|存储网络安全 | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS 身份验证和访问控制

根据 PaaS 功能及其用途, 使用网络控制来限制访问可能是不可能或不切实际的。 作为 PaaS 的分层安全模型的一部分, Azure 提供各种身份验证和访问控制机制来限制对服务的访问, 即使允许网络流量也是如此。 PaaS 功能的典型身份验证机制包括 Azure Active Directory、应用程序级身份验证以及共享密钥或访问签名。 用户安全标识后, 可以能否角色来控制用户可以执行的操作。 这些工具可以能否作为替代措施, 也可以作为免费的措施来限制对服务的访问。

|Resource | 链接|
|---|---|
|控制和授予对 SQL 数据库和 SQL 数据仓库的数据库访问权限 | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Azure 存储服务的授权 | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure 策略是实施和维护 Azure 环境逻辑体系结构的完整性的关键组件。 考虑到各种服务和通过 Azure 服务提供的网络流量路径, 非常重要的一点是, 多用户人员知道其环境中存在的资源以及可用的网络入口点。 为了确保不会在 Azure 环境中创建未经授权网络入口点, 无人参与实体应利用 Azure 策略来控制可部署的资源类型以及这些资源的配置。 实际示例包括将资源限制为仅限于那些已获授权和批准使用的资源, 在存储上强制实施 HTTPS 加密, 并要求 Nsg 添加到子网。

|Resource | 链接|
|---|---|
|Azure 策略概述 | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|允许的资源类型示例策略 | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|确保 HTTPS 存储帐户示例策略|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|在子网中强制 NSG 示例策略| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Azure 中的网关组件管理从 Azure 环境到其他网络的流量, 请参阅有关[网关出口流量管理和控制](gateway-egress-traffic.md)的文章。
