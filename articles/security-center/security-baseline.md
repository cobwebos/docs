---
title: Azure 安全中心安全基线
description: 安全中心安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f41dfa0889b7f0c34486eee5d16362f4c3c7360d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894586"
---
# <a name="azure-security-baseline-for-security-center"></a>Azure 安全中心安全基线

此安全基线将指南从 [Azure 安全基准](../security/benchmarks/overview.md)应用到 Azure 安全中心。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“安全控件”分组，此类控件按适用于 Azure 安全中心的 Azure 安全基准和相关的指南定义。 排除了不适用于 Azure 安全中心的控件。 若要查看 Azure 安全中心如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 安全中心安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：Azure 安全中心是 Azure 的核心产品/服务。 不能将虚拟网络、子网或网络安全组直接关联到安全中心。 如果为计算资源启用了数据收集，然后安全中心通过 Log Analytics 工作区存储它收集的数据，则可以将该工作区配置为使用专用链接通过虚拟网络中的专用终结点访问工作区数据。 此外，如果使用数据收集，则安全中心依赖部署到服务器的 Log Analytics 代理来收集安全数据，并为这些计算资源提供保护。 Log Analytics 代理要求为安全中心的适当操作打开特定的端口和协议。 锁定网络以仅允许这些必需的端口和协议，并仅添加应用程序通过网络安全组运行所需的其他规则。

- [Azure 安全中心中的数据收集](security-center-enable-data-collection.md)

- [使用网络安全组筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

- [使用 Log Analytics 代理的防火墙要求](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [了解 Azure 专用链接](../private-link/private-link-overview.md) 

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：Azure 安全中心产品/服务不直接与虚拟网络集成，但可从使用 Log Analytics 代理配置的服务器（这些服务器已部署在网络上）上收集数据。 配置为将数据发送到安全中心的服务器要求允许特定端口和协议才能进行适当通信。 使用 Azure Policy 为这些网络资源定义并强制实施标准安全配置。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、角色分配和 Azure Policy 分配）来简化大规模的 Azure 部署。 可以将蓝图应用于新的订阅，通过一致且安全的方式部署安全中心配置和相关的网络资源。

- [Azure 安全中心中的数据收集](security-center-enable-data-collection.md)

- [使用 Log Analytics 代理的防火墙要求](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：Azure 安全中心产品/服务不直接与虚拟网络集成，但可从使用 Log Analytics 代理配置的服务器（这些服务器已部署在网络上）上收集数据。 配置为将数据发送到安全中心的服务器要求允许特定端口和协议才能进行适当通信。 使用 Azure Policy 为这些网络资源定义并强制实施标准安全配置。

将资源标记用于网络安全组和其他资源，例如网络上配置为将安全日志发送到 Azure 安全中心的服务器。 对于单独的网络安全组规则，请使用“说明”字段来记录允许流入/流出网络的流量的规则。 

使用标记相关的任何内置 Azure Policy 定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可使用 Azure PowerShell 或 Azure CLI，基于资源的标记查找资源或对其执行操作。 

- [Azure 安全中心中的数据收集](security-center-enable-data-collection.md)

- [使用 Log Analytics 代理的防火墙要求](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags) 

- [如何创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md) 

- [如何使用网络安全组规则筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视资源配置并检测对与 Azure 安全中心相关的网络资源所做的更改。 在 Azure Monitor 中创建警报，以便在关键资源发生更改时通知你。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view) 

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：使用中心 Log Analytics 工作区聚合由 Azure 安全中心及其连接的源生成的安全数据。 

配置安全中心的数据收集，将安全数据和事件从连接的 Azure 计算资源发送到中心 Log Analytics 工作区。 除了数据收集，还可以使用连续导出功能将由安全中心生成的安全警报和建议流式传输到中心 Log Analytics 工作区。 在 Azure Monitor 中，可以查询从安全中心和连接的 Azure 资源生成的安全数据并对其执行分析。 

或者，可以将安全中心生成的数据发送到 Azure Sentinel 或第三方 SIEM。

- [导出安全警报和建议](continuous-export.md)

- [Azure 安全中心中的数据收集](security-center-enable-data-collection.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](../azure-monitor/learn/quick-collect-azurevm.md)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：Azure Monitor 活动日志会自动提供，这些日志包含针对资源（如 Azure 安全中心）的所有写入操作，包括执行的操作、启动操作的人员以及发生时间。 将 Azure 活动日志发送到 Log Analytics 工作区以合并日志并延长保留期。

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md) 

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

- [将活动日志发送到 Log Analytics 工作区](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期存储和存档存储。 

- [更改 Log Analytics 中的数据保留期](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [如何为 Azure 存储帐户日志配置保留策略](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视由 Azure 安全中心及其连接的源生成的日志中是否存在异常行为，并定期查看结果。 使用 Azure Monitor 和 Log Analytics 工作区查看日志并对日志数据执行查询。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Log Analytics 查询入门](../azure-monitor/log-query/get-started-portal.md) 

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：配置 Azure Monitor 日志警报，查询由活动日志或 Azure 安全中心生成的数据所记录的无用或异常活动。 设置操作组，以便在针对异常活动发起日志警报时通知组织，使其可以采取措施。 安全中心工作流自动化功能用于触发关于安全警报和建议的逻辑应用。 安全中心工作流可用于通知用户，以便他们能够根据警报信息对事件进行响应，或采取措施以更正资源。

另外，还可以启用 Azure 安全中心与 azure Sentinel 相关的相关数据，并将这些数据提供给 azure。 Azure Sentinel 支持行动手册，允许对安全相关问题的自动威胁做出响应。

- [Azure 安全中心工作流自动化](workflow-automation.md)

- [如何在 Azure 安全中心管理警报](security-center-managing-and-responding-alerts.md) 

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

- [在 Azure Sentinel 中设置自动威胁响应](../sentinel/tutorial-respond-threats-playbook.md)

- [Azure Monitor 中的日志警报](../azure-monitor/platform/alerts-unified-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：借助基于 Azure 角色的访问控制 (Azure RBAC)，可以通过角色分配管理对 Azure 资源的访问。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 Azure 安全中心具有“安全读取者”或“安全管理员”内置角色，这些角色允许用户读取或更新安全策略以及关闭警报和建议。

- [Azure 安全中心中的权限](security-center-permissions.md)

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：创建有关使用专用于 Azure 平台或特定于 Azure 安全中心产品/服务的管理帐户的标准操作过程。 使用 Azure 安全中心标识和访问管理来监视 Azure Active Directory 中管理帐户的数量。 安全中心还具有“安全管理员”内置角色，该角色允许用户更新安全策略以及关闭警报和建议，确保定期评审和协调具有此角色分配的所有用户。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

- [Azure 安全中心中的权限](security-center-permissions.md)

- [如何使用 Azure 安全中心监视标识和访问（预览）](security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：请尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问建议。

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory MFA 以访问 Azure 安全中心和 Azure 门户，并遵循任何安全中心标识和访问建议。 

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 安全中心监视标识和访问](security-center-identity-access.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：对于需要提升的权限的管理任务，请使用安全的 Azure 托管工作站（也称为特权访问工作站，简称 PAW）。

- [了解安全的 Azure 托管工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何启用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告和监视来检测环境中何时发生可疑活动或不安全的活动。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [如何在 Azure 安全中心内监视用户的标识和访问活动](security-center-identity-access.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用 Azure AD 命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。 

- [如何配置 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 Azure 安全中心时，请使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 Azure 安全中心具有可分配的内置角色（例如“安全管理员”），该角色允许用户更新安全策略以及关闭警报和建议。

- [Azure 安全中心中的权限](security-center-permissions.md)

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure Active Directory 提供日志来帮助发现过时的帐户。 此外，请使用 Azure AD 标识和访问评审来有效管理组成员身份、对企业应用程序的访问以及角色分配。 可定期评审与 Azure 安全中心相关的用户访问权限，确保相应人员继续拥有访问权限。 

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/) 

- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，因此可以与任何 SIEM/监视工具集成。 

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。  

- [如何将 Azure 活动日志与 Azure Monitor 集成](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报 

**指导**：使用 Azure AD 标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可将数据引入 Azure Sentinel 以做进一步调查。 

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记来辅助跟踪 Azure 资源，例如 Log Analytics 工作区，该工作区存储来自 Azure 安全中心的敏感安全信息。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅和管理组对各个安全域（如环境类型和数据敏感度级别）实现隔离。 你可以限制对应用程序和企业环境所需 Azure 资源的访问级别。 可以通过 Azure RBAC 来控制对 Azure 资源的访问。

默认情况下，Azure 安全中心数据存储在安全中心后端服务中。 如果组织还要求将这些数据存储在自己的资源中，则可以配置 Log Analytics 工作区来存储安全中心数据、警报和建议。 使用自己的工作区时，可以根据数据环境来源配置不同的工作区，从而实现进一步分离。

- [导出安全警报和建议](continuous-export.md)

- [Azure 安全中心中的数据收集](security-center-enable-data-collection.md)

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription) 

- [如何创建管理组](../governance/management-groups/create.md) 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：加密传输中的所有敏感信息。 确保连接到 Azure 资源的任何客户端能够协商 TLS 1.2 或更高版本。 任何配置有 Log Analytics 代理的虚拟机，如果要将数据发送到 Azure 安全中心，还应配置为使用 TLS 1.2。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。 

- [安全地将数据发送到 Log Analytics](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问 

**指南**：使用 Azure 基于角色的访问控制 (Azure RBAC) 管理对与 Azure 安全中心相关的数据和资源的访问。 Azure 安全中心具有“安全读取者”或“安全管理员”内置角色，这些角色允许用户读取或更新安全策略以及关闭警报和建议。 用于存储安全中心收集的数据的 Log Analytics 工作区也具有可分配的内置角色，例如“Log Analytics 读者”、“Log Analytics 参与者”等。 分配用户完成其所需任务需要的最少许可角色。 例如，将“读者”角色分配到只需查看有关资源的安全运行状况而不执行操作（例如应用建议或编辑策略）的用户。

- [Azure Log Analytics 工作区的权限](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Azure 安全中心中的权限](security-center-permissions.md)

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： Azure 安全中心使用配置的 Log Analytics 工作区来存储数据、警报和生成的建议。 为你为安全中心数据收集配置的工作区配置客户托管的密钥 (CMK) 。 CMK 使保存或发送到工作区的所有数据都可以使用由您创建并拥有的 Azure Key Vault 密钥进行加密。 

- [Azure Monitor 客户管理的密钥](../azure-monitor/platform/customer-managed-keys.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：当对与 Azure 安全中心相关的关键 Azure 资源进行更改时，可以使用 Azure Monitor 创建警报。 这些更改可能包括修改与安全中心有关的配置的任何操作，例如禁用警报或建议，或更新或删除数据存储。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：使用通用风险评分程序（例如通用漏洞评分系统）或第三方扫描工具提供的默认风险评级。

- [NIST 出版物 - 通用漏洞评分系统](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure Resource Graph 查询和发现订阅中与 Azure 安全中心相关的所有资源。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅，以发现安全中心资源。 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md) 

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：使用标记来辅助跟踪 Azure 资源，例如 Log Analytics 工作区，该工作区存储来自 Azure 安全中心的敏感安全信息。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适当的情况下，使用标记、管理组和单独的订阅来组织和跟踪 Azure 安全中心资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](../governance/management-groups/create.md)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。 

使用 Azure Resource Graph 查询和发现订阅中的资源。  确保环境中的所有 Azure 资源均已获得批准。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：当组织在清点和审核过程中不再需要与 Azure 安全中心相关的 Azure 资源时，将其删除。

- [Azure 资源组和资源删除](../azure-resource-manager/management/delete-resource-group.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：使用 Azure Policy 对可使用以下内置策略定义在订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure Policy 为 Azure 安全中心及其连接的工作区定义和实现标准安全配置。 在“Microsoft.OperationalInsights”和“Microsoft.Security”命名空间中使用 Azure Policy 别名创建自定义 Azure Policy 定义，审核或强制实施安全中心及其 Log Analytics 工作区的配置。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy 的“拒绝”和“如果不存在则进行部署”效果来跨 Azure 资源强制实施安全设置。 此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 资源的安全配置。 

- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md) 

- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md) 

- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略定义、azure 资源管理器模板和所需状态配置脚本。 若要访问在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。 

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。 使用 Azure Policy 别名创建自定义策略，审核或强制实施与 Azure 安全中心相关的资源的配置。 此外，也可以使用 Azure 自动化来部署配置更改。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：在“Microsoft.OperationalInsights”和“Microsoft. Security”命名空间中使用内置的 Azure Policy 定义以及 Azure Policy 别名创建自定义策略，审核和强制实施 Azure 资源配置并为其发出警报。 使用 Azure 策略效果“审核”、“拒绝”和“如果不存在则进行部署”自动强制实施 Azure 资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**： Azure 安全中心使用配置的 Log Analytics 工作区来存储数据、警报和生成的建议。 为你为安全中心数据收集配置的工作区配置客户托管的密钥 (CMK) 。 CMK 使保存或发送到工作区的所有数据都可以使用由您创建并拥有的 Azure Key Vault 密钥进行加密。 

- [Azure Monitor 客户管理的密钥](../azure-monitor/platform/customer-managed-keys.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](/azure/security/benchmarks/security-control-malware-defense)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：Azure 安全中心不用于存储或处理文件。 你需要负责预先扫描要上传到非计算 Azure 资源（包括 Log Analytics 工作区）的任何内容。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份 

**指南**：遵循基础结构即代码 (IAC) 方法，并使用 Azure 资源管理器采用 JavaScript 对象表示法 (JSON) 模板（该模板可用作与资源相关的配置的备份）部署与 Azure 安全中心相关的资源。

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [用于安全资源的 Azure 资源管理器模板](/azure/templates/microsoft.security/allversions)

- [关于 Azure 自动化](../automation/automation-intro.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：Azure 安全中心使用 Log Analytics 工作区存储其生成的数据、警报和建议。 可以配置 Azure Monitor 和被安全中心用来启用客户管理的密钥的工作区。 如果使用 Key Vault 存储客户管理的密钥，请确保定期对密钥进行自动备份。

- [如何备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：确保能够使用 Azure 资源管理器支持的模板文件定期执行还原操作。 测试对备份的客户管理的密钥进行还原。

- [使用 Azure 资源管理器模板管理 Log Analytics 工作区](../azure-monitor/platform/template-workspace-configuration.md)

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：使用 Azure DevOps 安全地存储和管理你的代码，例如自定义 Azure Policy 定义和 Azure 资源管理器模板。 若要保护在 Azure DevOps 中管理的资源，可以向特定用户、内置安全组或 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）中定义的组或 Active Directory（如果与 TFS 集成）授予或拒绝授予权限。 使用基于角色的访问控制保护客户管理的密钥。

此外，在 Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。  如果将 Azure 存储用于存储 Azure 资源管理器模板备份，请启用软删除以在 blob 或 blob 快照被删除时保存和恢复数据。 

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

- [如何在密钥保管库中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Azure 存储 Blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 

- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。  你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。 

- [Azure 安全中心中的安全警报](security-center-alerts-overview.md) 

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 查明弱点和差距，并根据需要修改你的响应计划。 

- [NIST 发布内容 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。 

- [如何设置 Azure 安全中心安全联系人](security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议，以便确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。 

- [如何配置连续导出](continuous-export.md) 

- [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心的工作流自动化功能，针对安全警报和建议自动触发响应，以保护 Azure 资源。 

- [如何在安全中心配置工作流自动化](workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
