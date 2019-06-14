---
title: Azure 安全性与合规性蓝图 - 符合 Australia PROTECTED 的 IaaS Web 应用程序
description: Azure 安全性与合规性蓝图 - 符合 Australia PROTECTED 的 IaaS Web 应用程序
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 3c82a88ea15b52672f9bed428e2e7af40a65309c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610204"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Azure 安全性与合规性蓝图 - 符合 Australia Protected 的 IaaS Web 应用程序

## <a name="overview"></a>概述
此 Azure 安全性与合规性蓝图提供有关部署适合用于收集、存储和检索澳大利亚信号局 (ASD) 生成的且符合澳大利亚政府信息安全手册 (ISM) 目标的 AU-PROTECTED 政府数据的基础结构即服务 (IaaS) 环境的指导。 此蓝图展示了一个通用参考体系结构，有助于演示在安全、合规的多层环境中正确处理敏感政府数据的方式。

此参考体系结构、实施指南和威胁模型为客户采取自己的规划和系统认证过程提供了基础，可帮助客户以遵从 ASD 的方式将工作负荷部署到 Azure 中。 客户可以选择实施 Azure VPN 网关或 ExpressRoute 来使用联合服务，以及将本地资源与 Azure 资源相集成。 客户必须考虑到使用本地资源对安全造成的影响。 必须完成其他配置才能满足所有要求，因为这些要求可能根据每个客户的实施细节而异。

若要符合 ASD 的要求，必须由信息安全注册评估师审核系统。 客户有责任对使用本体系结构构建的任何解决方案进行相应的安全性与符合性评估，因为具体要求可能会因客户的具体实施情况而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此解决方案为带有 SQL Server 后端的 IaaS Web 应用程序部署参考体系结构。 体系结构包括 Web 层、数据层、Active Directory 基础结构、应用程序网关和负载均衡器。 部署到 Web 层和数据层的虚拟机在可用性集中配置，SQL Server 实例在 Always On 可用性组中配置，以确保高可用性。 在将虚拟机加入域的同时，使用 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。 可以通过管理守护主机进行安全的连接，以便管理员访问部署的资源。

该体系结构提供一个可将本地网络扩展到 Azure，让企业用户通过组织的专用局域网或 Internet 访问基于 Web 的工作负荷的安全混合环境。 对于本地解决方案，客户需负责安全、运营与合规性的方方面面。

此解决方案中的 Azure 资源可以通过 Azure VPN 网关使用 IPSec VPN，或者通过 ExpressRoute 连接到本地网络或数据中心共置设施（例如堪培拉的 CDC）。 在决定是否使用 VPN 时，应该考虑到传输数据的分类和网络路径。 运行大规模任务关键型工作负荷且要满足大数据要求的客户应该考虑混合网络体系结构，使用 ExpressRoute 来与 Azure 服务建立专用网络连接。 有关 Azure 连接机制的详细信息，请参阅指导和建议部分。

应使用 Azure Active Directory 的联合身份验证，使用户能够使用本地凭据进行身份验证，并使用本地 Active Directory 联合身份验证服务基础结构来访问云中的所有资源。 Active Directory 联合身份验证服务可为此混合环境提供简化、安全的标识联合身份验证和 Web 单一登录功能。 有关 Azure Active Directory 设置的更多详细信息，请参阅指导和建议部分。

该解决方案使用 Azure 存储帐户。客户可将存储帐户配置为使用存储服务加密，以维持静态数据的机密性。 Azure 在客户选择的区域存储三个数据副本，以提供复原能力。 Azure 区域部署在可复原的区域对中，地理冗余的存储可确保将数据复制到同样包含三个副本的第二个区域。 这可以防止客户主要数据位置发生的不利事件导致数据丢失。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有资源。 Azure Active Directory 基于角色的访问控制用于控制对 Azure Key Vault 中部署的资源和密钥的访问。 通过 Azure 安全中心和 Azure Monitor 监视系统运行状况。 客户配置两个监视服务捕获日志并在单独的、可轻松导航的仪表板中显示系统运行状况。 Azure 应用程序网关配置为防护模式的防火墙，要求 TLS 流量的版本至少为 1.2。

![符合 AU-PROTECTED 的 IaaS Web 应用程序参考体系结构](images/au-protected-iaaswa-architecture.png?raw=true "符合 AU-PROTECTED 的 IaaS Web 应用程序参考体系结构示意图") 

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了更多详细信息。

- 可用性集
    - (1) SQL 群集节点
    - (1) Web/IIS
- Azure Active Directory
- Azure 应用程序网关
    - (1) Web 应用程序防火墙
        - 防火墙模式：防护
        - 规则集：OWASP 3.0
        - 侦听器端口：443
- Azure 云见证
- Azure 密钥保管库
- Azure 负载均衡器
- Azure Monitor
- Azure 资源管理器
- Azure 安全中心
- Azure Monitor 日志
- Azure 存储
- Azure 虚拟机
    - (1) 管理/守护 (Windows Server 2016 Datacenter)
    - (2) SQL Server 群集节点（Windows Server 2016 上的 SQL Server 2017）
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Azure 虚拟网络
    - (4) 网络安全组
    - Azure 网络观察程序
- 恢复服务保管库

此蓝图包含尚未通过澳大利亚网络安全中心 (ACSC) 的“受保护”分类认证的 Azure 服务。 此参考体系结构包含的所有服务已通过 ACSC 的“散播限制标记”(DLM) 级别的认证。 Microsoft 建议客户查看与这些 Azure 服务相关的已发布安全与审核报告，并使用其风险管理框架来确定 Azure 服务是否适合其内部认证以及在“受保护”分类级别使用。

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了部署和实施要素。

**守护主机**：守护主机是允许用户访问此环境中已部署资源的单一入口点。 守护主机通过仅允许来自安全列表上的公共 IP 地址的远程流量来提供到已部署资源的安全连接。 要允许远程桌面 (RDP) 流量，需要在网络安全组中定义流量的源。

此解决方案使用以下配置将虚拟机创建为已加入域的守护主机：
-   [反恶意软件扩展](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure 诊断扩展](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用虚拟机时可减少其资源消耗量

### <a name="virtual-network"></a>虚拟网络
体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

**网络安全组**：此解决方案在一个体系结构中部署资源，在虚拟网络中包含独立的 Web 子网、数据库子网、Active Directory 子网和管理子网。 子网从逻辑上是以应用于各个子网的网络安全组规则进行分隔，以限制子网之间只能有系统和管理功能所必需的流量。

请参阅此解决方案中部署的[网络安全组](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)的配置。 组织可以根据[此文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)中的说明编辑上面的文件，对网络安全组进行配置。

每个子网有一个专用的网络安全组：
- 应用程序网关有 1 个网络安全组 (LBNSG)
- 守护主机有 1 个网络安全组 (MGTNSG)
- 1 个网络安全组用于 SQL 服务器和云见证 (SQLNSG)
- 1 个网络安全组用于 Web 层 (WEBNSG)

### <a name="data-in-transit"></a>传输中的数据
默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 

对于从客户自有网络传输的“受保护”数据，该体系结构将使用 Internet，或者将 ExpressRoute 与配置有 IPSEC 的 VPN 网关配合使用。

此外，通过 HTTPS 利用 TLS 1.2 发生到 Azure 通过 Azure 管理门户的所有事务。

### <a name="data-at-rest"></a>静态数据
该体系结构通过加密、数据库审核和其他措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，所有 [Azure 存储](https://azure.microsoft.com/services/storage/)都使用[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。 这有助于保护数据，以支持澳大利亚政府 ISM 规定的组织安全承诺与合规性要求。

**Azure 磁盘加密**：[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**SQL Server**：SQL Server 实例使用以下数据库安全措施：
-   [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017)跟踪数据库事件并将其写入审核日志。
-   [透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [加密列](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于减少访问活动，并避免敏感数据通过未经授权的访问离开数据库。 **客户需要负责调整动态数据掩码设置，使之遵循其数据库架构。**

### <a name="identity-management"></a>身份管理
客户可以使用本地 Active Directory 联合身份验证服务来与 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)（Microsoft 的多租户基于云的目录和标识管理服务）联合。 [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 可将本地目录与 Azure Active Directory 集成。 此解决方案中的所有用户需要 Azure Active Directory 帐户。 使用联合登录时，用户可以使用本地凭据登录到 Azure Active Directory 并对 Azure 资源进行身份验证。

此外，以下 Azure Active Directory 功能有助于管理对 Azure 环境中数据的访问：
- 使用 Azure Active Directory 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。
- [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问数据。 订阅访问仅限于订阅管理员。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 使客户能够最大限度地减少有权访问特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。

**Azure 多重身份验证**：为保护标识，应实施多重身份验证。 [Azure 多重身份验证](https://azure.microsoft.com/services/multi-factor-authentication/)是一种易于使用、可缩放且可靠的解决方案，用于提供第二种身份验证方法来保护用户。 Azure 多重身份验证使用云的强大功能，并与本地 Active Directory 和自定义应用相集成。 这种保护可以延伸到高事务量的任务关键型方案。

### <a name="security"></a>安全
**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据：

- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块保护的 2048 位 RSA 密钥。
- 使用基于角色的访问控制向所有用户和标识授予了最低必需权限。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。
- 此解决方案与 Azure Key Vault 集成，用于管理 IaaS 虚拟机磁盘加密密钥和机密。

**修补程序管理**：部署为此参考体系结构一部分的 Windows 虚拟机默认配置为接收来自 Windows 更新服务的自动更新。 此解决方案还包括 [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)服务，通过该服务可以在需要时创建更新部署，以修补虚拟机。

**恶意软件防护**：用于虚拟机的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)提供实时保护功能，当已知的恶意软件或不需要的软件试图在受保护的虚拟机上进行安装或运行时，它使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。

**Azure 安全中心**：借助 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组[预定义的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据从环境中收集到的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)，以帮助事件响应团队调查和解决威胁。

此外，此参考体系结构利用 Azure 安全中心内的[漏洞评估](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)。 配置后，合作伙伴代理（例如 Qualys）会向合作伙伴的管理平台报告漏洞数据。 反过来，合作伙伴的管理平台又会向 Azure 安全中心发回漏洞和运行状况监视数据，使客户能够快速识别有漏洞的虚拟机。

**Azure 应用程序网关**：体系结构使用配置了 Web 应用程序防火墙并启用了 OWASP 规则集的应用程序网关，来降低安全漏洞风险。 其他功能包括：

- [端到端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 启用 [SSL 卸载](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- 禁用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)（预防模式）
- 结合 OWASP 3.0 规则集的[保护模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)
- 启用[诊断日志记录](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [自定义运行状况探测](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure 安全中心](https://azure.microsoft.com/services/security-center)和 [Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)提供额外的保护和通知。 Azure 安全中心还提供信誉系统。

### <a name="business-continuity"></a>业务连续性
**高可用性**：解决方案在[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中部署所有虚拟机。 可用性集可确保虚拟机能够跨多个隔离的硬件群集分布，从而改进可用性。 计划内或计划外维护活动期间，至少有一台虚拟机可用，满足 99.95% Azure SLA。

**恢复服务保管库**：[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)存储备份数据并保护此体系结构中的所有 Azure 虚拟机配置。 通过恢复服务保管库，客户可以从 IaaS 虚拟机还原文件和文件夹，而无需还原整个虚拟机，从而缩短还原时间。

**云见证**：[云见证](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)是在 Windows Server 2016 中利用 Azure 作为仲裁点的一种故障转移群集仲裁见证。 与其他任何仲裁见证一样，云见证可获得投票并可参与仲裁计算，但它使用标准公开可用的 Azure Blob 存储。 这消除了在公有云中托管的虚拟机的额外维护开销。

### <a name="logging-and-auditing"></a>日志记录和审核
Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的见解。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

**Azure Monitor 日志**：这些日志合并在[Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)进行处理、 存储和仪表板报告。 收集以后，数据会按数据类型整理到不同的表中，这样就可以对所有数据进行集中分析，不管其最初来源是什么。 此外，Azure 安全中心与 Azure Monitor 日志允许客户使用 Kusto 查询来访问其安全事件数据并将其与其他服务中的数据集成。

以下 Azure[监视解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)作为此体系结构的一部分包括在内：
-   [Active Directory 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署的服务器基础结构的优先建议列表。
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署的服务器基础结构的优先建议列表。
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

**Azure 自动化**：[Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 Azure SQL Server 收集日志。 自动化[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)解决方案使得客户能够轻松识别环境中的更改。

**Azure Monitor**：[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

[Azure 网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview):Azure 网络观察程序提供所需的工具用于监视、诊断 Azure 虚拟网络中的资源、查看其指标，以及为其启用或禁用日志。  英联邦实体应为 NSG 和虚拟机实施网络观察程序流日志。 这些日志只能存储在专门用于存储安全日志的存储帐户中，对该存储帐户的访问应受基于角色的访问控制的保护。

## <a name="threat-model"></a>威胁模型
此参考体系结构的数据流图可供[下载](https://aka.ms/au-protected-iaaswa-tm)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 AU-PROTECTED 的 IaaS Web 应用程序威胁模型](images/au-protected-iaaswa-threat-model.png?raw=true "符合 AU-PROTECTED 的 IaaS Web 应用程序威胁模型示意图")

## <a name="compliance-documentation"></a>符合性文档
此合规性文档由 Microsoft 根据其提供的平台和服务生成。 由于客户部署的范围非常广泛，本文档仅针对 Azure 环境中托管的解决方案提供了通用方法。 客户可以根据自己的操作系统环境和业务成果，确定并使用替代的产品和服务。 选择使用本地资源的客户必须解决这些本地资源的安全和运营要求。 客户可以定制所述的解决方案，以解决具体的本地部署与安全要求。

[Azure 安全性与合规性蓝图 - AU-PROTECTED 客户责任矩阵](https://aka.ms/au-protected-crm)列出了 AU-PROTECTED 要求的所有安全控制措施。 此矩阵详细说明了每项控制措施的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性与合规性蓝图 - AU-PROTECTED IaaS Web 应用程序实施矩阵](https://aka.ms/au-protected-iaaswa-cim)提供了有关 IaaS Web 应用程序体系结构解决哪些 AU-PROTECTED 措施控制的信息，包括实施方案如何满足每个所述控制措施的要求的详细说明。

## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
对于分类的信息，需要配置安全 IPSec VPN 隧道，以便与特定资源建立安全连接，这些资源是作为此 IaaS Web 应用程序参考体系结构的一部分部署的。 通过适当设置 IPSec VPN，客户可以在传输过程中为数据增加一个保护层。

在 Azure 中实施安全 IPSec VPN 隧道，可在本地网络与 Azure 虚拟网络之间建立虚拟专用连接。 此连接可通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全地传输信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了专用连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间的专用链接，被认为是一种专用网络。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快且延迟时间更短。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。 

为了帮助保护分类的数据（不管是使用 Internet 还是 Azure ExpressRoute），客户必须通过应用以下设置来配置其 IPSec VPN：

•   必须为客户 VPN 发起方配置不超过 14400 秒的 SA 生存期。
•   客户 VPN 发起方必须禁用 IKEv1 主动模式。
•   客户 VPN 发起方必须配置“完全向前保密”。
•   客户 VPN 发起方必须配置为使用 HMAC-SHA256 或更强的加密。

请查看[此处](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)提供的 VPN 设备配置选项和 IPSec/ IKE 参数。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 设置
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 对于管理部署以及预配与环境交互的人员的访问至关重要。 只需[单击四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将现有 Windows Server Active Directory 与 Azure Active Directory 集成。

此外，[Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 可让客户配置与本地 [Active Directory 联合身份验证服务]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs)和 Azure Active Directory 的联合。 通过联合登录，客户可让用户在企业网络中使用本地密码登录到基于 Azure Active Directory 的服务，而无需再次输入密码。 使用 Active Directory 联合身份验证服务的联合选项，可以部署 Active Directory 联合身份验证服务的新安装，或者在 Windows Server 2012 R2 场中指定现有安装。

若要防止分类数据同步到 Azure Active Directory，客户可以通过在 Azure Active Directory Connect 中应用以下设置，来限制要复制到 Azure Active Directory 的属性：
- [启用筛选](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [禁用密码哈希同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [禁用密码写回](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [禁用设备写回](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- 保留[防止意外删除](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes)和[自动升级](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)的默认设置

## <a name="disclaimer"></a>免责声明
- 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
- 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
- 客户可复制本文档，将其用于内部参考。
- 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
- 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
- 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
