---
title: Azure 安全性与符合性蓝图 - 适用于英国官方工作负载的 PaaS Web 应用程序托管
description: Azure 安全性与符合性蓝图 - 适用于英国官方工作负载的 PaaS Web 应用程序托管
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: b4b37a073f41295bf800d4e6c34681e0a06799c7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450317"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure 安全性与符合性蓝图：适用于英国官方工作负载的 PaaS Web 应用程序托管

## <a name="azure-security-and-compliance-blueprints"></a>Azure 安全性和符合性蓝图

Azure 蓝图由指导文档和自动化模板组成，用于部署基于云的体系结构，为具有认证或符合性要求的方案提供解决方案。 Azure 蓝图提供指南和自动化模板集合，使 Microsoft Azure 客户能够通过预配可扩展的基础结构来加速业务目标交付以满足任何其他要求。

## <a name="overview"></a>概述

此 Azure 安全性与符合性蓝图提供了指南和自动化脚本，提供 适用于处理归类为[英国官方](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/251480/Government-Security-Classifications-April-2014.pdf)工作负载的 Microsoft Azure [平台即服务 (PaaS)](https://azure.microsoft.com/overview/what-is-paas/)托管 Web 应用程序体系结构。 该安全分类包含公共部门创建或处理的大部分信息。 这包括日常业务运营和服务，如果这些信息丢失、被盗或已发布在媒体上，某些情况可能会产生破坏性后果。 官方分类的典型威胁配置文件与提供有价值信息和服务的私营企业大致相同。 英国官方预计需要维护英国政府数据或服务免受具有有限能力和资源威胁或攻击者造成的泄露，例如（但不限于）黑客行动者、单一问题压力团体、调查记者、有能力的个人黑客和大多数犯罪个人和团体。

英国国家网络安全中心 (NCSC) 对此蓝图进行了审查，并与 NCSC 14 云安全原则保持一致。

该体系结构使用 Azure [平台即服务](https://azure.microsoft.com/overview/what-is-paas/)组件来提供一个环境，使客户能够避免购买软件许可证、管理基础的应用程序体系结构和中间件或开发工具以及其他资源的费用和复杂性。 客户管理他们开发的应用程序和服务，专注于提供业务价值，而 Microsoft Azure 管理其他 Azure 资源（例如虚拟机、存储和网络），将架构管理的[更多责任分工](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility)放到 Azure 平台上。 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)提供自动缩放和高可用性，支持 Windows 和 Linux，并允许将 GitHub、Visual Studio Team Servicess 或任何 Git 存储库作为默认服务进行自动部署。 通过使用应用服务，开发人员可以专注于创造商业价值，而无需管理基础结构的开销。 可以构建新的 Java、 PHP、 Node.js、 Python、 HTML 或 C# web 应用程序领域，也可以将现有的云或本地 Web 应用程序迁移到 Azure 应用服务 （尽管需要进行彻底的尽职调查和测试以确认性能）。

此蓝图侧重于提供安全基础，[平台即服务](https://azure.microsoft.com/overview/what-is-paas/)基于 Web 的界面，适用于公共和后台用户。 此蓝图设计方案会考虑使用 Azure 托管的基于 Web 的服务，公共用户可以安全地提交、查看和管理敏感数据；后台或政府运营商也可以安全地处理公共用户提交的敏感数据。 此方案的用例可能包括：

- 提交纳税申报表的用户，由政府运营商处理提交的内容；
- 用户通过基于 Web 的应用程序请求服务，后台用户验证并提供服务；或
- 用户查找并查看与政府服务有关的公共域帮助信息。

使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)和 Azure 命令行界面脚本，该蓝图部署了一个符合英国国家网络安全中心 (NCSC) 14 项[云安全原则](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)和 Internet 安全中心 (CIS) [关键安全控制措施](https://www.cisecurity.org/critical-controls.cfm)的环境。 NCSC 建议客户使用云安全原则来评估服务的安全属性，并帮助了解客户与供应商之间的责任划分。 Microsoft 已针对这些原则提供了相关信息，以帮助用户更好的了解责任划分。 Microsoft 白皮书[使用 Microsoft Azure 的英国云的 14 项云安全控制措施](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1)支持此体系结构和相应的 Azure 资源管理器模板。 该体系结构已经过 NCSC 审核，符合英国 NCSC 14 项云安全原则，从而使公共部门组织能够快速跟踪其在全球和英国 Microsoft Azure 云上使用基于云的服务履行合规义务的能力。 此模板会部署工作负荷的基础结构。 客户必须安装和配置应用程序代码和支持的业务层和数据层软件。 [此处](https://aka.ms/ukofficial-paaswa-repo/)提供了详细的部署说明。

此蓝图是一个基础体系结构。 我们的客户可以使用此蓝图作为其基于 Web 的官方分类工作负载的基础，并根据自己的要求扩展模板和资源。 此蓝图建立在[UK-OFFICAL Three-Tier IaaS Web 应用程序蓝图](https://aka.ms/ukofficial-iaaswa)的原则之上，为我们的客户提供[基础结构即服务 (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)和 PaaS 实现选项，用于托管基于 Web 的工作负载。

要部署此蓝图，Azure 订阅是必需的。 如果没有 Azure 订阅，则可以免费快速注册：Azure入门。 单击[此处](https://aka.ms/ukofficial-paaswa-repo/)获取部署说明。

## <a name="architecture-and-components"></a>体系结构和组件

此蓝图在支持英国官方工作负载的 Azure 云环境中提供 Web 应用程序托管解决方案。 该体系结构提供了一个安全的环境，可以利用 Azure 平台即服务功能。 环境中部署了两个应用服务 Wep 应用（一个用于公共用户，另一个用于后台用户），这两个应用与 API 应用层一起为 Web 前端提供业务服务。 Azure SQL 数据库部署为应用程序的托管关系数据存储。 通过 TLS 1.2 对来自平台外部的这些组件以及所有这些组件之间的连接进行加密，以确保私密传输数据，并只有通过 Azure Active Directory 进行身份验证后才能访问数据。

![适用于英国官方工作负载的 PaaS Web 应用程序托管参考体系结构图](images/ukofficial-paaswa-architecture.png?raw=true "PaaS Web Application Hosting for UK OFFICIAL Workloads reference architecture diagram")

作为部署体系结构的一部分，此解决方案还部署了安全存储供应、监控和日志记录、统一安全管理和高级威胁防护以及管理功能，以确保客户拥有保护和监控其环境所必需的所有工具。

此解决方案使用以下 Azure 服务。 要详细了解部署体系结构，请参阅[部署体系结构](#deployment-architecture)部分。

- Azure Active Directory
- 托管服务标识
- 应用服务
- Web 应用
- API 应用
- Azure DNS
- Key Vault
- Azure Monitor
- Application Insights
- Log Analytics
- Azure 资源管理器
- Azure 安全中心
- Azure SQL 数据库
- Azure 存储

## <a name="deployment-architecture"></a>部署体系结构

以下部分详细描述了部署和实施要素。

### <a name="security"></a>安全

#### <a name="identity-and-authentication"></a>标识和身份验证

此蓝图确保通过目录和身份管理服务保护对资源的访问。 此体系结构充分利用[标识作为安全边界](https://docs.microsoft.com/azure/security/security-paas-deployments#identity-as-the-primary-security-perimeter)。 

以下技术在 Azure 环境中提供标识管理功能：

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 解决方案的所有用户（包括访问 SQL 数据库的用户）都在 Azure Active Directory 中创建。
- 使用 Azure AD 执行面向 Web 应用程序的操作员的身份验证和 Azure 资源管理的访问。 有关详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。
- 数据库列加密使用 Azure AD 对访问 Azure SQL 数据库的应用程序进行身份验证。 有关详细信息，请参阅[Always Encrypted：保护 SQL 数据库中的敏感数据](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。
- 为面向 Web 应用程序的用户配置公共访问。 要通过 Active Directory 或社交网络标识提供者创建并验证帐户，可根据需要集成 [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 可以检测潜在漏洞，为风险帐户提供建议，以增强组织身份的安全状况，配置对检测到的与组织身份相关的可疑操作的自动响应，调查可疑事件并采取适当措施解决这些问题。
- [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 可在 Azure 中实现极有针对性的访问管理。 订阅访问仅限于订阅管理员，Azure Key Vault 访问仅限于需要密钥管理访问权限的用户。
- 通过利用 [Azure Active Directory 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)，客户可以根据具体情况（如位置、设备、状态和登录风险），对其环境中的应用程序或用户实施额外的安全控制。
- [Azure DDoS 防护](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model)与应用程序设计最佳做法相结合，提供针对 DDoS 攻击的防御，具有始终在线的流量监控，以及对常见网络级攻击的实时缓解。 通过 PaaS 体系结构，平台级 DDoS 防护对客户而言是透明的且被集成到平台中，但需要注意的是，应用程序安全设计责任在于客户。

#### <a name="data-in-transit"></a>传输中的数据

数据是从外部的传输的，并在 Azure 组件之间使用[传输层安全性/安全套接字层 (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption)进行保护，该协议使用基于共享秘密的对称加密来加密在网络上传输的通信。 默认情况下使用 TLS 1.2 保护流量。

#### <a name="security-and-malware-protection"></a>安全和恶意软件防护

在 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)可集中查看所有 Azure 资源的安全状态。 可立即确认相应的安全控制措施是否部署到位且配置正确，并且可以快速识别任何需要关注的资源。

[Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-overview)是个性化的云顾问程序，可帮助遵循最佳做法来优化 Azure 部署。 它可分析资源配置和遥测使用情况，并推荐解决方案，有助于提高 Azure 资源的经济效益、性能、高可用性和安全性。

[Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)是一种实时保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。 默认情况下，此选项安装在基础 PaaS 虚拟机基础结构上，并由 Azure Fabric 以向客户透明的方式进行管理，确保

### <a name="paas-services-in-this-blueprint"></a>此蓝图中的 PaaS 服务

#### <a name="azure-app-service"></a>Azure 应用服务

Azure Web 应用为使用 Java、PHP、Node.js Python、HTML和 C# 开发的 Web 应用程序提供了完全托管的 Web 托管环境，而无需管理基础架构 它提供自动缩放和高可用性、支持 Windows 和 Linux，并允许从[Visual Studio Team Services](https://azure.microsoft.com/services/visual-studio-team-services/) 或任何基于 Git 的存储库进行自动部署。

应用服务符合 [ISO、SOC、和 PCI 标准](https://www.microsoft.com/TrustCenter/)，可以使用[ Azure Active Directory ](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)或者通过社交登录（[Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication)[Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication)、[Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication) 和 [Microsoft 身份验证](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication)）对用户进行身份验证。

基本、标准和高级计划适用于生产工作负载，并在专用虚拟机实例上运行。 每个实例均可支持多个应用程序和域。 应用服务还支持 [IP 地址限制](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)，如果需要的话，可以将流量安全地传输到受信任的 IP 地址，还可以[管理服务标识](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)以便安全连接到其他的 PaaS 服务（[如  和](https://azure.microsoft.com/services/key-vault/) [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)）。 如果需要额外保护，还可使用独立计划在私有专用 Azure 环境中托管应用，对于需要与本地网络或其他性能和规模安全连接的应用而言，这是理想选择。

此模板将部署以下应用服务功能：

- [标准](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)应用服务计划层
- 多个 Web 应用[部署槽位](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing)：开发、预览、QA、UAT 和生产（默认槽位）。
- [托管服务标识](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)连接到[ Azure Key Vault](https://azure.microsoft.com/services/key-vault/)（这还可以用于提供访问[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)） 
- 集成 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps)来监视性能
- [诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- 指标[警报](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API 应用](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL 数据库

SQL 数据库是 Microsoft Azure 中通用的关系数据库托管服务，支持关系数据、JSON、空间和 XML 等结构。 SQL 数据库提供托管的单一 SQL 数据库、[弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)中的托管 SQL 数据库以及 SQL [管理实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)（公共预览版）。 它可以实现[动态可缩放性能](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)，并提供[列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview)（用于极端分析和报告）和[内存中 OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory)（用于极端事务处理）等选项。 Microsoft 可无缝处理 SQL 代码库的所有修补和更新，并避开底层基础结构的所有管理。

此蓝图中的 Azure SQL 数据库

Azure SQL 数据库实例使用以下数据库安全措施：

- [服务器级和数据库级防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，或通过[虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)使用[虚拟网络规则](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)。
- [透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，有助于保护 Azure SQL 数据库和 Azure 数据仓库免受恶意活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。
- [Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)，可以在一个中心位置集中管理数据库用户和其他 Microsoft 服务的标识。 集中 ID 管理提供一个单一位置来管理数据库用户，并简化权限管理。
- 使用 Azure Active Directory 进行数据库管理
- [审核日志](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)用于存储帐户
- 指标[警报](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)，提醒失败的 DB 连接
- [SQL 威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted 列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure 存储

Microsoft [Azure 存储](https://azure.microsoft.com/services/storage/)是 Microsoft 管理的云服务，提供高可用性、安全性、持久性、可扩展性和冗余性的存储。 Azure 存储包括 Blob 存储、文件存储和队列存储。

#### <a name="azure-storage-in-this-blueprint"></a>此蓝图中的 Azure 存储

此模板使用以下 Azure 存储组件：

- [存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- 仅允许 HTTPS 连接

#### <a name="data-at-rest"></a>静态数据

通过[存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)写入 Azure 存储的所有数据都通过 256 位 AES 加密进行加密，这是可用的最强块密码之一。 可以将 Microsoft 管理的加密密钥与 SSE 一起使用，也可以使用[自己的加密密钥](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)。

可以使用[虚拟网络规则](https://docs.microsoft.com/azure/storage/common/storage-network-security)通过[虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)保护存储帐户。

有关保护 Azure 存储的详细的信息，请参阅[安全指南](https://docs.microsoft.com/azure/storage/common/storage-security-guide)。


### <a name="secrets-management"></a>机密管理

#### <a name="azure-key-vault"></a>Azure 密钥保管库

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 用于保护应用程序密钥和机密，以确保第三方无法访问它们。 Key Vault 不适合用作用户密码的存储。 它被称为保管库，允许用户创建多个安全容器。 这些保管库受硬件安全模块 (HSM) 的支持。 保管库可以集中存储应用程序机密，降低安全信息意外丢失的可能性。 Key Vault 还控制并记录外界对其所存储内容的访问。 Azure Key Vault 负责处理传输层安全性 (TLS) 证书的请求和续订事宜，其提供的功能是可靠的证书生命周期管理解决方案所必需的。

#### <a name="azure-key-vault-in-this-blueprint"></a>此蓝图中的 Azure Key Vault

- 保存存储访问密钥，并为面向客户的 Web 应用程序的[管理服务标识](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)授予读取访问权限
- 保存 SQL Server DBA 密码（在单独的保管库中）
- 诊断日志记录

### <a name="monitoring-logging-and-audit"></a>监视、日志记录和审核

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Azure 中的一项服务，可帮助收集和分析云和本地环境中资源生成的数据。

#### <a name="log-analytics-in-this-blueprint"></a>此蓝图中的 Log Analytics

- SQL 评估
- Key Vault 诊断
- Application Insights 连接
- Azure 活动日志

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 用于监视实时 Web 应用程序，它将自动检测性能异常、分析性能、诊断问题以及了解用户与应用交互的方式。 Application Insights 可部署在本地或云中托管的各种平台上（包括 .NET、Node.js 和 J2EE）。 它与 DevOps 进程集成，并且具有与不同开发工具的连接点。

#### <a name="application-insights-in-this-blueprint"></a>此蓝图中的 Application Insights

此模板使用以下 Application Insights 组件：

- 每个站点的应用程序洞察仪表板（操作员、客户和 API）

#### <a name="azure-activity-logs"></a>Azure 活动日志

[Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log)审核订阅的控制平面事件。 通过活动日志，可确定订阅中对资源执行的任何写入操作（PUT、POST、DELETE）的“内容、执行者和时间”等信息。 还可以了解该操作和其他相关属性的状态。

#### <a name="azure-monitor"></a>Azure Monitor

使用 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) 可收集指标、活动日志和诊断日志，为 Azure 服务启用核心监视。 Azure Monitor 针对 Microsoft Azure 中的大多数服务提供基本级别的基础结构指标和日志。

## <a name="threat-model"></a>威胁模型

此参考体系结构的数据流图可供[下载](https://aka.ms/ukofficial-paaswa-tm)，也可以在下面找到。 此模型有助于客户在做出修改时了解系统基础结构中存在的潜在风险点。

![适用于英国官方工作负载威胁模型的 PaaS Web 应用程序托管](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS Web Application Hosting for UK OFFICIAL Workloads threat model")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC 云安全原则符合性文档

Crown Commercial Service（一家致力于改善政府商业和采购活动的机构）续订了 Microsoft 的分层式企业云服务并将级别提升到 G-Cloud v6，其中涵盖 OFFICIAL 级别的所有产品/服务。 在 [Azure 英国 G-Cloud 安全评估摘要](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud)中可以找到 Azure 和 G-Cloud 的详细信息。

此蓝图符合 NCSC [云安全原则](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)中所述的 14 项云安全原则，可帮助确保所用环境支持分类为“UK-OFFICIAL”的工作负载。

[Azure安全性与符合性蓝图 - 英国官方客户责任矩阵](https://aka.ms/ukofficial-crm)（Excel 工作簿）列出了所有 14 项云安全原则，并指明每项原则（或原则的细则）的实施是由 Microsoft、客户还是两者共同负责。

[Azure 安全性与符合性蓝图 - 英国官方原则实施矩阵的 PaaS Web 应用程序](https://aka.ms/ukofficial-paaswa-pim)（Excel 工作簿）中列出了所有 14 项云安全原则，并针对在“客户责任矩阵”中指定由客户负责的每项原则（或原则的细则）指明：1) 蓝图是否实施该原则；2) 如何根据原则要求完成实施的说明。  

此外，云安全联盟 (CSA) 也发布了“云控制矩阵”，以帮助客户评估云提供商，以及确定在转移到云服务之前所要解答的问题。 作为回应，Microsoft Azure 解答了 CSA 舆论评估计划调查问卷 ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA))，其中阐述了 Microsoft 如何满足所提议的原则。

## <a name="third-party-assessment"></a>第三方评估

此蓝图已通过英国国家网络安全中心审查 (NCSC) 并符合 NCSC 的 14 项云安全原则

自动化模板已通过英国客户成功单元 Azure 云解决方案架体系结构团队和 Microsoft 合作伙伴 [Ampliphae](http://www.ampliphae.com/) 的测试。


## <a name="deploy-the-solution"></a>部署解决方案

此 Azure 安全性和符合性蓝图自动化包含的 JSON 配置文件和 PowerShell 脚本可供 Azure 资源管理器的 API 服务用来在 Azure 中部署资源。 [此处](https://aka.ms/ukofficial-paaswa-repo)提供了详细的部署说明。

为部署提供了三种方法，简单表述为 [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 适用于快速构建测试环境；参数化 [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 方法为工作负载环境提供了更好的配置；以及基于 Azure 门户的部署，其中操作员可以通过 Azure 门户指定部署参数。 

1.  将[此](https://aka.ms/ukofficial-paaswa-repo) GitHub 存储库克隆或下载到本地工作站。
2.  查看[方法 1：Azure CLI 2（Express 版本）](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version)并执行提供的命令。
3.  查看[方法 1a：（配置通过脚本参数部署）的 Azure CLI 2](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments)并执行提供的命令
4.  查看[方法 2：Azure 门户部署过程](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process)并执行列出的命令

## <a name="guidance-and-recommendations"></a>指导和建议

### <a name="api-management"></a>API 管理

[Azure API 管理](https://azure.microsoft.com/services/api-management/)可以在 API 应用服务前使用，为公开、代理和保护 API 提供额外的安全性、限制和控制保护。

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)可以作为控件，允许用户注册、创建标识并启用公共 Web 应用程序的授权和访问控制。

## <a name="disclaimer"></a>免责声明

- 本文档仅供参考。 MICROSOFT 对本文档中的信息不作任何明示、默示或法定的担保。 本文档“按原样”提供。 本文档表达的信息和观点，包括 URL 和其他 Internet 网站参考，若有更改，恕不另行通知。 阅读本文档的客户须自行承担使用风险。
- 本文档不向客户提供对任何 Microsoft 产品或解决方案的任何知识产权的任何法律权利。
- 客户可复制本文档，将其用于内部参考。
- 本文档中的某些建议可能会导致 Azure 中数据、网络或计算资源使用量的增加，还可能导致客户 Azure 许可或订阅成本增加。
- 本体系结构旨在作为客户的基础，以根据他们的特定需求进行调整，而不应在生产环境中按原样使用。
- 本文档是作为参考内容制定的，不应该用于定义客户用来满足特定符合性要求和法规的所有方法。 客户应在已批准的客户实施项目中向其组织寻求合法支持。
