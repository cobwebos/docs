---
title: "Azure 安全性和符合性蓝图 - UK-OFFICIAL 三层式 Web 应用程序自动化"
description: "Azure 安全性和符合性蓝图 - UK-OFFICIAL 三层式 Web 应用程序自动化"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 9d95ccdd536efbff1540fab2b564e7745f5ac397
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2018
---
# <a name="azure-security-and-compliance-blueprint---uk-offical-three-tier-web-applications-automation"></a>Azure 安全性和符合性蓝图 - UK-OFFICIAL 三层式 Web 应用程序自动化

## <a name="overview"></a>概述

 本文提供用于交付 Microsoft Azure 三层式基于 Web 的体系结构的自动化脚本及相关指导，这些体系结构适合用于处理在英国分类为“OFFICIAL”（官方）的许多工作负荷。

 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模板集将使用“基础结构即代码”方法，部署一个符合英国国家网络安全中心 (NCSC) 14 项[云安全原则](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)和 Internet 安全中心 (CIS) [关键安全控制措施](https://www.cisecurity.org/critical-controls.cfm)的环境。

 NCSC 建议客户使用“云安全原则”评估服务的安全属性，并帮助了解客户与供应商之间的责任划分。 我们提供了有关其中每项原则的信息，以帮助读者了解责任的划分。

 Microsoft 白皮书[使用 Microsoft Azure 的英国云的 14 项云安全控制措施](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1)支持此体系结构和相应的 Azure 资源管理器模板。 此白皮书全面阐述了如何使 Azure 服务适应英国 NCSC 的 14 项云安全原则，使各组织能够审视自身的能力，在短时间内达到有关在本地以及在英国的 Microsoft Azure 云中使用基于云的服务所要满足的法规要求。

 此模板会部署工作负荷的基础结构。 必须安装并配置应用程序代码和支持性的业务层与数据层软件。 [此处](https://aka.ms/ukwebappblueprintrepo)提供了详细的部署说明。

 如果没有 Azure 订阅，可以参阅 [Azure 入门](https://azure.microsoft.com/get-started/)快速轻松地注册一个订阅。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件

 Azure 模板在 Azure 云环境中提供一个支持 UK-OFFICIAL 工作负荷的三层式 Web 应用程序体系结构。 该体系结构提供一个可将本地网络扩展到 Azure，让企业用户或 Internet 用户安全访问基于 Web 的工作负荷的安全混合环境。

![alt text](images/diagram.png?raw=true "Azure UK-OFFICAL 三层式体系结构")

 此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

(1) /16 虚拟网络 - 运营 VNet
- (3) /24 子网 - 3 层（Web、Biz（业务）、数据）
- (1) /27 子网 - ADDS
- (1) /27 子网 - 网关子网
- (1) /29 子网 - 应用程序网关子网
- 使用默认（Azure 提供）的 DNS
- 已与管理 VNet 建立对等互连
- 用于管理流量的网络安全组 (NSG)

(1) /24 虚拟网络 - 管理 VNet
- (1) /27 子网
- 使用 (2) ADD DNS 和 (1) Azure DNS 条目
- 已与运营 VNet 建立对等互连
- 用于管理流量的网络安全组 (NSG)

(1) 应用程序网关
- WAF - 已启用
- WAF 模式 - 防护
- 规则集：OWASP 3.0
- 在端口 80 上设置了 HTTP 侦听器
- 通过 NSG 调控连接/流量
- 已定义公共 IP 地址终结点 (Azure)

(1) VPN - 基于路由，站点到站点 IPSec VPN 隧道
- 已定义公共 IP 地址终结点 (Azure)
- 通过 NSG 调控连接/流量
- (1) 本地网络网关（本地终结点）
- (1) Azure 网络网关（Azure 终结点）

(9) 虚拟机 - 使用 Azure IaaS 反恶意软件 DSC 设置部署所有 VM

- (2) Active Directory 域服务域控制器 (Windows Server 2012 R2)
  - (2) DNS 服务器角色 - 每个 VM 各有 1 个
  - (2) 与运营 VNet 连接的 NIC - 每个 VM 各有 1 个
  - 两者都已加入模板中定义的域
    - 部署过程中创建的域


- (1) Jumpbox（守护主机）管理 VM
  - 管理 VNet 上有 1 个使用公共 IP 地址的 NIC
    - NSG 用于将流量（入站/出站）限制为特定的源
  - 未加入域


- (2) Web 层 VM
  - (2) IIS 服务器角色 - 每个 VM 各有 1 个
  - (2) 与运营 VNet 连接的 NIC - 每个 VM 各有 1 个
  - 未加入域


- (2) Biz 层 VM
  - (2) 与运营 VNet 连接的 NIC - 每个 VM 各有 1 个
  - 未加入域


- (2) 数据层 VM
  - (2) 与运营 VNet 连接的 NIC - 每个 VM 各有 1 个
  - 未加入域

可用性集
- (1) Active Directory 域控制器 VM 集 - 2 个 VM
- (1) Web 层 VM 集 - 2 个 VM
- (1) Biz 层 VM 集 - 2 个 VM
- (1) 数据层 VM 集 - 2 个 VM

负载均衡器
- (1) Web 层负载均衡器
- (1) Biz 层负载均衡器
- (1) 数据层负载均衡器

存储
- (14) 存储帐户总数
  - Active Directory 域控制器可用性集
    - (2) 主要本地冗余存储 (LRS) 帐户 - 每个 VM 各有 1 个  
    - (1) ADDS 可用性集的诊断本地冗余存储 (LRS) 帐户
  - 管理 Jumpbox VM
    - (1) Jumpbox VM 的主要本地冗余存储 (LRS) 帐户
    - (1) Jumpbox VM 的诊断本地冗余存储 (LRS) 帐户
  - Web 层 VM
    - (2) 主要本地冗余存储 (LRS) 帐户 - 每个 VM 各有 1 个  
    - (1) Web 层可用性集的诊断本地冗余存储 (LRS) 帐户
  - Biz 层 VM
    - (2) 主要本地冗余存储 (LRS) 帐户 - 每个 VM 各有 1 个  
    - (1) Biz 层可用性集的诊断本地冗余存储 (LRS) 帐户
  - 数据层 VM
    - (2) 主要本地冗余存储 (LRS) 帐户 - 每个 VM 各有 1 个  
    - (1) 数据层可用性集的诊断本地冗余存储 (LRS) 帐户

### <a name="deployment-architecture"></a>部署体系结构：

**本地网络**：在组织中实施的专用局域网。

**生产 VNet**：生产 [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview)（虚拟网络）托管 Azure 中运行的应用程序和其他运营资源。 每个 VNet 可以包含用于隔离和管理网络流量的多个子网。

**Web 层**：处理传入的 HTTP 请求。 通过此层返回响应。

**业务层**：实施系统的业务流程和其他功能逻辑。

**数据库层**：提供持久性数据存储，使用 [SQL Server Always On 可用性组](https://msdn.microsoft.com/library/hh510230.aspx)实现高可用性。 客户可以使用 [Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)作为一种 PaaS 替代方案。

**网关**：[VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)在本地网络中的路由器与生产 VNet 之间提供连接。

**Internet 网关和公共 IP 地址**：Internet 网关通过 Internet 向用户公开应用程序服务。 结合 Web 应用程序防火墙 (WAF) 保护，使用可提供第 7 层路由和负载均衡功能的[应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)来保护访问这些服务的流量。

**管理 VNet**：此 [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) 包含针对生产 VNet 中运行的工作负荷实施管理和监视功能的资源。

**Jumpbox**：也称为[守护主机](https://en.wikipedia.org/wiki/Bastion_host)，它是网络中的一个安全 VM，管理员可使用它来连接到生产 VNet 中的 VM。 Jumpbox 中的某个 NSG 只允许来自安全列表中的公共 IP 地址的远程流量。 若要允许远程桌面 (RDP) 流量，需要在该 NSG 中定义流量的源。 使用受保护的 Jumpbox VM 通过 RDP 管理生产资源。

**用户定义的路由**：[用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)用于定义 Azure VNet 中的 IP 流量流。

**已建立网络对等互连的 VNet**：使用 [VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)连接生产环境和管理 VNet。
这些 VNet 仍作为单独的资源进行管理，但在建立各种连接时，这些虚拟机上只显示一个 VNet。 这些网络直接使用专用 IP 地址相互通信。 只能在同一 Azure 区域中的 VNet 之间建立 VNet 对等互连。

**网络安全组**：[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 包含允许或拒绝 VNet 中流量的访问控制列表。 NSG 可用于在子网或单个 VM 的级别保护流量。

**Active Directory 域服务 (AD DS)**：此体系结构提供专用的 [Active Directory 域服务](https://technet.microsoft.com/library/hh831484.aspx)部署。

**日志记录和审核**：[Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)捕获针对订阅中的资源执行的操作，例如，操作的发起者、操作发生时间、操作状态，以及可能有助于调查该操作的其他属性值。 Azure 活动日志是一个 Azure 平台服务，可捕获针对订阅执行的所有操作。 可根据需要将日志存档或导出。

**网络监视和警报**：[Azure 网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)是一个平台服务，可针对 VNet 中的网络流量提供网络数据包捕获、流日志记录、拓扑工具和诊断。

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="business-continuity"></a>业务连续性

**高可用性**：服务器工作负荷将分组到[可用性集](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，帮助确保 Azure 中虚拟机的高可用性。 此配置有助于确保在发生计划内或计划外维护事件时至少有一个虚拟机可用，并满足 99.95% 的 Azure SLA 要求。

### <a name="logging-and-audit"></a>日志记录和审核

**监视**：[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) 是一个平台服务，为监视所有 Azure 资源的活动日志、指标和诊断日志提供一个中心位置。 可将 Azure Monitor 配置为可视化、查询、路由、存档和处理来自 Azure 中资源的指标和日志。 建议使用基于资源的访问控制来保护审核线索，帮助确保用户无法修改日志。

**活动日志**：配置 [Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)可提供针对订阅中资源执行的操作的深入信息。

**诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是资源发出的所有日志。 这些日志可能包括 Windows 事件系统日志，以及 Blob、表和队列的日志。

**防火墙日志**：应用程序网关提供完整的诊断和访问日志。 防火墙日志可用于已启用 WAF 的应用程序网关资源。

**日志存档**：可将日志数据存储配置为写入集中式 Azure 存储帐户，以根据定义的保留期存档。 可以使用 Azure Log Analytics 或第三方 SIEM 系统处理日志。

### <a name="identity"></a>标识

**Active Directory 域服务**：此体系结构在 Azure 中提供 Active Directory 域服务。 有关在 Azure 中实施 Active Directory 的具体建议，请参阅以下文章：

[将 Active Directory 域服务 (AD DS) 扩展到 Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain)。

[有关在 Azure 虚拟机上部署 Windows Server Active Directory 的指导](https://msdn.microsoft.com/library/azure/jj156090.aspx)。

**Active Directory 集成**：客户可能希望使用 [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) 集成或 [Azure 中已加入本地林的 Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest) 作为专用 AD DS 体系结构的替代方案。

### <a name="security"></a>“安全”

**管理安全性**：此蓝图允许管理员使用 RDP 从受信任的源连接到管理 VNet 和 Jumpbox。 使用 NSG 控制管理 VNet 的网络流量。 仅限受信任 IP 范围内可访问包含 Jumpbox 的子网的流量访问端口 3389。

连接到管理 VNet 和 Jumpbox 时，客户也可以考虑使用[增强的安全管理模型](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access)来保护环境。 建议想要增强安全性的客户使用[特权访问工作站](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw)和 RDGateway 配置。 使用网络虚拟设备和公共/专用外围网络会进一步增强安全性。

**保护网络**：建议对每个子网使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG)，以针对入站流量提供第二层保护，并绕过错误配置的或已禁用的网关。 示例 - [用于部署 NSG 的资源管理器模板](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups)。

**保护公共终结点**：Internet 网关通过 Internet 向用户公开应用程序服务。 可使用提供 Web 应用程序防火墙和 HTTPS 协议管理的[应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)保护访问这些服务的流量。

**IP 范围**：体系结构中的 IP 范围是建议的范围。 建议客户考虑自身的环境，并使用适当的范围。

**混合连接**：基于云的工作负荷使用 Azure VPN 网关通过 IPSEC VPN 连接到本地数据中心。 客户应确保使用适当的 VPN 网关连接到 Azure。 示例 - [VPN 网关资源管理器模板](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection)。 运行大规模任务关键型工作负荷且要满足大数据要求的客户可能需要考虑混合网络体系结构，使用 [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) 来与 Microsoft 云服务建立专用网络连接。

**关注点分离**：此参考体系结构将用于管理运营和业务运营的 VNet 分离开来。 将 VNet 和子网分离可以实现流量管理，包括根据 [Microsoft 云服务和网络安全](https://docs.microsoft.com/azure/best-practices-network-security)最佳做法，在网段之间使用 NSG 来限制传入和传出流量。

**资源管理**：将 VM、VNet 和负载均衡器等 Azure 资源分组到 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource)可对其进行管理。 然后，可将基于资源的访问控制角色分配到每个资源组，以将访问权限限制给已获授权的用户。

**访问控制限制**：使用[基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) 管理应用程序中的资源；可以使用[自定义角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) RBAC 来限制 DevOps 可在每个层上执行的操作。 授予权限时，请使用[最低特权原则](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1)。 记录所有管理操作并执行定期审核，确保所有配置更改按计划进行。

**Internet 访问**：此参考体系结构利用 [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)作为面向 Internet 的网关和负载均衡器。 某些客户还可以考虑使用第三方网络虚拟设备作为附加的网络安全层，取代 [Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)。

**Azure 安全中心**：[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)在一个中心视图中提供订阅中资源的安全状态，并提供建议帮助避免资源泄密。 此外，它还可用于启用更精细的策略。 例如，可将策略应用到特定的资源组，使企业能够根据风险调整方向。 建议客户在其 Azure 订阅中启用 Azure 安全中心。

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC 云安全原则符合性文档

Crown Commercial Service（一家致力于改善政府商业和采购活动的机构）续订了 Microsoft 的分层式企业云服务并将级别提升到 G-Cloud v6，其中涵盖 OFFICIAL 级别的所有产品/服务。 在 [Azure 英国 G-Cloud 安全评估摘要](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud)中可以找到 Azure 和 G-Cloud 的详细信息。

此蓝图符合 NCSC [云安全原则](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)中所述的 14 项云安全原则，可帮助确保所用环境支持分类为“UK-OFFICIAL”的工作负荷。

[客户责任矩阵](https://aka.ms/blueprintuk-gcrm)（Excel 工作簿）中列出了所有 14 项云安全原则，并指明每项原则（或原则的细则）的实施是由 Microsoft、客户还是两者共同负责。

[原则实施矩阵](https://aka.ms/ukwebappblueprintpim)（Excel 工作簿）中列出了所有 14 项云安全原则，并针对在“客户责任矩阵”中指定由客户负责的每项原则（或原则的细则）指明：1) 蓝图自动化是否实施该原则；2) 如何根据原则要求完成实施的说明。 [此处](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md)也提供了这些内容。

此外，云安全联盟 (CSA) 也发布了“云控制矩阵”，以帮助客户评估云提供商，以及确定在转移到云服务之前所要解答的问题。 作为回应，Microsoft Azure 解答了 CSA 舆论评估计划调查问卷 ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA))，其中阐述了 Microsoft 如何满足所提议的原则。

## <a name="deploy-the-solution"></a>部署解决方案

部署用户可使用两种方法部署此蓝图自动化。 第一种方法使用 PowerShell 脚本，而第二种方法使用 Azure 门户部署参考体系结构。 [此处](https://aka.ms/ukwebappblueprintrepo)提供了详细的部署说明。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
