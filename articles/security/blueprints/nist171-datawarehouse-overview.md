---
title: Azure 安全性和符合性蓝图 - 符合 NIST SP 800-171 的数据仓库
description: Azure 安全性和符合性蓝图 - 符合 NIST SP 800-171 的数据仓库
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 98eb3834efa6dc6ce5d53990f4bb530351660b8a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946713"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure 安全性和符合性蓝图 - 符合 NIST SP 800-171 的数据仓库

## <a name="overview"></a>概述
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) 提供有关保护驻留在非联邦信息系统和组织中的受控未分类信息 (CUI) 的指导原则。 NIST SP 800-171 为保护 CUI 机密性建立了 14 个系列的安全要求。

本 Azure 安全性和符合性蓝图提供的指导可帮助客户在 Azure 中部署数据仓库体系结构，用于实施一部分 NIST SP 800-171 控制措施。 该解决方案展示了客户可以满足特定安全性和符合性要求的方式。 此外，它还是客户在 Azure 中构建和配置其数据仓库解决方案的基础。

此参考体系结构、相关实施指南和威胁模型的主要宗旨是为客户适应其特定要求奠定基础， 请不要在生产环境中原样照搬。 客户负责对任何使用此体系结构构建的解决方案进行适当的安全性和符合性评估。 要求可能因每个客户的具体实施方案而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此解决方案提供了一个实现高性能和安全云数据仓库的参考体系结构。 此体系结构中有两个单独的数据层。 一个层是在群集的 SQL 环境中导入、存储和暂存数据的位置。 另一个层是用于 SQL 数据仓库的。 在这个层中，通过使用提取-转换-加载 (ETL) 工具（例如，[PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL 查询）来加载数据以进行处理。 将数据存储到 SQL 数据仓库后，即可大规模地运行分析。

Azure 为客户提供各种报告和分析服务。 此解决方案包括 SQL Server Reporting Services，可快速从 SQL 数据仓库创建报告。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 我们建议使用受信任的证书颁发机构来增强安全性，这是最佳做法。

Azure SQL 数据仓库将数据存储在按列存储的关系表中。 这种格式在显著降低数据存储费用的同时，还可提升查询性能。 根据使用情况要求，如果没有需要计算资源的活动进程，可以将 SQL 数据仓库计算资源放大或缩小或完全关闭。

SQL Server 负载均衡器将管理 SQL 流量以确保高性能。 此参考体系结构中的所有虚拟机 (VM) 都部署在一个可用性集中，其中 SQL Server 实例配置在 Always On 可用性组中。 这种配置可提供高可用性和灾难恢复功能。

此数据仓库参考体系结构还包含用于管理体系结构内资源的 Active Directory 层。 Active Directory 子网可在更大的 Active Directory 林结构下轻松采用。 这样，即使无法访问更大的林，环境也可以连续运行。 所有 VM 均已加入到 Active Directory 层的域中。 它们使用 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。

该解决方案使用 Azure 存储帐户。客户可将存储帐户配置为使用存储服务加密，以维持静态数据的机密性。 Azure 会在客户选择的数据中心内存储三个数据副本，以提供复原能力。 异地冗余存储可确保将数据复制到数百英里之外的辅助数据中心，并在该数据中心内再次存储为三个副本。 此安排可防止客户主要数据中心发生的不利事件导致数据丢失。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有资源。 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 用于控制对已部署资源的访问。 这些资源包括 Azure Key Vault 中的客户密钥。 通过 Azure 安全中心和 Azure Monitor 监视系统运行状况。 客户可将这两个监视服务均配置为捕获日志。 系统运行状况在一个仪表板中显示，方便使用。

VM 可作为管理守护主机。 它可为管理员提供安全连接以访问部署的资源。 数据将通过此管理守护主机加载到临时区域。 我们建议配置 VPN 或 Azure ExpressRoute 连接，以便进行管理和将数据导入参考体系结构子网。

![符合 NIST SP 800-171 的数据仓库的参考体系结构关系图](images/nist171-dw-architecture.png "符合 NIST SP 800-171 的数据仓库的参考体系结构关系图")

此解决方案使用以下 Azure 服务。 有关详细信息，请参阅[部署体系结构](#deployment-architecture)部分。

- 可用性集
    - Active Directory 域控制器
    - SQL Server 群集节点和见证
- Azure Active Directory
- Azure 数据目录
- Azure Key Vault
- Azure Monitor (日志)
- Azure 安全中心
- Azure 负载均衡器
- Azure 存储
- Azure 虚拟机
    - (1) 守护主机
    - (2) Active Directory 域控制器
    - (2) SQL Server 群集节点
    - (1) SQL Server 见证
- Azure 虚拟网络
    - (1) /16 网络
    - (4) /24 网络
    - (4) 网络安全组
- 恢复服务保存库
- SQL 数据仓库
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了部署和实施要素。

**AZURE SQL 数据仓库**:[SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)是一种企业数据仓库, 它利用大规模并行处理来跨 pb 的数据快速运行复杂的查询。 用户可以使用简单的 PolyBase T-SQL 查询将大数据导入到 SQL 数据仓库中，并利用大规模并行处理的强大功能来运行高性能分析。

**SQL Server Reporting Services**:[SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs)提供了有关 SQL 数据仓库的表、图表、地图、仪表、矩阵等的快速创建报表。

**Azure 数据目录**：[数据目录](../../data-catalog/overview.md)可帮助管理数据的用户更轻松地发现和理解数据源。 常见的数据源可以针对数据进行注册、标记和搜索。 数据将保留在现有位置，但其元数据的副本将添加到数据目录。 包含对数据源位置的引用。 会对元数据编制索引以轻松通过搜索发现每个数据源。 另外，索引编制还会让发现数据源的用户对其易于理解。

**守护主机**：堡垒主机是用户可用于访问此环境中部署的资源的单一入口点。 守护主机通过仅允许来自安全列表中公共 IP 地址的远程流量来提供到已部署资源的安全连接。 若要允许远程桌面流量，必须在网络安全组中定义流量的源。

此解决方案使用以下配置将 VM 创建为已加入域的守护主机：
-   [反恶意软件扩展](https://docs.microsoft.com/azure/security/fundamentals/antimalware)。
-   [Azure 诊断扩展](../../virtual-machines/windows/extensions-diagnostics-template.md)。
-   使用 Key Vault 的 [Azure 磁盘加密](../azure-security-disk-encryption-overview.md)。
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用 VM 时可减少其资源消耗量。
-   已启用 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)，以便凭据和其他机密在与运行操作系统相隔离的受保护环境中运行。

### <a name="virtual-network"></a>虚拟网络
此参考体系结构定义一个地址空间为 10.0.0.0/16 的专用虚拟网络。

**网络安全组**：[网络安全组](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSG) 包含允许或拒绝虚拟网络中流量的访问控制列表。 NSG 可用于在子网或单个 VM 的级别保护流量。 存在以下 NSG：
  - 用于数据层的 NSG（SQL Server 群集、SQL Server 见证和 SQL 负载均衡器）
  - 用于管理守护主机的 NSG
  - 用于 Active Directory 的 NSG
  - 用于 SQL Server Reporting Services 的 NSG

每个 NSG 都打开了特定的端口和协议，以便解决方案能够安全正确地工作。 此外，为每个 NSG 启用了以下配置：
  - [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中。
  - Azure Monitor 日志已连接到[NSG 的诊断](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)。

**子网**：每个子网与其相应的 NSG 关联。

### <a name="data-at-rest"></a>静态数据
体系结构通过多种措施值保护静态数据。 这些措施包括加密和数据库审核。

**Azure 存储**：为了满足静态数据加密要求，所有[存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](../../storage/common/storage-service-encryption.md)。 此功能有助于保护数据，以支持组织安全承诺和符合性要求。

**Azure 磁盘加密**：[磁盘加密](../azure-security-disk-encryption-overview.md)使用 Windows 的 BitLocker 功能，为操作系统和数据磁盘提供卷加密。 此解决方案与 Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库**：SQL 数据库实例使用以下数据库安全措施：
-   使用 [Active Directory 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](../../sql-database/sql-database-auditing.md)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](../../sql-database/sql-database-threat-detection.md)启用检测并对出现的潜在威胁做出响应。 出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式时，它会提供安全警报。
-   [加密列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用服务器才能访问明文数据。
- [扩展属性](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)可用于停止处理数据主体。 用户可以向数据库对象添加自定义属性。 另外，他们还可以将数据标记为“已停止”，以支持应用程序逻辑防止处理关联的财务数据。
- [行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)使用户能够定义策略来限制对数据的访问，以停止处理。
- [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄露。 它可以自动发现潜在的敏感数据，并建议应用合适的掩码。 动态数据掩码有助于减少访问，以便敏感数据不会由未经授权访问而退出数据库。 客户需要负责根据其数据库架构调整设置。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理数据访问的功能：
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 此解决方案的所有用户（包括访问 SQL 数据库的用户）均在 Azure AD 中创建。
-   使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅如何[将应用程序与 Azure AD 集成](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)。 数据库列加密还使用 Azure AD 对访问 SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) 可供管理员用于定义细化的访问权限。 使用它，管理员可以仅授予用户执行其作业所需的访问次数。 无需向每个用户授予 Azure 资源的不受限访问权限，管理员可以只允许使用特定的操作来访问资源和数据。 订阅访问仅限于订阅管理员。
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) 可供客户用来最大限度地减少有权访问特定信息（例如数据）的用户数量。 管理员可以使用 Azure AD Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 此外，还可以根据需要，使用此功能来实施按需、实时的管理性访问。
- [Azure Active Directory 标识保护](../../active-directory/identity-protection/overview.md)可检测到影响组织标识的潜在漏洞。 它将自动响应配置为检测与组织标识相关的可疑操作。 它还会调查可疑事件，以采取适当的措施进行解决。

### <a name="security"></a>安全性
**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Key Vault 帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Key Vault 功能帮助客户保护数据：
- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块保护的 2048 位 RSA 密钥。
- 使用 RBAC 可向所有用户和标识授予最低要求的权限。
- Key Vault 的诊断日志已启用，其保留期至少为 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

**修补程序管理**：默认情况下, 在此参考体系结构中部署的 Windows Vm 配置为从 Windows 更新服务接收自动更新。 此解决方案还包括 [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)服务，可以在需要时创建更新部署，以修补 VM。

**恶意软件防护**：适用于 Vm 的[Microsoft 反恶意](https://docs.microsoft.com/azure/security/fundamentals/antimalware)软件提供了实时保护功能, 可帮助识别和删除病毒、间谍软件和其他恶意软件。 客户可以配置当已知恶意软件或不需要的软件试图在受保护的 VM 上安装或运行时生成的警报。

**Azure 安全中心**：借助[安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外，安全中心还会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

安全中心会使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 安全中心有一组[预定义的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，会在出现威胁或可疑活动时触发。 客户可以使用[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据已从环境中收集到的数据定义新的安全警报。

安全中心提供按优先级排序的安全警报和事件。 安全中心让客户可以更轻松地发现并解决潜在安全问题。 针对每个检测到的威胁会生成一个[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。 事件响应团队可在调查和修正威胁时使用这些报告。

此外，此参考体系结构还使用安全中心内的[漏洞评估](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)功能。 配置该功能后，合作伙伴代理（例如 Qualys）可向合作伙伴的管理平台报告漏洞数据。 反过来，合作伙伴的管理平台也会向安全中心提供漏洞和运行状况监视数据。 客户可以使用此信息来快速确定易受攻击的 VM。

### <a name="business-continuity"></a>业务连续性
**高可用性**：服务器工作负荷在[可用性集中](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)进行分组, 以帮助确保 Azure 中 vm 的高可用性。 计划内或计划外维护活动期间，至少有一台 VM 可用，满足 99.95% Azure SLA 的要求。

**恢复服务保管库**:[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)包含备份数据并保护此体系结构中所有 vm 的配置。 通过恢复服务保管库，客户可以从 IaaS VM 还原文件和文件夹，而无需还原整个 VM。 此过程可加快还原时间。

### <a name="logging-and-auditing"></a>日志记录和审核

Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](../../azure-monitor/platform/activity-logs-overview.md)提供对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](../../azure-monitor/platform/diagnostic-logs-overview.md)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、存储日志、密钥保管库审核日志以及 Azure 应用程序网关访问和防火墙日志。 所有诊断日志都将写入到集中式加密 Azure 存储帐户以进行存档。 用户可以配置多达 730 天的保留期，以满足其特定要求。

**Azure Monitor 日志**：这些日志合并到[Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)中, 以便进行处理、存储和仪表板报告。 收集数据后，会针对 Log Analytics 工作区中的每种数据类型将数据整理到单独的表中。 如此一来，无论数据的原始源如何，所有数据都可以一起分析。 安全中心与 Azure Monitor 日志集成。 客户可以使用 Kusto 查询访问其安全事件数据, 并将其与其他服务中的数据合并。

以下 Azure[监视解决方案](../../monitoring/monitoring-solutions.md)包括在此体系结构中:
-   [Active Directory 评估](../../azure-monitor/insights/ad-assessment.md)：Active Directory 运行状况检查解决方案会定期评估服务器环境的风险和运行状况。 此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。
- [SQL 评估](../../azure-monitor/insights/sql-assessment.md)：SQL 运行状况检查解决方案会定期评估服务器环境的风险和运行状况。 此解决方案为客户提供了特定于已部署服务器基础结构的建议优先级列表。
- [代理运行状况](../../monitoring/monitoring-solution-agenthealth.md)：代理运行状况解决方案报告部署的代理数量及其地理分布状况。 此外，它还报告未响应代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

**Azure 自动化**：[自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 SQL 数据库收集日志。 客户可以使用自动化[更改跟踪](../../automation/change-tracking.md)解决方案轻松识别环境中的更改。

**Azure Monitor**：[Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 可以帮助用户跟踪性能、维护安全和确定趋势。 组织可以使用它来审核、创建警报并存档数据。 此外，他们还可以跟踪 Azure 资源中的 API 调用。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/nist171-dw-tm)，也可以在此处找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 NIST SP 800-171 的数据仓库威胁模型](images/nist171-dw-threat-model.png "符合 NIST SP 800-171 的数据仓库威胁模型")

## <a name="compliance-documentation"></a>符合性文档
[Azure 安全性和符合性蓝图 - NIST SP 800-171 客户责任矩阵](https://aka.ms/nist171-crm)列出了 NIST SP 800-171 要求的所有安全控制措施。 此矩阵详细说明了每项控制措施的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性与合规性蓝图 - NIST SP 800-171 数据仓库控制措施实施矩阵](https://aka.ms/nist171-dw-cim)提供了有关数据仓库体系结构涵盖的 NIST SP 800-171 控制措施的信息。 其中包括实施方案如何满足每个所涵盖控制措施要求的详细说明。

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
必须配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为 此数据仓库参考体系结构一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 实现。 客户可以利用此连接在客户的网络和 Azure 之间安全“传输”加密链接内的信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，因此，Microsoft 提供了另一个更为安全的连接选项。 ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接可直接连接到客户的电信服务提供商。 因此，数据不会在 Internet 上传输且不会对 Internet 公开。 与典型连接相比，这些连接在可靠性、速度、延迟和安全性方面都更胜一筹。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-process"></a>提取-转换-加载过程
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 可将数据加载到 SQL 数据仓库中，而无需单独的 ETL 或导入工具。 PolyBase 允许通过 T-SQL 查询访问数据。 Microsoft 商业智能和分析堆栈以及与 SQL Server 兼容的第三方工具都可以与 PolyBase 一起使用。

### <a name="azure-ad-setup"></a>Azure AD 设置
[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 对于管理部署以及预配与环境交互的人员的访问权限至关重要。 只需[单击四下](../../active-directory/hybrid/how-to-connect-install-express.md)，即可将本地部署 Active Directory 与 Azure AD 集成。 此外，客户还可以将已部署的 Active Directory 基础结构（域控制器）绑定到 Azure AD。 若要执行此操作，请将 Active Directory 基础结构作为 Azure AD 林的子域进行部署。

### <a name="optional-services"></a>可选服务
Azure 提供了各种服务，以帮助存储和暂存格式化和未格式化数据。 根据客户需求，可以将以下服务添加到此参考体系结构中：
-   [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)是为复杂的混合 ETL、提取-加载-转换和数据集成项目而构建的托管云服务。 数据工厂具有帮助跟踪和查找数据的功能。 可视化和监视工具可识别数据何时到达以及来自何处。 客户可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储中引入数据。 他们可以使用这些管道从内部源和外部源中引入数据。 然后，客户可以处理和转换数据，以便将其输出到数据存储，例如 SQL 数据仓库。
- 客户可以在 [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) 中暂存非结构化数据，以在一个位置捕获任何大小、类型和引入速度的数据，以用于操作性和探索性的分析。 Azure Data Lake 具有支持数据提取和转换的功能。 Data Lake Store 与 Hadoop 生态系统中的大多数开源组件兼容。 此外，还与其他 Azure 服务完美集成，例如 SQL 数据仓库。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
