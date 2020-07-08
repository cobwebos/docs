---
title: 适用于流分析的 Azure 安全基线
description: 适用于流分析的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9d085ba494ea6bb6e9e80490d85e50f100fc0908
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485550"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>适用于流分析的 Azure 安全基线

适用于流分析的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**： Azure 流分析不支持使用网络安全组（NSG）和 Azure 防火墙。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**： Azure 流分析不支持使用虚拟网络和子网。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：使用 Azure 安全中心威胁防护来检测和警报与已知的恶意或未使用的 Internet IP 地址的通信。

* [Azure 安全中心内 Azure 服务层的威胁防护](https://docs.microsoft.com/azure/security-center/threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**： Azure 流分析不使用网络安全组，也不捕获 Azure Key Vault 的流日志。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统（IDS/IPS）

**指南**：使用 Azure 安全中心威胁防护来检测 Azure 订阅环境中的异常或潜在有害操作。

* [Azure 安全中心内 Azure 服务层的威胁防护](https://docs.microsoft.com/azure/security-center/threat-protection)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**： Azure 流分析不支持使用虚拟网络和网络规则。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**： Azure 流分析不支持虚拟网络和网络设备的使用。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**： Azure 流分析不支持使用虚拟网络和流量配置规则。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视资源配置并检测流分析资源的更改。 在 Azure Monitor 中创建当关键资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 Azure 资源的时间源，如流分析。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：通过 Azure Monitor 引入日志来聚合安全数据，如审核事件和请求。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储 accountyfor 长期/存档存储，如不可变的存储和强制保留保留的安全功能。

* [如何使用 Azure Monitor 收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Azure 流分析上的诊断设置以访问管理、安全和诊断日志。 你还可以启用 Azure 活动日志诊断设置，并将日志发送到相同的 Log Analytics 工作区或存储帐户。

* [Azure 流分析提供诊断日志和活动数据供审查](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：将安全事件日志存储在 Azure 存储帐户或 Log Analytics 工作区中时，可以根据组织的要求来设置保留策略。

* [Azure 流分析提供诊断日志和活动数据供审查](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

* [如何为 Azure 存储帐户日志配置保留策略](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

* [更改 Log Analytics 中的数据保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：分析和监视日志中的异常行为，并定期查看流分析资源的结果。 使用 Azure Monitor 的 Log Analytics 工作区来查看日志并执行针对日志数据的查询。 或者，可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [有关 "Log Analytics" 工作区的详细信息](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何在 Azure Monitor 中执行自定义查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：启用流分析的诊断设置，并将日志发送到 Log Analytics 工作区。 将 Log Analytics 工作区加入 Azure Sentinel，因为它提供了安全业务流程自动化响应 (SOAR) 解决方案。 这样便可以创建 playbook（自动化解决方案）并将其用于修正安全问题。

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何针对 Log Analytics 日志数据发出警报](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Azure 流分析提供诊断日志和活动数据供审查](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;流分析不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**： Azure Monitor 中的 Azure DNS 分析（预览版）解决方案将深入了解有关安全性、性能和操作的 DNS 基础结构。 此功能当前不支持 Azure 流分析，但你可以使用第三方 dns 日志记录解决方案。

* [使用 DNS Analytics 预览解决方案收集有关 DNS 基础结构的见解](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： Azure AD 具有必须显式分配的内置角色。 可以通过查询角色来发现成员身份。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：流分析没有默认密码的概念，因为 Azure Active Directory 提供身份验证并由基于角色的访问控制（RBAC）保护以管理服务。 根据注入流服务和输出服务，您需要轮换作业中配置的凭据。

* [为流分析作业的输入和输出旋转登录凭据](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：创建标识管理和角色安全计划，遵循最佳做法，包括管理员角色的最低特权访问原则。 使用 Azure Privileged Identity Management （PIM）提供对 Azure AD 和 Azure 资源的实时特权访问。 使用 Azure PIM 警报和审核历史记录来监视管理帐户的活动。 使用 Azure AD 安全报表来帮助确定可能已泄露的管理帐户。

* [了解详细信息](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：尽可能使用 Azure Active Directory SSO，而不是为每个服务配置独立凭据。 实现 Azure 安全中心标识 &amp; 访问建议。

* [了解 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory 多重身份验证（MFA）并遵循 Azure 安全中心的标识和访问管理建议来帮助保护你的流分析资源。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心内监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：对配置为登录和配置流分析资源的多重身份验证（MFA）使用 paw （特权访问工作站）。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：对来自管理帐户的可疑活动进行日志和警报

**指南**：在环境中发生可疑活动或不安全活动时，使用 Azure Active Directory 安全报告生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

* [如何确定标记为存在风险活动的 Azure AD 用户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 安全中心内监视用户的标识和访问活动](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 提供了基于角色的访问控制（RBAC），以便对客户端对流分析资源的访问进行精细控制。

* [如何创建和配置 Azure AD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：查看 Azure Active Directory 日志，以帮助发现可包括具有存储帐户管理角色的帐户的陈旧帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 应定期查看用户访问权限，以确保只有正确的用户才能继续访问。

* [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视器尝试访问停用的凭据

**指南**：为 Azure 流分析和 Azure Active Directory 启用诊断设置，将所有日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需的警报（例如，尝试访问禁用的机密）。

* [将 Azure AD 日志与 Azure Monitor 日志集成](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory 的风险和标识保护功能配置对检测到的与流分析资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：不适用;Azure 流分析不支持客户密码箱。

* [公开上市中支持的服务和方案](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的流分析资源。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：通过在同一订阅中放置输入、输出和存储帐户，隔离流分析作业。 可以限制流分析，以控制对应用程序和企业环境所需的流分析资源的访问级别。 可以通过 Azure AD RBAC 控制对 Azure 流分析的访问。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [理解 Azure 流分析的输入](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-add-inputs)

* [了解 Azure 流分析的输出](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**： Azure 流分析资源尚不支持数据丢失防护功能。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感数据，并防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [如何保护 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**： Azure 流分析会加密所有传入和传出通信，并支持 TLS 1.2。 内置检查点也是加密的。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：对于 Azure 流分析资源，数据标识功能尚不可用。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用基于角色的访问控制（RBAC）来控制用户与服务的交互方式。

* [如何在 Azure 中配置 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

指南：不适用；此建议旨在用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：流分析不会存储传入的数据，因为所有处理都是在内存中完成的。 任何包含需要由流分析保存的查询和函数的私有数据都存储在配置的存储帐户中。 使用客户托管的密钥（CMK）加密存储帐户中静态的输出数据。 即使没有 CMK，流分析也会自动在其基础结构中采用同类最佳的加密标准来加密和保护数据。

* [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 azure 活动日志结合使用，以创建有关 Azure 流分析资源的生产实例发生更改的警报。

* [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：通过 Azure 安全中心的建议，保护 Azure 流分析资源。

Microsoft 对支持 Azure 流分析的基础系统执行漏洞管理。

* [了解 Azure 安全中心建议](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

* [了解 Azure 安全中心安全分数](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure 流分析资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure 策略将对可以使用以下内置策略定义在客户订阅中创建的资源类型进行限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

指南：不适用；此建议旨在用于计算资源和整个 Azure。

Azure 安全中心监视：不适用

责任：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，使用 Azure 资源关系图查询/发现订阅中的资源。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

指南：不适用；此建议旨在用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护批准的软件标题的清单

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器的交互能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 "streamanalytics-default-central-us" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure 流分析的配置。 你还可以使用与 Azure 流分析相关的内置策略定义，例如：-应启用 Azure 流分析中的诊断日志

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy 内置策略定义](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此项指导适用于计算资源。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure Repos 安全地存储和管理代码，包括自定义 azure 策略、azure 资源管理器模板、所需状态配置脚本、用户定义函数、查询。 若要访问 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或在 Azure Active Directory （Azure AD）中定义的、与 Azure Active Directory DevOps 集成的权限，或在与 TFS 集成的情况下使用的组。

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [关于 Azure DevOps 中的权限和组](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署适用于 Azure 资源的配置管理工具

**指南**：使用 "streamanalytics-default-central-us" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：对 Azure 资源实现自动配置监视

**指南**：使用 "streamanalytics-default-central-us" 命名空间中的 Azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））自动强制实施 Azure 流分析资源的配置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：流分析作业使用的输入或输出资源的连接详细信息存储在配置的存储帐户中。 加密存储帐户以保护你的所有数据。 并且定期为流分析作业的输入或输出旋转凭据。

* [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection)

* [轮转流分析作业的输入和输出的登录凭据](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-login-credentials-inputs-outputs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：针对输出的托管标识身份验证向流分析作业提供直接访问服务（包括 Power BI 和存储帐户），而不是使用连接字符串。

* [使用托管标识在 Azure Data Lake Storage Gen1 中对流分析进行身份验证](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls)

* [使用托管标识对 azure 流分析作业进行 azure Blob 存储输出的身份验证](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)

* [使用托管标识对 Azure 流分析作业进行身份验证，以便 Power BI](https://docs.microsoft.com/azure/stream-analytics/powerbi-output-managed-identity)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

责任：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的基础主机（例如，Azure 流分析）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

预先扫描要上传到 Azure 资源的任何内容，如应用服务、流分析、Blob 存储等。Microsoft 无法访问这些实例中的数据。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。

Azure 安全中心监视：不适用

责任：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：基于所选的输出服务类型。 您可以根据输出服务的建议准则，执行输出数据的自动备份。 内部数据（包括用户定义的函数、查询、数据快照）存储在配置的存储帐户中，你可以定期进行备份。

Microsoft Azure 存储帐户中的数据始终会自动复制，以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。

你还可以使用生命周期管理功能将数据备份到存档层。 此外，为存储在存储帐户中的备份启用软删除。

* [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [了解 Azure 存储冗余和服务级别协议](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

* [管理 Azure Blob 存储生命周期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)

Azure 存储 blob 的软删除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份任何客户托管的密钥

**指南**：内部数据（包括用户定义的函数、查询、数据快照）存储在配置的存储帐户中，你可以定期进行备份。

为了从存储帐户支持的服务备份数据，可以使用多种方法，包括使用 azcopy 或第三方工具。 Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据对象。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。

* [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [AzCopy 入门](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

* [为 Blob 存储设置和管理不可变性策略](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

使用 Azure CLI 或 PowerShell，可以在 Azure Key Vault 中支持客户管理/提供的密钥。

* [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证包括客户托管密钥在内的所有备份

**指南**：定期执行备份数据的数据还原，以测试数据的完整性。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：默认情况下，Azure 存储中存储的流分析备份支持加密，因此无法关闭。 你应将备份视为敏感数据，并应用相关的访问和数据保护控件作为此基线的一部分。

* [Azure 流分析中的数据保护](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

* [授权访问 Azure 存储中的数据](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产或非生产），并创建命名系统以清楚地标识和分类 Azure 资源，尤其是那些用于处理敏感数据的资源。 你要保证根据 Azure 资源以及事件的发生环境的重要性来设置警报修正的优先级。

* [Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [使用标记组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行练习来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的一方访问了你的数据，Microsoft 将使用安全事件联系人信息来与你取得联系。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

责任：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，可通过 "逻辑应用" 自动触发有关安全警报和建议的响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指南**： 

* [遵循 Microsoft 订婚规则确保你的渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
