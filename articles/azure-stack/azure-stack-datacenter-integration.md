---
title: "有关 Azure 堆栈的常规数据中心集成注意事项集成系统 |Microsoft 文档"
description: "了解可以执行哪些操作来现在规划和准备数据中心与多节点 Azure 堆栈的集成。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: f93fc95d6bed517cae3adb706f690941f97c366e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>有关集成 Azure 堆栈系统的数据中心集成注意事项
如果你感兴趣的集成的 Azure 堆栈系统，你应了解一些围绕部署和系统如何适应你的数据中心的主要规划注意事项。 本文概述了这些注意事项，可帮助你做出你 Azure 堆栈的多节点系统的重要的基础结构决策。 使用你的 OEM 硬件供应商联系，与它们部署到你的数据中心的 Azure 堆栈时，可帮助了解这些注意事项。  

> [!NOTE]
> 仅可以从授权的硬件供应商购买 azure 堆栈多节点系统。 

若要部署 Azure 堆栈，你需要在部署开始帮助转迅速和顺利地进程之前给你的解决方案提供商提供规划信息。 将必需的范围信息跨网络、 安全和标识信息与很多可能需要从许多不同的区域和决策者的知识的重要决策。 因此，你可能需要拉入与你的组织中的多个团队的人员，以确保部署开始之前已准备好所有必需的信息。 它可以帮助进行对话硬件供应商时收集此信息，因为它们可能具有建议对你决定有所帮助。

时研究和收集所需的信息，你可能需要对你的网络环境进行一些预部署配置更改。 这可能包括保留 Azure 堆栈解决方案中，配置你的路由器、 交换机和防火墙来准备新的 Azure 堆栈解决方案交换机连接的 IP 地址空间。 请确保有帮助你规划主题区域专家达内联。

## <a name="management-considerations"></a>管理注意事项
Azure 堆栈是一个密封的系统，其中基础结构已被锁定同时从权限和网络透视。 网络访问控制列表 (Acl) 应用来阻止所有未经授权的传入流量和基础结构组件之间的所有不必要通信。 这使难未经授权的用户访问系统。

对于日常管理和操作，对基础结构没有不受限制的管理员权限。 Azure 堆栈运算符必须管理的系统通过管理员门户或通过 Azure 资源管理器 （通过 PowerShell 或 REST API）。 没有对系统其他管理工具，如 Hyper-v 管理器或故障转移群集管理器所访问。 为了帮助保护系统，无法在 Azure 堆栈基础结构的组件安装第三方软件 （例如，代理）。 通过 PowerShell 或 REST API，则会发生与外部管理和安全软件互操作性。

当通过警报中介步骤未解决的问题的故障排除需要更高的访问级别时，你必须使用 Microsoft 支持。 通过支持，没有方法来提供对系统执行更高级的操作的临时的完全权限管理员访问权限。 

## <a name="identity-considerations"></a>标识注意事项

### <a name="choose-identity-provider"></a>选择标识提供程序
你将需要考虑你想要使用 Azure 堆栈部署，任一 Azure AD 或 AD FS 的标识提供程序。 而不完整的系统重新部署的部署后，你无法切换标识提供程序。

自选标识提供程序没有租户虚拟机、 标识系统和它们使用的帐户上的任何影响，是否可以加入 Active Directory 域，等等。这是独立的。

你可以了解有关选择中的标识提供程序[Azure 堆栈集成的系统连接模型文章](.\azure-stack-connection-models.md)。

### <a name="ad-fs-and-graph-integration"></a>AD FS 和关系图的集成
如果你选择部署为标识提供程序中使用 AD FS 的 Azure 堆栈，你必须将 Azure 堆栈上的 AD FS 实例集成使用现有 AD FS 实例通过联合信任。 这允许在现有 Active Directory 林中使用 Azure 堆栈中的资源进行身份验证的身份。

你还可以向现有的 Active Directory 集成 Azure 堆栈中的 Graph 服务。 这使您可以管理基于角色的访问控制 (RBAC) Azure 堆栈中。 委派对资源的访问后，图组件查找中使用 LDAP 协议的现有 Active Directory 林中的用户帐户。

下图显示集成的 AD FS 和 Graph 通信流。
![显示 AD FS 和 Graph 通信流的图示](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>授权模型
你必须决定你想要使用的授权模型。 可用选项取决于部署连接到 internet 的 Azure 堆栈：
- 有关[连接部署](azure-stack-connected-deployment.md)，你可以选择为你的使用付费或基于容量的许可。 为你的使用付费需要与 Azure 报告使用情况，然后通过 Azure 商务计费的连接。 
- 如果仅基于容量的许可支持你[部署断开连接](azure-stack-disconnected-deployment.md)从 internet。 

有关授权模型的详细信息，请参阅[打包和定价的 Microsoft Azure 堆栈](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。


## <a name="naming-decisions"></a>命名的决策

你将需要考虑你想要计划你的 Azure 堆栈命名空间，尤其是区域名称和外部域名。 外部完全限定的域名 (FQDN) 的 Azure 堆栈部署面向公众的终结点是这两个名称的组合： &lt;*区域*&gt;。&lt;*fqdn*&gt;。 例如， *east.cloud.fabrikam.com*。在此示例中，Azure 堆栈门户将在以下 Url:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> 为你的 Azure 堆栈部署选择的区域名称必须是唯一的并将显示在门户的地址。 

下表总结了这些域命名决策。

| 名称 | 说明 | 
| -------- | ------------- | 
|区域名称 | 你的第一个 Azure 堆栈区域的名称。 此名称用作 Azure 堆栈将管理的公共虚拟 IP 地址 (Vip) FQDN 的部分。 通常情况下，区域名称将是例如数据中心位置的物理位置标识符。 | 
| 外部域名 | 具有面向外部的 Vip 的终结点域名系统 (DNS) 区域的名称。 FQDN 中用于这些公共 Vip。 | 
| 私有 （内部） 的域名 | 在 Azure 堆栈上创建基础结构管理的域 （和内部 DNS 区域） 的名称。 
| | |

## <a name="certificate-requirements"></a>证书要求

对于部署，你将需要为面向公众的终结点提供安全套接字层 (SSL) 证书。 在高级别，证书具有以下要求：

- 可以使用一个通配符证书，也可以使用的一组专用证书，并仅为终结点，例如存储和密钥保管库使用通配符。
- 可以通过公共受信任的证书颁发机构 (CA) 颁发证书或客户管理的 CA。

有关哪些 PKI 证书部署所需 Azure 堆栈，以及如何获取它们，请参阅[Azure 堆栈公钥基础结构证书要求](azure-stack-pki-certs.md)。  


> [!IMPORTANT]
> 作为常规指导原则，应使用提供的 PKI 证书信息。 获取 Azure 堆栈任何 PKI 证书之前，使用你的 OEM 硬件合作伙伴。 他们将提供更详细的证书的指南和要求。


## <a name="time-synchronization"></a>时间同步
必须选择将使用与服务器同步 Azure 堆栈的具体时间。  时间 symbolization 对 Azure 堆栈及其基础结构角色，至关重要，因为它用于生成用于对内部服务相互进行身份验证的 Kerberos 票证。

你必须指定的 IP 时间同步服务器，但大部分基础结构中的组件可以解析 URL，一些只能支持 IP 地址。 如果你是使用断开连接的部署选项，则必须指定确保可以从 Azure 堆栈中的基础结构网络访问，则你企业网络上的时间服务器。

## <a name="connect-azure-stack-to-azure"></a>连接到 Azure 的 Azure 堆栈

对于混合云方案，你需要规划要连接到 Azure 的 Azure 堆栈的方式。 有两个受支持的方法，可将 Azure 堆栈中的虚拟网络连接到 Azure 中的虚拟网络： 
- **站点到站点**。 IPsec （IKE v1 和 IKE v2） 通过虚拟专用网络 (VPN) 连接。 此类型的连接需要 VPN 设备或路由和远程访问服务 (RRAS)。 有关在 Azure 中的 VPN 网关的详细信息，请参阅[有关 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 通过此隧道的通信进行加密和安全。 但是，带宽受到隧道 (100-200 Mbps) 的最大吞吐量。
- **出站 NAT**。 默认情况下，Azure 堆栈中的所有虚拟机将都具有与通过出站 nat。 外部网络建立连接 在 Azure 堆栈中创建的每个虚拟网络获取公共 IP 地址分配给它。 是否虚拟机直接分配公共 IP 地址，或使用公共 IP 地址的负载平衡器后，它会将通过使用虚拟网络的 VIP 的出站 NAT 的出站访问。 这适用于由虚拟机启动和发送到外部网络 （internet 或 intranet） 的通信仅。 它不用于外部与虚拟机进行通信。

### <a name="hybrid-connectivity-options"></a>混合连接选项

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

### <a name="using-expressroute"></a>使用 ExpressRoute

可以到通过 Azure 连接 Azure 堆栈[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)于单租户 intranet 和多租户方案。 你将需要通过预配的 ExpressRoute 线路[连接提供商](https://docs.microsoft.com/azure/expressroute/expressroute-locations)。

下图显示 ExpressRoute 单租户方案 (其中"客户的连接"为 ExpressRoute 线路)。

![关系图显示单租户 ExpressRoute 方案](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

下图显示多租户方案 ExpressRoute。

![关系图显示多租户 ExpressRoute 方案](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部监视
若要获取的所有警报的单一视图从你的 Azure 堆栈部署和设备，并将警报集成到现有 IT 服务管理的工作流对于票证，你可以[与外部数据中心监视解决方案集成Azure堆栈](azure-stack-integrate-monitor.md).

包括 Azure 堆栈解决方案，硬件生命周期主机是硬件的 OEM 供应商提供的管理工具运行的 Azure 堆栈之外的计算机。 你可以使用这些工具或其他直接将与你的数据中心中的现有监视解决方案的集成的解决方案。

下表总结了当前可用选项的列表。

| 区域 | 外部的监视解决方案 |
| -- | -- |
| Azure 堆栈软件 | [Azure 堆栈适用于 Operations Manager 管理包](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios 插件](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>基于 REST 的 API 调用 | 
| 物理服务器 (通过 IPMI Bmc) | OEM 硬件-Operations Manager 供应商管理包<br>OEM 硬件供应商提供的解决方案<br>硬件供应商 Nagios 插件 | OEM 合作伙伴支持的监视解决方案 （包括） | 
| 网络设备 (SNMP) | Operations Manager 网络设备发现<br>OEM 硬件供应商提供的解决方案<br>Nagios 交换机插件 |
| 租户订阅运行状况监视 | [System Center Management Pack for Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

请注意以下要求：
- 你使用的解决方案必须是无代理。 你无法安装在 Azure 堆栈组件内的第三方代理。 
- 如果你想要使用 System Center Operations Manager，则需要 Operations Manager 2012 R2 或 Operations Manager 2016。

## <a name="backup-and-disaster-recovery"></a>备份和灾难恢复

规划备份和灾难恢复涉及规划这两个基础 Azure 堆栈承载的基础结构 IaaS 虚拟机和 PaaS 服务，以及租户应用程序和数据。 必须单独规划对于这些数据。

### <a name="protect-infrastructure-components"></a>保护基础结构组件

你可以[备份 Azure 堆栈](azure-stack-backup-back-up-azure-stack.md)基础结构组件到 SMB 共享，你可以指定：

- 你将需要上现有的基于 Windows 的文件服务器或第三方设备的外部 SMB 文件共享。
- 为网络交换机和硬件生命周期主机的备份，应使用此相同的共享。 OEM 硬件供应商将帮助提供有关备份和还原这些组件的指南，因为这是外部的 Azure 堆栈。 你负责运行备份的工作流根据 OEM 供应商的建议。

如果发生灾难性数据丢失，则可以使用基础结构备份到进行种子重新设定部署数据，如部署输入和标识符服务帐户，CA 根证书、 （在断开连接的部署） 的联合的资源、 计划、 产品/服务，订阅、 配额、 RBAC 策略和角色分配和密钥保管库密钥中。
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>保护 IaaS 虚拟机上的租户应用程序

Azure 堆栈不会备份最多租户应用程序和数据。 你必须规划备份和灾难恢复保护到 Azure 堆栈外部的目标。 租户保护是一个租户驱动的活动。 对于 IaaS 虚拟机，租户可以使用来宾内技术来保护文件的文件夹、 应用程序数据和系统状态。 但是，为企业或服务提供程序，你可能想要提供同一数据中心内或外置方式在云中备份和恢复解决方案。

若要备份 Linux 或 Windows IaaS 虚拟机，你必须有权访问来宾操作系统使用备份产品来保护文件、 文件夹、 操作系统状态和应用程序数据。 你可以使用 Azure Backup 中，System Center Data Center Protection Manager，或支持第三方产品。

若要将数据复制到辅助位置，并安排应用程序故障转移，如果发生灾难，可以使用 Azure Site Recovery 或受支持的第三方产品。 （初始版本的集成系统，在 Azure Site Recovery 不支持故障回复。 但是，你可以获得通过手动过程的故障回复。）此外，支持 （如 Microsoft SQL Server) 的本机复制的应用程序可以将数据复制到应用程序正在其中运行的另一个位置。

> [!IMPORTANT]
> 在集成的系统的初始版本，我们将支持 IaaS 虚拟机的来宾级别的工作的保护技术。 无法在底层基础结构服务器上安装代理。

## <a name="learn-more"></a>了解详细信息

- 有关用例、 购买、 合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure 堆栈](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure 堆栈信息的路线图和地域可用性集成的系统，请参阅白皮书： [Azure 堆栈： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 

## <a name="next-steps"></a>后续步骤
[Azure 堆栈部署连接模型](azure-stack-connection-models.md)