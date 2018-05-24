---
title: Azure 安全性和符合性蓝图 - 实现 FedRAMP 自动化的数据仓库
description: Azure 安全性和符合性蓝图 - 实现 FedRAMP 自动化的数据仓库
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 110ce131286f437e051dd859eb4d2baa29f106f6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206273"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure 安全性和符合性蓝图：实现 FedRAMP 自动化的数据仓库

## <a name="overview"></a>概述

[联邦风险与授权管理计划 (FedRAMP)](https://www.fedramp.gov/) 是美国政府范围的计划，它提供一种标准化方法来对云产品和服务进行安全评估、授权和持续监视。 本 Azure 安全性和合规性蓝图指导如何传递帮助实现 FedRAMP 高控件子集的 Microsoft Azure 数据仓库体系结构。 此解决方案指导适用于常用参考体系结构的 Azure 资源的部署和配置，演示了客户如何通过多种方式达到特定的安全和符合性要求，是客户在 Azure 上生成和配置其数据仓库解决方案的基础。

此参考体系结构、相关控件实现指南和威胁模式预期作用为客户调整到特定需求的基础结构，并且不能按现状在生产环境中使用。 在未经修改的情况下将应用程序直接部署到此环境并不足以完全符合 FedRAMP 高基线的要求。 请注意以下事项：
- 体系结构提供一个基线来帮助客户以遵从 FedRAMP 的方式将工作负载部署到 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件

此解决方案提供了一个实现高性能和安全云数据仓库的数据仓库参考体系结构。 在此体系结构中有两个独立的数据层：一个用于在群集 SQL 环境中导入、存储和暂存数据，另一个用于使用 ETL 工具（例如 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL 查询）加载数据进行处理的 Azure SQL 数据仓库。 将数据存储到 Azure SQL 数据仓库后，可大规模地运行分析。

Microsoft Azure 为客户提供各种报告和分析服务。 此解决方案包括 SQL Server Reporting Services (SSRS)，以便快速从 Azure SQL 数据仓库创建报告。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 作为最佳做法，Azure 建议使用受信任的证书颁发机构来增强安全性。

Azure SQL 数据仓库中的数据存储在带有列存储的关系表中，这种格式显著降低了数据存储成本，同时提高了查询性能。  根据使用要求，如果没有需要计算资源的活动进程，可以将 Azure SQL 数据仓库计算资源放大或缩小或完全关闭。

SQL 负载均衡器管理 SQL 流量，确保高性能。 此参考体系结构中的所有虚拟机都部署在一个可用性集中，其中包含配置在 AlwaysOn 可用性组中的 SQL Server 实例，以实现高可用性和灾难恢复功能。

此数据仓库参考体系结构还包含用于管理体系结构内资源的 Active Directory (AD) 层。 Active Directory 子网支持在较大的 AD 林结构下轻松采用，即使访问较大的林不可用时也可以继续操作环境。 所有虚拟机均已通过域加入 Active Directory 层，并使用 Active Directory 组策略在操作系统级别强制实施安全性和符合性配置。

虚拟机将用作管理守护主机，为管理员提供访问部署资源的安全连接。 数据将通过此管理守护主机加载到临时区域。 **Azure 建议配置 VPN 或 Azure ExpressRoute 连接，以便进行管理和将数据导入参考体系结构子网。**

![符合 FedRAMP 的数据仓库的参考体系结构关系图](images/fedramp-datawarehouse-architecture.png?raw=true "符合 FedRAMP 的数据仓库的参考体系结构关系图")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

Azure 虚拟机
-   (1) 守护主机
-   (2) Active Directory 域控制器
-   (2) SQL Server 群集节点
-   (1) SQL Server 见证服务器

可用性集
-   (1) Active Directory 域控制器
-   (1) SQL 群集节点和见证服务器

虚拟网络
-   (4) 子网
-   (4) 网络安全组

SQL 数据仓库

SQL Server Reporting Services

Azure SQL 负载均衡器

Azure Active Directory

恢复服务保管库

Azure 密钥保管库

Operations Management Suite (OMS)

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了开发和实施要素。

**SQL 数据仓库**：[SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)是一个企业数据仓库 (EDW)，它利用大规模并行处理 (MPP) 对 PB 量级的数据快速运行复杂的查询。 使用简单的 PolyBase T-SQL 查询将大数据导入 SQL 数据仓库，然后利用 MPP 运行高性能分析。

**SQL Server Reporting Services**：使用 [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) 可为 Azure SQL 数据仓库快速创建包含表格、图表、地图、仪表，矩阵等的报表。

**守护主机**：守护主机是允许用户访问此环境中已部署资源的单一入口点。 守护主机通过仅允许来自安全列表上的公共 IP 地址的远程流量来提供到已部署资源的安全连接。 若要允许远程桌面 (RDP) 流量，需要在网络安全组 (NSG) 中定义流量的源。

使用以下配置创建了一个用作已加入域的守护主机的虚拟机：
-   [反恶意软件扩展](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [OMS 扩展](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 诊断扩展](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)（遵从 Azure 政府版、PCI DSS、HIPAA 和其他要求）。
-   [自动关闭策略](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，在不使用虚拟机时可减少其资源消耗量
-   已启用 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)，以便凭据和其他机密在与正在运行的操作系统隔离的受保护环境中运行

### <a name="virtual-network"></a>虚拟网络
此参考体系结构定义一个地址空间为 10.0.0.0/16 的专用虚拟网络。

**网络安全组**：[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 包含允许或拒绝 VNet 中流量的访问控制列表 (ACL)。 NSG 可用于在子网或单个 VM 的级别保护流量。 存在以下 NSG：
  - 用于数据层的 NSG（SQL Server群集、SQL Server 见证和 SQL 负载均衡器）
  - 用于管理守护主机的 NSG
  - 用于 Active Directory 的 NSG
  - 用于 SQL Server Reporting Services 的 NSG

每个 NSG 都打开了特定的端口和协议，以便解决方案能够安全正确地工作。 此外，为每个 NSG 启用了以下配置：
  - [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
  - 已将 OMS Log Analytics 连接到 [NSG 的诊断功能](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子网**：每个子网都与其相应的 NSG 相关联。

### <a name="data-at-rest"></a>静态数据
该体系结构通过加密、数据库审核和其他措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，所有 [Azure 存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。

**Azure 磁盘加密**
[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为 OS 和数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库**Azure SQL 数据库实例使用以下数据库安全措施：
-   使用 [AD 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   SQL 数据库配置为使用[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它执行数据和日志文件的实时加密和解密，以保护静态信息。 TDE 可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)阻止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)在潜在威胁发生时启动对它们的检测和响应，方式为提供可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式的安全性警报。
-   [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用服务器才能访问明文数据。
-   参考体系结构部署后可以完成 [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)。 客户将需要调整动态数据掩码设置以遵循其数据库架构。

### <a name="business-continuity"></a>业务连续性
**高可用性**：服务器工作负荷将分组到[可用性集](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，帮助确保 Azure 中虚拟机的高可用性。 计划内或计划外维护活动期间，至少有一台虚拟机可用，满足 99.95% Azure SLA。

**恢复服务保管库**：[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)存储备份数据并保护此体系结构中的所有 Azure 虚拟机配置。 通过恢复服务保管库，客户可以从 IaaS VM 还原文件和文件夹，而无需还原整个 VM，从而实现更快的还原时间。

### <a name="logging-and-audit"></a>日志记录和审核
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 可广泛记录系统和用户活动以及系统运行状况。 OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 解决方案收集和分析 Azure 和本地环境中的资源生成的数据。
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志，以及 Azure Blob 存储、表和队列的日志。
- **防火墙日志**：应用程序网关提供完整的诊断和访问日志。 防火墙日志适用于已启用 WAF 的应用程序网关资源。
- **日志存档**：所有诊断日志写入到集中式的加密 Azure 存储帐户，并根据定义的保留期（2 天）存档。 这些日志连接到 Azure Log Analytics 进行处理、存储和仪表板报告。

此外，包含以下 OMS 解决方案作为此体系结构的一部分：
-   [AD 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案定期评估服务器环境的风险和运行状况，并且提供特定于部署服务器基础结构的优先建议列表。
-   [反恶意软件评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)：反恶意软件解决方案报告恶意软件、威胁和防护状态。
-   [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)：Azure 自动化解决方案存储、运行和管理 runbook。
-   [安全和审核](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)：“安全和审核”仪表板通过提供有关安全域、值得注意的问题、检测、威胁智能和常见安全性查询的指标，提供对资源安全状态的高级见解。
-   [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案定期评估服务器环境的风险和运行状况，并且为客户提供特定于已部署服务器基础结构的优先建议列表。
-   [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)：“更新管理”解决方案允许客户管理操作系统安全更新，包括可用更新的状态以及安装所需更新的过程。
-   [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：“代理运行状况”解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [Azure 活动日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。
-   [更改跟踪](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：“更改跟踪”解决方案使得客户能够轻松识别环境中的更改。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供标识管理功能。
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) 可以是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 SQL 数据库的用户）都在 Azure Active Directory 中创建。
-   使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 此外，数据库列加密使用 Azure AD 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。
-   [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 可在 Azure 中实现极有针对性的访问管理。 订阅访问仅限于订阅管理员。

若要详细了解 Azure SQL 数据库安全功能的用法，请参阅 [Contoso Clinic 演示应用程序](https://github.com/Microsoft/azure-sql-security-sample)示例。

### <a name="security"></a>安全
**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。

**恶意软件防护**：用于虚拟机的 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)提供实时保护功能，当已知的恶意软件或不需要的软件试图在受保护的虚拟机上进行安装或运行时，它使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。

**修补程序管理**：部署为此参考体系结构一部分的 Windows 虚拟机默认配置为接收来自 Windows 更新服务的自动更新。 此解决方案还包括 OMS [Azure 自动化](https://docs.microsoft.com/azure/automation/automation-intro)服务，通过它可以在需要时创建更新部署修补虚拟机。


## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="expressroute-and-vpn"></a>ExpressRoute 和 VPN
需要配置 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或安全 VPN 隧道，以便安全地建立与作为 此数据仓库参考体系结构的一部分部署的资源的连接。 由于 ExpressRoute 连接不通过 Internet，因此与通过 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟以及更高的安全性。 通过正确地设置 ExpressRoute 或 VPN，客户可以在传输过程中添加一层保护数据。

### <a name="extract-transform-load-etl-process"></a>提取-转换-加载 (ETL) 过程
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 可将数据加载到 Azure SQL 数据仓库中，而无需单独的 ETL 或导入工具。 PolyBase 允许通过 T-SQL 查询访问数据。 Microsoft 的商业智能和分析堆栈以及与 SQL Server 兼容的第三方工具都可以与 PolyBase 一起使用。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 设置
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 对于管理与环境交互的人员的部署和预配访问至关重要。 只需[单击四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将现有 Windows Server Active Directory 与 AAD 集成。 客户还可以通过将部署的 Active Directory 基础结构作为 AAD 林的子域，将部署的 Active Directory 基础结构（域控制器）绑定到现有 AAD。

### <a name="additional-services"></a>其他服务
尽管此数据仓库体系结构不适用于部署到 [Azure 商业版](https://azure.microsoft.com/overview/what-is-azure/)环境，但是可以通过此参考体系结构中所描述的服务以及仅在 Azure 商业版环境中可用的其他服务实现类似目标。 请注意，Azure 商业版将 FedRAMP JAB P-ATO 维持在中等影响级别，使得政府机构和合作伙伴可以将中等敏感信息部署到利用 Azure 商业版环境的云。

Azure 商业版提供各种服务用于处理在数据仓库中使用的格式化和未格式化数据存储和过渡区，包括：
-   [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)是为复杂的混合提取-转换-加载 (ETL)、提取-加载-转换 (ELT) 和数据集成项目而构建的托管云服务。 使用 Azure 数据工厂，客户可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据。 然后，客户可以处理和转换数据以便将其输出到数据存储，例如 Azure SQL 数据仓库。
-   凭借 [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)，可以在单个位置捕获任何大小、类型和引入速度的数据，以用于操作性和探索性分析。 Azure Data Lake Store 与 Hadoop 生态系统中的大多数开源组件兼容，并可以与其他 Azure 服务（如 Azure SQL 数据仓库）很好地集成。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图 (DFD) 可供[下载](https://aka.ms/blueprintdwthreatmodel)，也可以在下面找到。

![符合 FedRAMP 的数据仓库的威胁模型](images/fedramp-datawarehouse-threat-model.png?raw=true "符合 FedRAMP 的数据仓库的威胁模型")

## <a name="compliance-documentation"></a>符合性文档
[Azure 安全性和合规性蓝图 – FedRAMP 高客户责任表](https://aka.ms/blueprinthighcrm)列出所有 FedRAMP 高基线所需的安全性控件。 同样，[Azure 安全性和合规性蓝图 – FedRAMP 中客户责任表](https://aka.ms/blueprintcrmmod)列出所有 FedRAMP 中基线所需的安全性控件。 两份文档详细说明每个控件的实现是 Microsoft 的责任还是客户的责任，亦或是两者共享责任。

[Azure 安全性和合规性蓝图 – FedRAMP 高控件实现表](https://aka.ms/blueprintdwcimhigh)和 [Azure 安全性和合规性蓝图 – FedRAMP 中控件实现表](https://aka.ms/blueprintdwcimmod)提供每个 FedRAMP 基线的数据仓库体系结构所涵盖的控件信息，包括实现如何满足每个所涵盖控件的要求的详细描述。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
