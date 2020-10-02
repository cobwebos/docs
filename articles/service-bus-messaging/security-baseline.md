---
title: 适用于服务总线的 Azure 安全基准
description: Service Bus 安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6242e593171bd8a01d01739b884ae8a1ca7beb4d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629754"
---
# <a name="azure-security-baseline-for-service-bus"></a>适用于服务总线的 Azure 安全基准

适用于服务总线的 Azure 安全基准包含有助于改进部署安全状况的建议。 此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview-v1.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。 有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

若要查看服务总线如何完全映射到 Azure 安全基准，请参阅 [完整的服务总线安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源 

**指南**：服务总线与 Azure 专用链接服务的集成支持从工作负荷（例如，绑定到虚拟网络的虚拟机）进行安全的专用访问。 创建到服务总线命名空间的专用终结点连接。 专用终结点使用虚拟网络中的专用 IP 地址，从而将该服务有效地引入到虚拟网络。 到服务的所有流量都可以通过该专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。

你还可以使用防火墙保护 Azure 服务总线命名空间。 Azure 服务总线支持针对入站防火墙支持的基于 IP 的访问控制。 可以通过 Azure 门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell 设置防火墙规则。

- [允许通过专用终结点访问 Azure 服务总线命名空间](private-link-service.md)

- [允许从特定 IP 地址或范围访问 Azure 服务总线命名空间](service-bus-ip-filtering.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指南**：如果使用 Azure 虚拟机访问服务总线实体，请启用网络安全组 (NSG) 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。 

使用 Azure 安全中心并按照网络保护建议来帮助保护 Azure 中的服务总线资源。

- [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

- [了解 Azure 安全中心提供的网络安全](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在与服务总线命名空间相关联的虚拟网络上启用 DDoS 保护标准，以防止分布式拒绝服务 (DDoS) 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

- [如何配置 DDoS 防护](../virtual-network/manage-ddos-protection.md)

- [Azure 安全中心集成威胁情报](/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：如果使用 Azure 虚拟机访问服务总线实体，则可以使用网络观察程序数据包捕获来调查异常活动。

- [如何启用网络观察程序](../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：如果使用 azure 虚拟机来访问服务总线实体，请从 Azure Marketplace 中选择一个产品/服务，其中支持具有负载检查功能的 ID/IPS 功能。 如果你的组织不需要基于负载检查的入侵检测和/或防护，则可以使用 Azure 服务总线的内置防火墙功能。 您可以使用防火墙规则限制对服务总线命名空间的访问，或限制 IP 地址的限制。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何在服务总线命名空间中为指定的 IP 地址添加防火墙规则](service-bus-ip-filtering.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制，以筛选进出服务总线资源的流量。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称 (例如，服务总线) ，可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 

- [了解并使用服务标记](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure 服务总线命名空间和 azure 策略关联的网络资源的标准安全配置。 使用 "node.js" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行服务总线命名空间的网络配置。 你还可以使用与 Azure 服务总线相关的内置策略定义，例如：

- 服务总线应使用虚拟网络服务终结点
- 应启用服务总线中的诊断日志

如果内置定义不满足组织的需求，则还可以构造自定义策略定义。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [适用于服务总线命名空间的 Azure 内置策略](/azure/service-bus-messaging/policy-samples#azure-service-bus-messaging)

- [用于网络的 Azure Policy 示例](../governance/policy/samples/built-in-policies.md#network)

- [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对虚拟网络以及与服务总线命名空间相关联的网络安全和通信流相关的其他资源使用标记。 对于单独的网络安全组规则，请使用 "说明" 字段为允许进出与 Service Bus 命名空间相关联的网络的流量的任何规则指定业务需求、持续时间和其他描述性信息。 

使用与标记相关的任何内置 Azure 策略定义（如 "需要标记和值"）来确保使用标记创建所有资源并通知现有未标记资源。 

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。 

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags) 

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md) 

- [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 服务总线相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合服务总线资源生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析、为长期或存档存储配置 Azure 存储帐户。 你还可以配置与要发送到 Azure Sentinel 或第三方 SIEM 的服务总线相关的日志。

- [如何配置 Azure 服务总线的诊断设置](service-bus-diagnostic-logs.md)

- [了解 Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [如何开始将 Azure Monitor 与第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Azure 服务总线命名空间的诊断设置。 Azure 服务总线当前支持活动和操作或诊断日志。 活动日志包含对作业执行的操作的相关信息。 诊断日志提供有关使用 API 或通过使用语言 SDK 的管理客户端对命名空间执行的操作和操作的更丰富信息。 具体而言，这些日志捕获操作类型，包括队列创建、所用的资源和操作状态。

- [如何为 Azure 服务总线启用诊断设置](service-bus-diagnostic-logs.md)

- [如何启用 Azure 活动日志的诊断设置](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性法规来设置 Log Analytics 工作区保持期，以捕获和查看与服务总线相关的事件。

- [如何为 Log Analytics 工作区设置日志保持参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：分析和监视日志中的异常行为，并定期查看与你的服务总线实体相关的结果。 使用 Azure Monitor 查看日志，并对与服务总线相关的日志数据执行查询。

- [有关 "Log Analytics" 工作区的详细信息](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：将 Azure 安全中心与 Log Analytics 工作区结合使用，以便在安全日志和事件中发现异常活动时进行监视和警报。 此外，也可以将和机载数据启用到 Azure Sentinel。

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

- [如何在 Azure 安全中心管理警报](../security-center/security-center-managing-and-responding-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：当前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure 基于角色的访问控制 (RBAC) 使你可以通过角色分配管理对 Azure 资源的访问权限。 你可以将这些角色分配给用户、组服务主体和托管标识。 对于服务总线，有预定义的内置角色，可以通过 Azure CLI、Azure PowerShell 或 Azure 门户等工具来清点或查询这些角色。

- [Azure 服务总线的内置角色](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：通过 Azure Active Directory (Azure AD) 控制对服务总线的控制平面访问。 Azure AD 没有默认密码。

通过使用托管标识、应用注册或共享访问签名 Azure AD 控制对服务总线的数据平面访问。 共享访问签名由连接到服务总线命名空间的客户端使用，可以随时重新生成。

- [了解服务总线的共享访问签名](service-bus-sas.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

如果内置定义不满足组织的需求，则还可以构造自定义策略定义。

- [如何使用 Azure 安全中心来监视标识和访问](../security-center/security-center-identity-access.md)

- [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指南**： Microsoft Azure 为基于 Azure Active Directory (Azure AD) 的资源和应用程序提供集成的访问控制管理。 将 Azure AD 与 Azure 服务总线配合使用的主要优势在于，你无需再将凭据存储在代码中。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 用于请求令牌的资源名称为 https://servicebus.azure.net/。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 将向应用程序返回一个访问令牌，然后应用程序可使用该访问令牌向 Azure 服务总线资源授予请求。

- [如何使用 Azure AD 对应用程序进行身份验证以访问服务总线资源](authenticate-application.md)

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory 多重身份验证 (MFA) ，并遵循 Azure 安全中心的标识和访问管理建议来帮助保护启用了服务总线的资源。

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：使用具有多重身份)  (验证的特权访问工作站 (PAW) 配置为登录和配置启用了服务总线的资源。

- [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

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

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 AZURE ACTIVE DIRECTORY (AD) 作为 Azure 资源（如服务总线）的集中身份验证和授权系统。 这就允许 azure RBAC) 使用 Azure 基于角色的访问控制 (管理敏感资源。

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [使用 Azure Active Directory 授予对服务总线资源的访问权限](authenticate-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**： Azure Active Directory (Azure AD) 会提供日志，以帮助你发现陈旧的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

另外，还会定期旋转服务总线命名空间的共享访问签名。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

- [了解服务总线命名空间的共享访问签名](service-bus-sas.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，这允许你与 Azure Sentinel 或第三方 SIEM 工具集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 然后，在 Azure Monitor 可以为日志中发生的某些操作配置所需的日志警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [使用 Azure Active Directory 授予对服务总线资源的访问权限](authenticate-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory 的标识保护和风险检测功能来配置自动响应，以检测与启用了服务总线的资源相关的检测到的可疑操作。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;对于服务总线，尚不支持客户密码箱。

- [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：对与服务总线相关的资源使用标记，以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：为开发、测试和生产实施单独的订阅和管理组。 服务总线命名空间应由配置了专用终结点并适当标记的虚拟网络进行分隔。

- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)

- [如何创建管理组](/azure/governance/management-groups/create)

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：使用虚拟机访问服务总线实体时，请使用虚拟网络、专用终结点、服务总线防火墙、网络安全组和服务标记，以减轻数据渗透的可能性。

Microsoft 管理 Azure 服务总线的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

- [为 Azure 服务总线命名空间配置 IP 防火墙规则](service-bus-ip-filtering.md)

- [允许从特定虚拟网络访问 Azure 服务总线命名空间](private-link-service.md)

- [允许通过专用终结点访问 Azure 服务总线命名空间](private-link-service.md)

- [了解网络安全组和服务标记](/azure/virtual-network/security-overview)

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，Azure 服务总线强制实施 TLS 加密的通信。 目前支持 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和1.1 处于覆盖整个行业的路径，因此请尽可能使用 TLS 1.2 或更高版本。

- [若要了解服务总线的安全功能，请参阅网络安全](network-security.md)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：对于 Azure 服务总线，数据标识、分类和丢失防护功能尚不可用。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**： Azure 服务总线支持使用 Azure Active Directory (Azure AD) 对服务总线实体的请求进行授权。 使用 Azure AD，你可以使用 Azure RBAC)  (Azure 基于角色的访问控制向安全主体（可以是用户或应用程序服务主体）授予权限。

- [了解 Azure RBAC 和 Azure 服务总线的可用角色](authenticate-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： Azure 服务总线支持通过 Microsoft 管理的密钥或客户托管的密钥来加密静态数据。 此功能使你能够创建、轮换、禁用和撤消对用于加密 Azure 服务总线数据的客户托管密钥的访问权限。

- [如何配置客户托管密钥以加密 Azure 服务总线](configure-customer-managed-key.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 azure 活动日志结合使用，以创建有关 Azure 服务总线的生产实例以及其他关键或相关资源发生更改的警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现所有资源 (包括订阅中) Azure 服务总线命名空间。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

- [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure 服务总线命名空间和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

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

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，使用 Azure 资源关系图查询和发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型
- 允许的资源类型

如果内置定义不满足组织的需求，则还可以构造自定义策略定义。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：定义和实现 Azure 服务总线部署的标准安全配置。 你还可以使用 Azure 服务总线的内置策略定义，例如：

- 应启用服务总线中的诊断日志
- 服务总线应使用虚拟网络服务终结点限制到专用网络的网络流量。

使用 "node.js" 命名空间中的 Azure 策略别名创建自定义策略，以审核或强制执行配置。

- [适用于服务总线的 Azure 内置策略 ](/azure/service-bus-messaging/policy-samples)

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在]）在支持服务总线的资源或应用程序之间强制实施安全设置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [有关 Azure 策略效果的详细信息](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：使用 "system.servicemodel" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用 "system.servicemodel" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））自动强制执行 Azure 服务总线部署和相关资源的配置。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：对于在 Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，用于访问你的服务总线实体，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 服务总线部署的共享访问签名管理。 请确保启用 Key Vault 软删除。

- [使用 Azure Active Directory 验证托管标识，以访问服务总线资源](service-bus-managed-service-identity.md)

- [配置服务总线的客户托管密钥](configure-customer-managed-key.md)

- [如何创建 Key Vault](/azure/key-vault/quick-create-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：对于在 Azure App Service 上运行的 Azure 虚拟机或 web 应用程序，用于访问服务总线实体，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 服务总线。 请确保启用 Key Vault 软删除。

使用托管标识，通过 Azure Active Directory (Azure AD) 中的自动托管标识提供 Azure 服务。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [使用 Azure Active Directory 验证托管标识，以访问服务总线资源](service-bus-managed-service-identity.md)

- [配置服务总线的客户托管密钥](configure-customer-managed-key.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：预扫描正在上传到非计算 azure 资源（如 Azure 服务总线、应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等）的任何内容。Microsoft 无法访问这些实例中的数据。

在支持 Azure 服务的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：配置 Azure 服务总线的异地灾难恢复。 当整个 Azure 区域或数据中心（如果未使用可用性区域）遭遇停机时，在不同区域或数据中心中继续进行数据处理就显得至关重要。 在这种情况下，异地灾难恢复和异地复制对于任何企业而言都是至关重要的功能。 Azure 服务总线支持命名空间级别的异地灾难恢复和异地复制。

- [了解 Azure 服务总线的异地灾难恢复](service-bus-geo-dr.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**： Azure 服务总线通过 azure 存储服务加密 (azure SSE) 提供静态数据的加密。 Service Bus 依赖于 Azure 存储来存储数据，默认情况下，使用 Microsoft 托管密钥对存储在 Azure 存储中的所有数据进行加密。 如果使用 Azure Key Vault 来存储客户管理的密钥，请确保定期自动备份密钥。

请确保通过以下 PowerShell 命令定期自动备份 Key Vault 机密： AzKeyVaultSecret

- [如何配置客户托管的密钥以加密静态 Azure 服务总线数据](configure-customer-managed-key.md)

- [如何备份 Key Vault 机密](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：测试还原已备份的客户托管密钥使用来加密服务总线数据。

- [如何配置客户托管的密钥以加密静态 Azure 服务总线数据](configure-customer-managed-key.md)

- [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 Key Vault 中启用软删除，以防止意外或恶意删除密钥。 Azure 服务总线需要客户管理的密钥才能进行软删除，并且不会清除已配置的密钥。

- [如何在密钥保管库中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [设置密钥保管库与密钥](../event-hubs/configure-customer-managed-key.md)

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

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。 

- [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md) 

- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [NIST 发布：针对 IT 计划和功能的测试、培训和试验计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

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

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**： 

使用工作流自动化功能 Azure 安全中心自动触发对安全警报和建议的响应，以保护 Azure 资源。 

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
