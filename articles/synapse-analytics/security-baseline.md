---
title: Synapse Analytics 的 Azure 安全基线
description: Synapse Analytics security 基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7f05e4fb0443107370f9182706bd35b45771e0f2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210892"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Synapse Analytics 的 Azure 安全基线

适用于 Synapse Analytics 的 Azure 安全基线包含的建议可帮助你提高部署的安全状况。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：通过专用链接保护 Azure SQL Server 到虚拟网络。 使用 azure 专用链接，可以通过虚拟网络中的专用终结点访问 Azure PaaS 服务。 虚拟网络与服务之间的流量将遍历 Microsoft 主干网络。

或者，在连接到 Synapse SQL 池时，通过使用网络安全组缩小到 SQL 数据库的传出连接的范围。 通过将 "允许 Azure 服务" 设置为 "关闭"，禁用通过公共终结点将所有 Azure 服务流量发送到 SQL 数据库。 确保防火墙规则中不允许使用公共 IP 地址。

* [了解 Azure 专用链接](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [了解 Azure Synapse SQL 的专用链接](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视和记录虚拟网络、子网和网络接口的配置和流量

**指南**：连接到 AZURE Synapse SQL 池时，如果已启用网络安全组 (NSG) 流日志，请将日志发送到 Azure 存储帐户以进行流量审核。

还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用;此建议适用于 Azure 应用服务或托管 web 应用程序的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意 IP 地址的通信

**指南**：将高级威胁防护 (ATP) 用于 AZURE Synapse SQL。 ATP 会检测异常活动，这些活动表明对访问或利用数据库的异常和潜在有害尝试，并且它可能会触发各种警报，如 "潜在的 SQL 注入" 和 "从异常位置访问"。 ATP 是) 产品/服务的高级数据安全 (的一部分，可通过中央 SQL 广告门户进行访问和管理。

在与 Azure Synapse SQL 关联的虚拟网络上启用 DDoS 保护，以防止受到分布式拒绝服务攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

* [了解 Azure Synapse SQL 的 ATP](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [如何为 Azure SQL Database 启用高级数据安全性](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [广告概述](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何配置 DDoS 防护](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 安全中心集成的威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：连接到 AZURE Synapse SQL 池时，如果已启用网络安全组 (NSG) 流日志，请将日志发送到 Azure 存储帐户以进行流量审核。 你还可以将流日志发送到 Log Analytics 工作区，或将流日志流式传输到事件中心。 如果需要调查异常活动，请启用网络观察程序数据包捕获。

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：将高级威胁防护 (ATP) 用于 AZURE Synapse SQL。 ATP 会检测异常活动，这些活动表明对访问或利用数据库的异常和潜在有害尝试，并且它可能会触发各种警报，如 "潜在的 SQL 注入" 和 "从异常位置访问"。 ATP 是) 产品/服务的高级数据安全 (的一部分，可通过中央 SQL 广告门户进行访问和管理。 ATP 还将警报与 Azure 安全中心集成。

* [了解 Azure Synapse SQL 的 ATP](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用;此建议适用于 Azure 应用服务或托管 web 应用程序的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

使用 Azure Synapse SQL 池的服务终结点时，需要出站到 Azure SQL 数据库的公共 IP 地址：必须在 Azure SQL 数据库 Ip 上打开网络安全)  (组，以允许连接到 Azure SQL 数据库 Ip。 为此，可以使用适用于 Azure SQL 数据库的 NSG 服务标记。

* [了解 Azure SQL 数据库的服务终结点的服务标记](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [了解和使用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 策略相关的 SQL 池资源的网络安全配置。 你可以使用 "Sql-dmo" 命名空间来定义自定义策略定义，或使用为 Azure SQL 数据库/服务器网络保护设计的任何内置策略定义。 适用于 Azure SQL 数据库服务器的适用内置网络安全策略的一个示例是： "SQL Server 应使用虚拟网络服务终结点"。

使用 Azure 蓝图可以通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理模板、基于角色的访问控制 (RBAC) 和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对网络安全组使用标记 (NSG) 以及与网络安全和流量流相关的其他资源。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 AZURE Synapse SQL 池相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 Azure 资源的时间源。 你可以为计算部署更新时间同步。

* [如何为 Azure 计算资源配置时间同步](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：可以为特定数据库定义审核策略，也可以为 azure (中托管 azure Synapse) 的默认服务器策略定义审核策略。 服务器策略适用于服务器上的所有现有数据库和新建数据库。

如果启用服务器审核，它将一直应用于数据库。  将不考虑数据库审核设置审核数据库。

启用审核后，可以将它们写入 Azure 存储帐户、Log Analytics 工作区或事件中心中的审核日志。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何为 Azure SQL 资源设置审核](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：在 azure sql server 级别上为 Synapse SQL 池启用审核，并为 (Azure 存储、Log Analytics 或事件中心) 的审核日志选择存储位置。

可以在数据库级别或服务器级别上启用审核，并且建议仅在服务器级别启用审核，除非你需要为特定数据库配置单独的数据接收器或保留期。

* [如何为 Azure SQL 数据库启用审核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [如何为服务器启用审核](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [服务器级和数据库级审核策略的差异](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用;此基准用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在存储帐户、Log Analytics 的工作区或事件中心中存储与你的 Synapse SQL 池相关的日志时，请根据你的组织的符合性法规设置日志保持期。

* [管理 Azure Blob 存储生命周期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [如何在 Log Analytics 工作区中设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [捕获事件中心中的流式处理事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指导**：分析和监视日志中的异常行为，并定期审查结果。 结合 Azure 安全中心使用适用于 Azure SQL 数据库的高级威胁防护，对与 SQL 数据库相关的异常活动发出警报。 或者，根据与 SQL 数据库相关的指标值或 Azure 活动日志条目来配置警报。

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [了解 Azure SQL 数据库的高级威胁防护和警报](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [如何为 Azure SQL Database 启用高级数据安全性](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何配置 Azure SQL 数据库的自定义警报](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：将 Azure SQL 数据库的高级威胁防护 (ATP) 与 Azure 安全中心结合使用，监视异常活动并发出警报。 ATP 是高级数据安全 (ADS) 产品的一部分，可通过门户中的中央 SQL 广告进行访问和管理。 广告包括用于发现和分类敏感数据、呈现和缓解潜在的数据库漏洞，以及检测可能指示数据库威胁的异常活动的功能。

或者，你可以将和机载数据启用到 Azure Sentinel。

* [了解 Azure SQL 数据库的高级威胁防护和警报](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [如何为 Azure SQL Database 启用高级数据安全性](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何在 Azure 安全中心管理警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;对于与 Synapse SQL 池相关的资源，反恶意软件解决方案由 Microsoft 在底层平台上进行管理。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;与 Synapse SQL 池相关的资源不生成 DNS 日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用;命令行审核不适用于 Azure Synapse SQL。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：通过 AZURE ACTIVE DIRECTORY 或 SQL 身份验证对用户进行身份验证。

首次部署 Azure SQL 时，需为该登录名指定管理员登录名和关联的密码。 此管理帐户称为服务器管理员。您可以通过打开 Azure 门户并导航到服务器或托管实例的 "属性" 选项卡来确定数据库的管理员帐户。 你还可以配置具有完全管理权限的 Azure AD 管理员帐户，若要启用 Azure Active Directory 身份验证，则需要此帐户。

对于管理操作，请使用必须显式分配的 Azure 内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

* [SQL 数据库的身份验证](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [为非管理用户创建帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [使用 Azure Active Directory 帐户进行身份验证](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [如何管理 Azure SQL 中的现有登录名和管理员帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Azure 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Azure Active Directory 没有默认密码的概念。 预配 Azure Synapse SQL 池时，建议选择将身份验证与 Azure Active Directory 集成。 使用此身份验证方法时，用户将提交用户帐户名，并请求服务使用 Azure Active Directory (Azure AD) 中存储的凭据信息。

* [如何配置和管理 Azure SQL Azure Active Directory 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [了解 Azure SQL 中的身份验证](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕使用专用管理帐户创建策略和过程。 使用 Azure 安全中心的标识和访问管理来监视通过 Azure Active Directory 登录的管理帐户的数量。

若要确定数据库的管理员帐户，请打开 Azure 门户，然后导航到服务器或托管实例的“属性”选项卡。

* [了解 Azure 安全中心标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何管理 Azure SQL 中的现有登录名和管理员帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory (SSO 的单一登录) 

**指南**：使用 Azure 应用注册 (服务主体) 来检索可用于在控制面与数据仓库交互的令牌 (通过 API 调用 Azure 门户) 。

* [如何调用 Azure REST API](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何将客户端应用程序（服务主体）注册到 Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Synapse SQL REST API 信息](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理的建议。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [了解 Azure SQL 中的 MFA](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：对管理任务使用 Azure 托管的安全工作站

**指南**：使用具有多重身份)  (验证的特权访问工作站 (PAW) 配置为登录和配置 Azure 资源。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告在环境中发生可疑活动或不安全的活动时生成日志和警报。

结合 Azure 安全中心使用适用于 Azure SQL 数据库的高级威胁防护来检测异常活动，并对其发出警报，指出对访问或利用数据库的异常和潜在有害尝试。

使用 SQL Server 审核可以创建服务器审核，其中可能包含服务器级事件的服务器审核规范，以及数据库级事件的数据库审核规范。 可将审核的事件写入事件日志或审核文件。

* [如何确定标记为存在风险活动的 Azure AD 用户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 安全中心监视用户的标识和访问活动](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [查看高级威胁防护和潜在警报](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [了解 Azure SQL 中的登录名和用户帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [了解 SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问门户和 Azure 资源管理。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：在 Synapse SQL 池中创建 Azure sql 数据库服务器的 AZURE ACTIVE DIRECTORY (AD) 管理员。

* [如何配置和管理 Azure SQL Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [如何创建和配置 Azure AD 实例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**： Azure Active Directory 提供有助于发现陈旧帐户的日志。 此外，还可以使用 Azure Active Directory 访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期查看用户的访问权限，以确保只有正确的用户才能继续访问。

使用 SQL 身份验证时，请在数据库中创建包含的数据库用户。 确保将一个或多个数据库用户放入一个自定义数据库角色，该角色具有适用于该用户组的特定权限。

* [如何使用访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [了解 Azure SQL 中的登录名和用户帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：使用 Azure SQL 配置 AZURE ACTIVE DIRECTORY (AD) 身份验证，并为 Azure Active Directory 用户帐户启用诊断设置，将审核日志和登录日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需的警报。

使用 SQL 身份验证时，请在数据库中创建包含的数据库用户。 确保将一个或多个数据库用户放入一个自定义数据库角色，该角色具有适用于该用户组的特定权限。

* [如何使用访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [如何配置和管理 Azure SQL 数据库的 Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [了解 Azure SQL 中的登录名和用户帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帐户登录行为偏差时发出警报

**指南**：使用 Azure Active Directory (Azure AD) Identity Protection 和风险检测功能，以配置对检测到的与用户标识相关的可疑操作的自动响应。 此外，还可以将数据导入到 Azure Sentinel，进一步进行调查。

使用 SQL 身份验证时，请在数据库中创建包含的数据库用户。 确保将一个或多个数据库用户放入一个自定义数据库角色，该角色具有适用于该用户组的特定权限。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [了解 Azure SQL 中的登录名和用户帐户](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：在 Microsoft 需要访问 Synapse SQL 池中的 Azure SQL 数据库相关数据的支持方案中，Azure 客户密码箱提供了一个用于查看和批准或拒绝数据访问请求的接口。

* [了解客户密码箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

数据发现 &amp; 分类内置于 Azure SYNAPSE SQL 中。 它提供用于发现、分类、标记和报告数据库中的敏感数据的高级功能。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [了解数据发现 &amp; 分类](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 资源应由虚拟网络/子网分开、正确标记并在网络安全组或 Azure 防火墙内保护。 应该隔离存储或处理敏感数据的资源。 使用专用链接;在虚拟网络中部署 Azure SQL Server，并使用 "专用" 链接安全地连接。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何设置 Azure SQL 数据库的专用链接](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：对于 Synapse SQL 池中的任何 Azure sql 数据库，存储或处理敏感信息，使用标记将数据库和相关资源标记为敏感。 在 Azure SQL 数据库实例上，将专用链接配置为结合网络安全组 (NSG) 服务标记，以防止敏感信息的渗透。

此外，适用于 Azure SQL 数据库的高级威胁防护、Azure SQL 托管实例和 Azure Synapse 可检测异常活动，指示访问或利用数据库的异常和潜在有害尝试。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容都视为敏感内容，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [如何配置 Private Link 和 Nsg 以防止 Azure SQL 数据库实例上的数据渗透](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [了解 Azure SQL 数据库的高级威胁防护](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**： Azure SQL 数据库通过使用传输层安全性对活动中的数据进行加密来保护数据。 对于所有连接，SQL Server 都将对 SSL/TLS) 始终强制执行加密 (。 这样可以确保在客户端与服务器之间传输的所有数据经过加密，而不管连接字符串中的 Encrypt 或 TrustServerCertificate 设置如何。

* [了解传输中的 Azure SQL 加密](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：使用 AZURE Synapse SQL 数据发现 &amp; 分类功能。 数据发现 &amp; 分类提供 AZURE SQL 数据库中内置的高级功能，用于发现、分类、 &amp; 保护数据库中的敏感数据。

数据发现 &amp; 分类是高级数据安全产品/服务的一部分，它是一个用于高级 SQL 安全功能的统一包。 数据发现 &amp; 分类可以通过中央 SQL ADS 门户进行访问和管理。

此外，还可以在 Azure 门户中 (DDM) 策略设置动态数据掩码。 DDM 建议引擎会将数据库中的某些字段标记为可能的敏感字段，这可能是掩蔽的候选项。

* [如何使用 Azure SQL Server 的数据发现和分类](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [了解 Azure Synapse SQL 的动态数据掩码](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指导**：使用 azure RBAC)  (azure 基于角色的访问控制来管理对 Synapse SQL 池中的 Azure SQL 数据库的访问。

授权是由用户帐户的数据库角色成员身份和对象级权限控制。 作为最佳实践，应向用户授予所需的最低权限。

* [如何将 Azure SQL Server 与 Azure Active Directory 进行身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [如何在 Azure SQL Server 中控制访问权限](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [了解 Azure SQL 中的授权和身份验证](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用;Microsoft 管理 Azure Synapse SQL 的底层基础结构，并实施了严格控制以防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：透明数据加密 (TDE) 通过加密静态数据，帮助保护 AZURE Synapse SQL 免受恶意脱机活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。 在 Azure 中，TDE 的默认设置是 DEK 受内置服务器证书保护。 另外，还可以使用客户托管的 TDE （也称为创建自己的密钥 (BYOK) 对 TDE 的支持。 在此方案中，用于加密 DEK 的 TDE 保护器是客户管理的非对称密钥，该密钥存储在客户自有且自行管理的 Azure Key Vault（Azure 的基于云的外部密钥管理系统）中，并且永远不会离开该密钥保管库。

* [了解服务托管的透明数据加密](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [了解客户管理的透明数据加密](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [如何使用自己的密钥打开 TDE](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建有关 Synapse SQL 池和其他关键或相关资源的生产实例发生更改的警报。

此外，还可以使用 Azure 门户为 SQL Synapse 池中的数据库设置警报。 当某些指标（例如数据库大小或 CPU 使用率）达到阈值时，警报可以向你发送电子邮件或调用 Webhook。

* [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何为 Azure SQL Synapse 创建警报](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：若要在 azure SQL 数据库上执行漏洞评估，请启用高级数据安全并遵循 Azure 安全中心提供的建议。

* [如何对 Azure SQL 数据库运行漏洞评估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [如何启用高级数据安全性](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何实现 Azure 安全中心漏洞评估建议](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：漏洞评估是内置于 AZURE Synapse SQL 中的扫描服务。 该服务采用一个可以标记安全漏洞的规则知识库。 它会重点列出违背最佳做法的情况，例如配置不当、权限过度分配以及敏感数据未受保护。 可以通过核心) 门户中的 SQL 高级数据安全 (广告访问和管理漏洞评估。

* [在 SQL ADS 门户中管理和导出漏洞评估扫描](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

数据发现 &amp; 分类内置于 Azure SYNAPSE SQL 中。 它提供用于发现、分类、标记和报告数据库中的敏感数据的高级功能。

* [了解 Azure 安全中心安全功能分数](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [了解数据发现 &amp; 分类](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动资产发现解决方案

**指南**：使用 Azure 资源关系图在)  (中查询和发现与订阅中的 Synapse SQL 池相关的所有资源。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

尽管可以通过 Azure Resource Graph 发现经典 Azure 资源，但我们强烈建议今后创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看你的 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义和维护已批准的 Azure 资源的清单

**指南**：定义与 Synapse SQL 池相关的已批准 Azure 资源的列表。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure 资源关系图查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略对可在客户)  (订阅中创建的资源类型（使用以下内置策略定义）施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：不适用;此建议适用于在计算资源上运行的应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：对于业务操作所需的与 Synapse SQL 池相关的任何资源，但可能会对组织造成更高的风险，应在其自己的虚拟机和/或虚拟网络中隔离，并使用 Azure 防火墙或网络安全组进行充分的保护。

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 "sql-dmo" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行与 Synapse Sql 池相关的资源的配置。 你还可以使用 Azure 数据库/服务器的内置策略定义，例如：
- 在 SQL 服务器上部署威胁检测
- SQL Server 应使用虚拟网络服务终结点

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：不适用;Azure Synapse SQL 没有可配置的安全设置。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：利用 Azure 安全中心对任何与 Synapse SQL 池相关的资源执行基线扫描。

* [如何在 Azure 安全中心修正建议](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：透明数据加密 (TDE) 与客户托管密钥一起使用 Azure Key Vault 允许使用称为 TDE 保护器的客户托管非对称密钥 (DEK) 加密自动生成的数据库加密密钥。 这通常也称为透明数据加密的创建自己的密匙 (BYOK) 支持。 在 BYOK 方案中，TDE 保护程序存储在客户拥有和管理的 Azure Key Vault 中。 此外，请确保在 Azure Key Vault 中启用软删除。

* [如何使用 Azure Key Vault 中的客户托管密钥启用 TDE](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [如何在 Azure Key Vault 中启用软删除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：使用托管标识，通过 AZURE ACTIVE DIRECTORY (AD) 中的自动托管标识提供 Azure 服务。 通过托管标识，你可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Azure Key Vault，而无需在代码中包含任何凭据。

* [教程：使用 Windows VM 系统分配托管标识访问 Azure SQL](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [如何配置托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：通过实现凭据扫描器来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。 Microsoft 处理底层平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，AZURE Synapse SQL) ;但是，它不会在客户内容上运行。

预先扫描要上传到非计算 Azure 资源的任何内容，例如应用服务、Data Lake Storage、Blob 存储、Azure SQL Server 等。Microsoft 无法访问这些实例中的数据。

* [了解适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。 Microsoft 处理底层平台的反恶意软件。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期自动备份

**指南**：在一整天内会自动创建 Synapse SQL 池的快照，创建7天可用的还原点。 无法更改此保留期。 SQL 池支持八小时恢复点目标 (RPO)。 可以根据过去七天捕获的任意一个快照，还原主要区域中的数据仓库。 请注意，如果需要，还可以手动触发快照。

* [Azure Synapse SQL 池中的备份和还原](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：数据仓库的快照在一整天内会自动创建，创建的还原点可用于七天。 无法更改此保留期。 SQL 池支持八小时恢复点目标 (RPO)。 可以根据过去七天捕获的任意一个快照，还原主要区域中的数据仓库。 请注意，如果需要，还可以手动触发快照。

如果使用客户管理的密钥来加密数据库加密密钥，请确保密钥正在备份。

* [Azure Synapse SQL 池中的备份和还原](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [如何备份 Azure Key Vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：定期测试还原点以确保快照有效。 若要从还原点还原现有的 SQL 池，可以使用 Azure 门户或 PowerShell。 测试已备份客户托管密钥的还原。

* [如何还原 Azure Key Vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Azure Synapse SQL 池中的备份和还原](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [如何还原现有的 SQL 池](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 Azure SQL 数据库中，可以使用 (LTR) 的长期备份保留策略来配置单个或共用数据库，以将数据库备份自动保留在单独的 Azure Blob 存储容器中，最长可达10年。 Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。

默认情况下，存储帐户中的数据使用 Microsoft 托管的密钥进行加密。 可以依赖于使用 Microsoft 托管的密钥来加密数据，也可以使用你自己的密钥来管理加密。 如果你正在用 Key Vault 管理自己的密钥，请确保已启用软删除。

* [管理 Azure SQL 数据库长期备份保留](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [如何在 Key Vault 中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：空值

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：确保在书面的事件响应计划中定义人员职责，以及事件处理/管理的各个阶段。

* [如何在 Azure 安全中心配置工作流自动化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心将为警报分配严重性来帮助你确定每条警报的处理优先顺序，以便在资源泄密时可以立即采取措施。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

* [Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期练习以测试系统的事件响应能力。 识别弱点和差距，并根据需要修改计划。

* [可以参考 NIST 发布：针对 IT 计划和功能的测试、培训和运用计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：[请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [在以下网页中可以找到有关 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试的详细信息](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
