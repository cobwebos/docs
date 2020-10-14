---
title: Azure Web 应用程序防火墙的 azure 安全基线
description: Azure Web 应用程序防火墙安全基准为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: edb184fa286eb6212f714c18830540e105ea3305
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92021171"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure Web 应用程序防火墙的 azure 安全基线

此安全基线将 [Azure 安全性基准1.0 版](../security/benchmarks/overview-v1.md) 中的指南应用于 Azure Web 应用程序防火墙。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容由 Azure 安全基准定义的 **安全控制** 和适用于 Azure Web 应用程序防火墙的相关指南进行分组。 排除了不适用于 Azure Web 应用程序防火墙的**控件**。 

若要查看 Azure Web 应用程序防火墙如何完全映射到 Azure 安全基准，请参阅 [完整的 Azure Web 应用程序防火墙安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：使用 Microsoft Azure Web 应用程序防火墙 (WAF) 对 web 应用程序进行集中保护，使其免受常见攻击和漏洞（如 SQL 注入和跨站点脚本）的影响。 

- 检测模式：使用此模式来了解网络流量、了解和检查误报。 监视并记录所有威胁警报。 请确保已选择 "诊断和 WAF 日志"，并已打开。 请注意，当传入请求处于检测模式下运行时，WAF 不会阻止这些请求。
- 防护模式：阻止规则检测到的入侵和攻击。 攻击者收到 "403 未经授权的访问" 异常，连接被关闭。 阻止模式会在 WAF 日志中记录此类攻击。

用 WAF 的检测模式为你的网络流量基线。 确定流量需求后，将 WAF 配置为防止不需要的流量。

跟进安全中心的高严重性建议，以获取任何不受 WAF 保护的启用 web 的资源。  

- [Web 应用程序防火墙 CRS 规则组和规则](ag/application-gateway-crs-rulegroups-rules.md) 

- [应用程序网关上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [前门上的 WAF 模式](afds/afds-overview.md#waf-modes)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：将自定义规则用于 Azure Web 应用程序防火墙 (WAF) 以允许和阻止流量。 例如，来自某个 IP 地址范围的所有流量都可以被阻止。 将 Azure WAF 配置为在阻止模式下运行，这会阻止规则检测到的入侵和攻击。 攻击者收到 "403 未经授权的访问" 异常，连接被关闭。 阻止模式会在 WAF 日志中记录此类攻击。

- [应用程序网关上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [前门上的 WAF 模式](afds/afds-overview.md#waf-modes)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**： Azure Web 应用程序防火墙 (WAF) 是 azure web 应用程序保护的核心组件。 使用 Azure WAF 为 web 应用程序提供集中式保护，使其免受来自 OWASP 排名前10类的已知攻击签名的常见攻击和漏洞的影响。

自定义 Azure WAF 与规则和规则组，以满足 web 应用程序要求并消除误报。 为 WAF 后面的每个站点关联 Azure WAF 策略，以允许进行特定于站点的配置。 Azure WAF 支持异地筛选、速率限制、Azure 托管的默认规则集规则。 可以创建和自定义规则来满足应用程序的需求。 

在确定的时间段后，将 Azure WAF 配置为在检测模式下将网络流量设置为基线后在防护模式下运行。 Azure WAF 阻止阻止模式下的规则检测到的入侵和攻击。 攻击者收到 "403 未经授权的访问" 异常，连接被关闭。 阻止模式会在 WAF 日志中记录此类攻击。

- [应用程序网关上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [前门上的 WAF 模式](afds/afds-overview.md#waf-modes)

- [Web 应用程序防火墙 CRS 规则组和规则](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用标记创建、关联和逻辑地组织 Azure 订阅中的资源，以检测常见的应用程序配置错误 (例如 APACHE 和 IIS) 。 

基于应用的标记元数据将规则和规则组应用于 Azure Web 应用程序防火墙 (WAF) 策略。

- [应用程序网关上的 WAF 策略](https://docs.microsoft.com/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [前门上的 WAF 策略](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与 Azure Web 应用程序防火墙关联的网络安全组使用标记 (Azure 应用程序网关子网中的 WAF) ，以及与网络安全和通信流相关的任何其他资源。 对于单独的网络安全组规则，请使用 "说明" 字段来指定任何允许进出网络流量的规则的业务需求、持续时间等。

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

选择 "Azure PowerShell" 或 "Azure CLI" 以根据其标记对资源进行查找或执行操作。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Web 应用程序防火墙 (WAF) 部署有关的网络设置和资源的更改。 在 Azure Monitor 中创建警报，这些警报将在对关键网络设置或资源进行更改时触发。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：为 Azure Web 应用程序防火墙创建网络规则 (WAF) ，以允许使用适当的端口和协议（如端口 123 over UDP）访问 NTP 服务器。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：配置 Azure Web 应用程序防火墙 () WAF 要发送到 SIEM 安全日志管理解决方案（如 Azure Sentinel）或第三方的日志。 这些日志包括 Azure 活动、诊断和实时 WAF 日志，然后可以在不同的工具（例如 Azure Monitor、Excel 和 Power BI）中查看这些日志。 通过 azure Web 应用程序防火墙日志，可了解 Azure WAF 评估、匹配和阻止的数据。

Azure Sentinel 包含一个内置的 Azure WAF 工作簿，该工作簿提供 Azure WAF 上的安全事件概述。 此工作簿包含事件、匹配和阻止规则，以及记录到防火墙日志中的其他所有内容。 此遥测可用于启动操作手册自动化，以根据 Sentinel 收集的 WAF 事件来通知或采取更正措施。

- [查看活动日志](../azure-resource-manager/management/view-activity-logs.md)

- [应用程序网关的诊断日志](../application-gateway/application-gateway-diagnostics.md)

- [将数据从 Microsoft web 应用程序防火墙连接到 Azure Sentinel](/azure/sentinel/connect-microsoft-waf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：在 Azure Web 应用程序防火墙上启用日志记录 (WAF) 资源以访问审核、安全和诊断日志。 Azure Web 应用程序防火墙提供已配置的诊断日志中提供的每个检测到的威胁的详细报告。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

- [日志记录概述](ag/ag-overview.md#logging)

- [Azure Monitor 日志查询概述](../azure-monitor/log-query/log-query-overview.md)

- [Azure 平台日志概述](../azure-monitor/platform/platform-logs-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：将 Azure Web 应用程序防火墙 (WAF) 日志发送到自定义存储帐户，并定义保留策略。 根据组织的符合性要求，使用 Azure Monitor 设置 Log Analytics 工作区保持期。
- [为存储帐户配置监视](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**： Azure Web 应用程序防火墙 (WAF) 提供有关检测到的每个威胁的详细报告。 日志记录与 Azure 诊断日志集成在一起，提供有关操作和错误的信息，这些信息对审核和故障排除非常重要。 

Azure WAF 实例与安全中心集成，以发送警报和报告的健康信息。 使用安全中心的建议来检测未受保护的 web 应用程序并保护这些易受攻击的资源。 

Azure Sentinel 包含一个内置的 WAF 事件工作簿，该工作簿概述了 WAF 上的安全事件。 这些规则包括事件、匹配和阻止规则，以及记录到防火墙日志中的其他所有内容。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor) 

- [如何 Azure 应用程序网关启用诊断设置](../application-gateway/application-gateway-diagnostics.md)

- [在 Azure 前门中监视指标和日志](../frontdoor/front-door-diagnostics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：启用 Azure 活动日志诊断设置和 azure WAF 的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的数据提供许多其他见解。 基于 WAF 指标为异常活动创建警报。 例如，如果阻止的请求数超过 "X"，则 "do"。

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何在 Azure 中创建警报](/azure/azure-monitor/learn/tutorial-response)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 

Azure WAF 通过检查入站 web 流量来阻止攻击（例如 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击），提供对 web 应用程序的集中保护。

- [如何部署 Azure WAF](ag/create-waf-policy-ag.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure Active Directory (Azure AD) 有内置角色是可查询的，且必须显式分配。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**： WAF 中没有可用的本地管理员分配。 但是，在环境中可能有 Azure Active Directory (Azure AD) 管理员角色，从而允许对 Azure WAF 资源进行管理控制。
使用可访问 Azure Web 应用程序防火墙 (WAF) 实例的专用管理帐户时，最好创建标准操作过程。 使用安全中心的标识和访问管理功能来监视管理帐户的数量。

- [了解 Azure 安全中心标识和访问](../security-center/security-center-identity-access.md)

- [了解如何在 Azure Database for PostgreSQL 中创建管理员用户](../postgresql/howto-create-users.md#the-server-admin-account)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (Azure AD) 多重身份验证 (MFA) 并遵循安全中心的标识和访问管理建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用具有多重身份)  (验证的特权访问工作站 (PAW) 配置为登录并配置 Azure Web 应用程序防火墙 (WAF) 和相关资源。 

- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用安全中心来监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：配置条件访问策略的位置条件并管理命名位置。 

创建具有命名位置的 IP 地址范围或国家/地区的逻辑分组。 将敏感资源（如 Azure Key Vault 机密）的访问权限限制为已配置的命名位置。

- [Azure Active Directory 条件访问中的位置条件是什么](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据，还可以 salts、哈希和安全地存储用户凭据。
- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 使用 Azure 标识访问评审来高效地管理组成员身份、对企业应用程序的访问和角色分配。 定期查看用户访问权限，以确保只有活动用户才能继续访问。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：通过任何 SIEM 或监视工具（例如 Azure Sentinel），将 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源。

通过为 Azure Active Directory Azure AD (创建诊断设置来简化此过程) 用户帐户，并将审核日志和登录日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory (Azure AD) 的风险和标识保护功能，配置对检测到的与用户标识相关的可疑操作的自动响应。 将数据引入 Azure Sentinel 以便进一步调查。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪 Azure Web 应用程序防火墙 (WAF) 和存储或处理敏感信息的相关资源。
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：对单个安全域（例如，开发、测试和生产环境）使用单独的订阅和管理组实现隔离。 

使用 Azure Active Directory (Azure AD) 基于角色的访问控制 (Azure RBAC) 来控制对 Azure 资源的访问。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure Web 应用程序防火墙 (WAF) 实例和相关资源的任何客户端都能够协商 TLS 1.2 或更高版本。

遵循安全中心的静态加密和传输中的加密安全中心建议（如果适用）。

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用 Azure Active Directory (控制对 azure 资源的访问 Azure AD) 基于角色的访问控制 (Azure RBAC) 。
- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：将静态加密用于所有 azure 资源，包括 Azure Web 应用程序防火墙 (WAF) 和相关资源。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，你可以选择管理自己的密钥。

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

- [如何配置客户管理的加密密钥](/azure/storage/common/storage-encryption-keys-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：配置 Azure Web 应用程序防火墙 (WAF) 在以预确定的时间为检测模式下的网络流量基线后，在防护模式下运行。 

Azure WAF 在预防模式下，阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 阻止模式会在 WAF 日志中记录此类攻击。

- [应用程序网关和 Azure 安全中心之间的集成概述](../application-gateway/application-gateway-integration-security-center.md#overview)

- [应用程序网关上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [前门上的 WAF 模式](afds/afds-overview.md#waf-modes)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询或发现订阅中的所有资源，例如、计算、存储、网络、端口和协议等。 

确保适当的 (读取租户中的) 权限，并枚举所有 Azure 订阅以及订阅中的资源。 尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：在 Azure Web 应用程序防火墙上使用标记 (WAF) 策略。 标记可以与资源相关联，并以逻辑方式应用，以组织对客户订阅中的这些资源的访问。 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure WAF 和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指南**：根据组织需求创建已批准资源的清单，包括配置。

使用 Azure 策略限制可在订阅中创建的资源类型。 使用 Azure Resource Graph 查询和发现订阅中的资源。 确保环境中存在的所有 Azure 资源都已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。
使用 Azure 资源关系图可查询或发现 Azure Web 应用程序防火墙 (WAF) 其订阅中的资源。 确保环境中存在所有 Azure WAF 和相关资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：使用 azure 策略监视和删除未经批准的 azure WAF 资源，拒绝 azure WAF 的部署或特定类型的 WAF，例如 azure WAF V1 vs V2。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用 Azure Policy 限制可在环境中预配的服务。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：若要限制用户使用 Azure 资源管理器与 Azure 资源管理器进行交互的能力，请访问 "Microsoft Azure 管理" 应用程序的 "阻止访问"。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**： Azure Web 应用程序防火墙 (WAF) 可以通过网络、资源组或订阅与不同环境相关联，以便将高风险应用程序隔离开来。

- [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)

- [使用 Azure 管理组来组织资源](../governance/management-groups/overview.md)

- [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：定义和实现与 Azure Web 应用程序防火墙 (WAF) 部署有关的网络设置的标准安全配置。
使用 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure 应用程序网关、虚拟网络、网络安全组的网络配置，并使用内置策略定义。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 策略 [拒绝] 和 [部署但不存在] 效果在 Azure Web 应用程序防火墙 (WAF) 和相关资源上强制实施安全设置。 

使用 Azure 资源管理器模板来维护你的组织所需的 Azure WAF 和相关资源的安全配置。

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略和 azure 资源管理器模板。 

如果与 Azure DevOps 集成，则授予或拒绝特定用户、内置安全组或 Azure Active Directory 组的权限（如果与 Azure 集成，则授予或拒绝） Azure AD) （如果与 Active Directory 集成，则 Team Foundation Server (TFS (）。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.Network”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 开发用于管理策略异常的进程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 文档](/azure/governance/policy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.Network”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 

使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在]）效果自动强制执行 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 文档](/azure/governance/policy)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：使用 Azure Key Vault 安全地存储证书。 Azure Key Vault 是由平台管理的机密存储，可以用来保证机密、密钥和 SSL 证书的安全。 

Azure 应用程序网关支持与密钥保管库集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 

- [如何使用 Azure PowerShell 通过 Key Vault 证书配置 SSL 终止](../application-gateway/configure-keyvault-ps.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描器来识别代码中的凭据，这些凭据还会鼓励将发现的凭据迁移到更安全的位置，例如 Azure Key Vault。
- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：请确保为 Azure Key Vault 启用软删除。 软删除允许恢复已删除的密钥保管库和保管库对象，例如密钥、机密和证书。

- [如何使用 Azure Key Vault 软删除](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心为每个警报分配一个严重性，以帮助确定应该首先调查的警报的优先级。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。
清楚地标记订阅 (例如，生产、非生产) 并创建命名系统，以明确标识和分类 Azure 资源。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。
- [有关 IT 计划和功能的测试、培训和试验计划，请参阅 NIST 的发布指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。
- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 根据组织的要求，使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：若要在安全警报和建议上通过 "逻辑应用" 自动触发响应，请使用安全中心中的工作流自动化功能。
- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
