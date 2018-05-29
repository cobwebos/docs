---
title: Azure 安全性和合规性蓝图 - 用于 GDPR 的分析
description: Azure 安全性和合规性蓝图 - 用于 GDPR 的分析
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 9114dea1f95198b5ff8dc1bf759be5d1179885fd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161840"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Azure 安全性和合规性蓝图：用于 GDPR 的分析

## <a name="overview"></a>概述
一般数据保护条例 (GDPR) 包含许多有关收集、存储和使用个人信息的要求，包括组织如何识别和保护个人数据、满足透明度要求、检测和报告个人数据泄露以及培训隐私人员和其他员工。 GDPR 使个人可以更好地控制其个人数据，并对收集、处理或分析个人数据的组织赋予许多新义务。 GDPR 针对向欧盟 (EU) 居民提供产品及服务，或收集和分析欧盟居民相关信息的组织制定了新的规则。 无论组织位于何处，均需遵守 GDPR。

Microsoft 为 Azure 设计了业界领先的安全措施和隐私策略，以保护云中的数据，包括由 GDPR 确定的个人数据类别。 Microsoft 的[合同条款](http://aka.ms/Online-Services-Terms)承诺 Microsoft 满足处理器的要求。

此 Azure 安全性和符合性蓝图提供了在 Azure 中部署有助于满足 GDPR 要求的数据分析体系结构的指导。 此解决方案演示了客户可以满足特定安全性和符合性要求的方式，并且可以作为客户在 Azure 中构建和配置自己的数据分析解决方案的基础。 客户可以利用此参考体系结构，并按照 Microsoft 的[四步流程](https://aka.ms/gdprebook)实现 GDPR 符合性：
1. 发现：确定哪些个人数据存在以及这些数据位于何处。
2. 管理：控制个人数据的使用和访问方式。
3. 保护：建立安全控制以防止、检测并应对漏洞和数据泄露。
4. 报告：保留所需的文档以及管理数据请求和违规通知。

此参考体系结构、相关实施指南和威胁模型旨在作为客户适应其特定要求的基础，不应在生产环境中按原样使用。 请注意以下事项：
- 该体系结构提供了一个基准，可帮助客户以符合 GDPR 的方式将工作负荷部署到 Azure。
- 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全与合规评估；具体要求根据客户的每种实施方式具体情况而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此解决方案提供一个分析平台，客户可以在此平台上构建自己的分析工具。 参考体系结构概述了一个泛型用例，其中客户通过 SQL/数据管理员的批量数据导入或者通过经操作用户的操作数据更新输入数据。 两个工作流都将导入数据的 Azure Functions 合并到 Azure SQL 数据库。 Azure Functions 必须由客户通过 Azure 门户进行配置，以处理特定于客户每个分析需求的导入任务。

Azure 为客户提供各种各样的客户报告和分析服务；但是，此解决方案将 Azure 机器学习服务与 Azure SQL 数据库 相结合，以快速浏览数据并通过更智能的客户数据建模更快地传递结果。 Azure 机器学习是一种意在通过探索数据集间的新关系加快查询速度的机器学习。 一旦通过多个统计函数训练数据，最多 7 个额外查询池（包括客户服务器一共 8 个）可以与相同的表格模式同步，从而分散查询工作负荷并减少响应时间。

对于增强的分析和报告，可以通过列存储索引配置 Azure SQL 数据库。 Azure 机器学习和 Azure SQL 数据库都可以响应客户使用情况进行扩展、收缩或关闭。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 作为最佳做法，Azure 建议使用受信任的证书颁发机构来增强安全性。

一旦数据上传到 Azure SQL 数据库并被 Azure 机器学习训练，操作用户和 SQL/数据管理员通过 Power BI 提取其摘要。 Power BI 直观地显示数据，并整合多个数据集的信息以提供更好的见解。 其高度适应性和与 Azure SQL 数据库的轻松集成确保客户可以按照自己的业务需求配置它，从而解决各种方案。

整个解决方案基于客户从 Azure 门户配置的 Azure 存储构建。 Azure 存储通过“存储服务加密”加密所有数据，以保持静态数据的机密性。 异地冗余存储 (GRS) 确保客户主数据中心的不良事件不会导致数据丢失，因为第二个副本将存储在数百英里以外的独立位置中。

为了增强安全性，此体系结构通过 Azure Active Directory 和 Azure Key Vault 管理资源。 通过 Operations Management Suite (OMS) 和 Azure Monitor 监视系统运行状况。 客户配置两个监视服务捕获日志并在单独的、可轻松导航的仪表板中显示系统运行状况。

通常通过 SQL Server Management Studio (SSMS) 管理 Azure SQL 数据库，它从配置为通过安全 VPN 或 ExpressRoute 连接访问 Azure SQL 数据库的本地计算机运行。 **Azure 建议配置 VPN 或 ExpressRoute 连接，以便进行管理和将数据导入参考体系结构资源组。**

![用于 GDPR 的分析参考体系结构图](images/gdpr-analytics-architecture.png?raw=true "用于 GDPR 的分析参考体系结构图")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

- Azure Functions
- Azure SQL 数据库
- Azure 机器学习
- Azure Active Directory
- Azure 密钥保管库
- Operations Management Suite (OMS)
- Azure Monitor
- Azure 存储
- Power BI 仪表板
- Azure 数据目录
- Azure 安全中心
- Application Insights
- Azure 事件网格
- 网络安全组

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了部署和实施要素。

**Azure 事件网格**
[客户可以通过 Azure 事件网格](https://docs.microsoft.com/en-us/azure/event-grid/overview)使用基于事件的体系结构轻松生成应用程序。 用户选择要订阅的 Azure 资源，并提供要向其发送事件的事件处理程序或 Webhook 终结点。 在创建事件订阅时，客户可以通过向 Webhook URL 中添加查询参数来保护 Webhook 终结点。 Azure 事件网格仅支持 HTTPS Webhook 终结点。 借助 Azure 事件网格，客户可以控制授予不同用户用来执行各种管理操作的访问级别，例如列出事件订阅、创建新的事件订阅及生成密钥。 事件网格会利用 Azure 基于角色的访问控制 (RBAC)。

**Azure Functions**
[Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) 是一个无服务器计算服务，用户可以使用它按需运行代码，而无需显式预配或管理基础结构。 使用 Azure Functions 可以运行脚本或代码片段，以响应各种事件。

**Azure 机器学习**
[Azure 机器学习](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)是一项数据科研技术，可以让计算机根据现有的数据来预测将来的行为、结果和趋势。

**Azure 数据目录**：[数据目录](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog)可帮助管理数据的用户更轻松地发现和理解数据源。 常见的数据源可以针对个人数据进行注册、标记和搜索。 数据将保留在现有位置，但其元数据的副本将连同数据源位置的引用一起添加到数据目录。 此元数据还会编制索引，方便通过搜索功能轻松发现每个数据源，并让发现数据源的用户理解该数据源。

### <a name="virtual-network"></a>虚拟网络
此参考体系结构定义了一个地址空间为 10.0.0.0/16 的专用 VNet。

**网络安全组**：[NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 包含允许或拒绝 VNet 中流量的访问控制列表 (ACL)。 NSG 可用于在子网或单个 VM 的级别保护流量。 存在以下 NSG：
  - 用于 Active Directory 的 NSG
  - 针对工作负荷的 NSG

每个 NSG 都打开了特定的端口和协议，以便解决方案能够安全正确地工作。 此外，为每个 NSG 启用了以下配置：
  - [诊断日志和事件](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
  - 已将 OMS Log Analytics 连接到 [NSG 的诊断功能](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子网**：每个子网都与其相应的 NSG 相关联。

### <a name="data-in-transit"></a>传输中的数据
默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 通过 Azure 门户到 Azure 存储的所有事务均通过 HTTPS 进行。

### <a name="data-at-rest"></a>静态数据

该体系结构通过加密、数据库审核和其他措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，所有 [Azure 存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。 这有助于保护和维护个人数据，以支持 GDPR 定义的组织安全承诺和符合性需求。

**Azure 磁盘加密**
[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库**：Azure SQL 数据库实例使用以下数据库安全措施：
-   使用 [AD 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   Azure SQL 数据库配置为使用[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 TDE 可确保存储的个人数据不会受到未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)阻止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)在潜在威胁发生时启动对它们的检测和响应，方式为提供可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式的安全性警报。
-   [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感个人数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- 可以使用[扩展属性](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)来终止数据主体的处理，因为它允许用户向数据库对象添加自定义属性并将数据标记为“停用”来支持应用程序逻辑，以防止处理相关的个人数据。
- [行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)使用户能够定义策略来限制对数据的访问，以停止处理。
- [SQL 动态数据掩码 (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) 通过对非特权用户或应用程序模糊化敏感个人数据来限制此类数据的泄漏。 DDM 可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于识别符合 GDPR 保护条件的个人数据，并减少访问活动，避免通过未经授权的访问退出数据库。 **注意：客户需要调整 DDM 设置才能遵循其数据库架构。**

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理个人数据访问的功能。
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 此解决方案的所有用户（包括访问 Azure SQL 数据库的用户）均在 AAD 中创建。
-   使用 AAD 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 此外，数据库列加密还使用 AAD 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
-   [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问个人数据。 订阅访问仅限于订阅管理员。
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 使客户能够最大限度地减少有权访问特定信息（如个人数据）的用户数量。  管理员可以使用 AAD Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、适时的管理访问。
-   [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。

### <a name="security"></a>安全
**机密管理**此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护个人数据以及此类数据的访问：
- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块 (HSM) 的保护。 密钥类型为 HSM 保护的 2048 位 RSA 密钥。
- 使用 RBAC 向所有用户和标识授予了最低必需权限。
- 启用 Key Vault 的诊断日志时，保留期设置为至少 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

**安全警报**：客户可以使用 [Azure 安全中心](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)监视流量、收集日志和分析数据源是否有威胁。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护个人数据。 Azure 安全中心针对检测到的每种威胁提供[威胁智能报告](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report)，以帮助事件响应团队调查和解决威胁。

### <a name="logging-and-auditing"></a>日志记录和审核

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 可广泛记录系统和用户活动以及系统运行状况。 OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 解决方案收集和分析 Azure 和本地环境中的资源生成的数据。
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志，以及 Azure Blob 存储、表和队列的日志。
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
-   [更改跟踪](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking)：“更改跟踪”解决方案使得客户能够轻松识别环境中的更改。

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) 通过使客户能够审核、创建警报和存档数据（包括在客户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

**Application Insights**
[Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它们检测性能异常，并包含强大的分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。 它旨在帮助用户持续提高性能与可用性。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/gdprAnalyticsdfd)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![用于 GDPR 的分析威胁模型](images/gdpr-analytics-threat-model.png?raw=true "用于 GDPR 的分析威胁模型")

## <a name="compliance-documentation"></a>符合性文档
[Azure 安全性和符合性蓝图 - GDPR 客户责任矩阵](https://aka.ms/gdprCRM)列出了所有 GDPR 项目的控制方和处理方责任。 请注意，对于 Azure 服务，客户通常是控制方，Microsoft 充当处理方。

[Azure 安全性和符合性蓝图 - GDPR 数据分析实现矩阵](https://aka.ms/gdprAnalytics)提供了 数据分析体系结构处理哪些 GDPR 项目的信息，包括实现如何满足每个所述项目的要求的详细说明。


## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此数据分析参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过“隧道”安全传输信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 由于 ExpressRoute 连接不通过 Internet，因此与通过 Internet 的典型连接相比，这些连接提供更高的可靠性、更快的速度、更低的延迟以及更高的安全性。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-etl-process"></a>提取-转换-加载 (ETL) 过程
[PolyBase](https://docs.microsoft.com/en-us/sql/relational-databases/polybase/polybase-guide) 可将数据加载到 Azure SQL 数据库中，而无需单独的 ETL 或导入工具。 PolyBase 允许通过 T-SQL 查询访问数据。 Microsoft 的商业智能和分析堆栈以及与 SQL Server 兼容的第三方工具都可以与 PolyBase 一起使用。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 设置
[Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) 对于管理与环境交互的人员的部署和预配访问至关重要。 只需[单击四下](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将现有 Windows Server Active Directory 与 AAD 集成。 客户还可以通过将部署的 Active Directory 基础结构作为 AAD 林的子域，将部署的 Active Directory 基础结构（域控制器）绑定到现有 AAD。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
