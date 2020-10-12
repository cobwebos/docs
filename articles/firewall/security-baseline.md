---
title: 适用于 Azure 防火墙的 Azure 安全基线
description: Azure 防火墙安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24c9e5137e59fbd14a489b4d41709f39aa448ed8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231344"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>适用于 Azure 防火墙的 Azure 安全基线

此安全基线将指南从 [Azure 安全基准](../security/benchmarks/overview.md)应用到 Azure 防火墙。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容分组为由 Azure 安全基准和适用于 Azure 防火墙的相关指南定义的“安全控件”。 排除了不适用于 Azure 防火墙的“控件”。 若要查看 Azure 防火墙如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 防火墙安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](/azure/security/benchmarks/security-control-network-security)。

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：Azure 防火墙与 Azure Monitor 集成，用于记录防火墙处理的流量。

此外，使用 Azure 安全中心并遵循网络保护建议来帮助保护与 Azure 防火墙相关的网络资源。

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：启用基于威胁情报的筛选，以提醒和拒绝来自/到达已知恶意 IP 地址和域的流量。 可以为防火墙启用基于威胁情报的筛选，以提醒和拒绝来自/到达已知恶意 IP 地址和域的流量。

- [基于 Azure 防火墙威胁智能的筛选](threat-intel.md)

- [了解 Azure 安全中心集成的威胁情报](/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：在 Azure 防火墙中，服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。

Azure 防火墙服务标记可用在“网络规则目标”字段中，并可对 Azure 防火墙定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。

此外，客户定义的标记（例如 IP 组）也受支持，并可在网络规则或应用程序规则中使用。 支持应用程序规则中的 FQDN 标记，以允许所需出站网络流量通过防火墙。

请注意，无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

 

- [Azure 防火墙服务标记](service-tags.md)

- [可用服务标记](../virtual-network/service-tags-overview.md#available-service-tags)

- [Azure 防火墙中的 IP 组](ip-groups.md)

- [FQDN 标记概述](fqdn-tags.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：Azure 防火墙尚不完全支持 Azure Policy。 Azure 防火墙管理器可用于实现安全配置的标准化。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控制措施和策略），来简化大规模的 Azure 部署。 可将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视资源配置，并检测 Azure 防火墙资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

- [监视 Azure 防火墙日志和指标](/azure/firewall/tutorial-diagnostics)

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md) 

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 azure 防火墙的 azure 资源的时间源。 客户需要创建一个网络规则来允许此访问，或者为你在他们的环境中使用的时间服务器创建一个网络规则。

- [NTP 服务器访问](protect-windows-virtual-desktop.md#additional-considerations)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：可以启用日志数据并将其载入 Azure Sentinel 或第三方 SIEM，以对各种日志进行集中安全日志管理。

活动日志可用于审核 Azure 防火墙上的操作，并监视对资源的操作。 活动日志包含资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

Azure 防火墙还提供以下诊断日志，以提供有关客户应用程序和网络规则的信息。

应用程序规则日志：每当建立与某个配置的应用程序规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。

网络规则日志：每当建立与某个配置的网络规则匹配的新连接，就会为接受/拒绝的连接生成一条日志

注意：仅当针对环境中的每个 Azure 防火墙启用时，才会将这两种日志保存到存储帐户、流式传输到事件中心和/或发送到 Azure Monitor 日志。

- [Azure 防火墙日志](logs-and-metrics.md)

活动日志中的资源操作列表：Azure 资源管理器资源提供程序操作

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：活动日志可用于审核 Azure 防火墙上的操作，并监视对资源的操作。 活动日志包含 Azure 资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 Azure 防火墙还提供以下诊断日志，以提供有关客户应用程序和网络规则的信息。 

应用程序规则日志：每当建立与某个配置的应用程序规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。

网络规则日志：每当建立与某个配置的网络规则匹配的新连接，就会为接受/拒绝的连接生成一条日志

请注意，仅当针对环境中的每个 Azure 防火墙启用时，才会将这两种日志保存到存储帐户、流式传输到事件中心和/或发送到 Azure Monitor 日志。

- [Azure 防火墙日志](logs-and-metrics.md)

- [活动日志中的资源操作列表](../role-based-access-control/resource-provider-operations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，可根据组织的合规性规则设置 Log Analytics 工作区保持期。 对于所有工作区，数据保留可配置为 30 到 730 天（2 年），具体取决于所选的定价层。

有三个选项可用于存储日志存储保留：

如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。

若要集成其他安全信息和事件管理 (SEIM) 工具，获取资源警报，则事件中心是很好的选择。

Azure Monitor 日志最适合用于应用程序常规实时监视或查看趋势。

- [Azure 防火墙日志和指标](logs-and-metrics.md)

- [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md)

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：Azure 防火墙与 Azure Monitor 集成，可用于查看和分析防火墙日志。 日志可发送到 Log Analytics、Azure 存储或事件中心。 它们可在 Log Analytics 中进行分析，也可通过 Excel 和 Power BI 等不同工具进行分析。 Azure 防火墙日志有几种不同类型。

活动日志可用于审核 Azure 防火墙上的操作，并监视对资源的操作。 活动日志包含资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

Azure 防火墙还提供诊断日志，以提供有关客户应用程序和网络规则的信息。

当每个与你配置的一个应用程序规则匹配的新连接都生成一个已接受/已拒绝连接的日志时，就会创建应用程序规则日志。 

当每个与你配置的一个网络规则匹配的新连接都生成一个已接受/已拒绝连接的日志时，就会创建网络规则日志

请注意，仅当针对环境中的每个 Azure 防火墙启用它时，才会将这两种日志保存到存储帐户、流式传输到事件中心和/或发送到 Azure Monitor 日志。

Azure Monitor 日志可用于对应用程序进行常规实时监视或查看趋势。

- [Azure 防火墙日志和指标](logs-and-metrics.md)

- [诊断日志](logs-and-metrics.md#diagnostic-logs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：使用 Azure 安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。 

或者，可以启用数据并将其载入 Azure Sentinel。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：Azure AD 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

还可以通过使用 Microsoft 服务的 Azure AD Privileged Identity Management 特权角色和 Azure 资源管理器来启用实时/足够访问权限。

- [详细了解 Privileged Identity Management](/azure/active-directory/privileged-identity-management)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：请尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用配置了多重身份验证 (MFA) 的 PAW（特权访问工作站）来登录和配置 Azure 防火墙和相关资源。 

- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告在环境中发生可疑活动或不安全的活动时生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。 

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。 

- [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。 

你可以为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区以简化上述过程。 可在 Log Analytics 工作区中配置所需的警报。 

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：可使用 Azure AD 风险和标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。 

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记可帮助跟踪存储或处理敏感信息的 Azure 防火墙和相关资源。 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅和管理组对各个安全域（如环境类型和数据敏感度级别）实现隔离。 你可以限制对应用程序和企业环境所需 Azure 防火墙资源的访问级别。 可以通过 Active Directory 基于角色的访问控制来控制对 Azure 资源的访问。 

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](../governance/management-groups/create.md)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：利用 Azure 市场中有关网络外围的第三方解决方案，该解决方案可监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。 

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure 防火墙和相关资源的任何客户端能够协商 TLS 1.2 或更高版本。 

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。 

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：使用第三方活动发现工具，通过 Azure 防火墙和相关资源来识别存储在 Azure 资源中的所有敏感信息，并更新组织的敏感信息清单。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制对 Azure 防火墙和相关资源的访问。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：使用 Azure 防火墙和相关资源对所有 Azure 资源进行静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，你可以选择管理自己的密钥。 

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

- [如何配置客户管理的加密密钥](../storage/common/storage-encryption-keys-portal.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure 防火墙发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用到提供元数据的 Azure 防火墙和相关资源，以便按逻辑将其组织到分类中。 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪 Azure 防火墙和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](../governance/management-groups/create.md)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指南**：根据组织需求创建已批准的 Azure 防火墙资源（包括配置）的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 可查询/发现订阅中的 Azure 防火墙资源。 确保环境中的所有 Azure 防火墙和相关资源均已获得批准。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：实现自己的过程，用于删除未经授权的 Azure 防火墙和相关资源。 还可以使用第三方解决方案来识别未获批准的 Azure 防火墙和相关资源

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：使用 Azure Policy 限制可在环境中预配的服务。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，使用 Azure 条件访问限制用户与 Azure 资源管理器的交互能力。 

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：对于业务运营可能需要的应用程序或组织风险配置文件不同的环境，应通过单独的 Azure 防火墙实例进行隔离和分隔。

- [使用 Azure 门户部署和配置 Azure 防火墙](deploy-cli.md)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

目前不完全支持 Azure Policy。 

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy [拒绝]和[不存在时部署]在 Azure 防火墙和相关资源中强制实施安全设置。  此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 防火墙和相关资源的安全配置。

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略和 Azure 资源管理器模板。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：使用 Azure Policy 为 Azure 防火墙和相关资源定义和实现标准安全配置。 使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 防火墙资源的网络配置。 还可以使用与特定资源相关的内置策略定义。  

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 通过托管标识，你可以对支持 Azure 资源管理器的 Azure AD 身份验证并且可以与 API/Azure 门户/CLI/PowerShell 一起使用的任何服务进行验证。

- [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：使用 Azure 资源管理器导出 JavaScript 对象表示法 (JSON) 模板中的 Azure 防火墙和相关资源，该模板可用作 Azure 防火墙和相关配置的备份。  还可以从 Azure 门户使用 Azure 防火墙的“导出模板”功能导出 Azure 防火墙配置。  使用 Azure 自动化自动运行备份脚本。

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [使用模板部署 Azure 防火墙](deploy-template.md)

- [Microsoft 网络 Azure 防火墙模板参考](/azure/templates/microsoft.network/azurefirewalls)

- [关于 Azure 自动化](../automation/automation-intro.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用 Azure 资源管理器导出 JavaScript 对象表示法 (JSON) 模板中的 Azure 防火墙和相关资源，该模板可用作 Azure 防火墙和相关配置的备份。  还可以从 Azure 门户使用 Azure 防火墙的“导出模板”功能导出 Azure 防火墙配置。

- [使用模板部署 Azure 防火墙](deploy-template.md)

- [Microsoft 网络 Azure 防火墙模板参考](/azure/templates/microsoft.network/azurefirewalls)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：确保能够使用 Azure 资源管理器模板备份的文件定期执行恢复操作。  

- [使用模板部署 Azure 防火墙](deploy-template.md)

- [Microsoft 网络 Azure 防火墙模板参考](/azure/templates/microsoft.network/azurefirewalls)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板。 若要保护在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 

此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。 

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md) 

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。 

- [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。

使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](../security-center/continuous-export.md)

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。 

- [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
