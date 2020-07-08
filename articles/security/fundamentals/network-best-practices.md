---
title: 网络安全最佳做法 - Microsoft Azure
description: 本文提供一系列有关使用内置 Azure 功能提高网络安全性的最佳实践。
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 4e64873cc2e7779c4d931018fd16bdca08596aa2
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757817"
---
# <a name="azure-best-practices-for-network-security"></a>Azure 网络安全最佳做法
本文介绍一系列 Azure 最佳做法以增强网络安全。 这些最佳实践衍生自我们的 Azure 网络经验和客户的经验。

对于每项最佳实践，本文将说明：

* 最佳实践是什么
* 为何要启用该最佳实践
* 如果无法启用该最佳实践，可能的结果是什么
* 最佳实践的可能替代方案
* 如何学习启用最佳实践

这些最佳做法以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。 看法和技术将随着时间改变，本文会定期更新以反映这些更改。

## <a name="use-strong-network-controls"></a>使用强网络控制
你可以将 [Azure虚拟机 (VM)](https://azure.microsoft.com/services/virtual-machines/) 和设备放在 [Azure虚拟网络](../../virtual-network/index.yml)上，从而将它们连接到其他网络设备。 也就是说，可以将虚拟网络接口卡连接到虚拟网络，允许启用了网络的设备之间进行基于 TCP/IP 的通信。 连接到 Azure 虚拟网络的虚拟机能够连接到相同虚拟网络、不同虚拟网络、Internet 或自己的本地网络上的设备。

规划网络和网络安全性时，建议集中执行以下操作：

- 管理核心网络功能，如 ExpressRoute、虚拟网络和子网预配以及 IP 寻址。
- 治理网络安全元素，如 ExpressRoute、虚拟网络和子网预配以及 IP 寻址等网络虚拟设备功能。

如果使用一组通用的管理工具来监视网络和网络的安全性，则可清楚地了解这两者。 一种简单、统一的安全策略可减少错误，因为这会改善人员理解和自动化可靠性。

## <a name="logically-segment-subnets"></a>以逻辑方式分段子网
Azure 虚拟网络类似于本地网络上的 LAN。 Azure 虚拟网络背后的思路是创建基于单个专用 IP 地址空间的网络，以将所有Azure 虚拟机置于其上。 可用的专用 IP 地址空间位于类别 A (10.0.0.0/8)、类别 B (172.16.0.0/12) 和类别 C (192.168.0.0/16) 范围内。

以逻辑方式对子网进行分段的最佳做法包括：

**最佳做法**：不要分配具有广泛范围的允许规则（例如，允许 0.0.0.0 到 255.255.255.255）。  
**详细信息**：确保故障排除过程不会建议或禁止设置这些类型的规则。 这些允许规则会导致错误的安全感，经常被红队发现并利用。

**最佳做法**：将较大的地址空间分段成子网。   
**详细信息**：使用基于 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 的子网原理来创建子网。

**最佳做法**：在子网之间创建网络访问控制。 子网之间的路由会自动发生，不需要手动配置路由表。 默认情况下，在 Azure 虚拟网络上创建的子网之间没有任何网络访问控制。   
**详细信息**：使用[网络安全组](/azure/virtual-network/virtual-networks-nsg)防止未经请求的流量进入 Azure 子网。 网络安全组是简单的有状态数据包检查设备，使用 5 元组方法（源 IP、源端口、目标 IP、目标端口和第 4 层协议）来创建网络流量的允许/拒绝规则。 可以允许或拒绝流往或来自单个 IP 地址、多个 IP 地址或整个子网的流量。

将网络安全组用于子网之间的网络访问控制时，可将属于同一安全区域或角色的资源置于其本身的子网中。

**最佳做法**：避免小型虚拟网络和子网，以确保简易性和灵活性。   
**详细信息**：大多数组织会添加比最初计划更多的资源，重新分配地址是劳动密集型工作。 使用小型子网会增加有限的安全值，将网络安全组映射到每个子网会增加开销。 广泛定义子网，以确保具有增长灵活性。

**最佳做法**：通过定义[应用程序安全组](https://azure.microsoft.com/blog/applicationsecuritygroups/)来简化网络安全组规则管理。  
**详细信息**：为你认为将来可能会更改或是在许多网络安全组间使用的 IP 地址列表定义一个应用程序安全组。 务必清楚地命名应用程序安全组，以便其他人可以理解其内容和用途。

## <a name="adopt-a-zero-trust-approach"></a>采用零信任方法
基于外围的网络在工作时假设网络中的所有系统都可以受信任。 但当前的员工会通过各种设备和应用，从任何位置访问其组织的资源，这使得外围安全控制不适用。 仅关注可以访问资源的用户的访问控制策略是不够的。 为了掌握安全与效率之间的平衡，安全管理员还需要考虑访问资源的方式。

网络需要从传统防御进行演化，因为网络可能容易受到侵害：攻击者可能会破坏受信任边界内的单个终结点，然后在整个网络中快速扩展立足点。 [零信任](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/)网络消除了基于外围中的网络位置的信任概念。 相反，零信任体系结构使用设备和用户信任声明来获取组织数据和资源的访问权限。 对于新计划，采用在访问时验证信任的零信任方法。

最佳做法包括：

**最佳做法**：基于设备、标识、保证、网络位置等提供对资源的条件访问。  
**详细信息**：[Azure AD 条件访问](../../active-directory/conditional-access/overview.md)使你可以根据所需条件实现自动访问控制决策，从而应用正确的访问控制。 有关详细信息，请参阅[使用条件访问管理对 Azure 管理的访问](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)。

**最佳做法**：仅在工作流审批之后才启用端口访问。  
**详细信息**：可以使用 [Azure Security Center 中的 实时 VM 访问](../../security-center/security-center-just-in-time.md)来锁定发往 Azure VM 的入站流量，降低遭受攻击的可能性，同时在需要时还允许轻松连接到 VM。

**最佳做法**：授予执行特权任务的临时权限，防止恶意用户或未授权用户在权限过期后获得访问权限。 只有在用户需要的情况下，才会授予访问权限。  
**详细信息**：使用 Azure AD Privileged Identity Management 或第三方解决方案中的实时访问来授予执行特权任务的权限。

零信任是网络安全的下一步发展。 网络攻击的状态促使组织采用“假定违规”思维方式，但这种方法不应受到限制。 零信任网络可保护公司数据和资源，同时确保组织可以使用相关技术来构建新式工作区，这些技术使员工能够以任何方式随时随地提高工作效率。

## <a name="control-routing-behavior"></a>控制路由行为
将虚拟机置于 Azure 虚拟网络时，即使其他 VM 位于不同的子网，VM 也可以连接到同一虚拟网络上的任何其他 VM。 这是可能的，原因是默认启用的系统路由集合允许这种类型的通信。 这些默认路由可让相同虚拟网络上的 VM 彼此发起连接，以及与 Internet 连接（仅适用于 Internet 的出站通信）。

尽管默认系统路由适用于许多部署方案，但有时也需要针对部署自定义路由配置。 可以配置下一个跃点地址，用于访问特定目标。

建议你在为虚拟网络部署安全设备时配置[用户定义的路由](../../virtual-network/virtual-networks-udr-overview.md)。 我们将在后面的标题为[保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)的部分中讨论此问题。

> [!NOTE]
> 不需要用户定义的路由，默认的系统路通常有效。
>
>

## <a name="use-virtual-network-appliances"></a>使用虚拟网络设备
网络安全组和用户定义的路由可以在网络和 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的传输层上提供一定的网络安全措施。 但在某些情况下，建议在高级别堆栈中启用安全性。 在此类情况下，建议部署 Azure 合作伙伴所提供的虚拟网络安全设备。

Azure 网络安全设备可提供比网络级控制所提供的更高的安全性。 虚拟网络安全设备的网络安全功能包括：


* 防火墙
* 入侵检测/入侵防护
* 漏洞管理
* 应用程序控制
* 基于网络的异常检测
* Web 筛选
* 防病毒
* 僵尸网络防护

要查找可用的 Azure 虚拟网络安全设备，请转到 [Azure 市场](https://azure.microsoft.com/marketplace/)并搜索“安全”和“网络安全”。

## <a name="deploy-perimeter-networks-for-security-zones"></a>为安全区部署外围网络
[外围网格](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)（也称为 DMZ）是物理或逻辑网络区段，可在资产与 Internet 之间提供额外的安全层。 外围网络边缘的专用网络访问控制设备只允许所需流量流入虚拟网络。

外围网络非常有用，因为可以将网络访问控制管理、监视、日志记录和报告的重点放在位于 Azure 虚拟网络边缘的设备上。 在外围网络中通常将启用分布式拒绝服务 (DDoS) 预防、入侵检测/入侵防护系统 (IDS/IPS)、防火墙规则和策略、Web 筛选、网络反恶意软件等。 网络安全设备位于 Internet 与 Azure 虚拟网络之间，在两个网络上均有接口。

尽管这是外围网络的基本设计，但有许多不同的设计，例如背靠背式、三闸式、多闸式。

基于前面提到的零信任概念，建议考虑将外围网络用于所有高安全性部署，以增强 Azure 资源的网络安全和访问控制级别。 可以使用 Azure 或第三方解决方案在资产与 Internet 之间提供额外的安全层：

- Azure 本机控制。 [Azure 防火墙](/azure/firewall/overview)和[应用程序网关中的 Web 应用程序防火墙](../../application-gateway/features.md#web-application-firewall)通过完全有状态防火墙即服务、内置高可用性、无限制的云可伸缩性、FQDN 筛选、对 OWASP 核心规则集的支持以及简单的设置和配置，来提供基本安全性。
- 第三方产品/服务。 在 [Azure 市场](https://azuremarketplace.microsoft.com/)中搜索下一代防火墙 (NGFW) 和其他第三方产品/服务，它们可提供熟悉的安全工具和显著增强的网络安全级别。 配置可能会更加复杂，但第三方产品/服务可能会允许你使用现有功能和技能组。

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>避免向具有专用 WAN 链接的 Internet 公开
许多组织选择了混合 IT 路由。 使用混合 IT 时，有些企业的信息资产是在 Azure 中，而有些企业则维持在本地。 在许多情况下，服务的某些组件在 Azure 中运行，而其他组件则维持在本地。

在混合 IT 方案中，通常有某种类型的跨界连接。 跨界连接可让公司将其本地网络连接到 Azure 虚拟网络。 可用的跨界连接解决方案有两种：

* [站点到站点 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。 它是一种值得信赖、可靠且成熟的技术，但连接是通过 Internet 进行的。 带宽限制在 1.25 Gbps 左右。 在某些情况下，站点到站点 VPN 是一个理想选择。
* Azure ExpressRoute。 建议使用 [ExpressRoute](../../expressroute/expressroute-introduction.md) 进行跨界连接。 使用 ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。 ExpressRoute 是你本地位置与 Microsoft Exchange 托管提供商之间专用的 WAN 链接。 因为这是电信运营商连接，所以数据不会通过 Internet 传输，也不会暴露在 Internet 通信的潜在风险中。

ExpressRoute 连接的位置可能会影响防火墙容量、可伸缩性、可靠性和网络流量可见性。 需要确定在现有（本地）网络中终止 ExpressRoute 的位置。 可以：

- 如果需要查看流量、需要继续执行隔离数据中心的现有做法或者只是将 extranet 资源放在 Azure 上，请在防火墙之外终止（外围网络范例）。
- 在防火墙之内终止（网络扩展范例）。 这是默认建议。 在所有其他情况下，建议将 Azure 视为第 n 个数据中心。

## <a name="optimize-uptime-and-performance"></a>优化运行时间和性能
如果服务已关闭，便无法访问信息。 如果性能太差而无法使用数据，则可以将此数据视为无法访问。 从安全角度来看，需要尽可能确保服务有最佳的运行时间和性能。

用于增强可用性和性能的常用且有效的方法是负载均衡。 负载均衡是将网络流量分布于服务中各服务器的方法。 例如，如果服务中有前端 Web 服务器，可以使用负载均衡将流量分布于多台前端 Web 服务器。

这种流量分布将提高可用性，因为如果其中一台 Web 服务器不可用，负载均衡器停止将流量发送到该服务器，并将它重定向到仍在运行的服务器。 负载均衡还对性能有帮助，因为处理请求的处理器、网络和内存开销将分布于所有负载均衡的服务器之间。

建议尽可能为服务采用适当的负载均衡。 以下是 Azure 虚拟网络级别和全球级别的方案，以及每个级别的负载均衡选项。

**情形**：你有如下应用程序：

- 要求来自同一用户/客户端会话的请求访问相同后端虚拟机。 此类示例如购物车应用和 Web 邮件服务器。
- 仅接受安全连接，因此与服务器进行未加密的通信是不可接受的选项。
- 要求将长时间运行的同一 TCP 连接上多个 HTTP 请求路由到或负载均衡到不同的后端服务器。

**负载均衡选项**：使用 [Azure 应用程序网关](/azure/application-gateway/application-gateway-introduction)，一个 HTTP Web 流量负载均衡器。 应用程序网关支持网关上的端到端 TLS 加密和 [TLS 终止](/azure/application-gateway/application-gateway-introduction)。 然后，Web 服务器可以免受加密和解密开销以及未加密流向后端服务器的流量的负担。

**情形**：需要在位于 Azure 虚拟网络中的服务器之间对来自 Internet 的传入连接进行负载均衡。 也就是说当：

- 具有接受来自 Internet 的传入请求的无状态应用程序时。
- 不需要粘性会话或 TLS 卸载时。 粘性会话是与应用程序负载均衡一起使用的方法，用于实现服务器关联。

**负载均衡选项**：使用 Azure 门户[创建外部负载均衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)，该均衡器将多个 VM 之间的传入请求进行分散，以提供更高级别的可用性。

**情形**：需要从不在 Internet 上的 VM 对连接进行负载均衡。 大多数情况下，接受的用于进行负载均衡的连接由 Azure 虚拟网络上的设备发起，例如 SQL Server 实例或内部 Web 服务器。   
**负载均衡选项**：使用 Azure 门户[创建内部负载均衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)，该均衡器将多个 VM 之间的传入请求进行分散，以提供更高级别的可用性。

**情形**：你需要全球负载均衡，因为：

- 拥有广泛分布在多个地区的云解决方案，并且需要可能的最高级别的正常运行时间（可用性）。
- 需要可能的最高级别的正常运行时间，以确保即使整个数据中心不可用，服务仍然可用。

**负载均衡选项**：使用 Azure 流量管理器。 流量管理器可以根据用户的位置，对服务的连接进行负载均衡。

例如，如果用户从欧盟对服务发出请求，此连接会被定向到位于欧盟数据中心的服务。 这一部分的流量管理器全局负载均衡有助于改善性能，因为连接到最近的数据中心比连接到远处的数据中心还要快。

## <a name="disable-rdpssh-access-to-virtual-machines"></a>禁用对虚拟机的 RDP/SSH 访问
使用[远程桌面协议](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) 和[安全外壳](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) 协议可以访问 Azure 虚拟机。 这些协议支持远程管理 VM，并且是数据中心计算中的标准协议。

在 Internet 上使用这些协议的潜在安全问题是，攻击者可以使用[暴力破解](https://en.wikipedia.org/wiki/Brute-force_attack)技术来访问 Azure 虚拟机。 攻击者获取访问权限后，就可以使用 VM 作为破坏虚拟网络上其他计算机的启动点，甚至攻击 Azure 之外的网络设备。

我们建议禁用从 Internet 对 Azure 虚拟机的直接 RDP 和 SSH 访问。 禁用从 Internet 的直接 RDP 和 SSH 访问之后，有其他选项可用于访问这些 VM 以便进行远程管理。

**情形**：可让单个用户通过 Internet 连接到 Azure 虚拟网络。   
**选项**：[点到站点 VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) 是远程访问 VPN 客户端/服务器连接的另一种说法。 建立点到站点连接之后，用户能够使用 RDP 或 SSH 连接到位于用户通过点到站点 VPN 连接的 Azure 虚拟网络上的任何 VM。 此处假设用户有权访问这些 VM。

点到站点 VPN 比直接 RDP 或 SSH 连接更安全，因为用户必须事先通过两次身份验证才将连接到 VM。 首先，用户需要进行身份验证（并获得授权）以建立点到站点 VPN 连接。 其次，用户需要进行身份验证（并获得授权）以建立 RDP 或 SSH 会话。

**情形**：使本地网络上的用户能够连接到 Azure 虚拟网络上的 VM。   
**选项**：[站点到站点 VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) 通过 Internet 将整个网络连接到另一个网络。 可以使用站点到站点 VPN 将本地网络连接到 Azure 虚拟网络。 本地网络上的用户通过站点到站点 VPN 使用 RDP 或 SSH 协议进行连接。 不必允许通过 Internet 进行的直接 RDP 或 SSH 访问。

**情形**：使用专用的 WAN 链接提供类似于站点到站点 VPN 的功能。   
**选项**：使用 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)。 它提供类似于站点到站点 VPN 的功能。 它们的主要区别包括：

- 专用的 WAN 链接不会遍历 Internet。
- 专用的 WAN 链接通常更稳定且性能更佳。

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问
使用虚拟网络服务终结点可通过直接连接将虚拟网络专用地址空间和虚拟网络标识扩展到 Azure 服务。 使用终结点可以保护关键的 Azure 服务资源，只允许在客户自己的虚拟网络中对其进行访问。 从虚拟网络发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络中。

服务终结点提供以下优势：

- **提高了 Azure 服务资源的安全性**：使用服务终结点，可在虚拟网络中保护 Azure 服务资源。 在虚拟网络中保护服务资源可以完全消除通过公共 Internet 对这些资源进行访问，只允许来自客户自己的虚拟网络的流量，从而提高了安全性。
- **来自虚拟网络的 Azure 服务流量的最佳路由**：虚拟网络中强制 Internet 流量通过本地和/或虚拟设备（称为强制隧道）的任何路由也会强制 Azure 服务流量采用与 Internet 流量相同的路由。 服务终结点为 Azure 流量提供最佳路由。

  终结点始终将服务流量直接从虚拟网络带至 Azure 主干网络上的服务。 将流量保留在 Azure 主干网络上可以通过强制隧道持续审核和监视来自虚拟网络的出站 Internet 流量，而不会影响服务流量。 详细了解[用户定义的路由和强制隧道](../../virtual-network/virtual-networks-udr-overview.md)。

- **设置简单，管理开销更少**：不再需要使用虚拟网络中的保留公共 IP 地址通过 IP 防火墙保护 Azure 资源。 无需使用 NAT 或网关设备即可设置服务终结点。 只需单击一下子网，即可配置服务终结点。 不会产生与终结点维护相关的额外开销。

要了解服务终结点及可使用服务终结点的 Azure 服务和区域的详细信息，请参阅[虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="next-steps"></a>后续步骤
有关通过 Azure 设计、部署和管理云解决方案时可以使用的更多安全最佳做法，请参阅 [Azure 安全最佳做法和模式](best-practices-and-patterns.md)。
