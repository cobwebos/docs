---
title: Azure 容器注册表的 Azure 安全基线
description: Azure 容器注册表的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3fcbc386c60611493912fdfb17226490549cdc53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89396805"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure 容器注册表的 Azure 安全基线

Azure 容器注册表的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指导**：Azure 虚拟网络为 Azure 资源和本地资源提供安全的专用网络。 通过施加限制措施，只允许从 Azure 虚拟网络访问专用 Azure 容器注册表，可以确保只有该虚拟网络中的资源可以访问该注册表。 对于跨界方案，你还可以配置防火墙规则，只允许从特定 IP 地址访问注册表。 配置防火墙访问规则和服务标记，以便从防火墙后面访问你的容器注册表。

使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问权限： https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

配置规则以访问防火墙后面的 Azure 容器注册表： https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指导**：使用 Azure 安全中心并修正网络保护建议来帮助保护 Azure 中的网络资源。 启用 NSG 流日志，并将日志发送到存储帐户中进行流量审核。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

保护你的网络资源： https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：不适用。 基准适用于 Azure 应用服务或托管 Web 应用程序的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：在虚拟网络上启用 DDoS 标准保护，以防范 DDoS 攻击。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。  在组织的每个网络边界上部署 Azure 防火墙，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

可以使用 Azure 安全中心实时网络访问，将 NSG 配置为只能在有限时间内将终结点公开给已批准的 IP 地址。 另请使用 Azure 安全中心自适应网络强化，推荐基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

如何配置 DDoS 保护：  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

如何部署 Azure 防火墙： https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

了解 Azure 安全中心集成的威胁情报： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

了解 Azure 安全中心自适应网络强化： https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure 安全中心实时网络访问控制： https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指导**：为附加到子网的、用于保护 Azure 容器注册表的网络安全组 (NSG) 启用 NSG 流日志。 你可以将 NSG 流日志记录到 Azure 存储帐户中，以生成流记录。 如果需要调查异常活动，请启用 Azure 网络观察程序数据包捕获。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用网络观察程序： https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能。 如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用包含威胁情报功能的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测和/或拒绝恶意流量。

Azure 市场： https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

如何部署 Azure 防火墙： https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火墙配置警报： https://docs.microsoft.com/azure/firewall/threat-intel


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：不适用。 基准适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：对于需要访问容器注册表的资源，请在网络安全组或 Azure 防火墙中使用 Azure 容器注册表服务的虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称“AzureContainerRegistry”，可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

按服务标记允许访问： https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure 容器注册表关联的网络资源定义和实施标准安全配置。 在“Microsoft.ContainerRegistry”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施你的容器注册表的网络配置。 

可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控件和策略）来简化大规模的 Azure 部署。 轻松将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何创建 Azure 蓝图： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：客户可以使用 azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 azure 资源管理器模板、azure RBAC 控件和策略）来简化大规模的 azure 部署。 轻松将蓝图应用到新的订阅，并通过版本控制来微调控制措施和管理。

如何创建 Azure 蓝图： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与容器注册表相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

如何查看和检索 Azure 活动日志事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure Monitor 中创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：Microsoft 维护 Azure 资源的时间源，但你可以选择管理计算资源的时间同步设置。

如何为 Azure 计算资源配置时间同步： https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure 安全中心监视**：目前不可用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：通过 Azure Monitor 引入日志来聚合由 Azure 容器注册表生成的安全数据。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

用于诊断评估和审核的 Azure 容器注册表日志： https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：Azure Monitor 针对注册表中的用户驱动事件收集资源日志（前称为诊断日志）。 收集并使用这些数据来审核注册表身份验证事件，并提供有关注册表项目（例如拉取和推送事件）的完整活动跟踪，以便诊断注册表的安全问题。

用于诊断评估和审核的 Azure 容器注册表日志： https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用。 基准适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

如何为 Log Analytics 工作区设置日志保留参数： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：分析和监视 Azure 容器注册表日志，看其是否存在异常行为，可以定期查看结果。 使用 Azure Monitor 的 Log Analytics 工作区来查看日志并执行针对日志数据的查询。

用于诊断评估和审核的 Azure 容器注册表日志： https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

了解 Log Analytics 工作区： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure Monitor 中执行自定义查询： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure Log Analytics 工作区在与 Azure 容器注册表相关的安全日志和事件中监视异常活动并对其发出警报。

用于诊断评估和审核的 Azure 容器注册表日志： https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

如何针对 Log Analytics 日志数据发出警报： https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用。 Azure 容器注册表不会处理或生成与反恶意软件相关的日志。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用。 Azure 容器注册表是一个终结点，不会启动通信，并且该服务不查询 DNS。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用。 基准适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (Azure AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

对于每个 Azure 容器注册表，请对是已启用还是已禁用内置管理员帐户进行跟踪。 不使用该帐户时请将其禁用。

如何使用 PowerShell 获取 Azure AD 中的目录角色： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 Azure AD 中目录角色的成员： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure 容器注册表管理员帐户： https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：Azure Active Directory (Azure AD) 没有默认密码的概念。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度要求的密码，这些要求因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

如果启用了 Azure 容器注册表的默认管理员帐户，则会自动创建复杂密码并对其进行轮换。 不使用该帐户时请将其禁用。

Azure 容器注册表管理员帐户： https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

另外，请创建相关过程来启用容器注册表的内置管理员帐户。 不使用该帐户时请将其禁用。

了解 Azure 安全中心标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure 容器注册表管理员帐户： https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：请尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

若要单独访问容器注册表，请使用与 Azure Active Directory 集成的单个登录名。

了解 Azure AD 的 SSO：  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

到容器注册表的单独登录： https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory (Azure AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理的建议。

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心监视标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了 MFA 的 PAW（特权访问工作站）来登录并配置 Azure 资源。

了解特权访问工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 安全中心监视**：空值

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，请使用 Azure Active Directory (Azure AD) 安全报告来生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

如何确定标记为有风险活动 Azure AD 用户：  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何在 Azure 安全中心监视用户的标识和访问活动： https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

如何在 Azure 中配置命名位置：  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

如何创建和配置 Azure AD 实例： https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (Azure AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

了解 Azure AD 报告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问评审： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的企图

**指导**：你有权访问 Azure Active Directory (Azure AD) 登录活动、审核和风险事件日志源，因此可以与任何安全信息和事件管理 (SIEM) /监视工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

如何将 Azure 活动日志集成到 Azure Monitor：  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure Active Directory (Azure AD) 风险和标识保护功能配置对检测到的与用户标识相关的可疑操作的自动响应。 

如何查看 Azure AD 风险登录： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用 Identity Protection 风险策略： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不可用；Azure 容器注册表当前不支持客户密码箱。

客户密码箱支持的服务列表： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



Azure 安全中心监视：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用资源标记可以帮助跟踪用于存储或处理敏感信息的 Azure 容器注册表。

在注册表中对容器映像或其他项目进行标记和版本控制并锁定映像或存储库，以便跟踪可存储或处理敏感信息的映像。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

有关对容器映像进行标记和版本控制的建议： https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

锁定 Azure 容器注册表中的容器映像： https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的容器注册表、订阅和/或管理组。 存储或处理敏感数据的资源应当充分隔离。

资源应当按虚拟网络或子网进行分隔，相应地进行标记，并由网络安全组 (NSG) 或 Azure 防火墙提供保护。

如何创建其他 Azure 订阅：  https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问权限： https://docs.microsoft.com/azure/container-registry/container-registry-vnet

如何创建采用某个安全配置的 NSG： https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”：

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：在网络外围部署一个自动化工具，用于监视敏感信息的未授权传输，并阻止此类传输，同时提醒信息安全专业人员。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：确保连接到 Azure 容器注册表的任何客户端能够协商 TLS 1.2 或更高版本。 默认情况下，Microsoft Azure 资源会协商 TLS 1.2。

请按照 Azure 安全中心的建议，了解静态加密和传输中加密（如果适用）。

了解 Azure 的传输中加密： https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 容器注册表。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 azure RBAC)  (azure 基于角色的访问控制来控制对 azure 容器注册表中的数据和资源的访问。 

如何配置 Azure RBAC：  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure 容器注册表角色和权限： https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：如果需要在计算资源上确保符合性，则实施第三方工具（如基于主机的自动数据丢失防护解决方案），以便对数据强制实施访问控制，即使数据从系统复制也是如此。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：在所有 Azure 资源上使用静态加密。 默认情况下，Azure 容器注册表中的所有数据都使用 Microsoft 托管密钥进行静态加密。

了解 Azure 中的静态加密： https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure 容器注册表中客户管理的密钥： https://aka.ms/acr/cmk



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：Azure Monitor 针对注册表中的用户驱动事件收集资源日志（前称为诊断日志）。 收集并使用这些数据来审核注册表身份验证事件，并提供有关注册表项目（例如拉取和推送事件）的完整活动跟踪，以便诊断注册表的操作问题。

用于诊断评估和审核的 Azure 容器注册表日志： https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：遵循 Azure 安全中心的在容器映像上执行漏洞评估的建议。 （可选）从 Azure 市场部署第三方解决方案，用于执行映像漏洞评估。

如何实施 Azure 安全中心漏洞评估建议： https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure 容器注册表与安全中心的集成（预览版）： https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**： Microsoft 在支持 Azure 容器注册表的底层系统上执行修补程序管理。

当检测到来自操作系统和其他修补程序的基础映像更新时，将自动执行容器映像更新。

关于 Azure 容器注册表任务的基础映像更新： https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指导**：可以使用第三方解决方案来修补应用程序映像。  此外，还可以运行 Azure 容器注册表任务，根据基础映像中的安全修补程序或其他更新自动更新容器注册表中的应用程序映像。

关于 ACR 任务的基础映像更新： https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：将 Azure 容器注册表 (ACR) 与 Azure 安全中心集成，以便定期扫描容器映像中的漏洞。 （可选）从 Azure 市场部署第三方解决方案，用于执行定期的映像漏洞扫描。

Azure 容器注册表与安全中心的集成（预览版）： https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure 安全中心监视**：是

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：将 Azure 容器注册表 (ACR) 与 Azure 安全中心集成，以便定期扫描容器映像中的漏洞并对风险进行分类。 （可选）从 Azure 市场部署第三方解决方案，用于执行定期的映像漏洞扫描和风险分类。

Azure 容器注册表与安全中心的集成（预览版）： https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 安全中心监视**：空值

**责任**：客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。  确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

如何使用 Azure Resource Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

了解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：Azure 容器注册表为注册表中的映像维护元数据（包括标记和清单）。 请遵循用于标记项目的建议做法。

关于注册表、存储库和映像： https://docs.microsoft.com/azure/container-registry/container-registry-concepts

有关对容器映像进行标记和版本控制的建议： https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：Azure 容器注册表为注册表中的映像维护元数据（包括标记和清单）。 请遵循用于标记项目的建议做法。

关于注册表、存储库和映像： https://docs.microsoft.com/azure/container-registry/container-registry-concepts

有关对容器映像进行标记和版本控制的建议： https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指导**：你需要根据组织需求创建已批准的 Azure 资源的清单。  

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 查询/发现订阅中的资源。  确保环境中存在的所有 Azure 资源已获得批准。

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：分析和监视 Azure 容器注册表日志，看其是否存在异常行为，可以定期查看结果。 使用 Azure Monitor 的 Log Analytics 工作区来查看日志并执行针对日志数据的查询。

用于诊断评估和审核的 Azure 容器注册表日志： https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

了解 Log Analytics 工作区： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure Monitor 中执行自定义查询： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。  你可以实施自己的解决方案来删除未经授权的 Azure 资源。 Azure 自动化简介： https://docs.microsoft.com/azure/automation/automation-intro


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用。 基准设计用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：利用 Azure Policy 限制可在环境中预配的服务。

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Policy 拒绝特定的资源类型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指导**：不适用。 基准设计用于计算资源。



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的功能

**指导**：使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

如何配置条件访问以阻止访问 Azure 资源管理器： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

例如，如何在 Windows 环境中控制 PowerShell 脚本执行： https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：业务运营所需的软件可能会给组织带来更高的风险，应将其隔离在自己的虚拟机和/或虚拟网络中，并通过 Azure 防火墙或网络安全组进行充分的保护。

如何创建虚拟网络： https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建采用安全配置的 NSG： https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 或 Azure 安全中心来维护所有 Azure 资源的安全配置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：利用 Azure 安全中心建议“修复虚拟机上安全配置中的漏洞”，维护所有计算资源上的安全配置。

如何监视 Azure 安全中心建议： https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何修正 Azure 安全中心建议： https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure Policy 效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用。 基准适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用自定义 Azure 策略定义，请使用 Azure Repos 安全地存储和管理你的代码。

如何在 Azure DevOps 中存储代码：  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文档：  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用。 基准适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指导**：使用 Azure 策略针对系统配置发出警报，以及审核和强制实施系统配置。 另外，开发一个用于管理策略例外的流程和管道。

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用。 基准适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指导**：使用 Azure 安全中心对 Azure 资源执行基线扫描。

使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

如何在 Azure 安全中心修正建议： https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

使用 Azure Policy 审核 Azure 容器注册表的合规性： https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用。 基准适用于计算资源。


**Azure 安全中心监视**：是

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

如何与 Azure 托管标识集成：  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何对 Key Vault 进行身份验证： https://docs.microsoft.com/azure/key-vault/general/authentication

如何分配 Key Vault 访问策略： https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

在 Azure 容器注册表任务中使用 Azure 托管标识： https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

如何配置托管标识： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

使用托管标识向 Azure 容器注册表进行身份验证： https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

如何设置凭据扫描程序： https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：使用适用于 Azure 云服务和虚拟机的 Microsoft Antimalware 来持续监视和保护资源。 对于 Linux，请使用第三方反恶意软件解决方案。

如何为云服务和虚拟机配置 Microsoft 反恶意软件：  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：在支持 azure 服务的基础主机上启用了 Microsoft 反恶意软件 (例如，Azure 容器注册表) ，但是不会在客户内容上运行。

预扫描任何上传到非计算 Azure 资源（例如应用服务、Data Lake Storage、Blob 存储等）的文件。


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：不适用。 基准适用于计算资源。 Microsoft 处理底层平台的反恶意软件。


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：始终自动复制 Microsoft Azure 容器注册表中的数据以确保持久性和高可用性。 Azure 容器注册表会复制你的数据，以便在发生计划内和计划外事件时保护你的数据

（可选）异地复制容器注册表，以在多个 Azure 区域中维护注册表副本。 

Azure 容器注册表中的异地复制： https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：（可选）通过将容器映像从一个注册表导入到另一个注册表来备份它们。

使用 Azure 命令行工具或 SDK 在 Azure Key Vault 中备份客户管理的密钥。

将容器映像导入到容器注册表： https://docs.microsoft.com/azure/container-registry/container-registry-import-images

如何在 Azure 中备份密钥保管库密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：使用 Azure 命令行工具或 SDK 在 Azure Key Vault 中测试已备份的客户管理的密钥的还原。

如何在 Azure 中还原 Azure Key Vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：你可以在 Azure Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

如何在 Key Vault 中启用“软删除”： https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员的所有职责，以及事件处理/管理从检测到事件后审查的各个阶段。

如何在 Azure 安全中心配置工作流自动化： https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

关于建立自己的安全事件响应流程的指南： https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全响应中心事件解析：  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划： https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：Azure 安全中心为每条警报分配严重性，方便你根据优先级来确定应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。


**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities（IT 规划和功能的测试、培训与演练计划指南）： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。  事后审查事件，确保问题得到解决。

如何设置 Azure 安全中心安全联系人： https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

如何配置连续导出：  https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure Sentinel：  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

如何配置工作流自动化和逻辑应用：  https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题

**指南**：遵循 Microsoft 订婚规则确保你的渗透测试不违反 Microsoft 政策：  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

可在以下位置找到有关 Microsoft 管理的云基础结构、服务和应用程序的 Microsoft 策略、红组和实时站点渗透测试的详细信息：  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
