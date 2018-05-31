---
title: Azure 安全性和符合性蓝图 - 用于 FedRAMP 的分析
description: Azure 安全性和符合性蓝图 - 用于 FedRAMP 的分析
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: db9e49cc4dc02b6864bee2dc4b73ff3c085f5380
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206276"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure 安全性和符合性蓝图：用于 FedRAMP 的分析

## <a name="overview"></a>概述

[联邦风险与授权管理计划 (FedRAMP)](https://www.fedramp.gov/) 是美国政府范围的计划，它提供一种标准化方法来对云产品和服务进行安全评估、授权和持续监视。 本 Azure 安全性和符合性蓝图指导如何传递帮助实现 FedRAMP 高控件子集的 Microsoft Azure 分析体系结构。 此解决方案指导适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全性和符合性要求，是客户在 Azure 上生成和配置其分析解决方案的基础。

此参考体系结构、相关的控制实现指南和威胁模型旨在作为客户适应其特定要求的基础，不应在生产环境中按原样使用。 在未经修改的情况下将应用程序直接部署到此环境并不足以完全符合 FedRAMP 高基线的要求。 请注意以下事项：
- 体系结构提供一个基线来帮助客户以遵从 FedRAMP 的方式将工作负载部署到 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全性与符合性评估；具体要求根据客户的每种实施方式具体情况而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件

此解决方案提供一个分析平台，客户可以在此平台上构建自己的分析工具。 该参考体系结构概述了一个泛型用例，其中客户通过 SQL/数据管理员的批量数据导入或通过经操作用户的操作数据更新输入数据。 两个工作流都会将用于导入数据的 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 合并到 SQL 数据库。 Azure Functions 必须由客户通过 Azure 门户进行配置，以处理特定于客户每个分析需求的导入任务。

Microsoft Azure 为客户提供各种报告和分析服务；但此解决方案会将 Azure Analysis Services 与 Azure SQL 数据库 相结合，以快速浏览数据并通过更智能的客户数据建模更快地传递结果。 Azure Analytics Services 是一种意在通过探索数据集间的新关系加快查询速度的机器学习。 数据一旦通过多个统计函数定型后，便可与相同的表格模型同步多达 7 个额外查询池（包括客户服务器在内一共 8 个），以分担查询工作负荷并减少响应时间。

对于增强的分析和报告，可以通过列存储索引配置 SQL 数据库。 Azure Analytics Services 和 SQL 数据库都可以进行横向或纵向扩展，或者完全关闭以响应客户使用情况。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 最佳做法是，Azure 建议使用受信任的证书颁发机构来增强安全性。

数据上传到 Azure SQL 数据库并被 Azure Analysis Services 定型后，操作用户和 SQL/数据管理员便可通过 Power BI 使用该数据。 Power BI 可直观地显示数据并整合多个数据集的信息以提供更好的见解。 其高度适应性和与 Azure SQL 数据库的轻松集成可确保客户按照自己的业务需求进行配置，从而处理各种情况。

整个解决方案基于帐户客户从 Azure 门户配置的 Azure 存储进行构建。 Azure 存储通过“存储服务加密”加密所有数据，以保持静态数据的机密性。  异地冗余存储 (GRS) 确保客户主数据中心的不良事件不会导致数据丢失，因为第二个副本将存储在数百英里以外的独立位置中。

为了增强安全性，此体系结构通过 Azure Active Directory 和 Azure Key Vault 管理资源。 通过 Operations Management Suite (OMS) 和 Azure Monitor 监视系统运行状况。 客户配置两个监视服务捕获日志并在单独的、可轻松导航的仪表板中显示系统运行状况。

Azure SQL 数据库通常通过 SQL Server Management Studio (SSMS) 进行管理，后者从配置为通过安全 VPN 或 ExpressRoute 连接访问 Azure SQL 数据库的本地计算机运行。 **Azure 建议配置 VPN 或 Azure ExpressRoute 连接，以便进行管理和将数据导入参考体系结构资源组。**

![用于 FedRAMP 参考体系结构图的分析](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics for FedRAMP reference architecture diagram")

### <a name="roles"></a>角色
该分析蓝图概述的方案包括以下三种常规用户类型：操作用户、SQL/数据管理员和系统工程师。 Azure 基于角色的访问控制 (RBAC) 可通过内置自定义角色实现精确的访问管理。 这些资源可用于配置[基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)以及概述和实现[预定义的角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)。

#### <a name="systems-engineer"></a>系统工程师
系统工程师拥有 Azure 客户订阅，并通过 Azure 门户配置解决方案的部署。

#### <a name="sqldata-administrator"></a>SQL/数据管理员
SQL/数据管理员构建批量数据导入函数和操作数据更新函数以更新到 Azure SQL 数据库。 SQL/数据管理器不负责数据库中的任何操作数据更新，但能够通过 Power BI 查看数据。

#### <a name="operational-user"></a>操作用户
操作用户定期更新数据，并拥有日常数据生成。 操作用户也通过 Power BI 解释结果。

### <a name="azure-services"></a>Azure 服务

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。
- Azure Functions
- Azure SQL 数据库
- Azure Analysis Service
- Azure Active Directory
- Azure 密钥保管库
- OMS
- Azure Monitor
- Azure 存储
- ExpressRoute/VPN 网关
- Power BI 仪表板

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了开发和实施要素。

![替换文字](images/fedramp-analytics-components.png?raw=true "用于 FedRAMP 组件的分析示意图")

**Azure Functions**：[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 解决方案通过大多数编程语言在云中运行小段代码。 此解决方案中的函数与 Azure 存储集成以自动将客户数据拉入云，从而促成与其他 Azure 服务的集成。 函数可轻松缩放，并且仅在运行时产生费用。

**Azure Analysis Service**：[Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) 提供企业数据建模和与 Azure 数据平台服务的集成。 通过将多个源的数据结合到单个数据模型中，Azure Analysis Service 加快浏览大量数据的速度。

**Power BI**：[Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) 为尝试从数据处理行为中提取更好见解的客户提供分析和报告功能。

### <a name="networking"></a>网络
**网络安全组**：设置 [Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 来管理流向部署资源和服务的流量。 网络安全组设置为默认拒绝方案，并且仅允许预配置访问控制列表 (ACL) 中包含的流量。

每个 NSG 都打开了特定的端口和协议，以便解决方案能够安全正确地工作。 此外，为每个 NSG 启用了以下配置：
  - [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
  - 已将 OMS [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) 连接到 NSG 的诊断功能。

### <a name="data-at-rest"></a>静态数据
该体系结构通过加密、数据库审核和其他措施保护静态数据。

**数据复制** Azure 政府有两个关于[数据复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy)的选项：
 - 数据复制的默认设置为“异地冗余存储(GRS)”，以便在主要区域外的单独数据中心中异步存储客户数据。 这可确保主要数据中心全损事件的数据恢复。
 - 还可以通过 Azure 存储帐户配置“本地冗余存储(LRS)”。 LRS 复制存储缩放单元中的数据，此单元的托管区域与客户创建帐户的区域相同。 为确保在主要存储缩放单元失败时不会出现备份数据丢失情况，需同时复制所有数据。

**Azure 存储** 为满足加密静态数据要求，此参考体系结构中部署的所有服务都利用 [Azure 存储](https://azure.microsoft.com/services/storage/)，以便通过[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)存储数据。

**Azure 磁盘加密**
[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为 OS 和数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库** Azure SQL 数据库实例使用以下数据库安全措施：
-   使用 [AD 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   SQL 数据库配置为使用[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它执行数据和日志文件的实时加密和解密，以保护静态信息。 TDE 可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)在潜在威胁发生时启动对它们的检测和响应，方式为提供可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式的安全性警报。
-   [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用服务器才能访问明文数据。
-   [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)可以在参考体系结构部署完毕之后再完成。 客户将需要调整动态数据掩码设置以遵循其数据库架构。

### <a name="logging-and-audit"></a>日志记录和审核
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) 生成完整显示的监视数据（包括活动日志、指标和诊断数据），使用户可以全面了解系统运行状况。  
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 可广泛记录系统和用户活动以及系统运行状况。 OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 解决方案收集和分析 Azure 和本地环境中的资源生成的数据。
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作见解。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志，以及 Azure Blob 存储、表和队列的日志。
- **防火墙日志**：应用程序网关提供完整的诊断和访问日志。 防火墙日志适用于已启用 WAF 的应用程序网关资源。
- **日志存档**：所有诊断日志写入到集中式的加密 Azure 存储帐户，并根据定义的保留期（2 天）存档。 这些日志连接到 Azure Log Analytics 进行处理、存储和仪表板报告。

此外，以下 OMS 解决方案作为此体系结构的一部分包括在内：
-   [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)：Azure 自动化解决方案用于存储、运行和管理 runbook。
-   [安全和审核](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)：安全和审核仪表板通过提供安全域、值得注意的问题、检测、威胁智能和常见安全性查询的指标，提供对资源安全性状态的高级见解。
-   [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署服务器基础结构的优先建议列表。
-   [Azure Activity Logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案帮助客户跨所有 Azure 订阅分析 Azure 活动日志。

### <a name="identity-management"></a>身份管理
-   使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 此外，数据库列加密使用 Azure AD 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。
-   [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 可在 Azure 中实现极有针对性的访问管理。 订阅访问仅限于订阅管理员。

若要详细了解 Azure SQL 数据库安全功能的用法，请参阅 [Contoso Clinic 演示应用程序](https://github.com/Microsoft/azure-sql-security-sample)示例。

### <a name="security"></a>安全
**机密管理**：解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="expressroute-and-vpn"></a>ExpressRoute 和 VPN
需要配置 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或安全 VPN 隧道，以安全地建立与作为此数据分析参考体系结构的一部分部署的资源的连接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 通过适当设置 ExpressRoute 或 VPN，客户可以在传输过程中为数据添加一层保护。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 设置
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 对于管理部署以及预配与环境交互的人员的访问至关重要。 只需[单击四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将现有 Windows Server Active Directory 与 AAD 集成。 客户还可以通过将部署的 Active Directory 基础结构作为 AAD 林的子域，将部署的 Active Directory 基础结构（域控制器）绑定到现有 AAD。

### <a name="additional-services"></a>其他服务
#### <a name="iaas---vm-vonsiderations"></a>IaaS - VM 注意事项
此 PaaS 解决方案不包括任何 Azure IaaS VM。 客户可以创建 Azure VM 运行多个 PaaS 服务。 在这种情况下，可以利用用于业务持续性和 OMS 的特定功能和服务：

##### <a name="business-continuity"></a>业务连续性
- **高可用性**：服务器工作负荷将分组到[可用性集](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，帮助确保 Azure 中虚拟机的高可用性。 计划内或计划外维护活动期间，至少有一台虚拟机可用，满足 99.95% Azure SLA。

- **恢复服务保管库**：[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)存储备份数据并保护此体系结构中的所有 Azure 虚拟机配置。 通过恢复服务保管库，客户可以从 IaaS VM 还原文件和文件夹，而无需还原整个 VM，从而缩短还原时间。

##### <a name="oms"></a>OMS
-   [AD 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署服务器基础结构的优先建议列表。
-   [反恶意软件评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)：反恶意软件解决方案报告恶意软件、威胁和防护状态。
-   [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)：更新管理解决方案允许客户管理操作系统安全更新，包括可用更新的状态以及安装所需更新的过程。
-   [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)：更改跟踪解决方案使得客户能够轻松识别环境中的更改。

##### <a name="security"></a>安全
- **恶意软件防护**：用于虚拟机的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)提供实时保护功能，当已知的恶意软件或不需要的软件试图在受保护的虚拟机上进行安装或运行时，它使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。
- **修补程序管理**：部署为此参考体系结构一部分的 Windows 虚拟机默认配置为接收来自 Windows 更新服务的自动更新。 此解决方案还包括 OMS [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)服务，通过该服务可以在需要时创建更新部署，以修补虚拟机。

#### <a name="azure-commercial"></a>Azure 商业版
尽管此数据分析体系结构不适用于部署到 [Azure 商业版](https://azure.microsoft.com/overview/what-is-azure/)环境，但是可以通过此参考体系结构中所描述的服务以及仅在 Azure 商业版环境中可用的其他服务实现类似目标。 请注意，Azure 商业版将 FedRAMP JAB P-ATO 维持在中等影响级别，政府机构和合作伙伴可以将中等敏感信息部署到利用 Azure 商业版环境的云。

Azure 商业版提供各种分析服务，有助于从大量数据中提取见解：
-   [Azure 机器学习工作室](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio)（[Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/) 的组件）从一个或更多数据源开发预测性分析模型。 可在多次迭代中使用统计函数生成 Power BI 等应用程序稍后可以使用的有效模型。
-   使用 [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)，可以在单个位置捕获任何大小、类型和引入速度的数据，以用于操作性和探索性分析。 Azure Data Lake Store 与 Hadoop 生态系统中的大多数开源组件兼容，并可以与其他 Azure 服务很好地集成。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图 (DFD) 可供[下载](https://aka.ms/blueprintanalyticsthreatmodel)，也可以在下面找到：

## <a name="compliance-documentation"></a>符合性文档
[Azure 安全性和符合性蓝图 - FedRAMP 高客户责任矩阵](https://aka.ms/blueprinthighcrm)列出了 FedRAMP 高基线所需的所有安全性控件。 类似地，[Azure 安全性和符合性蓝图 - FedRAMP 中等客户责任矩阵](https://aka.ms/blueprintanalyticscimmod)列出了 FedRAMP 中等基线所需的所有安全性控件。 两份文档详细说明每个控件的实现是 Microsoft 的责任还是客户的责任，亦或是两者共同的责任。

[Azure 安全性和符合性蓝图 - FedRAMP 高控件实现矩阵](https://aka.ms/blueprintanalyticscimhigh)和 [Azure 安全性和符合性蓝图 – FedRAMP 中等控件实现矩阵](https://aka.ms/blueprintanalyticscimmod)提供每个 FedRAMP 基线的分析体系结构所涵盖的控件信息，包括实现如何满足每个所涵盖控件的要求的详细描述。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
