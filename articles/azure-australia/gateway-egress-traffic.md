---
title: 控制 Azure 澳大利亚的出口流量
description: 控制 Azure 中的出口流量以满足澳大利亚政府对安全 Internet 网关的要求的关键要素
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602084"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>控制 Azure 澳大利亚的出口流量

保护 ICT 系统的基本组件是控制网络流量。 只需将通信限制为系统正常运行所需的流量, 就能减少泄露的可能性。 可见性和控制与你的应用程序和服务通信的外部系统有助于检测受攻击的系统, 以及尝试或成功地渗透数据。 本文提供了有关如何在 Azure 中处理出站 (出口) 网络流量的信息, 并提供了有关为连接到 internet 的系统实现网络安全控制的建议, 该系统与澳大利亚网络安全中心 (ACSC) 保持一致ACSC 信息安全手册 (ISM) 的使用者指南和意向。

## <a name="requirements"></a>要求

在 ISM 中定义了英联邦系统的整体安全要求。 为了帮助实现网络安全方面的英联邦实体, ACSC 已_发布 ACSC 保护:实现网络分段和隔离_, 并帮助保护云环境中的系统 ACSC 已发布_适用于租户的云计算安全性_。

ACSC 文档概述了实现网络安全和控制流量的上下文, 并提供了有关网络设计和配置的实用建议。

ACSC 文档中已标识了控制 Azure 中的出口流量的以下关键要求。

描述|Source
--------------- |------------------
**实现网络分段和隔离**, 例如, 使用基于主机的防火墙和网络访问控制, 将入站和出站网络连接限制为仅限所需端口和协议。| [租户的云计算](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
在租户 (包括租户的远程用户) 和云服务之间**实现充足的高带宽、低延迟、可靠网络连接**, 以满足租户的可用性要求  | [ACSC 保护:实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**不只是在网络层上应用技术**。 在可能的情况下, 应尽可能地对每个主机和网络进行分段和隔离。 在大多数情况下, 这种情况适用于最新的数据链路层, 包括应用程序层;但是, 在敏感环境下, 物理隔离可能适用。 基于主机和网络范围的度量值应以互补的方式进行部署并进行集中监视。 只需实现防火墙或安全设备, 只能实现唯一的安全措施。 |[ACSC 保护:实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**使用最低权限原则, 并需要‐到‐** 。 如果主机、服务或网络不需要与另一台主机、服务或网络通信, 则不应允许这样做。 如果主机、服务或网络只需要与特定端口或协议上的另一台主机、服务或网络通信, 则应该仅将其限制为这些端口和协议。 在网络中采用这些原则将补充用户权限的 minimisation, 并显著提高环境的总体安全状况。 |[ACSC 保护:实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**根据主机和网络的敏感性或关键性, 将它们分隔到业务运营**。 这可能包括根据不同的安全分类、安全域或某些主机或网络的可用性/完整性要求使用不同的硬件或平台。 具体而言, 单独的管理网络, 并考虑对敏感环境进行物理隔离的带外管理网络。 |[ACSC 保护:实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**标识、身份验证和授权所有实体对所有其他实体的访问权限**。 所有用户、主机和服务都应将其访问权限限制为仅限于执行其指定职责或功能所需的所有其他用户、主机和服务。 如果可能, 应尽可能禁用所有完全或本地服务, 以尽可能地禁用或降级标识、身份验证和授权服务。 |[ACSC 保护:实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**实现允许列表网络流量, 而不是拒绝列表**。 仅允许访问已知良好的网络流量 (标识、身份验证和授权的流量), 而不是拒绝对已知的不良网络通信 (例如, 阻止特定地址或服务) 的访问。 允许列表导致将一个高级安全策略拒绝列表, 并显著提高你的容量来检测和评估潜在的网络入侵。 |[ACSC 保护:实现网络分段和隔离](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**定义允许网站的允许列表并阻止所有未列出的网站**会有效地删除对手使用的最常见的数据传递和渗透技术之一。 如果用户具有访问大量网站或快速更改网站列表的合法要求, 则为;应考虑此类实现的成本。 即使是相对较少的允许列表, 它也提供比拒绝列表更好的安全性, 或者没有任何限制, 同时仍然降低实现成本。 许可允许列表的一个示例可能是允许整个澳大利亚子域, 即 "* au", 或允许来自 Alexa 站点排名的前1000站点 (筛选动态域名系统 (DDNS) 域和其他不适当的域之后)。| [澳大利亚政府信息安全手册 (ISM)](https://www.cyber.gov.au/ism)
|

本文介绍如何控制离开 Azure 环境的网络流量。 其中介绍了使用基础结构即服务 (IaaS) 和平台即服务 (PaaS) 在 Azure 中部署的系统。

[网关](gateway-ingress-traffic.md)网络流量文章用于处理进入你的 Azure 环境的网络流量, 并将其提供给本文以获取完整的网络控制覆盖范围。

## <a name="architecture"></a>体系结构

若要适当控制出口流量, 请在设计和实施网络安全时, 首先了解 Azure 中的传出网络流量在 IaaS 和 PaaS 中的工作方式。 本部分概述如何处理 Azure 中托管的资源生成的出站流量, 以及可用于限制和控制该流量的安全控制。

### <a name="architecture-components"></a>体系结构组件

此处显示的体系结构关系图描述了在退出部署到虚拟网络的子网中的系统时网络流量可以采取的可能路径。 虚拟网络中的流量受到管理, 并在子网级别进行管理, 路由和安全规则适用于中包含的资源。 与出口通信相关的组件分为系统、有效路由、下一跃点类型、安全控制和 PaaS 出口。

![体系结构](media/egress-traffic.png)

### <a name="systems"></a>笔记本电脑

系统是 Azure 资源和相关组件, 可在作为虚拟网络一部分的 IP 子网内生成出站流量。

| 组件 | 描述 |
| --- | ---|
|虚拟网络 (VNet) | VNet 是 Azure 中的基础资源, 它提供用于部署资源和启用通信的平台和边界。 VNet 存在于 Azure 区域中, 并为 VNet 集成资源 (例如虚拟机) 定义 IP 地址空间和路由边界。|
|Subnet | 子网是在 VNet 中创建的 IP 地址范围。 可以在 VNet 中创建多个子网, 以便进行网络分段。|
|网络接口| 网络接口是 Azure 中存在的资源。 它连接到虚拟机, 并为其关联的子网中的专用非 Internet 可路由 IP 地址分配。 此 IP 地址是通过 Azure 资源管理器动态或静态分配的。|
|公共 IP| 公共 IP 是一种资源, 用于保留 Microsoft 拥有的、可通过 Internet 路由的 IP 地址, 这些地址来自指定的区域, 可在虚拟网络中使用。 它可以与特定网络接口或 PaaS 资源关联, 使资源能够与 Internet、ExpressRoute 和其他 PaaS 系统进行通信。|
|

### <a name="routes"></a>路由

传出流量所采用的路径取决于该资源的有效路由, 这是由了解到与所有可能的源和 Azure 路由逻辑的应用组合决定的路由结果集。

| 组件 | 描述 |
|--- | ---|
|系统路由| Azure 自动创建系统路由，并将路由分配到虚拟网络中的每个子网。 不能创建或删除系统路由, 但可以用自定义路由替代系统路由。 Azure 为每个子网创建默认的系统路由, 并在特定的 Azure 功能能否时向特定子网或每个子网添加其他可选的默认路由。|
|服务终结点| 服务终结点提供从子网到特定 PaaS 功能的直接、专用出口连接。 服务终结点 (仅适用于 PaaS 功能的子集) 为访问 PaaS 的 VNet 中的资源提供了更高的性能和安全性。|
|路由表| 路由表是 Azure 中的一种资源, 可以创建它来指定用户定义的路由 (Udr), 以便通过边界网关协议来补充或覆盖系统路由或路由了解到。 每个 UDR 指定一个网络、一个网络掩码和下一个跃点。 可以将路由表关联到子网, 同一个路由表可以与多个子网相关联, 但子网只能有零个或一个路由表。|
|边界网关协议(BGP)| BGP 是一种自治系统路由协议。 自治系统是一种常见的管理和公用路由策略下的网络或网络组。 BGP 用于在自治系统之间交换路由信息。 可通过虚拟网络网关将 BGP 集成到虚拟网络。|
|

### <a name="next-hop-types-defined"></a>定义的下一跃点类型

Azure 中的每个路由都包括网络范围和关联的子网掩码以及下一个跃点, 该跃点确定如何处理流量。

下一个跃点类型 | 描述
---- | ----
**虚拟网络** | 在虚拟网络的地址空间内的地址范围之间路由流量。 Azure 使用地址前缀创建路由，该前缀对应的每个地址范围是在虚拟网络的地址空间中定义的。 如果虚拟网络地址空间定义了多个地址范围, Azure 将为每个地址范围创建单个路由。 Azure 使用为每个地址范围创建的路由在 VNet 中的子网之间自动路由流量。
**VNet 对等互连** | 在两个虚拟网络之间创建虚拟网络对等互连时, 会为每个虚拟网络的每个地址范围添加一个到它所对等互连的虚拟网络的路由。 流量按虚拟网络中的子网的相同方式在对等互连虚拟网络之间路由。
**虚拟网络网关** | 向虚拟网络添加虚拟网络网关时, 会添加一个或多个虚拟网络网关作为下一跃点类型列出的路由。 包括的路由是在本地网络网关资源中配置的路由, 以及通过 BGP 了解到的任何路由。
**虚拟设备** | 虚拟设备通常运行网络应用程序, 例如防火墙。 虚拟设备允许额外处理流量, 例如筛选、检查或地址转换。 具有虚拟设备跃点类型的每个路由还必须指定下一跃点 IP 地址。
**VirtualNetworkServiceEndpoint** | 启用服务终结点时, 会将特定服务的公共 IP 地址作为路由添加到下一跃点 VirtualNetworkServiceEndpoint 的子网。 为虚拟网络中的单个子网上的单个服务启用服务终结点。 Azure 服务的公共 IP 地址定期更改。 当地址更改时，Azure 自动管理路由表中的地址。
**Internet** | 使用 Internet 的下一个跃点的流量将退出虚拟网络, 并自动将其转换为公共 IP 地址, 无论是从关联的 Azure 区域中提供的动态池中, 还是通过使用为该资源配置的公共 IP 地址。 如果目标地址适用于 Azure 的某个服务, 则流量通过 Azure 的主干网络直接路由到该服务, 而不是将流量路由到 Internet。 Azure 服务之间的流量不跨越 Internet，不管虚拟网络存在于哪个 Azure 区域，也不管 Azure 服务的实例部署在哪个 Azure 区域。
**无** | 将删除下一跃点为 none 的流量。 Azure 为保留地址前缀创建系统默认路由, 并将 "无" 作为下一跃点类型。 还可以使用路由表来添加下一跃点为 none 的路由, 以防止将流量路由到特定网络。
|

### <a name="security-controls"></a>安全控件

控件 | 描述
----- | -----
**网络安全组 (Nsg)** | Nsg 控制进出 Azure 中的虚拟网络资源的流量。 Nsg 应用允许或拒绝的流量的规则, 其中包括 Azure 中的流量以及 Azure 和外部网络 (如本地或 Internet) 之间的流量。 Nsg 应用于虚拟网络中的子网或单个网络接口。
**Azure 防火墙** | Azure 防火墙是一种托管的基于云的网络安全服务, 可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以通过基于 IP 地址、协议和端口的传统网络筛选规则来配置 Azure 防火墙, 还支持基于完全限定的域名 (FQDN)、服务标记和内置威胁智能进行筛选。
**网络虚拟设备 (NVA)** | 网络虚拟设备是虚拟机媒体, 可为 Azure 提供网络、安全性和其他功能。 Nva 在虚拟网络和部署中以 Vm 的形式支持网络功能和服务。 Nva 可用于满足特定要求、与管理和操作工具集成, 或提供与现有产品的一致性。 Azure 支持一系列第三方网络虚拟设备，包括 Web 应用程序防火墙 (WAF)、防火墙、网关/路由器、应用程序交付控制器 (ADC) 以及 WAN 优化器。
**服务终结点策略 (预览)** | 虚拟网络服务终结点策略允许你筛选 Azure 服务的虚拟网络流量, 只允许通过服务终结点进行特定的 Azure 服务资源。 终结点策略针对发往 Azure 服务的虚拟网络流量提供精细的访问控制。
**Azure Policy** | Azure 策略是 Azure 中的一项服务, 可用于创建、分配和管理策略。 这些策略使用规则来控制可部署的资源类型以及这些资源的配置。 如果资源不满足要求或可用于监视以报告符合性状态, 则可以使用策略来强制实施符合性。
|

### <a name="paas-egress"></a>PaaS 出口

大多数 PaaS 资源不会生成传出流量, 但会响应入站请求 (如应用程序网关、存储、SQL 数据库等) 或从其他资源 (如服务总线和 Azure 中继) 中继数据。 PaaS 资源 (如应用服务到存储或 SQL 数据库) 之间的网络通信由 Azure 控制和包含, 并通过标识和其他资源配置控件 (而不是网络分段或隔离) 进行保护。

部署到虚拟网络中的 PaaS 资源接收专用 IP 地址, 并与虚拟网络中的其他资源一样, 受任何路由控制和 Nsg 的限制。 虚拟网络中不存在的 PaaS 资源将利用在资源的所有实例之间共享的 IP 地址池, 这些实例可通过 Microsoft 文档发布, 也可通过 Azure 资源管理器确定。

## <a name="general-guidance"></a>通用指南

若要在 Azure 中设计和构建安全解决方案, 了解和控制网络流量至关重要, 以便只能进行标识的和授权的通信。 本指南的目的和后续部分中的特定组件指南旨在说明可以能否的工具和服务, 以应用[ACSC 保护中所述的原则:跨 Azure 工作负荷实施](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)网络分段和隔离。 这包括详细介绍如何创建虚拟体系结构来保护资源, 前提是不能应用在本地环境中可能存在的相同传统物理和网络控件。

### <a name="guidance"></a>指南

* 限制虚拟网络的出口点数
* 覆盖不需要直接出站通信到 Internet 的所有子网的系统默认路由
* 设计和实现完整的网络体系结构, 以识别和控制 Azure 资源的所有入口和出口点
* 如 Microsoft 虚拟数据中心 (VDC) 文档中所述, 请考虑 utilising 虚拟网络的中心和辐射网络设计
* 具有内置安全功能以连接到 Internet 的出站连接的利用产品 (例如, Azure 防火墙、网络虚拟设备或 Web 代理)
* 使用标识控件 (例如基于角色的访问、条件性访问和多重身份验证 (MFA)) 来限制网络配置特权
* 实现锁定以防止修改或删除网络配置的关键元素
* 在 VNet 集成配置中部署 PaaS 以增加隔离和控制
* 实现 ExpressRoute 以连接到本地网络
* 实现 Vpn 以与外部网络集成
* 利用 Azure 策略, 以将区域和资源仅限于系统功能所必需的区域和资源
* 利用 Azure 策略, 以强制实施资源的基准安全配置
* 利用网络观察程序和 Azure Monitor 来记录、审核和查看 Azure 中的网络流量

### <a name="resources"></a>资源

项 | 链接
-----------| ---------
_澳大利亚法规和政策符合性文档, 包括使用者指南_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure 虚拟数据中心_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC 网络分段_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_租户的 ACSC Cloud Security_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC 信息安全手册_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>组件指南

本部分提供有关与 Azure 中部署的系统的出口流量相关的单个组件的更多指导。 每节描述特定组件的用途, 其中包含指向文档和配置指南的链接, 可用于帮助进行设计和生成活动。

### <a name="systems-security"></a>系统安全性

与 Azure 中的资源的所有通信都通过 Microsoft 维护的网络基础结构, 提供了连接和安全功能。 Microsoft 将自动就地保护一系列保护, 以保护 Azure 平台和网络基础结构, 并在 Azure 中提供附加功能作为服务来控制网络流量并建立网络分段和分离.

### <a name="virtual-network-vnet"></a>虚拟网络 (VNet)

虚拟网络是 Azure 中网络的基本构建基块之一。 虚拟网络定义要跨各种系统使用的 IP 地址空间和路由边界。 虚拟网络划分为子网, 虚拟网络中的所有子网都具有相互之间的直接网络路由。 通过使用虚拟网络网关 (ExpressRoute 或 VPN), 虚拟网络内的系统可以与本地和外部环境集成, 通过 Azure 提供网络地址转换 (NAT) 和公共 IP 地址分配, 系统可以连接到 Internet 或其他 Azure 区域和服务。 了解虚拟网络和关联的配置参数和路由对于理解和控制出口网络流量至关重要。

虚拟网络在 Azure 中形成基本地址空间和路由边界, 可用作网络分段和隔离的主要构建基块。

| Resource | 链接 |
| --- | --- |
| *虚拟网络概述* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *规划虚拟网络操作指南*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *创建虚拟网络快速入门* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

子网是在 Azure 中进行网络分段和隔离的关键组件。 可以使用子网来提供系统之间的分离。 子网是虚拟网络中的资源, 其中应用了 Nsg、路由表和服务终结点。 子网可以用作防火墙规则和访问控制列表的源和目标地址。

应计划虚拟网络中的子网, 以满足工作负荷和系统的要求。 设计或实现子网所涉及的人员应参考网络分段的 ACSC 准则, 确定如何将系统组合到一个子网中。

|Resource|链接|
|---|---|
|*添加、更改或删除虚拟网络子网* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>网络接口

网络接口是来自虚拟机的所有传出流量的源。 网络接口启用 IP 寻址的配置, 可用于应用 Nsg 或通过 NVA 路由流量。 虚拟机的网络接口应进行适当规划和配置, 以与总体网络分段和隔离目标保持一致。

|Resource|链接|
|---|---|
|*创建、更改或删除网络接口* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*网络接口 IP 寻址*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>VNet 集成 PaaS

PaaS 可以提供增强的功能和可用性, 并减少管理开销, 但必须适当地进行保护。 若要提高控制、强制网络分段或为应用程序和服务提供安全传出点, 可以将许多 PaaS 功能与虚拟网络集成。

Microsoft 使用 PaaS 作为系统或应用程序体系结构的集成部分, 提供多种机制来将 PaaS 部署到虚拟网络。 部署方法有助于限制访问, 同时提供与内部系统和应用程序的连接和集成。 示例包括应用服务环境、SQL 托管实例等。

将 PaaS 部署到已实现路由和 NSG 控制的虚拟网络中时, 了解资源的特定通信要求非常重要, 包括来自 Microsoft 服务的管理访问权限以及当从这些服务中答复传入的请求时, 通信流量将会传输。

| Resource  | 链接  |
| --- | --- |
| *Azure 服务的虚拟网络集成* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *将应用与 Azure 虚拟网络集成操作指南* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>公共 IP

公共 IP 地址在虚拟网络外部进行通信时使用。 这包括 PaaS 资源和下一跃点 Internet 的任何路由。 无人处理的实体应该仔细规划公共 IP 地址的分配, 并仅将其分配给存在正版要求的资源。 一般而言, 公共 IP 地址应分配给虚拟网络 (如 Azure 防火墙、VPN 网关或网络虚拟设备) 的受控传出点。

|Resource|链接|
|---|---|
|*公共 IP 地址概述*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*创建、更改或删除公共 IP 地址* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>有效路由

有效路由是由系统路由、服务终结点、路由表以及 BGP 和 Azure 路由逻辑的组合决定的路由的结果集。 当出站流量是从子网发送时，Azure 使用最长前缀匹配算法，根据目标 IP 地址选择路由。 如果多个路由包含同一地址前缀，Azure 根据以下优先级选择路由类型：

1. 用户定义的路由
2. BGP 路由
3. 系统路由

需要注意的是, 即使 BGP 路由更具体, 与虚拟网络、虚拟网络对等互连或虚拟网络服务终结点相关的流量的系统路由也是首选路由。

在 Azure 中设计或实现路由拓扑中涉及的人员应了解 Azure 如何路由流量, 并制定一种体系结构, 该体系结构可平衡系统的必要功能和所需的安全和可见性。 应注意如何适当地规划环境以避免过多的干预和异常来路由行为, 因为这会增加复杂性, 并可能会导致故障排除和错误查找更难使用。

| Resource | 链接  |
| --- | --- |
| *查看有效路由* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>系统路由

对于[系统路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes), 设计或虚拟网络的实现中涉及的人员应了解默认的系统路由, 以及可用于补充或替代这些路由的选项。

### <a name="service-endpoints"></a>服务终结点

在子网上启用[服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)可提供与关联的 PaaS 资源的直接通信路径。 这可以通过将可用的通信路径限制为该服务来提高性能和安全性。 使用服务终结点会将潜在的数据渗透路径引入为默认配置, 以允许访问 PaaS 服务的所有实例, 而不是应用程序或系统所需的特定实例。

英联邦实体应评估与提供 PaaS 资源的直接访问权限相关的风险, 包括误用路径的可能性和后果。

若要降低与服务终结点相关的潜在风险, 请在可能的情况下实现服务终结点策略, 或者考虑在 Azure 防火墙或 NVA 子网上启用服务终结点, 并在其他可以应用筛选、监视或检查。

|Resource|链接|
|---|---|
|*教程：使用 Azure 门户, 使用虚拟网络服务终结点限制对 PaaS 资源的网络访问* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>路由表

路由表提供管理员配置的机制, 用于控制 Azure 中的网络流量。 可以能否路由表, 将流量转发到 Azure 防火墙或 NVA, 直接连接到外部资源, 或者替代 Azure 系统路由。 还可以使用路由表, 通过禁用虚拟网络网关路由传播, 阻止通过虚拟网络网关了解到的网络被子网中的资源使用。

|Resource|链接|
|---|---|
|*路由概念-自定义路由* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*教程：路由网络流量* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>边界网关协议(BGP)

虚拟网络网关可以能否 BGP, 以便与本地或其他外部网络动态交换路由信息。 BGP 适用于通过 expressroute 专用对等互连的 ExpressRoute 虚拟网络网关配置的虚拟网络, 并且在 Azure VPN 网关上启用。

在 Azure 中的虚拟网络和虚拟网络网关的设计或实现中涉及的人员应该花些时间来了解 Azure 中可用于 BGP 的行为和配置选项。

|Resource|链接|
|---|---|
|*路由概念:BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*ExpressRoute 路由要求* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*关于 Azure VPN 网关的 BGP* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*教程：通过 ExpressRoute Microsoft 对等互连配置站点到站点 VPN* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>下一跃点类型

### <a name="virtual-network"></a>虚拟网络

具有虚拟网络下一个跃点的路由会自动添加为系统路由, 但也可以添加到用户定义的路由, 以便在重写系统路由的情况下将流量定向回虚拟网络。

### <a name="vnet-peering"></a>VNet 对等

VNet 对等互连允许两个不同的虚拟网络之间的通信。 必须在每个虚拟网络上启用配置 VNet 对等互连, 但虚拟网络不需要位于同一区域、订阅或关联到相同的 Azure Active Directory (Azure AD) 租户。

在配置 VNet 对等互连时, 与 VNet 对等互连的设计或实现所涉及的人员了解四个关联的配置参数以及这些参数如何应用到对等机的每一方, 这一点很重要。

1. **允许虚拟网络访问：** 选择 "**启用**(默认)" 可启用两个虚拟网络之间的通信。 启用虚拟网络之间的通信可允许资源连接到任意虚拟网络，并以相同的带宽和延迟互相之间进行通信，就如同它们是连接到同一个虚拟网络一样。
2. **允许转发的流量：** 选中此复选框可允许网络流量*转发*的流量, 该流量不是源自虚拟网络-可通过对等互连流向此虚拟网络。 此设置是实现中心辐射型网络拓扑的基础。
3. **允许网关传输：** 选中此框可允许对等互连虚拟网络利用附加到此虚拟网络的虚拟网络网关。 允许通过虚拟网络网关资源在虚拟网络上启用*网关传输*, 但仅适用于在其他虚拟网络上启用了 "*使用远程网关*" 的情况。
4. **使用远程网关：** 选中此框可允许来自此虚拟网络的流量流过连接到要对等互连的虚拟网络的虚拟网络网关。 在没有虚拟网络网关的虚拟网络上启用了 "*使用远程网关*", 仅当在其他虚拟网络上启用了 "*允许网关传输*" 选项时才适用。

|Resource|链接|
|---|---|
| 概念：虚拟网络对等互连                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| 创建、更改或删除虚拟网络对等互连 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>虚拟网络网关

虚拟网络网关提供一种机制, 用于将虚拟网络与外部网络 (如本地环境、合作伙伴环境和其他云部署) 集成。 两种类型的虚拟网络网关是 ExpressRoute 和 VPN。

#### <a name="expressroute-gateway"></a>ExpressRoute 网关

ExpressRoute 网关提供从虚拟网络到本地环境的传出点, 应部署该点以满足安全性、可用性、财务和性能要求。 ExpressRoute 网关提供定义的网络带宽, 并在部署后产生使用成本。 虚拟网络只能有一个 ExpressRoute 网关, 但这可以连接到多个 ExpressRoute 线路, 并可通过 VNet 对等互连通过 VNet 对等互连使用多个虚拟网络, 允许共享带宽和连接。 应注意使用 ExpressRoute 网关配置本地环境和虚拟网络之间的路由, 以确保使用已知的受控网络传出点进行端到端连接。 通过 ExpressRoute 专用对等互连使用 ExpressRoute 网关的联邦实体还必须部署网络虚拟设备 (NVA), 以建立与本地环境的 VPN 连接, 以符合 ACSC 使用者指南。

请务必注意, ExpressRoute 网关对通过 BGP 交换的地址范围、社区和其他特定配置项目有限制。

| Resource  | 链接  |
|---|---|
| ExpressRoute 网关概述 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| 配置 ExpressRoute 的虚拟网络网关 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN 网关

Azure VPN 网关提供从虚拟网络到外部网络的出口网络点, 以实现安全的站点到站点连接。 VPN 网关提供定义的网络带宽, 并在部署后产生使用成本。 Utilising VPN 网关的英联邦实体应确保按照 ACSC 使用者指南进行配置。 虚拟网络只能有一个 VPN 网关, 但可以使用多个隧道对其进行配置, 并且可通过 VNet 对等互连将多个虚拟网络利用该网络, 允许多个虚拟网络共享带宽和连接。 可以通过 Internet 或通过 Microsoft 对等互连通过 ExpressRoute 建立 VPN 网关。

| Resource  | 链接 |
| --- | --- |
| VPN 网关概述| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| 规划和设计 VPN 网关 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Azure 澳大利亚的 azure VPN 网关 | [Azure 澳大利亚的 azure VPN 网关](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>虚拟设备的下一跃点

下一跃点虚拟设备能够处理 Azure 网络外部的网络流量和应用于虚拟网络的路由拓扑。 虚拟设备可以应用安全规则、翻译地址、建立 Vpn、代理流量或其他一系列功能。 下一跃点虚拟设备是通过路由表中的 Udr 应用的, 可用于将流量定向到跨多个 Nva 提供可用性的 Azure 防火墙、单个 NVA 或 Azure 负载均衡器。 若要使用虚拟设备进行路由, 必须为 IP 转发启用关联的网络接口。

| Resource  | 链接 |
| --- | ---|
| 路由概念:自定义路由 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| 启用或禁用 IP 转发 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>VirtualNetworkServiceEndpoint 的下一跃点

只有在子网中配置了服务终结点且不能通过路由表手动配置时, 才会添加下一跃点类型为 VirtualNetworkServiceEndpoint 的路由。

### <a name="next-hop-of-internet"></a>Internet 的下一个跃点

下一跃点 Internet 用于访问使用公共 IP 地址的任何资源, 其中包括 Internet 以及 Azure 区域中的 PaaS 和 Azure 服务。 下一跃点 Internet 不需要涵盖所有网络的默认路由 (0.0.0.0/0), 但可用于启用到特定公共服务的路由路径。 应使用下一跃点 Internet 将路由添加到授权服务和系统功能所需的功能, 例如 Microsoft 管理地址。

使用下一跃点 Internet 可以添加的服务示例有:

1. 用于 Windows 激活的密钥管理服务
2. 应用服务环境管理

|Resource|链接|
|---|---|
| Azure 中的出站连接 | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| 使用 Azure 自定义路由启用 KMS 激活 | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| 锁定应用服务环境  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>无的下一跃点

下一跃点 "无" 可用于阻止与特定网络的通信。 与用于控制是否允许或拒绝流量遍历可用网络路径的 NSG 相比, 使用 "无" 的下一跃点可以完全删除网络路径。 使用 "无" 下一跃点创建路由时应特别小心, 特别是将它应用于默认路由 0.0.0.0/0 时, 这可能会产生意外后果, 并可能会导致系统问题的故障排除变得复杂而耗时。

## <a name="security"></a>安全性

在 IaaS 和 PaaS 功能上实现网络分段和隔离控制是通过保护功能本身实现的, 通过从将与功能.

在 Azure 中设计和构建解决方案是创建逻辑体系结构的过程, 用于在整个 Azure 平台上了解、控制和监视网络资源。 此逻辑体系结构是在 Azure 平台中定义的软件, 取代了在传统网络环境中实施的物理网络拓扑。

创建的逻辑体系结构必须为可用性提供必要的功能, 但还必须提供安全和完整性所需的可见性和控制。

实现此结果基于实现必需的网络分段和隔离工具, 同时还保护和强制实施网络拓扑和这些工具的实现。

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

Nsg 用于指定子网或特定网络接口允许的入站和出站流量。 配置 Nsg 时, 无英实体应使用允许列表方法, 其中规则配置为允许具有默认规则的所需流量配置为拒绝不匹配特定允许语句的所有流量。 规划和配置 Nsg 时, 必须小心, 以确保正确捕获所有必要的入站和出站流量。 这包括识别并了解虚拟网络和本地环境中能否的所有专用 IP 地址范围, 以及 Azure 负载均衡器和 PaaS 管理要求等特定 Microsoft 服务。 设计和实现 Nsg 中涉及的人员还应了解如何使用服务标记和应用程序安全组来创建细化、服务和特定于应用程序的安全规则。

请务必注意, NSG 的默认配置允许向虚拟网络中的所有地址和所有公共 IP 地址进行出站流量。

|Resource|链接|
|---|---|
|网络安全概述 | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|创建、更改或删除网络安全组 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure 防火墙

可以能否 Azure 防火墙来构建中心辐射型网络拓扑, 并强制实施集中式网络安全控制。 通过实施允许列表方法, Azure 防火墙可用于满足 ISM 的出口流量的必要要求, 只需对系统功能所需的 IP 地址、协议、端口和 Fqdn 进行授权。 英联邦实体应该采取一种基于风险的方法来确定 Azure 防火墙提供的安全功能是否足以满足他们的要求。 对于除了 Azure 防火墙提供的其他安全功能之外的其他安全功能, 需要考虑实现 Nva。

|Resource|链接|
|---|---|
|*Azure 防火墙文档* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*教程：使用 Azure PowerShell 在混合网络中部署和配置 Azure 防火墙* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>网络虚拟设备 (Nva)

Nva 可用于构建中心辐射型网络拓扑, 提供增强或补充性的网络功能, 或者可用作 Azure 网络机制的替代方法, 以实现对本地网络服务的更好的支持和管理。 可以部署 Nva 来满足特定的安全要求, 例如;对与网络流量、HTTPS 解密、内容检查、筛选或其他安全功能关联的标识感知要求的情况。 应将 Nva 部署在高度可用的配置中, 并且 Nva 的设计或实现中涉及的人员应参阅相应的供应商文档, 了解有关 Azure 中部署的指南。

|Resource|链接|
|---|---|
|*部署高度可用的网络虚拟设备* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>服务终结点策略 (预览)

配置基于服务可用性的服务终结点策略, 并对数据渗透的可能性和影响进行安全风险评估。 应考虑将服务终结点策略用于 Azure 存储, 并根据关联的风险配置文件按案例管理其他服务。

| Resource | 链接  |
| --- | --- |
| *服务终结点策略概述* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *使用 Azure 门户创建、更改或删除服务终结点策略* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure 策略是实施和维护 Azure 环境逻辑体系结构的完整性的关键组件。 Azure 服务提供各种服务和出口网络流量路径。 非常重要的是, 英联邦实体认识到其环境中存在的资源以及可用的网络出口点。 为了确保不会在 Azure 环境中创建未经授权网络传出点, 无人参与实体应该使用 Azure 策略来控制可部署的资源类型以及这些资源的配置。 实际的示例包括将资源限制为仅允许使用并且需要将 Nsg 添加到子网的资源。

|Resource | 链接|
|---|---|
|*Azure 策略概述* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*允许的资源类型示例策略* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*在子网中强制 NSG 示例策略*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>PaaS 出口功能

PaaS 功能提供了更多的功能和简化的管理, 但为解决网络分段和隔离的要求提供了复杂性。 PaaS 功能通常配置有公共 IP 地址, 可以从 Internet 进行访问。  如果你使用的是系统和解决方案中的 PaaS 功能, 应小心识别组件之间的通信流, 并创建网络安全规则以仅允许该通信。 作为安全性的深层防御方法的一部分, PaaS 功能应配置为具有加密、身份验证和适当的访问控制和权限。  

### <a name="public-ip-for-paas"></a>PaaS 公共 IP

PaaS 功能的公共 IP 地址根据托管或部署服务的区域进行分配。 如果要为网络分段构建适当的网络安全规则和路由拓扑, 以及涵盖 Azure 虚拟网络、PaaS 和 ExpressRoute 的隔离和隔离, 则需要了解公共 IP 地址分配和区域。Internet 连接。 Azure 会从分配给每个 Azure 区域的池分配 IP 地址。 Microsoft 使每个区域中使用的地址可供下载, 以常规且受控的方式进行更新。 每个区域中可用的服务也经常随着新服务的发布或部署更广泛而发生变化。 英联邦实体应定期检查这些资料, 并可根据需要使用自动化来维护系统。 可以通过联系 Microsoft 支持部门获取每个区域中托管的某些服务的特定 IP 地址。

| Resource | 链接 |
| --- | --- |
| *Microsoft Azure 数据中心 IP 范围*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *每个区域的 Azure 服务*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 澳大利亚中部、澳大利亚中部-2、澳大利亚-东部、澳大利亚-东南部 & products = 全部](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Azure App Service 中的入站和出站 IP 地址* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>后续步骤

将整个体系结构和设计与适用于[IaaS 和 PaaS Web 应用程序的已发布受保护蓝图](https://aka.ms/au-protected)进行比较。
