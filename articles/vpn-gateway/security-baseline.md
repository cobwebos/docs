---
title: VPN 网关的 Azure 安全基线
description: VPN 网关安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7604e8519e7ae8db255a0e033ca3df0bb941a845
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626252"
---
# <a name="azure-security-baseline-for-vpn-gateway"></a>VPN 网关的 Azure 安全基线

此安全基线将 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md) 中的指南应用到 VPN 网关。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的 **安全控制措施** 进行分组，以及适用于 VPN 网关的相关指南。 排除了不适用于 VPN 网关的**控件**。

若要查看 VPN 网关如何完全映射到 Azure 安全基准，请参阅 [完整的 Vpn 网关安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：使用 VPN 网关子网时，避免将网络安全组 (NSG) 关联到网关子网。 将网络安全组与此子网关联可能会导致 VPN 网关停止按预期方式工作。  但是，为虚拟网络中的其他非 VPN 网关子网启用网络安全组。

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md) 

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md) 

- [使用 Azure 门户创建基于路由的 VPN 网关](create-routebased-vpn-gateway-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：使用 Azure 安全中心并按照网络保护建议来帮助保护 Azure 中的网络资源。 

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：在网关上或特定连接上启用 VPN 网关数据包捕获，具体取决于你的要求。

- [为 VPN 网关配置数据包捕获](packet-capture.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现 Azure 策略的网络资源的标准安全配置。

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 分配和 Azure 策略分配）来简化大规模的 Azure 部署。 可以将蓝图应用到新的或现有的订阅，并通过版本控制来微调控制和管理。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network) 

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视资源配置，并检测对虚拟网络资源所做的更改。 在 Azure Monitor 中创建警报，该警报将在与 VPN 网关相关的关键资源发生更改时触发。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入活动和诊断日志来聚合网络资源（如 VPN 网关资源）生成的安全数据。 使用 Azure Monitor 查询和执行日志数据分析，将 Azure 存储帐户用于这些日志的长期/存档存储。 

或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

- [针对来自 VPN 网关的诊断日志事件设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对 VPN 网关资源启用诊断设置，以便访问审核、安全和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。 

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性法规设置 Log Analytics 工作区保持期。 使用 Azure 存储帐户进行长期和存档存储。 

- [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 和 Log Analytics 工作区查看日志并对日志数据执行查询。 

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Log Analytics 查询入门](../azure-monitor/log-query/get-started-portal.md) 

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：将 Azure 安全中心与 Log Analytics 工作区结合使用，以便在安全日志和事件中发现异常活动时进行监视和警报。

或者，你可以将和机载数据启用到 Azure Sentinel。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：按照组织的要求，从 Azure MARKETPLACE 实现 DNS 日志记录解决方案的第三方解决方案。

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure 基于角色的访问控制 (RBAC) 使你可以通过角色分配管理对 Azure 资源的访问权限。 你可以将这些角色分配给用户、组服务主体和托管标识。 某些资源有预定义的内置角色，可以通过 Azure CLI、Azure PowerShell 或 Azure 门户等工具来清点或查询这些角色。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 

你还可以使用 Azure AD Privileged Identity Management 和 Azure 资源管理器启用实时访问。 

- [详细了解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：请尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问建议。 

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure AD MFA，并遵循 Azure 安全中心的标识和访问建议。 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指导**：对于需要提升的权限的管理任务，请使用安全的 Azure 托管工作站（也称为特权访问工作站，简称 PAW）。

- [了解安全的 Azure 托管工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何启用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告和监视来检测环境中何时发生可疑活动或不安全的活动。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用 Azure AD 命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。 

- [如何配置 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure AD 标识和访问评审来有效管理组成员身份、对企业应用程序的访问以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，因此可以与任何 SIEM/监视工具集成。 

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。 

- [如何将 Azure 活动日志与 Azure Monitor 集成](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure AD 标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。 

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**： VPN 网关为每个客户虚拟网络提供专用的 VM 实例。 为单独的安全域（如环境类型和数据敏感度级别）使用单独的虚拟网络、订阅和管理组实现隔离。 你可以限制对应用程序和企业环境所需 Azure 资源的访问级别。 可以通过 Active Directory 基于角色的访问控制来控制对 Azure 资源的访问。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：利用 Azure 市场中有关网络外围的第三方解决方案，监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。 

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。 

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**： VPN 网关在 Azure VPN 网关和客户本地 VPN 设备之间加密客户数据包 (S2S) 或 VPN 客户端 (P2S) 。 VPN 网关还支持 VNet 到 VNet 加密。

遵循 Azure 安全中心的静态加密和传输中加密的建议，获取虚拟网络中适用的资源。

- [关于 VPN 类型](vpn-gateway-about-vpn-gateway-settings.md#vpntype)

- [关于用于站点到站点 VPN 网关连接的 VPN 设备和 IPsec/IKE 参数](vpn-gateway-about-vpn-devices.md#ipsec)

- [关于加密要求和 Azure VPN 网关](vpn-gateway-about-compliance-crypto.md)

- [为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：使用第三方活动发现工具来确定你的技术系统存储、处理或传输的所有敏感信息，包括位于现场或远程服务提供商的信息，以及更新组织的敏感信息清单。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用 Azure AD RBAC 来控制对数据和资源的访问，否则使用服务特定的访问控制方法。 使用内置的基于角色的访问控制角色，如 "所有者"、"参与者" 或 "网络参与者"，然后将该角色分配给相应的作用域。 通过创建自定义角色并将虚拟网络、子网、VPN 网关、网络接口、网络安全组和路由表的特定权限分配给角色，为虚拟网络功能的子集分配特定权限。

- [如何在 Azure 中配置 RBAC](../role-based-access-control/role-assignments-portal.md)

- [计划虚拟网络](../virtual-network/virtual-network-vnet-plan-design-arm.md#permissions)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：配置 Azure Monitor 警报，以便在 azure 活动日志发生更改时触发你的 azure 活动日志，如 VPN 网关。 

- [基于 VPN 网关指标设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)

- [针对来自 VPN 网关的诊断日志事件设置警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：使用常见的风险评分计划 (例如，常见漏洞评分系统) 或第三方扫描工具提供的默认风险评级。

- [NIST 发布-常见漏洞计分系统](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现与你的订阅中的 VPN 网关相关的所有资源。 确保你具有适当的 (读取租户中的) 权限，并且能够枚举订阅中的所有资源。 此外，还可以使用 Azure CLI 枚举 VPN 网关资源。

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [VPN 网关的 Azure CLI](https://docs.microsoft.com/cli/azure/network/vnet-gateway?view=azure-cli-latest)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用到 VPN 网关资源，以根据定义的分类对它们进行逻辑组织。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适当的位置使用标记、管理组和单独的订阅来组织和跟踪 VPN 网关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。 

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription) 

- [如何创建管理组](/azure/governance/management-groups/create) 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，还可使用 Azure 资源关系图查询和发现订阅中的资源。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：根据组织的安全要求，客户可以根据需要分配 Azure 策略定义来阻止资源的创建或使用。 但是，你必须实现自己的过程，以删除未经批准或未授权的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略将对可在订阅中创建的资源类型的限制设置为使用以下内置策略定义：

- 不允许的资源类型

- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指导**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure AD 条件访问来限制用户与 Azure 资源管理器交互的能力。 

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 azure 策略别名创建自定义策略，以审核或强制执行 Azure 网络资源（包括 VPN 网关）的配置。 你还可以使用内置的 Azure Policy 定义。

Azure 资源管理器能够以 Java Script Object 符号 (JSON) 导出模板，应查看该模板以确保配置满足或超过组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 资源管理器模板和 azure 策略分配来安全地配置与 VPN 网关和相关资源关联的 Azure 资源。 Azure 资源管理器模板是基于 JSON 的文件，用于将虚拟机和 Azure 资源一起部署，并且需要维护自定义模板。 Microsoft 对基本模板进行维护。  在 [拒绝] 和 [部署（如果不存在]）模式下使用 Azure 策略，跨 Azure 资源强制实施安全设置。

- [有关创建 Azure 资源管理器模板的信息](../virtual-machines/windows/ps-template.md)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

- [适用于虚拟网络的 Azure 资源管理器模板示例](../virtual-network/template-samples.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略定义、azure 资源管理器模板和所需状态配置脚本。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。 

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure 策略别名创建自定义策略，以审核或强制执行 Azure 资源的配置。 你还可以使用与特定资源相关的内置策略定义。 此外，还可以使用 Azure 自动化来部署配置更改。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：分配 Azure 策略定义来度量与你的 VPN 网关资源相关的资源配置。 使用 Azure Policy insights 审核资源配置，并在发生关键配置更改时发出警报。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [适用于虚拟网络的 Azure Policy 内置示例](/azure/virtual-network/policy-samples)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**： VPN 网关服务在内部以加密形式存储和传输客户预共享密钥和证书。 客户需要在其自己的系统中保护预共享密钥或证书。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**： AZURE P2S VPN 支持以下三种身份验证方法：

- 基于证书
- RADIUS
- Azure Active Directory (Azure AD)

建议 Azure AD，因为这样可以利用托管标识。

- [配置租户](openvpn-azure-ad-tenant.md)

- [使用多客户端应用配置租户](openvpn-azure-ad-tenant-multi-app.md)

- [配置多重身份验证](openvpn-azure-ad-mfa.md)

- [配置 VPN 客户端](openvpn-azure-ad-client.md)

- [如何配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：使用 Azure 资源管理器部署 VPN 网关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作备份来还原 VPN 网关资源。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。

- [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)

- [适用于虚拟网络的 Azure 资源管理器模板示例](../virtual-network/template-samples.md)

- [资源组-导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](../automation/automation-intro.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用 Azure 资源管理器部署 VPN 网关资源。 Azure 资源管理器提供导出模板的功能，这些模板可用作备份来还原 VPN 网关和相关资源。 使用 Azure 自动化定期调用 Azure 资源管理器模板导出 API。

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [资源组-导出模板](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自动化简介](../automation/automation-intro.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：如果需要，请确保定期定期将 Azure 资源管理器模板部署到隔离的订阅。

- [使用 ARM 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略定义和 azure 资源管理器模板。 若要保护在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或 (Azure Active Directory 中定义的组的权限（如果与 Azure DevOps 集成） Azure AD) 。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

- [Azure 存储 Blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。 

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md) 

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 查明弱点和差距，并根据需要修改你的响应计划。 

- [NIST 发布内容 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。 

- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议，以便确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。 

- [如何配置连续导出](../security-center/continuous-export.md) 

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心的工作流自动化功能，针对安全警报和建议自动触发响应，以保护 Azure 资源。 

- [如何在安全中心配置工作流自动化](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
