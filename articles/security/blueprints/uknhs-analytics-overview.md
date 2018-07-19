---
title: Azure 安全性和符合性蓝图 - 用于 UK NHS 的数据分析
description: Azure 安全性和符合性蓝图 - 用于 UK NHS 的数据分析
services: security
author: jomolesk
ms.assetid: 103dff31-e262-44a6-9b50-3b3467c0cb3a
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 8885eba0d69c869ad5d298094b835f0351d8d94d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343156"
---
# <a name="azure-security-and-compliance-blueprint-data-analytics-for-uk-nhs"></a>Azure 安全性和符合性蓝图：用于 UK NHS 的数据分析

## <a name="overview"></a>概述

此 Azure 安全性和符合性蓝图提供适用于收集、存储、分析和检索医疗保健数据的数据分析解决方案的参考体系结构和指导。 此解决方案演示客户可通过哪些方式来遵守 [NHS Digital](https://digital.nhs.uk/)（英国健康与社会保健部 (DHSC) 的一家合作伙伴）发布的[云安全合理做法指南](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide)中提供的指导。 《云安全合理做法指南》基于英国国家网络安全中心 (NCSC) 发布的[云安全 14 项原则](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)。

此参考体系结构、实施指南和威胁模型旨在用作客户的基础体系结构，他们可以根据具体的要求进行调整，在未经额外配置的情况下，不应在生产环境中按原样使用。 客户负责针对使用本体系结构构建的任何解决方案开展相应的安全性与符合性评估；具体要求根据客户的每种实施方式具体情况而异。

## <a name="architecture-diagram-and-components"></a>体系结构示意图和组件

此解决方案提供一个分析平台，客户可以在此平台上构建自己的分析工具。 该参考体系结构概述了一个泛型用例，其中客户通过 SQL/数据管理员的批量数据导入或通过经操作用户的操作数据更新输入数据。 两个工作流都将导入数据的 Azure Functions 合并到 Azure SQL 数据库，所有外部连接都需要 TLSv1.2。 Azure Functions 必须由客户通过 Azure 门户进行配置，以处理特定于客户分析要求的导入任务。

Azure 为客户提供各种报告和分析服务；但此解决方案会将 Azure Analysis Services 与 Azure SQL 数据库 相结合，以快速浏览数据并通过更智能的客户数据建模更快地传递结果。 Azure Analytics Services 是一种意在通过探索数据集间的新关系加快查询速度的机器学习。 一旦通过多个统计函数训练数据，最多 7 个额外查询池（包括客户服务器一共 8 个）可以与相同的表格模式同步，从而分散查询工作负荷并减少响应时间。

对于增强的分析和报告，可以通过列存储索引配置 Azure SQL 数据库。 Azure Analytics Services 和 Azure SQL 数据库都可以进行横向或纵向扩展，或者完全关闭以响应客户使用情况。 所有 SQL 流量都通过包含自签名证书使用 SSL 加密。 作为最佳做法，Azure 建议使用受信任的证书颁发机构来增强安全性。

数据上传到 Azure SQL 数据库并被 Azure Analysis Services 定型后，操作用户和 SQL/数据管理员便可通过 Power BI 使用该数据。 Power BI 可直观地显示数据并整合多个数据集的信息以提供更好的见解。 其高度适应性和与 Azure SQL 数据库的轻松集成可确保客户按照自己的业务需求进行配置，从而处理各种情况。

该解决方案使用 Azure 存储帐户。客户可将存储帐户配置为使用存储服务加密，以维持静态数据的机密性。 Azure 在客户选择的数据中心存储三个数据副本，以提供复原能力。 地理冗余的存储确保将数据复制到数百英里以外的辅助数据中心，且同样在该数据中心存储三个副本，防止客户主要数据中心的不利事件导致数据丢失。

为了增强安全性，将通过 Azure 资源管理器以资源组的形式管理此解决方案中的所有资源。 Azure Active Directory 基于角色的访问控制用于控制对所部署资源（例如 Azure Key Vault 中的密钥）的访问。 通过 Azure 安全中心和 Azure Monitor 监视系统运行状况。 客户配置两个监视服务捕获日志并在单独的、可轻松导航的仪表板中显示系统运行状况。

Azure SQL 数据库通常通过 SQL Server Management Studio 进行管理，后者通过配置为经由安全 VPN 或 ExpressRoute 连接访问 Azure SQL 数据库的本地计算机运行。 **Microsoft 建议配置 VPN 或 ExpressRoute 连接，以便进行管理和将数据导入参考体系结构资源组**。

![用于 UK NHS 的分析参考体系结构图](images/uknhs-analytics-architecture.png?raw=true "用于 UK NHS 的分析参考体系结构图")

此解决方案使用以下 Azure 服务。 [部署体系结构](#deployment-architecture)部分提供了部署体系结构的详细信息。

- Azure Active Directory
- Azure Analysis Service
- Azure 自动化
- Azure 数据目录
- Azure 磁盘加密
- Azure 事件网格
- Azure Functions
- Azure 密钥保管库
- Azure Monitor
- Azure 安全中心
- Azure SQL 数据库
- Azure 存储
- Azure 虚拟网络
    - (1) /16 网络
    - (2) /24 网络
    - (2) 网络安全组
- Power BI 仪表板

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了部署和实施要素。

**Azure Functions**：[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 是一项无服务器的计算服务，用户可使用它按需运行代码，而无需显式预配或管理基础结构。 使用 Azure Functions 可以运行脚本或代码片段，以响应各种事件。

**Azure Analysis Service**：[Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) 提供企业数据建模和与 Azure 数据平台服务的集成。 通过将多个源的数据结合到单个数据模型中，Azure Analysis Service 加快浏览大量数据的速度。

### <a name="virtual-network"></a>虚拟网络

体系结构定义了一个地址空间为 10.200.0.0/16 的专用虚拟网络。

**网络安全组**：[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)包含允许或拒绝虚拟网络中的流量的访问控制列表。 网络安全组可用于在子网或单个虚拟机级别保护流量。 存在以下网络安全组：

  - Active Directory 有 1 个网络安全组
  - 工作负荷有 1 个网络安全组

每个网络安全组打开了特定的端口和协议，使解决方案能够安全正确地工作。 此外，为每个网络安全组启用了以下配置：

- [诊断日志和事件](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已启用并存储在存储帐户中
- Log Analytics 连接到[网络安全组的诊断日志](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**子网**：每个子网与其对应的网络安全组相关联。

### <a name="data-in-transit"></a>传输中的数据

默认情况下，Azure 会加密与 Azure 数据中心之间的所有通信。 通过 Azure 门户到 Azure 存储的所有事务均通过 HTTPS 进行。

### <a name="data-at-rest"></a>静态数据

该体系结构通过加密、数据库审核和其他措施保护静态数据。

**Azure 存储**：为了满足静态数据加密要求，所有 [Azure 存储](https://azure.microsoft.com/services/storage/)均使用[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。 这有助于保护数据，以支持 NHS Digital 定义的组织安全承诺和符合性要求。

**Azure 磁盘加密**：[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能，为数据磁盘提供卷加密。 此解决方案与 Azure Key Vault 集成，可帮助控制和管理磁盘加密密钥。

**Azure SQL 数据库**：Azure SQL 数据库实例使用以下数据库安全措施：

- 使用 [Active Directory 身份验证和授权](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。
- [SQL 数据库审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。
- Azure SQL 数据库配置为使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，它对数据库、相关备份和事务日志文件进行实时加密和解密，以保护静态信息。 透明数据加密可确保存储的数据免遭他人未经授权的访问。
- 在授予相应的权限前，[防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)会阻止对数据库服务器的所有访问。 防火墙基于每个请求的起始 IP 地址授予数据库访问权限。
- [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)可以通过为可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式提供安全警报，在发生潜在威胁时进行检测和响应。
- [加密列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可以确保敏感数据永远不会在数据库系统中以明文形式显示。 启用数据加密后，只有具有密钥访问权限的客户端应用程序或应用程序服务器才能访问明文数据。
- [SQL 数据库动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)通过对非特权用户或应用程序模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码可以自动发现潜在的敏感数据，并建议应用合适的掩码。 这有助于识别和减少数据访问，避免通过未经授权的访问退出数据库。 客户需要负责调整动态数据掩码设置，使之遵循其数据库架构。

### <a name="identity-management"></a>身份管理

以下技术在 Azure 环境中提供用于管理数据访问的功能：

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 Azure SQL 数据库的用户）都在 Azure Active Directory 中创建。
- 使用 Azure Active Directory 对应用程序执行身份验证。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。 此外，数据库列加密使用 Azure Active Directory 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅如何[保护 Azure SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
- [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)使管理员能够定义细粒度的访问权限，以仅授予用户执行作业所需的访问量。 无需向每个用户授予 Azure 资源的不受限权限，管理员可以只允许使用特定的操作来访问数据。 订阅访问仅限于订阅管理员。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 使客户能够最大限度地减少有权访问特定信息的用户数量。 管理员可以使用 Azure Active Directory Privileged Identity Management 来发现、限制和监视特权标识及其对资源的访问。 还可以根据需要，使用此功能来实施按需、实时的管理访问。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测会影响组织标识的潜在漏洞，配置自动化的措施来应对所检测到的与组织标识相关的可疑操作，调查可疑的事件以采取相应的措施予以解决。

### <a name="security"></a>“安全”

**机密管理**：此解决方案使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理密钥和机密。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 以下 Azure Key Vault 功能可帮助客户保护和访问此类数据：

- 根据需要配置高级访问权限策略。
- 使用对密钥和机密所需的最低权限来定义 Key Vault 访问策略。
- Key Vault 中的所有密钥和机密都有过期日期。
- Key Vault 中的所有密钥受专用硬件安全模块的保护。 密钥类型是硬件安全模块保护的 2048 位 RSA 密钥。
- 使用基于角色的访问控制向所有用户和标识授予了最低必需权限。
- 启用 Key Vault 的诊断日志时，保留期设置为至少 365 天。
- 对密钥进行允许的加密操作时，仅限必需的操作。

**Azure 安全中心**：借助 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)，客户可在工作负荷中集中应用和管理安全策略、限制威胁曝露，以及检测和应对攻击。 此外，Azure 安全中心会访问 Azure 服务的现有配置，以提供配置与服务建议来帮助改善安全状况和保护数据。

Azure 安全中心使用各种检测功能，提醒客户针对其环境的潜在攻击。 这些警报包含有关触发警报的内容、目标资源以及攻击源的重要信息。 Azure 安全中心有一组预定义的[安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)，这些警报在出现威胁或可疑活动时触发。 客户可以使用 Azure 安全中心的[自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，根据从环境中收集到的数据定义新的安全警报。

Azure 安全中心提供区分优先级的安全警报和事件，让客户更轻松地发现和解决潜在安全问题。 针对检测到的每种威胁生成[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)，以帮助事件响应团队调查和解决威胁。

### <a name="logging-and-auditing"></a>日志记录和审核

Azure 服务广泛记录系统和用户活动以及系统运行状况：
- **活动日志**：[活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)提供针对订阅中资源执行的操作的深入信息。 活动日志可帮助确定操作的发起方、发生的时间和状态。
- **诊断日志**：[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包括每个资源发出的所有日志。 这些日志包括 Windows 事件系统日志、Azure 存储日志、Key Vault 审核日志以及应用程序网关访问和防火墙日志。 所有诊断日志都将写入集中式加密 Azure 存储帐户进行存档。 保留期是允许用户配置的，最长为 730 天，具体取决于组织的保留期要求。

**Log Analytics**：这些日志将整合到 [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 中进行处理、存储和在仪表板上报告。 收集以后，数据会按数据类型整理到不同的表中，这样就可以对所有数据进行集中分析，不管其最初来源是什么。 此外，Azure 安全中心与 Log Analytics 集成，使客户能够使用 Log Analytics 查询来访问其安全事件数据，并将这些数据与其他服务中的数据合并。

以下 Log Analytics [管理解决方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)包含为此体系结构的一部分：
-   [Active Directory 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：Active Directory 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并且提供特定于部署服务器基础结构的优先建议列表。
- [SQL 评估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 运行状况检查解决方案按固定时间间隔评估服务器环境的风险和运行状况，并为客户提供特定于部署服务器基础结构的优先建议列表。
- [代理运行状况](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理运行状况解决方案报告已部署代理的数量及其地理分布，以及无响应的代理数量和提交操作数据的代理数量。
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：Activity Log Analytics 解决方案可帮助分析客户的所有 Azure 订阅的 Azure 活动日志。

**Azure 自动化**：[Azure 自动化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)可以存储、运行和管理 Runbook。 在此解决方案中，Runbook 可帮助从 Azure SQL 数据库收集日志。 自动化[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)解决方案使得客户能够轻松识别环境中的更改。

**Azure Monitor**：[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) 通过使组织能够审核、创建警报和存档数据（包括在用户的 Azure 资源中跟踪 API 调用），帮助用户跟踪性能、维护安全性和确定趋势。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/uknhs-analytics-tm)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![用于 UK NHS 的分析威胁模型](images/uknhs-analytics-threat-model.png?raw=true "用于 UK NHS 的分析威胁模型")

## <a name="compliance-documentation"></a>符合性文档

[Azure 安全性和符合性蓝图 - UK NHS 客户责任矩阵](https://aka.ms/uknhs-crm)列出了 UK NHS 设定的所有安全原则。 此矩阵说明说明了每项原则的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性和符合性蓝图 - UK NHS 数据分析实现矩阵](https://aka.ms/uknhs-analytics-cim)提供了 数据分析体系结构解决哪些 UK NHS 要求的信息，包括实现如何满足每个所述原则的要求的详细说明。


## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute

需要配置安全 VPN 隧道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地建立与作为此数据分析参考体系结构的一部分部署的资源的连接。 通过适当设置 VPN 或 ExpressRoute，客户可以为传输中的数据添加一层保护。

在 Azure 中实施安全 VPN 隧道，可在本地网络与 Azure 虚拟网络之间创建虚拟专用连接。 此连接通过 Internet 进行，可让客户在其网络与 Azure 之间的加密链路内通过&quot;隧道&quot;安全地传输信息。 站点到站点 VPN 是安全成熟的技术，各种规模的企业已部署数十年。 此选项使用 [IPsec 隧道模式](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))作为加密机制。

由于 VPN 隧道中的流量会通过站点到站点 VPN 在 Internet 上遍历，Microsoft 提供了另一个更安全的连接选项。 Azure ExpressRoute 是 Azure 与本地位置或 Exchange 托管提供商之间专用的 WAN 链接。 ExpressRoute 连接并不绕过 Internet，并且与通过 Internet 的典型连接相比，这些连接可靠性更高、速度更快、延迟时间更短且安全性更高。 此外，由于使用的是客户电信提供商的直接连接，数据不会通过 Internet 遍历，因此不会在 Internet 上公开。

我们编写了有关如何实施安全混合网络，以便将本地网络扩展到 Azure 的[最佳做法](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-process"></a>提取-转换-加载过程

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 可将数据加载到 Azure SQL 数据库，无需单独的提取、转换、加载或导入工具。 PolyBase 允许通过 T-SQL 查询访问数据。 Microsoft 的商业智能和分析堆栈以及与 SQL Server 兼容的第三方工具都可以与 PolyBase 一起使用。

### <a name="azure-active-directory-setup"></a>Azure Active Directory 设置
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 对于管理部署以及预配与环境交互的人员的访问至关重要。 只需[单击四下](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)，即可将现有 Windows Server Active Directory 与 Azure Active Directory 集成。 客户还可通过将部署的 Active Directory 基础结构作为 Azure Active Directory 林的子域，将部署的 Active Directory 基础结构（域控制器）绑定到现有 Azure Active Directory。

## <a name="disclaimer"></a>免责声明

 - 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
 - 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
 - 客户可复制本文档，将其用于内部参考。
 - 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
 - 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
 - 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
