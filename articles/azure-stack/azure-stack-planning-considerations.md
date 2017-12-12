---
title: "有关 Azure 堆栈的规划注意事项集成系统 |Microsoft 文档"
description: "了解可以执行哪些操作来立即计划和准备用于多节点 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: b39b3b8f73f9deef48173c712d7966eb13c6ecc3
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>有关 Azure 堆栈的规划注意事项集成系统

*适用范围： Azure 堆栈集成系统*

如果你感兴趣的集成的 Azure 堆栈系统，你将需要了解一些围绕部署和系统如何适应你的数据中心的主要规划注意事项。 本主题提供这些考虑事项的高级概述。

如果你决定购买一个集成的系统，原始设备制造商 (OEM) 硬件供应商可帮助指导你完成大部分详细的规划过程。 它们将执行实际的部署。

## <a name="management-considerations"></a>管理注意事项

Azure 堆栈是一个密封的系统，其中基础结构已被锁定同时从权限和网络透视。 网络访问控制列表 (Acl) 应用来阻止所有未经授权的传入流量和基础结构组件之间的所有不必要通信。 这使难未经授权的用户访问系统。

对于日常管理和操作，对基础结构没有不受限制的管理员权限。 Azure 堆栈运算符必须管理的系统通过管理员门户或通过 Azure 资源管理器 （通过 PowerShell 或 REST API）。 没有对系统其他管理工具，如 Hyper-v 管理器或故障转移群集管理器所访问。 为了帮助保护系统，无法在 Azure 堆栈基础结构的组件安装第三方软件 （例如，代理）。 通过 PowerShell 或 REST API，则会发生与外部管理和安全软件互操作性。

当通过警报中介步骤未解决的问题的故障排除需要更高的访问级别时，你必须使用支持。 通过支持，没有方法来提供对系统执行更高级的操作的临时的完全权限管理员访问权限。 

## <a name="deploy-connected-or-disconnected"></a>部署连接或断开连接
 
你可以选择部署 Azure 堆栈连接到 internet （和 Azure） 或断开连接。 若要从 Azure 堆栈，包括 Azure 堆栈和 Azure 之间的混合方案中获取最大益处你想要部署连接到 Azure。 下表可帮助汇总部署模式之间的主要区别。

| 区域 | 连接的模式 | 断开连接的模式 |
| -------- | ------------- | ----------|
| 标识提供者 | Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) | 仅限 AD FS |
| 应用商店联合 | 直接从 Azure 应用商店向 Azure 堆栈中的应用商店下载项 | 需要手动管理的 Azure 堆栈中的应用商店 |
| 授权模型 | 为你的使用付费或基于容量的 | 基于容量的仅|
| 修补程序和更新  | 直接向 Azure 堆栈下载更新包 | 需要可移动媒体和单独的连接的设备 |
| | | |

无法更改更高版本而不完整的系统重新部署的部署模式。

## <a name="identity-considerations"></a>标识注意事项

### <a name="choose-identity-provider"></a>选择标识提供程序

你将需要考虑你想要使用 Azure 堆栈部署，任一 Azure AD 或 AD FS 的标识提供程序。 而不完整的系统重新部署的部署后，你无法切换标识提供程序。

自选标识提供程序没有租户虚拟机、 标识系统和它们使用的帐户上的任何影响，是否可以加入 Active Directory 域，等等。这是独立的。

**请考虑使用 Azure AD 的原因**

- 你已有在 （如 Azure 或 Office 365） 的 Azure AD 中的现有投资。
- 你想要跨 Azure 和 Azure 堆栈云使用相同的标识。
- 你想要支持多租户方案，其中你可能会承载同一个 Azure 堆栈实例上的不同组织。
- 你想要使用基于 REST 的目录管理通过 Azure AD Graph 应用于预配用户、 组、 通过 Api 等。

> [!NOTE]
> 在同一时间，Azure 堆栈部署不能连接到 Azure AD 实例和现有的 AD FS 实例。 如果你部署具有 Azure AD 中，并且你想要使用现有 AD FS 实例，你可将联合，你的本地 AD FS 与 Azure AD 的实例。

**请考虑使用 AD FS 的原因**

- 没有任何或仅部分的 internet 连接。
- 没有法规遵从/自主性要求。
- 你想要使用你自己标识系统 （如您公司的 Active Directory) 的运算符和用户帐户。 若要执行此操作，你可以与现有 AD FS 实例 （在 Windows Server 2012、 2012 R2 或 2016年) 所支持的 Active Directory 联合。
- 可以没有现有的 Azure 投资，并且你不想要使用 Azure AD。

> [!NOTE]
> 你可以仅与另一个由 Active Directory 支持的 AD FS 实例联合 Azure 堆栈。 不支持其他标识提供程序。 如果你想要将 Azure 堆栈与使用其他标识提供程序，请考虑改为使用 Azure 的基于 AD 的部署。

### <a name="ad-fs-and-graph-integration"></a>AD FS 和关系图的集成

如果你选择部署为标识提供程序中使用 AD FS 的 Azure 堆栈，你必须将 Azure 堆栈上的 AD FS 实例集成使用现有 AD FS 实例通过联合信任。 这允许在现有 Active Directory 林中使用 Azure 堆栈中的资源进行身份验证的身份。

你还可以向现有的 Active Directory 集成 Azure 堆栈中的 Graph 服务。 这使您可以管理基于角色的访问控制 (RBAC) Azure 堆栈中。 委派对资源的访问后，图组件查找中使用 LDAP 协议的现有 Active Directory 林中的用户帐户。

下图显示集成的 AD FS 和 Graph 通信流。
![显示 AD FS 和 Graph 通信流的图示](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>授权模型

你必须决定你想要使用的授权模型。 对于连接部署，你可以选择为你的使用付费或基于容量的许可。 为你的使用付费需要与 Azure 报告使用情况，然后通过 Azure 商务计费的连接。 如果你部署仅基于容量的许可支持与 internet 断开了。 有关授权模型的详细信息，请参阅[打包和定价的 Microsoft Azure 堆栈](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。

## <a name="naming-decisions"></a>命名的决策

你将需要考虑你想要计划你的 Azure 堆栈命名空间，尤其是区域名称和外部域名。 你的 Azure 堆栈部署面向公众的终结点的完全限定的域名 (FQDN) 是这两个名称的组合&lt;*区域*&gt;&lt;*external_FQDN* &gt;，例如， *east.cloud.fabrikam.com*。在此示例中，Azure 堆栈门户将在以下 Url:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

下表总结了这些域命名决策。

| 名称 | 说明 | 
| -------- | ------------- | 
|区域名称 | 你的第一个 Azure 堆栈区域的名称。 此名称用作 Azure 堆栈将管理的公共虚拟 IP 地址 (Vip) FQDN 的部分。 通常情况下，区域名称将是例如数据中心位置的物理位置标识符。 | 
| 外部域名 | 具有面向外部的 Vip 的终结点域名系统 (DNS) 区域的名称。 FQDN 中用于这些公共 Vip。 | 
| 私有 （内部） 的域名 | 在 Azure 堆栈上创建基础结构管理的域 （和内部 DNS 区域） 的名称。 
| | |

## <a name="certificate-requirements"></a>证书要求

对于部署，你将需要为面向公众的终结点提供安全套接字层 (SSL) 证书。 在高级别，证书具有以下要求：

> [!IMPORTANT]
> 这篇文章中的证书信息被提供仅作为常规指导原则。 获取 Azure 堆栈的任何证书之前，使用你的 OEM 硬件合作伙伴。 他们将提供更详细的证书的指南和要求。

- 可以使用一个通配符证书，也可以使用的一组专用证书，并仅为终结点，例如存储和密钥保管库使用通配符。
- 必须由受信任的公用证书颁发机构 (CA) 颁发证书或客户管理的 CA。
 
下表显示了服务和数量的需要证书进行初始 Azure 堆栈部署的面向公众的终结点。 

| 用途 | 终结点 
| -------- | ------------- | 
| Azure 资源管理器 （管理员） | adminmanagment。[region]。[external_domain] |
| Azure 资源管理器 （用户） | 管理。[region]。[external_domain] |
| 门户 （管理员） | adminportal。[region]。[external_domain] |
| 门户 （用户） | 门户。 [region]。[external_domain] |
| 密钥保管库 （用户） | &#42;。保管库。[region]。[external_domain] |
| 密钥保管库 （管理员） | &#42;。adminvault。[region]。[external_domain] |
| 存储 | &#42;。blob。[region]。[external_domain]<br>&#42;。表。[region]。[external_domain]<br>&#42;。队列。[region]。[external_domain]  |
| 关系图 * * | 关系图。[region]。[external_domain] |
| AD FS * * | adfs。[region]。[external_domain] |
| | |

* * AD FS 部署仅需要关系图和 AD FS 终结点的证书。

如果你想要使用一个通配符证书，则需要初始 Azure 堆栈部署六个使用者备用名称 (San) 总数。 这些 San 是： 

- &#42;。[region]。[external_domain]
- &#42;。保管库。[region]。[external_domain]
- &#42;。adminvault。[region]。[external_domain]
- &#42;。blob。[region]。[external_domain]
- &#42;。表。[region]。[external_domain] 
- &#42;。队列。[region]。[external_domain]

## <a name="time-synchronization"></a>时间同步

必须将 Azure 堆栈时间服务器与通过 IP 地址解析的外部时间服务器同步。 企业网络上的时间服务器是断开连接的部署所必需的。

## <a name="network-connectivity"></a>网络连接

### <a name="dns-integration"></a>DNS 集成

若要解决从 Azure 堆栈 (例如，www.bing.com) 的外部 DNS 名称，你将需要提供 Azure 堆栈可用于为其 Azure 堆栈不是权威的 DNS 请求转发的 DNS 服务器。 作为部署输入，你必须提供至少两个服务器作为 DNS 转发器使用容错功能。

若要解决 Azure 堆栈 （例如，从企业林） 的外部 Azure 堆栈中的终结点的 DNS 名称，必须集成为 Azure 堆栈的外部的 DNS 区域托管 DNS 服务器托管你想要使用该父区域的 DNS 服务器。 这要求之间 Azure 堆栈和数据中心内的现有 DNS 区域的 DNS 名称解析。 若要完成此操作，将使用如条件转发或区域委派的方法。 我们建议条件转发如果用于 Azure 堆栈外部 DNS 命名空间可以直接控制的 DNS 服务器承载父区域。 （如果你外部的 Azure 堆栈 DNS 区域显示为你公司的域的名称 （例如，azurestack.contoso.com 和 contoso.com） 的子域，您必须改用区域委派。

### <a name="network-infrastructure"></a>网络基础结构

Azure 堆栈的网络基础结构包含的交换机配置的多个逻辑网络。 下图显示这些逻辑网络，以及它们与 top-柜顶式 (TOR)，集成基板管理控制器 (BMC) 和边框 （客户网络） 交换机。

![逻辑网络关系图和交换机连接](media/azure-stack-planning-considerations/NetworkDiagram.png)

下表显示了逻辑网络和关联必须规划的 IPv4 子网范围。

| 本地网络 | 说明 | 大小 | 
| -------- | ------------- | ------------ | 
| 公共 VIP | Azure 堆栈服务，由租户虚拟机使用的其余部分一小部分的公共 IP 地址。 Azure 堆栈基础结构使用此网络中的 32 个地址。 如果你打算使用 App Service 和 SQL 资源提供程序，这将使用 7 详细。 | 月 26 日 （62 主机）-/ 22 （1022年主机）<br><br>建议 = / 24 （254 个主机） | 
| 交换机基础结构 | 路由的目的，专用的点到点 IP 地址切换管理界面和环回地址分配给交换机。 | /26 | 
| 基础结构 | 用于 Azure 堆栈内部组件进行通信。 | /24 |
| Private | 用于存储网络和私有 Vip。 | /24 | 
| BMC | 用于与在物理主机上的 Bmc 进行通信。 | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>到边框的交换机的上行

你将需要配置为你的数据中心中的边框交换机的上行。 可以将从属于使用以下方法之一集成的 Azure 堆栈系统的顶部-柜顶式 (TOR) 交换机此第 3 层通信进行路由：

- 边界网关协议 (BGP) 
- 静态路由

我们建议 BGP，因为它允许发布的软件负载平衡多路复用器 (SLB MUX) 到外部网络的路由的自动更新。 这是重要信息： 如果在部署后添加公共 IP 地址范围。 如果这样做 BGP 对等互连从 TOR 交换机到聚合交换机中，TOR 交换机连接到、 公共 IP 地址范围，你将添加部署后自动播发到无需手动干预的聚合交换机。 如果你使用静态路由，必须手动更新的新范围的路由，每次添加一个公共 IP 地址块。

### <a name="proxy-server"></a>代理服务器

Azure 堆栈仅支持透明的代理服务器。 透明代理截获在网络层的请求，而无需任何特殊的客户端配置。

### <a name="publish-azure-stack-services"></a>发布 Azure 堆栈服务

你将需要 Azure 堆栈服务向用户提供从外部 Azure 堆栈。 Azure 堆栈将设置其基础结构角色的各种终结点。 这些终结点从公共 IP 地址池分配 Vip。 为每个终结点在部署时指定的外部 DNS 区域中创建 DNS 条目。 例如，用户门户中分配的 DNS 主机条目的"门户。 <*区域*>。 <*external_FQDN*>。" 

#### <a name="ports-and-urls"></a>端口和 Url

若要使 Azure 堆栈的服务 （如门户，Azure 资源管理器、 DNS 等） 可用的外部网络，则必须允许与这些终结点的入站的流量特定 Url、 端口和协议。
 
在部署中其中的透明代理上行到传统的代理服务器，你必须允许特定端口和 Url 用于出站通信。 这些包括端口和 Url 标识、 应用商店联合、 修补程序和更新、 注册和使用情况数据。

有关详细信息，请参阅：
- [入站的端口和协议](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [出站端口和 Url](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>连接到 Azure 的 Azure 堆栈

对于混合云方案，你需要规划要连接到 Azure 的 Azure 堆栈的方式。 有两个受支持的方法，可将 Azure 堆栈中的虚拟网络连接到 Azure 中的虚拟网络： 
- **站点到站点**。 IPsec （IKE v1 和 IKE v2） 通过虚拟专用网络 (VPN) 连接。 此类型的连接需要 VPN 设备或路由和远程访问服务 (RRAS)。 有关在 Azure 中的 VPN 网关的详细信息，请参阅[有关 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 通过此隧道的通信进行加密和安全。 但是，带宽受到隧道 (100-200 Mbps) 的最大吞吐量。
- **出站 NAT**。 默认情况下，Azure 堆栈中的所有虚拟机将都具有与通过出站 nat。 外部网络建立连接 在 Azure 堆栈中创建的每个虚拟网络获取公共 IP 地址分配给它。 是否虚拟机直接分配公共 IP 地址，或使用公共 IP 地址的负载平衡器后，它会将通过使用虚拟网络的 VIP 的出站 NAT 的出站访问。 这适用于由虚拟机启动和发送到外部网络 （internet 或 intranet） 的通信仅。 它不用于外部与虚拟机进行通信。

#### <a name="hybrid-connectivity-options"></a>混合连接选项

混合连接，务必要考虑你想要提供哪种类型的部署和部署位置。 你将需要考虑是否需要将每个租户的网络流量隔离和是否将有 intranet 或 internet 部署。

- **单租户 Azure 堆栈**。 看起来，至少从网络的角度来看，就像它是一个租户 Azure 堆栈部署。 可以有许多租户订阅，但与 intranet 服务，所有通信都经过同一个网络。 从一个订阅的网络流量传输通过作为另一个订阅相同的网络连接，并不需要进行隔离通过加密的隧道。

- **多租户 Azure 堆栈**。 为网络外部的 Azure 堆栈绑定的每个租户订阅的流量必须与其他租户的网络通信隔离 Azure 堆栈部署。
 
- **Intranet 部署**。 位于企业 intranet，通常出现在专用 IP 地址空间和一个或多个防火墙后 Azure 堆栈部署。 公共 IP 地址不真正公共的因为它们不能直接通过公共 internet 进行路由。

- **Internet 部署**。 连接到公共 Azure 堆栈部署为公用 VIP 范围内的 internet，并使用 internet 连接的公共 IP 地址。 部署可以仍位于防火墙后面，但公共 VIP 范围可以从公共 internet 和 Azure 直接访问。
 
下表总结了混合连接方案，对专业人员、 利弊和用例。

| 方案 | 连接方法 | 优点 | 缺点 | 适用于 |
| -- | -- | --| -- | --|
| 单租户 Azure 堆栈，intranet 部署 | 出站 NAT | 更高的速度更快的传输的带宽。 易于实现;所需的任何网关。 | 通信未加密;无隔离或超出 TOR 的加密。 | 所有租户都都同样受信任的企业部署。<br><br>具有 Azure ExpressRoute 线路到 Azure 的企业。 |
| 多租户 Azure 堆栈，intranet 部署 | 站点到站点 VPN | 来自租户 VNet 流量引导至目标是安全的。 | 带宽受限制站点到站点 VPN 隧道。<br><br>需要在虚拟网络和目标网络上的 VPN 设备网关。 | 必须从其他租户保护企业部署，其中一些租户通信。 |
| 单租户 Azure 堆栈，internet 部署 | 出站 NAT | 更高的速度更快的传输的带宽。 | 通信未加密;无隔离或超出 TOR 的加密。 | 托管租户其中获取其自己的 Azure 堆栈部署和一条专用的线路到 Azure 堆栈环境的方案。 例如，ExpressRoute 和多协议标签切换 (MPLS)。
| 多租户 Azure 堆栈，internet 部署 | 站点到站点 VPN | 来自租户 VNet 流量引导至目标是安全的。 | 带宽受限制站点到站点 VPN 隧道。<br><br>需要在虚拟网络和目标网络上的 VPN 设备网关。 | 托管提供程序要提供多租户云方案，其中租户不信任彼此和流量必须进行加密。
|  |  |  |  |  |

#### <a name="using-expressroute"></a>使用 ExpressRoute

可以到通过 Azure 连接 Azure 堆栈[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)于单租户 intranet 和多租户方案。 你将需要通过预配的 ExpressRoute 线路[连接提供商](https://docs.microsoft.com/azure/expressroute/expressroute-locations)。

下图显示 ExpressRoute 单租户方案 (其中"客户的连接"为 ExpressRoute 线路)。

![关系图显示单租户 ExpressRoute 方案](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

下图显示多租户方案 ExpressRoute。

![关系图显示多租户 ExpressRoute 方案](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部监视
若要获取的所有警报的单一视图从你的 Azure 堆栈部署和设备，并将警报集成到现有 IT 服务管理的工作流对于票证，你可以监视解决方案的外部数据中心与集成 Azure 堆栈。

包括 Azure 堆栈解决方案，硬件生命周期主机是硬件的 OEM 供应商提供的管理工具运行的 Azure 堆栈之外的计算机。 你可以使用这些工具或其他直接将与你的数据中心中的现有监视解决方案的集成的解决方案。

下表总结了当前可用选项的列表。

| 区域 | 外部的监视解决方案 |
| -- | -- |
| Azure 堆栈软件 | - [Azure 堆栈适用于 Operations Manager 管理包](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios 插件](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-基于 REST 的 API 调用 | 
| 物理服务器 (通过 IPMI Bmc) | Operations Manager 供应商管理包<br>OEM 硬件供应商提供的解决方案<br>硬件供应商 Nagios 插件 | OEM 合作伙伴支持的监视解决方案 （包括） | 
| 网络设备 (SNMP) | Operations Manager 网络设备发现<br>OEM 硬件供应商提供的解决方案<br>-Nagios 交换机插件 |
| 租户订阅运行状况监视 | - [System Center Management Pack for Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

请注意以下要求：
- 你使用的解决方案必须是无代理。 你无法安装在 Azure 堆栈组件内的第三方代理。 
- 如果你想要使用 System Center Operations Manager，这需要 Operations Manager 2012 R2 或 Operations Manager 2016。

## <a name="backup-and-disaster-recovery"></a>备份和灾难恢复

规划备份和灾难恢复涉及规划这两个基础 Azure 堆栈承载的基础结构 IaaS 虚拟机和 PaaS 服务，以及租户应用程序和数据。 必须单独规划对于这些数据。

### <a name="protect-infrastructure-components"></a>保护基础结构组件

Azure 堆栈备份到你指定的共享基础结构组件。

- 你将需要上现有的基于 Windows 的文件服务器或第三方设备的外部 SMB 文件共享。
- 为网络交换机和硬件生命周期主机的备份，应使用此相同的共享。 OEM 硬件供应商将帮助提供有关备份和还原这些组件的指南，因为这是外部的 Azure 堆栈。 你负责运行备份的工作流根据 OEM 供应商的建议。

如果发生灾难性数据丢失，则可以使用基础结构备份到进行种子重新设定部署数据，如部署输入和标识符服务帐户，CA 根证书、 （在断开连接的部署） 的联合的资源、 计划、 产品/服务，订阅、 配额、 RBAC 策略和角色分配和密钥保管库密钥中。
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>保护 IaaS 虚拟机上的租户应用程序

Azure 堆栈不会备份最多租户应用程序和数据。 你必须规划备份和灾难恢复保护到 Azure 堆栈外部的目标。 租户保护是一个租户驱动的活动。 对于 IaaS 虚拟机，租户可以使用来宾内技术来保护文件的文件夹、 应用程序数据和系统状态。 但是，为企业或服务提供程序，你可能想要提供同一数据中心内或外置方式在云中备份和恢复解决方案。

若要备份 Linux 或 Windows IaaS 虚拟机，你必须有权访问来宾操作系统使用备份产品来保护文件、 文件夹、 操作系统状态和应用程序数据。 你可以使用 Azure Backup 中，System Center Data Center Protection Manager，或支持第三方产品。

若要将数据复制到辅助位置，并安排应用程序故障转移，如果发生灾难，可以使用 Azure Site Recovery 或受支持的第三方产品。 （初始版本的集成系统，在 Azure Site Recovery 不支持故障回复。 但是，你可以获得通过手动过程的故障回复。）此外，支持 （如 Microsoft SQL Server) 的本机复制的应用程序可以将数据复制到应用程序正在其中运行的另一个位置。

> [!IMPORTANT]
> 在集成的系统的初始版本，我们将支持 IaaS 虚拟机的来宾级别的工作的保护技术。 无法在底层基础结构服务器上安装代理。

## <a name="next-steps"></a>后续步骤

- 有关用例、 购买、 合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure 堆栈](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure 堆栈信息的路线图和地域可用性集成的系统，请参阅白皮书： [Azure 堆栈： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
