---
title: Azure 安全性和符合性蓝图：针对 NIST SP 800-171 的数据分析
description: Azure 安全性和符合性蓝图：针对 NIST SP 800-171 的数据分析
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 02db944e96c74d865026a17a3871dbad2835cf1d
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056066"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure 安全性和符合性蓝图：针对 NIST SP 800-171 的数据分析

## <a name="overview"></a>概述
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) 提供有关保护驻留在非联邦信息系统和组织中的受控未分类信息 (CUI) 的指导原则。 NIST SP 800-171 为保护 CUI 机密性建立了 14 个系列的安全要求。

本 Azure 安全性和符合性蓝图提供的指导可帮助客户在 Azure 中部署数据分析体系结构，用于实施一部分 NIST SP 800-171 控制措施。 该解决方案展示了客户可以满足特定安全性和符合性要求的方式。 此外，它还是客户在 Azure 中构建及配置其数据分析解决方案的基础。

此参考体系结构、相关实施指南和威胁模型旨在为客户适应其特定要求奠定基础， 而不应在生产环境中按原样使用。 在不经修改的情况下部署此体系结构并不足以完全满足 NIST SP 800-171 的要求。 客户负责对任何使用此体系结构构建的解决方案进行适当的安全性和符合性评估。 要求可能因每个客户的具体实施方案而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件
此解决方案提供一个分析平台，客户可以在此平台上构建自己的分析工具。 参考体系结构概述了通用用例。 客户可以通过让 SQL/数据管理员执行大容量数据导入从而利用此体系结构来输入数据。 此外，客户还可以通过让操作用户执行操作数据更新从而利用此体系结构来输入数据。 两个工作流都将导入数据的 Azure Functions 合并到 Azure SQL 数据库。 Azure Functions 必须由客户通过 Azure 门户进行配置，以处理特定于客户分析要求的导入任务。

Azure 为客户提供各种报告和分析服务。 此解决方案利用 Azure 机器学习服务与 SQL 数据库快速浏览数据，并通过更智能的数据建模更快地提供结果。 机器学习旨在通过探索数据集间的新关系加快查询速度。 最初，通过多个统计函数定型数据。 然后，最多七个其他查询池可以与相同的表格模型进行同步，以分散查询工作负载并缩短响应时间。 客户服务器使查询池总数达到 8 个。

为了加强分析和报告，可以为 SQL 数据库配置列存储索引。 机器学习和 SQL 数据库均可进行扩展、收缩或彻底关闭以响应客户使用情况。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 我们建议使用受信任的证书颁发机构来增强安全性，这是最佳做法。

数据上传到 SQL 数据库并通过机器学习定型后，操作用户和 SQL/数据管理员通过 Power BI 提取其摘要。 Power BI 可直观地显示数据并整合多个数据集的信息以提供更好的见解。 Power BI 具有很高适应性并与 SQL 数据库轻松集成。 客户可以将其配置为处理其业务需求所需的各种方案。

整个解决方案基于客户从 Azure 门户配置的 Azure 存储构建。 存储通过“存储服务加密”加密所有数据，以保持静态数据的机密性。 异地冗余存储可确保客户主数据中心的负面事件不会导致数据丢失。 第二个副本存储在数百英里以外的独立位置。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有资源。 Azure Active Directory (Azure AD) 基于角色的访问控制 (RBAC) 用于控制对已部署资源的访问。 这些资源包括 Azure Key Vault 中的客户密钥。 通过 Azure 安全中心和 Azure Monitor 监视系统运行状况。 客户同时将这两个监视服务配置为捕获日志。 系统运行状况在一个仪表板中显示，方便使用。

通常通过 SQL Server Management Studio 管理 SQL 数据库。 它从配置为通过安全 VPN 或 Azure ExpressRoute 连接访问 SQL 数据库的本地计算机运行。 我们建议配置 VPN 或 ExpressRoute 连接，以便管理及将数据导入资源组。

![符合 NIST SP 800-171 的数据分析的参考体系结构关系图](images/nist171-analytics-architecture.png "符合 NIST SP 800-171 的数据分析的参考体系结构关系图")

此解决方案使用以下 Azure 服务。 有关详细信息，请参阅[部署体系结构](#deployment-architecture)部分。

- Application Insights
- Azure Active Directory
- Azure 数据目录
- Azure 磁盘加密
- Azure 事件网格
- Azure Functions
- Azure 密钥保管库
- Azure Log Analytics
- Azure 机器学习
- Azure Monitor
- Azure 安全中心
- Azure SQL 数据库
- Azure 存储
- Azure 虚拟网络
    - (1)/16 网络
    - (2)/24 网络
    - (2) 网络安全组
- Power BI 仪表板

## <a name="deployment-architecture"></a>部署体系结构
以下部分详细描述了部署和实施要素。

Azure 事件网格：[事件网格](https://docs.microsoft.com/azure/event-grid/overview)让用户能够利用基于事件的体系结构轻松构建应用程序。 用户选择他们想要订阅的 Azure 资源。 然后，他们为事件处理程序或 Webhook 提供一个终结点以供发送事件。 在创建事件订阅时，客户可以通过向 Webhook URL 中添加查询参数来保护 Webhook 终结点。 事件网格仅支持 HTTPS WebHook 终结点。 使用事件网格，客户可以控制分发给不同的用户以执行各种管理操作的访问级别。 用户可以列出事件订阅、创建新密钥以及生成密钥。 事件网格使用 Azure RBAC。

Azure Functions：[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 是一种无服务器计算服务，可用于按需运行代码。 无需显式预配或管理基础结构。 使用 Azure Functions 可以运行脚本或代码片段，以响应各种事件。

Azure 机器学习：[Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/preview/)是一项数据科研技术，可以让计算机根据现有的数据来预测将来的行为、结果和趋势。

Azure 数据目录：[数据目录](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog)可帮助管理数据的用户更轻松地发现和理解数据源。 常见的数据源可以针对数据进行注册、标记和搜索。 数据将保留在现有位置，但其元数据的副本将添加到数据目录。 包含对数据源位置的引用。 会对元数据编制索引以轻松通过搜索发现每个数据源。 索引编制还让发现数据源的用户易于理解。

### <a name="virtual-network"></a>虚拟网络
此参考体系结构定义一个地址空间为 10.0.0.0/16 的专用虚拟网络。

网络安全组：[网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 包含允许或拒绝虚拟网络中的流量的访问控制列表。 NSG 可用于在子网或单个虚拟机级别保护流量。 存在以下 NSG：
  - 用于 Active Directory 的 NSG
  - 针对工作负荷的 NSG

每个 NSG 都打开了特定的端口和协议，以便解决方案能够安全正确地工作。 此外，为每个 NSG 启用了以下配置：
  - [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
  - 已将 Log Analytics 连接到 [NSG 的诊断功能](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子网**：每个子网都与其相应的 NSG 相关联。

### <a name="data-in-transit"></a>传输中的数据
默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 通过 Azure 门户到存储的所有事务均通过 HTTPS 进行。

### <a name="data-at-rest"></a>静态数据

该体系结构通过加密、数据库审核和其他措施保护静态数据。

Azure 存储：为了满足静态数据加密要求，所有[存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。 此功能有助于保护数据，以支持 NIST SP 800-171 定义的组织安全承诺和符合性要求。

Azure 磁盘加密：[磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

Azure SQL 数据库：SQL 数据库实例使用以下数据库安全措施：
-   使用 [Active Directory 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
-   [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
-   SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
-   在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
-   [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)启用检测并对出现的潜在威胁做出响应。 出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式时，它会提供安全警报。
-   [加密列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 它可以自动发现潜在的敏感数据，并建议应用合适的掩码。 动态数据掩码有助于减少访问，以便敏感数据不会由未经授权访问而退出数据库。 客户需要负责根据其数据库架构调整设置。

### <a name="identity-management"></a>身份管理
以下技术在 Azure 环境中提供用于管理数据访问的功能：
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 此解决方案的所有用户（包括访问 SQL 数据库的用户）均在 Azure AD 中创建。
-   使用 Azure AD 对应用程序执行身份验证。 有关详细信息，请参阅如何[将应用程序与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 数据库列加密还使用 Azure AD 对访问 SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 可供管理员用于定义细化的访问权限。 使用它，管理员可以仅授予用户执行其作业所需的访问次数。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问数据。 订阅访问仅限于订阅管理员。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 可供客户用来最大限度地减少有权访问特定信息（例如数据）的用户数量。 管理员可以使用 Azure AD Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 此外，还可以根据需要，使用此功能来实施按需、实时的管理性访问。
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可检测影响组织标识的潜在漏洞。 它将自动响应配置为检测与组织标识相关的可疑操作。 它还会调查可疑事件，以采取适当的措施进行解决。

### <a name="security"></a>安全
**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Key Vault 帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Key Vault 功能帮助客户保护数据：
- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块保护的 2048 位 RSA 密钥。
- 使用 RBAC 可向所有用户和标识授予最低要求的权限。
- 启用 Key Vault 的诊断日志时，保留期设置为至少 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

Azure 安全中心：借助[安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负载中集中应用和管理安全策略、限制威胁暴露，以及检测和应对攻击。 此外，安全中心还会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

 安全中心会使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 安全中心有一组[预定义的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，会在出现威胁或可疑活动时触发。 客户可以使用[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据已从环境中收集到的数据定义新的安全警报。

 安全中心提供按优先级排序的安全警报和事件。 安全中心让客户可以更轻松地发现并解决潜在安全问题。 针对每个检测到的威胁会生成一个[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。 事件响应团队可在调查和修正威胁时使用这些报告。

### <a name="logging-and-auditing"></a>日志记录和审核

Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、存储日志、Key Vault 审核日志以及 Azure 应用程序网关访问和防火墙日志。 所有诊断日志都将写入集中式加密 Azure 存储帐户进行存档。 用户可以配置多达 730 天的保留期，以满足其特定要求。

Log Analytics：这些日志将整合到 [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 中用于进行处理、存储和仪表板报告。 收集数据后，会针对 Operations Management Suite 工作区中的每种数据类型将数据整理到单独的表中。 如此一来，无论数据的原始源如何，所有数据都可以一起分析。 安全中心与 Log Analytics 集成。 客户可以使用 Log Analytics 查询来访问其安全事件数据并将其与其他服务中的数据合并在一起。

以下 Log Analytics [管理解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)是此体系结构的一部分：
-   [Active Directory 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：可以使用 Active Directory 运行状况检查解决方案定期评估服务器环境的风险和运行状况。 此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案会定期评估服务器环境的风险和运行状况。 此解决方案为客户提供了特定于已部署服务器基础结构的建议优先级列表。
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告部署的代理数量及其地理分布状况。 此外，它还报告未响应代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户所有 Azure 订阅的 Azure 活动日志。

Azure 自动化：[自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 SQL 数据库收集日志。 客户可以使用自动化[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)解决方案轻松识别环境中的更改。

Azure Monitor：[Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 可以帮助用户跟踪性能、维护安全和确定趋势。 组织可以使用它来审核、创建警报并存档数据。 此外，他们还可以跟踪 Azure 资源中的 API 调用。

**Application Insights**：[Application Insights](https://docs.microsoft.com/azure/application-insights/) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理服务。 它可检测性能异常并包括强大的分析工具。 此工具可帮助诊断问题并帮助客户了解用户使用应用做了什么。 它旨在帮助用户持续提高性能与可用性。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/nist171-analytics-tm)，也可以在此处找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![符合 NIST SP 800-171 的数据分析威胁模型](images/nist171-analytics-threat-model.png "符合 NIST SP 800-171 的数据分析威胁模型")

## <a name="compliance-documentation"></a>符合性文档
[Azure 安全性和符合性蓝图 - NIST SP 800-171 客户责任矩阵](https://aka.ms/nist171-crm)列出了 NIST SP 800-171 要求的所有安全控制措施。 此矩阵详细说明了每项控制措施的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性和符合性蓝图 - NIST SP 800-171 数据分析控制措施实施矩阵](https://aka.ms/nist171-analytics-cim)提供了有关数据分析体系结构可应对的 NIST SP 800-171 控制措施的信息， 其中包括实施方案如何满足每个所涵盖控制措施要求的详细说明。


## <a name="guidance-and-recommendations"></a>指导和建议
### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
必须配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此数据分析参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行。 客户可以利用该连接在其网络和 Azure 之间安全“传输”加密链接内的信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，因此，Microsoft 提供了另一个更为安全的连接选项。 ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接直接连接到客户的电信服务提供商。 因此，数据不会在 Internet 上传输且不会对 Internet 公开。 与典型连接相比，这些连接在可靠性、速度、延迟和安全性方面都更胜一筹。 

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-process"></a>提取-转换-加载过程
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 可将数据加载到 SQL 数据库，无需单独的提取、转换、加载或导入工具。 PolyBase 允许通过 T-SQL 查询访问数据。 Microsoft 商业智能和分析堆栈以及与 SQL Server 兼容的第三方工具都可以与 PolyBase 一起使用。

### <a name="azure-ad-setup"></a>Azure AD 设置
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 对于管理部署以及预配与环境交互的人员的访问至关重要。 只需[单击四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将本地部署 Active Directory 与 Azure AD 集成。 客户还可以将已部署的 Active Directory 基础结构（域控制器）绑定到 Azure AD。 若要执行此操作，请将 Active Directory 基础结构作为 Azure AD 林的子域进行部署。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
