---
title: 适用于 Azure IoT 中心的 Azure 安全基线
description: Azure IoT 中心安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 31a3bfbc174ca9c4c46005a26cde65db1ff74b65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906913"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>适用于 Azure IoT 中心的 Azure 安全基线

适用于 Microsoft Azure IoT 中心的 Azure 安全基线包含可帮助你改进部署安全状况的建议。 此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。 有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：默认情况下，IoT 中心的主机名会映射到一个公共终结点，该终结点具有可通过 internet 以公开方式路由的 IP 地址。 不同的客户将共享此 IoT 中心公共终结点，广域网和本地网络中的 IoT 设备均可对其进行访问。

IoT 中心的功能，包括消息路由、文件上传和批量设备导入/导出，还需要配置有从 IoT 中心到客户拥有的 Azure 资源的连接（通过其公共终结点）。 这些连接路径上的流量共同构成了从 IoT 中心到客户资源的出口流量。

建议通过你自己运行的虚拟网络来限制与 Azure 资源（包括 Azure IoT 中心）的连接，以减少隔离网络中的连接风险，并直接将本地网络连接到 Azure 主干网络。 在可行的情况下，使用 Azure 专用链接和 Azure 专用终结点来启用从其他虚拟网络对服务的专用访问。

将设备中开放的硬件端口数保持在最低限度，以避免不必要的访问。 此外，建立相应机制来阻止或检测对设备的物理篡改。

- [IoT 虚拟网络支持](virtual-network-support.md)
- [大量网络最佳做法](https://docs.microsoft.com/azure/iot-fundamentals/security-recommendations?context=azure/iot-hub/rc/rc#networking)
- [Azure 专用链接概述](../private-link/private-link-overview.md)
- [Azure 网络安全组](../virtual-network/security-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 网络资源。 启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。 你还可以将流日志发送到 Log Analytics 工作区，然后使用流量分析来提供有关 Azure 云中流量模式的见解。 流量分析的优势包括能够可视化网络活动、识别热点和安全威胁、了解通信流模式，以及查明网络不当配置。
 
- [如何启用网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)
 
- [了解 Azure 安全中心提供的网络安全性](../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：使用 IoT 中心 IP 筛选规则阻止已知的恶意 IP。 另外，通过适用于 IoT 的 Azure 安全中心记录恶意尝试行为并针对其发出警报。

Azure DDoS 防护基本版已作为 IoT 中心的一部分启用，使用时无需额外付费。 始终可用的流量监控和常见网络级别攻击的实时风险缓解提供了 Microsoft 联机服务所采用的相同防御机制。 整个 Azure 全球网络的规模可用于跨区域分散和缓解攻击流量。

- [IoT 中心 IP 筛选器](iot-hub-ip-filtering.md)

- [用于 IoT 的 Azure 安全中心可疑 IP 地址通信](../defender-for-iot/concept-security-alerts.md)

- [管理 Azure DDoS 保护基本](../virtual-network/ddos-protection-overview.md)

- [Azure 安全中心的威胁防护](../security-center/threat-protection.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：不适用；此建议适用于生成网络数据包的产品/服务，这些数据包可由客户记录和查看。 IoT 中心不生成面向客户的网络数据包，并且不能直接部署到 Azure 虚拟网络中。

**Azure 安全中心监视**：否

**责任**：不适用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 IDS/IPS 功能。  如果不需要进行有效负载检查，则可以使用 Azure 防火墙威胁情报。 使用基于 Azure 防火墙威胁情报的筛选功能，针对进出已知恶意 IP 地址和域的流量发出警报并/或阻止该流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测并/或阻止恶意流量。 

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)

- [如何配置 Azure 防火墙警报](../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：对于需要访问 Azure IoT 中心的资源，请使用虚拟网络服务标记来定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名（例如，AzureIoTHub），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

- [如何对 Azure IoT 使用服务标记](iot-hub-understand-ip-address.md)
- [有关使用服务标记的详细信息](../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：使用 Azure Policy 为与 Azure IoT 中心命名空间关联的网络资源定义和实施标准安全配置。 在“Microsoft.Devices”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施机器学习命名空间的网络配置。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对与 Azure IoT 中心部署关联的网络资源使用标记，以便按逻辑将这些资源组织成某种分类。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure IoT 中心相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

- [如何查看和检索 Azure 活动日志事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：Microsoft 为日志中的时间戳维护用于 Azure 资源（例如 Azure IoT 中心）的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合 Azure IoT 中心生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用存储帐户进行长期/存档存储。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方安全信息和事件管理 (SIEM)。

- [设置 Azure IoT 日志](iot-hub-monitor-resource-health.md)
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：在 Azure 资源上启用 Azure IoT 诊断设置，以访问审核日志、安全日志和诊断日志。 活动日志自动可用，包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

- [设置 Azure IoT 中心日志](iot-hub-monitor-resource-health.md)

- [如何使用 Azure Monitor 收集平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)

- [了解 Azure 中的日志记录和不同的日志类型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规章，为与 Azure IoT 中心实例关联的 Log Analytics 工作区设置日志保持期。

- [如何设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：分析和监视日志中的异常行为，并定期审查来自 Azure IoT 中心的结果。 使用 Azure Monitor 和 Log Analytics 工作区查看日志并对日志数据执行查询。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。 

- [监视 Azure IoT 运行状况](iot-hub-monitor-resource-health.md)
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Log Analytics 查询入门](../azure-monitor/log-query/get-started-portal.md)
   
- [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：使用适用于 IoT 的 Azure 安全中心和 Log Analytics 工作区监视安全日志和事件中的异常活动并发出警报。 或者，可以启用数据并将其加入 Azure Sentinel。 还可以使用 Azure Monitor 定义可能带来安全隐患的操作警报，例如流量意外下降时。

- [监视 Azure IoT 中心运行状况](iot-hub-monitor-resource-health.md)
- [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)
- [用于 IoT 警报的 Azure 安全中心](../defender-for-iot/concept-security-alerts.md)

- [如何针对 Log Analytics 日志数据发出警报](../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用；Azure IoT 中心不会处理或生成与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用；Azure IoT 中心不会处理或生成与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：借助 Azure 基于角色的访问控制 (Azure RBAC)，可以通过角色分配管理对 Azure IoT 中心的访问。 可以将这些角色分配给用户、组服务主体和托管标识。 某些资源具有预定义的内置角色，可以通过工具（例如 Azure CLI、Azure PowerShell 或 Azure 门户）来清点或查询这些角色。 

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：对 Azure IoT 中心资源的访问管理通过 Azure Active Directory (Azure AD) 来控制。 Azure AD 没有默认密码。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。

还可以通过使用 Azure AD Privileged Identity Management 和 Azure 资源管理器来启用对管理帐户的即时访问。

- [详细了解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：对于访问 IoT 中心的用户，请使用 Azure Active Directory SSO。 请使用 Azure 安全中心标识和访问建议。 

- [了解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure AD MFA 来保护整个 Azure 租户，从而使所有服务受益。 IoT 中心服务不支持 MFA。  

- [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

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
- [如何在 Azure 安全中心内监视用户的标识和访问活动](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：对于访问 IoT 中心的用户，不支持条件访问。 若要缓解这种情况，请使用 Azure AD 命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问整个 Azure 租户，从而使包括 IoT 中心在内的所有服务受益。 

- [如何配置 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：对于访问 IoT 中心的用户，请使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

对于设备和服务访问，IoT 中心使用安全令牌和共享访问签名 (SAS) 令牌对设备和服务进行身份验证，以避免在网络上发送密钥。 

- [如何创建和配置 Azure AD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT 中心安全令牌](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure AD 标识和访问评审来有效管理组成员身份、对企业应用程序的访问以及角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

使用 Azure AD Privileged Identity Management (PIM) 在环境中发生可疑或不安全的活动时生成日志和警报。

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)
- [如何使用 Azure AD 标识和访问评审](../active-directory/governance/access-reviews-overview.md)
- [部署 Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，因此可以与任何 SIEM/监视工具集成。 

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。 

使用 Azure Monitor 诊断日志监视“连接”类别中未经授权的连接尝试。

- [如何将 Azure 活动日志与 Azure Monitor 集成](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [为 IoT 中心配置诊断日志](iot-hub-monitor-resource-health.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure AD 标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可将数据引入 Azure Sentinel 以做进一步调查。
  
- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)
  
- [ 如何配置和启用 Identity Protection 风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ 如何载入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：在 Microsoft 需要访问客户数据的支持场景中，Microsoft 将直接向客户请求数据。 

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。
 
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：使用单独的订阅和管理组对各个安全域（如环境类型和数据敏感度级别）实现隔离。 你可以限制对应用程序和企业环境所需 Azure 资源的访问级别。 可以通过 Azure RBAC 来控制对 Azure 资源的访问。
  
- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)
- [如何创建管理组](/azure/governance/management-groups/create)
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：利用 Azure 市场中有关网络外围的第三方解决方案，监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

对于由 Microsoft 管理的底层平台，Microsoft 会将所有客户内容都视为敏感信息，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：IoT 中心使用传输层安全性 (TLS) 保护来自 IoT 设备和服务的连接。 目前支持三个版本的 TLS 协议，即版本 1.0、1.1 和 1.2。 在连接到 IoT 中心时，强烈建议使用 TLS 1.2 作为首选 TLS 版本。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

- [IoT 中心的 TLS 支持](iot-hub-tls-support.md)
- [了解 Azure 传输中的加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：数据标识、分类和丢失防护功能尚不适用于 Azure IoT 中心。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于由 Microsoft 管理的 Azure 底层平台，Microsoft 会将所有客户内容都视为敏感信息，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指南**：对于访问 IoT 中心的控制平面用户，请使用 Azure RBAC 来控制访问。 对于访问 IoT 中心的数据平面，请使用 IoT 中心的共享访问策略。

- [如何配置 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [控制 IoT 中心的访问权限](iot-hub-devguide-security.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此项指导适用于计算资源。

Microsoft 管理 Azure IoT 中心的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：IoT 中心支持使用客户托管的密钥 (CMK) 对静态数据进行加密，该密钥也称为“自带密钥”(BYOK)。

Azure IoT 中心对写入数据中心的静态数据和传输中的数据进行加密，并在你访问数据时解密。 默认情况下，IoT 中心使用 Microsoft 托管的密钥来加密静态数据。

- [通过 IoT 中心的客户托管密钥加密静态数据](iot-hub-customer-managed-keys.md)

- [了解 Azure 中的静态加密](../security/fundamentals/encryption-atrest.md)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure IoT 中心的生产实例和其他关键或相关资源发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

[有关详细信息，请参阅 *Azure 安全基线：* 漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：不适用；Microsoft 对支持 Azure IoT 中心的底层系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；Microsoft 对支持 Azure IoT 中心的底层系统执行修补程序管理。 

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动化补丁管理解决方案

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：向 Azure 资源应用标记（并非所有资源都支持标记，但大多数资源都支持），以便按逻辑将这些资源组织成某种分类。

- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。
  
- [如何创建其他 Azure 订阅](/azure/billing/billing-create-subscription)
  
- [如何创建管理组](/azure/governance/management-groups/create)
  
- [如何创建和使用标记](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指导**：根据组织需求，创建已获批 Azure 资源以及已获批用于计算资源的软件的清单。

每个 IoT 中心都有一个标识注册表，可用于在服务中创建各设备的资源。 可将单个或一组设备标识添加到允许列表或方块列表，以便完全控制设备访问。

- [IoT 中心标识注册表](iot-hub-devguide-identity-registry.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。 

使用 Azure Resource Graph 查询和发现订阅中的资源。  确保环境中的所有 Azure 资源均已获得批准。 

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

* 不允许的资源类型
* 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure AD 条件访问来限制用户与 Azure 资源管理器交互的能力。
  
- [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure Policy 为 Azure IoT 中心服务定义和实现标准安全配置。 使用“Microsoft.Devices”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure IoT 中心服务的配置。

Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足组织的安全要求。

还可以使用来自 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)

- [在 Azure 门户中将单资源和多资源导出到模板](../azure-resource-manager/templates/export-template-portal.md)

- [安全建议 - 参考指南](../security-center/recommendations-reference.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。 此外，你可以使用 Azure 资源管理器模板维护组织所需的 Azure 资源的安全配置。  
 
- [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)
- [创建和管理策略以强制实施符合性](../governance/policy/tutorials/create-and-manage.md)
- [Azure 资源管理器模板概述](../azure-resource-manager/templates/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果对 Azure IoT 中心或相关资源使用自定义 Azure Policy 定义，请使用 Azure Repos 安全地存储和管理代码。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：在“Microsoft.Devices”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [如何使用别名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：使用 Azure 安全中心对 Azure 资源执行基线扫描。 此外，使用 Azure Policy 警告和审核 Azure 资源配置。 
 
- [如何在 Azure 安全中心修正建议](../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：IoT 中心使用安全令牌和共享访问签名 (SAS) 令牌对设备和服务进行身份验证，以避免在网络上发送密钥。 

将托管标识与 Azure Key Vault 结合使用，以简化云应用程序的机密管理。

- [IoT 中心安全令牌](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [如何将托管标识用于 IoT 中心](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [如何创建密钥保管库](/azure/key-vault/quick-create-portal)
- [如何使用托管标识提供 Key Vault 身份验证](/azure/key-vault/managed-identity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：IoT 中心使用安全令牌和共享访问签名 (SAS) 令牌对设备和服务进行身份验证，以避免在网络上发送密钥。 

使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

- [IoT 中心安全令牌](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [如何为 IoT 中心配置托管标识](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 
 
- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

[有关详细信息，请参阅 *Azure 安全基线：* 恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure 应用服务）的基础主机上启用，但不会对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预扫描要上传到非计算 Azure 资源的文件

**指南**：Microsoft 反恶意软件已在支持 Azure 服务（例如，Azure IoT 中心）的基础主机上启用，但它不会针对客户内容运行。

你需要负责预先扫描要上传到非计算 Azure 资源的任何内容。 Microsoft 无法访问客户数据，因此无法代表你对客户内容执行反恶意软件扫描。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此基准适用于计算资源。 在支持 Azure 服务的底层主机上已启用 Microsoft Antimalware，但是，该软件不会针对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

## <a name="data-recovery"></a>数据恢复

[有关详细信息，请参阅 *Azure 安全基线：* 数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：Azure IoT 中心服务提供的方法和框架可使 IoT 中心服务高度可用，并可根据特定的业务目标从灾难中恢复。 

- [IoT 中心高可用性和灾难恢复](iot-hub-ha-dr.md)

- [如何克隆 IoT 中心](iot-hub-how-to-clone.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：Azure IoT 中心建议辅助 IoT 中心必须包含所有可连接到解决方案的设备标识。 解决方案应该保留设备标识的异地复制备份，并在切换设备的活动终结点之前将其上传到辅助 IoT 中心。 IoT 中心的设备标识导出功能在此情景中很有用。

- [IoT 中心高可用性和灾难恢复](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT 中心设备标识导出](iot-hub-bulk-identity-mgmt.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：Azure IoT 中心建议辅助 IoT 中心必须包含所有可连接到解决方案的设备标识。 解决方案应该保留设备标识的异地复制备份，并在切换设备的活动终结点之前将其上传到辅助 IoT 中心。 IoT 中心的设备标识导出功能在此情景中很有用。

请定期在备份中执行内容数据还原。 请确保可以还原已备份的客户管理的密钥。

- [IoT 中心高可用性和灾难恢复](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [IoT 中心设备标识导出](iot-hub-bulk-identity-mgmt.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。 如果将 Azure 存储用于存储备份，请启用软删除以在 blob 或 blob 快照被删除时保存和恢复数据。
 
 
- [了解 Azure RBAC](../role-based-access-control/overview.md)
- [Azure Blob 存储的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理和管理从检测到事件后审查的各个阶段。 
  
- [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [Microsoft 安全响应中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [使用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果或分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

  
 此外，使用标记来标记订阅，并创建命名系统来对 Azure 资源进行标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。
  
- [Azure 安全中心的安全警报](../security-center/security-center-alerts-overview.md)
  
- [使用标记整理 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 查明弱点和差距，并根据需要修改你的响应计划。
  
- [NIST 出版物 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。
  
- [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议，以便确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。
  
- [如何配置连续导出](../security-center/continuous-export.md)
 
- [ 如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心的工作流自动化功能，针对安全警报和建议自动触发响应，以保护 Azure 资源。
  
- [ 如何在安全中心配置工作流自动化](../security-center/workflow-automation.md)

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
