---
title: Azure 容器注册表的 Azure 安全基线
description: Azure 容器注册表的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9225cfd9793a84f371387d6450a3dfa80ba74de3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547538"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure 容器注册表的 Azure 安全基线

Azure 容器注册表的 Azure 安全基线包含有助于改进部署安全状态的建议。

此服务的基线取自[Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有关如何使用最佳实践指南在 Azure 上保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虚拟网络上的网络安全组或 Azure 防火墙保护资源

**指南**：Azure 虚拟网络为 Azure 和本地资源提供安全的专用网络。 通过限制从 Azure 虚拟网络访问专用 Azure 容器注册表，可确保只有虚拟网络中的资源访问注册表。 对于跨界方案，还可以配置防火墙规则，以便仅允许从特定 IP 地址进行注册表访问。 从防火墙后面配置防火墙访问规则和服务标记以访问容器注册表。

使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问：https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

配置规则以访问防火墙后面的 Azure 容器注册表：https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 监视和记录 Vnet、子网和 NIC 的配置和流量

**指南**：使用 Azure 安全中心并修复网络保护建议，以帮助保护 Azure 中的网络资源。 启用 NSG 流日志并将日志发送到存储帐户以进行流量审核。

如何启用 NSG 流日志：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

保护您的网络资源：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="13-protect-critical-web-applications"></a>1.3： 保护关键 Web 应用程序

**指南**：不适用。 基准测试适用于托管 Web 应用程序的 Azure 应用服务或计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒绝与已知恶意 IP 地址的通信

**指南**：在虚拟网络上启用 DDoS 标准保护，以保护免受 DDoS 攻击。 使用 Azure 安全中心集成威胁智能拒绝与已知恶意或未使用的 Internet IP 地址的通信。  在启用威胁智能后，在每个组织的网络边界部署 Azure 防火墙，并将其配置为针对恶意网络流量"警报和拒绝"。

您可以使用 Azure 安全中心"及时网络"访问来配置 NSG，以在有限时间内将终结点暴露到已批准的 IP 地址。 此外，使用 Azure 安全中心自适应网络强化来建议基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

如何配置 DDoS 保护：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

如何部署 Azure 防火墙：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

了解 Azure 安全中心集成威胁情报：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

了解 Azure 安全中心自适应网络强化：https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure 安全中心正好及时进行网络访问控制：https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 记录网络数据包和流日志

**指南**：为附加到子网的 NSG 启用网络安全组 （NSG） 流日志，这些日志用于保护 Azure 容器注册表。 您可以将 NSG 流日志记录到 Azure 存储帐户中，以生成流记录。 如果需要调查异常活动，则启用 Azure 网络观察程序数据包捕获。

如何启用 NSG 流日志：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用网络观察程序：https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基于网络的入侵检测/入侵防御系统（IDS/IPS）

**指南**：从 Azure 应用商店中选择支持 IDS/IPS 功能的具有有效负载检查功能的产品/ 如果不需要基于有效负载检查的入侵检测和/或预防，则可以使用具有威胁智能的 Azure 防火墙。 Azure 防火墙威胁基于智能的筛选可以警报和拒绝来自已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

在每个组织的网络边界部署您选择的防火墙解决方案，以检测和/或拒绝恶意流量。

Azure 应用商店：https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

如何部署 Azure 防火墙：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火墙配置警报：https://docs.microsoft.com/azure/firewall/threat-intel


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 应用程序的流量

**指南**：不适用。 基准测试适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低网络安全规则的复杂性和管理开销

**指南**：对于需要访问容器注册表的资源，请使用 Azure 容器注册表服务的虚拟网络服务标记在网络安全组或 Azure 防火墙上定义网络访问控件。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名称"AzureContainerRegistry"，可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

允许按服务标记访问：https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 维护网络设备的标准安全配置

**指南**：使用 Azure 策略定义和实施与 Azure 容器注册表关联的网络资源的标准安全配置。 在"Microsoft.ContainerRegistry"和"Microsoft.Network"命名空间中使用 Azure 策略别名来创建自定义策略以审核或强制执行容器注册表的网络配置。 

可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境工件（如 Azure 资源管理器模板、RBAC 控件和策略）来简化大规模 Azure 部署。 轻松将蓝图应用于新订阅，并通过版本控制微调控制和管理。

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何创建 Azure 蓝图：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文档流量配置规则

**指南**：客户可以使用 Azure 蓝图在单个蓝图定义中打包关键环境工件（如 Azure 资源管理器模板、RBAC 控件和策略），从而简化大规模 Azure 部署。 轻松将蓝图应用于新订阅，并通过版本控制微调控制和管理。

如何创建 Azure 蓝图：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自动工具监控网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置并检测与容器注册表相关的网络资源的更改。 在 Azure 监视器中创建警报，这些警报将在对关键网络资源的更改时触发。

如何查看和检索 Azure 活动日志事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 监视器中创建警报：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心监视**：是

**责任**： 客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的时间同步源

**指南**：Microsoft 维护 Azure 资源的时间源，但是，您可以选择管理计算资源的时间同步设置。

如何为 Azure 计算资源配置时间同步：https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure 安全中心监视**：当前不可用

**责任**： 微软

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全日志管理

**指南**：通过 Azure 监视器引入日志以聚合 Azure 容器注册表生成的安全数据。 在 Azure 监视器中，使用日志分析工作区来查询和执行分析，并使用 Azure 存储帐户进行长期/存档存储。

用于诊断评估和审核的 Azure 容器注册表日志：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：Azure 监视器收集注册表中用户驱动事件的资源日志（以前称为诊断日志）。 收集和使用此数据以审核注册表身份验证事件，并提供注册表项目（如拉取和推送事件）的完整活动跟踪，以便您可以诊断注册表的安全问题。

用于诊断评估和审核的 Azure 容器注册表日志：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 从操作系统收集安全日志

**指南**：不适用。 基准测试适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全日志存储保留

**指南**：在 Azure 监视器中，根据组织的合规性法规设置日志分析工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

如何为日志分析工作区设置日志保留参数：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="26-monitor-and-review-logs"></a>2.6： 监视和查看日志

**指导**：分析和监视 Azure 容器注册表日志是否存在异常行为，并定期查看结果。 使用 Azure 监视器的日志分析工作区查看日志并执行日志数据的查询。

用于诊断评估和审核的 Azure 容器注册表日志：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

了解日志分析工作区：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 监视器中执行自定义查询：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 启用异常活动的警报

**指南**：使用 Azure 日志分析工作区监视和警报安全日志中与 Azure 容器注册表相关的异常事件。

用于诊断评估和审核的 Azure 容器注册表日志：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

如何对日志分析日志数据发出警报：https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反恶意软件日志记录

**指南**：不适用。 Azure 容器注册表不处理或生成与恶意软件相关的日志。


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="29-enable-dns-query-logging"></a>2.9： 启用 DNS 查询日志记录

**指南**：不适用。 Azure 容器注册表是终结点，不启动通信，服务不查询 DNS。

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="210-enable-command-line-audit-logging"></a>2.10： 启用命令行审核日志记录

**指南**：不适用。 基准测试适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 维持管理帐户的清单

**指南**： Azure 活动目录 （Azure AD） 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行临时查询以发现属于管理组成员的帐户。

对于每个 Azure 容器注册表，跟踪是启用还是禁用内置管理帐户。 不使用时禁用帐户。

如何使用 PowerShell 在 Azure AD 中获取目录角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 Azure AD 中的目录角色成员：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure 容器注册表管理员帐户：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

**指南**： Azure 活动目录 （Azure AD） 没有默认密码的概念。 需要密码的其他 Azure 资源强制创建具有复杂性要求和最小密码长度的密码，具体取决于服务。 您负责可能使用默认密码的第三方应用程序和应用商店服务。

如果启用了 Azure 容器注册表的默认管理员帐户，则会自动创建复杂密码，并应轮换。 不使用时禁用帐户。

Azure 容器注册表管理员帐户：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用专用管理帐户

**指导**：围绕使用专用管理帐户创建标准操作程序。 使用 Azure 安全中心标识和访问管理监视管理帐户数。

此外，创建过程以启用容器注册表的内置管理帐户。 不使用时禁用帐户。

了解 Azure 安全中心标识和访问：https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure 容器注册表管理员帐户：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活动目录的单一登录 （SSO）

**指南**：在可能的情况下，请使用 Azure 活动目录 SSO，而不是配置每个服务的各个独立凭据。 使用 Azure 安全中心标识和访问管理建议。

对于对容器注册表的单独访问，请使用与 Azure 活动目录集成的单个登录名。

使用 Azure AD 了解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

个人登录到容器注册表：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证

**指南**：启用 Azure 活动目录 （Azure AD） 多重身份验证 （MFA）， 并遵循 Azure 安全中心标识和访问管理建议。

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心内监视标识和访问：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用具有 MFA 的 PAW（特权访问工作站）来登录和配置 Azure 资源。

了解特权访问工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

**指南**：当环境中发生可疑或不安全活动时，请使用 Azure 活动目录 （Azure AD） 安全报告生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

如何识别标记为有风险活动的 Azure AD 用户：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何监视 Azure 安全中心中的用户身份和访问活动：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

**指南**：使用 Azure 活动目录 （Azure AD） 作为中央身份验证和授权系统。 Azure AD 对静态和传输中的数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行盐渍、哈希和安全地存储。

如何创建和配置 Azure AD 实例：https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期审查和协调用户访问

**指南**： Azure 活动目录 （Azure AD） 提供日志以帮助发现陈旧的帐户。 此外，使用 Azure 标识访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续访问。

了解 Azure AD 报告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问审核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 监视访问已停用帐户的尝试

**指南**：您可以访问 Azure 活动目录 （Azure AD） 登录活动、审核和风险事件日志源，这允许您与任何安全信息和事件管理 （SIEM） /监视工具集成。

您可以通过为 Azure 活动目录用户帐户创建诊断设置并将审核日志和登录日志发送到日志分析工作区来简化此过程。 您可以在日志分析工作区中配置所需的警报。

如何将 Azure 活动日志集成到 Azure 监视器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

**指南**：使用 Azure 活动目录 （Azure AD） 风险和身份保护功能配置自动响应，以检测与用户身份相关的可疑操作。 

如何查看 Azure AD 风险登录：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用身份保护风险策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

**指导**：不可用;Azure 容器注册表当前不支持客户密码箱。

客户密码箱支持服务列表：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

**指南**：使用资源标记帮助跟踪存储或处理敏感信息的 Azure 容器注册表。

在注册表中标记和版本容器映像或其他项目，并锁定图像或存储库，以帮助跟踪存储或处理敏感信息的图像。

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

标记和版本控制容器映像的建议：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

在 Azure 容器注册表中锁定容器映像：https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的容器注册表、订阅和/或管理组。 存储或处理敏感数据的资源应足够隔离。

资源应由虚拟网络或子网分隔，标记得当，并由网络安全组 （NSG） 或 Azure 防火墙保护。

如何创建其他 Azure 订阅：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问：https://docs.microsoft.com/azure/container-registry/container-registry-vnet

如何创建具有安全配置的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火墙配置警报或警报和拒绝：

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure 安全中心监视**：当前不可用

**责任**： 客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

**指南**：在网络外围部署自动工具，用于监视敏感信息的未授权传输，并阻止此类传输，同时提醒信息安全专业人员。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：当前不可用

**责任**： 共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：确保连接到 Azure 容器注册表的任何客户端能够协商 TLS 1.2 或更高。 默认情况下，Microsoft Azure 资源协商 TLS 1.2。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

了解使用 Azure 传输中的加密：https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure 安全中心监视**：是

**责任**： 共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

**指南**：Azure 容器注册表尚不可用数据标识、分类和损失预防功能。 如果需要符合性，则实施第三方解决方案。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

**指南**：使用 Azure 活动目录 （Azure AD） RBAC 控制对 Azure 容器注册表中的数据和资源的访问。 

如何在 Azure 中配置 RBAC：https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure 容器注册表角色和权限：https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

**指南**：如果需要符合计算资源，则实施第三方工具，如基于主机的自动数据丢失防护解决方案，以便强制访问数据，即使数据从系统复制。

对于由 Microsoft 管理的基础平台，Microsoft 将所有客户内容视为敏感内容，并竭尽全力防止客户数据丢失和暴露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已经实施并维护了一套强大的数据保护控制和功能。

了解 Azure 中的客户数据保护：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

**指南**：对所有 Azure 资源使用静态加密。 默认情况下，Azure 容器注册表中的所有数据都使用 Microsoft 管理的密钥进行静态加密。

了解 Azure 中静态加密：https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure 容器注册表中的客户管理密钥：https://aka.ms/acr/cmk



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

**指南**：Azure 监视器收集注册表中用户驱动事件的资源日志（以前称为诊断日志）。 收集和使用此数据以审核注册表身份验证事件，并提供注册表项目（如拉取和拉取事件）的完整活动跟踪，以便您可以诊断注册表的操作问题。

用于诊断评估和审核的 Azure 容器注册表日志：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 运行自动漏洞扫描工具

**指南**：按照 Azure 安全中心关于对容器映像执行漏洞评估的建议进行操作。 可以选择从 Azure 应用商店部署第三方解决方案以执行映像漏洞评估。

如何实施 Azure 安全中心漏洞评估建议：https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure 容器注册表与安全中心集成（预览）：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 安全中心监视**：是

**责任**： 客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动化操作系统修补程序管理解决方案

**指南**：Microsoft 对支持 Azure 容器注册表的基础系统执行修补程序管理。

当检测到操作系统和其他修补程序对基本映像的更新时，自动执行容器映像更新。

关于 Azure 容器注册表任务的基本映像更新：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自动化的第三方软件修补程序管理解决方案

**指南**：您可以使用第三方解决方案来修补应用程序映像。  此外，还可以运行 Azure 容器注册表任务，以便根据安全修补程序或基本映像中的其他更新自动更新容器注册表中的应用程序映像。

关于 ACR 任务的基本映像更新：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比较背对背漏洞扫描

**指南**：将 Azure 容器注册表 （ACR） 与 Azure 安全中心集成，以便定期扫描容器映像以访问漏洞。 可以选择从 Azure 应用商店部署第三方解决方案以执行定期映像漏洞扫描。

Azure 容器注册表与安全中心集成（预览）：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用风险评级流程确定修复已发现漏洞的优先级

**指南**：将 Azure 容器注册表 （ACR） 与 Azure 安全中心集成，以便定期扫描容器映像以寻找漏洞并对风险进行分类。 可以选择从 Azure 应用商店部署第三方解决方案，以执行定期映像漏洞扫描和风险分类。

Azure 容器注册表与安全中心集成（预览）：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="inventory-and-asset-management"></a>清单和资产管理

*有关详细信息，请参阅[安全控制：库存和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 资产发现

**指南**：使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。  请确保租户中的适当（读取）权限，并枚举订阅中的所有 Azure 订阅以及资源。

尽管可以通过资源图发现经典 Azure 资源，但强烈建议今后创建和使用 Azure 资源管理器资源。

如何使用 Azure 资源图创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

了解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

**指南**：Azure 容器注册表维护元数据，包括注册表中图像的标记和清单。 遵循标记工件的建议做法。

关于注册表、存储库和图像：https://docs.microsoft.com/azure/container-registry/container-registry-concepts

标记和版本控制容器映像的建议：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

**指南**：Azure 容器注册表维护元数据，包括注册表中图像的标记和清单。 遵循标记工件的建议做法。

关于注册表、存储库和图像：https://docs.microsoft.com/azure/container-registry/container-registry-concepts

标记和版本控制容器映像的建议：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指导**：您需要根据组织需要创建已批准的 Azure 资源的清单。  

**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

**指南**：使用 Azure 策略对可在订阅中创建的资源类型施加限制。

使用 Azure 资源图查询/发现其订阅中的资源。  确保环境中的所有 Azure 资源都获得批准。

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 图形创建查询：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中未经批准的软件应用程序

**指导**：分析和监视 Azure 容器注册表日志是否存在异常行为，并定期查看结果。 使用 Azure 监视器的日志分析工作区查看日志并执行日志数据的查询。

用于诊断评估和审核的 Azure 容器注册表日志：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

了解日志分析工作区：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 监视器中执行自定义查询：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

**指南**：Azure 自动化在工作负载和资源的部署、操作和停用期间提供完全控制。  您可以实现自己的解决方案来删除未经授权的 Azure 资源。 Azure 自动化简介：https://docs.microsoft.com/azure/automation/automation-intro


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="68-use-only-approved-applications"></a>6.8： 仅使用已批准的应用程序

**指南**：不适用。 基准测试是为计算资源设计的。


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="69-use-only-approved-azure-services"></a>6.9： 仅使用已批准的 Azure 服务

**指南**：利用 Azure 策略来限制可以在环境中预配哪些服务。

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒绝特定资源类型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="610-implement-approved-application-list"></a>6.10： 实施已批准的申请列表

**指南**：不适用。 基准测试是为计算资源设计的。



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制用户通过脚本与 AzureResource Manager 交互的能力

**指南**：使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

如何配置条件访问以阻止对 Azure 资源管理器的访问：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制用户在计算资源中执行脚本的能力

**指南**：使用操作系统特定的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

例如，如何控制 Windows 环境中的 PowerShell 脚本执行：https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或逻辑上隔离高风险应用程序

**指南**：业务操作所需的软件，但可能会给组织带来更高的风险，应隔离在自己的虚拟机和/或虚拟网络中，并且使用 Azure 防火墙或网络安全组进行充分保护。

如何创建虚拟网络：https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建具有安全配置的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 Azure 策略或 Azure 安全中心维护所有 Azure 资源的安全配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的操作系统配置

**指南**：利用 Azure 安全中心建议"修复虚拟机上的安全配置中的漏洞"来维护所有计算资源的安全配置。

如何监视 Azure 安全中心建议：https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何修复 Azure 安全中心建议：https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在整个 Azure 资源中强制实施安全设置。

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 维护安全的操作系统配置

**指南**：不适用。 基准测试适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**： 共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源的配置

**指南**：如果使用自定义 Azure 策略定义，请使用 Azure 存储库安全地存储和管理代码。

如何在 Azure DevOps 中存储代码：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存储库文档：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

**指南**：不适用。 基准测试适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系统配置管理工具

**指导**：使用 Azure 策略来警报、审核和强制执行系统配置。 此外，开发用于管理策略异常的流程和管道。

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用。 基准测试适用于计算资源。


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实现自动化配置监视

**指南**：使用 Azure 安全中心对 Azure 资源执行基线扫描。

使用 Azure 策略对可在订阅中创建的资源类型施加限制。

如何修复 Azure 安全中心中的建议：https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

使用 Azure 策略审核 Azure 容器注册表的合规性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 对操作系统实施自动配置监控

**指南**：不适用。 基准测试适用于计算资源。


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 机密

**指南**：将托管服务标识与 Azure 密钥保管库结合使用，以简化和保护云应用程序的秘密管理。

如何与 Azure 托管标识集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建密钥保管库：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供密钥保管库身份验证：https://docs.microsoft.com/azure/key-vault/managed-identity

在 Azure 容器注册表任务中使用 Azure 托管标识：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自动地管理身份

**指南**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 托管标识允许您对支持 Azure AD 身份验证的任何服务（包括密钥保管库）进行身份验证，而无需在代码中进行任何凭据。

如何配置托管标识：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

使用托管标识对 Azure 容器注册表进行身份验证：https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

**指南**：实现凭据扫描程序以标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。

如何设置凭据扫描程序：https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反恶意软件

**指南**：将 Microsoft 反恶意软件用于 Azure 云服务和虚拟机，以持续监视和防御资源。 对于 Linux，请使用第三方反恶意软件解决方案。

如何为云服务和虚拟机配置 Microsoft 反恶意软件：https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上载到非计算 Azure 资源的预扫描文件

**指南**：在支持 Azure 服务的基础主机上启用 Microsoft 反恶意软件（例如，Azure 容器注册表），但它不在客户内容上运行。

预扫描上载到非计算 Azure 资源（如应用服务、数据湖存储、Blob 存储等）的任何文件。


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 确保更新反恶意软件和签名

**指南**：不适用。 基准测试适用于计算资源。 Microsoft 处理底层平台的反恶意软件。


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 确保定期自动备份

**指南**：Microsoft Azure 容器注册表中的数据始终会自动复制，以确保持久性和高可用性。 Azure 容器注册表复制数据，以便数据免受计划内和计划外事件

可选地异地复制容器注册表，以在多个 Azure 区域中维护注册表副本。 

Azure 容器注册表中的异地复制：https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 执行完整的系统备份并备份任何客户托管密钥

**指南**：通过从一个注册表导入另一个注册表，可以选择备份容器映像。

使用 Azure 命令行工具或 SDK 在 Azure 密钥保管库中备份客户管理的密钥。

将容器映像导入容器注册表：https://docs.microsoft.com/azure/container-registry/container-registry-import-images

如何在 Azure 中备份密钥保管库密钥：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户托管密钥

**指南**：使用 Azure 命令行工具或 SDK 测试 Azure 密钥保管库中备份的客户托管密钥的恢复。

如何在 Azure 中还原 Azure 密钥保管库密钥：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理密钥

**指南**：您可以在 Azure 密钥保管库中启用软删除，以保护密钥免受意外或恶意删除。

如何在密钥保管库中启用软删除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指导**：为您的组织构建事件响应指南。 确保有书面事件响应计划，定义人员的所有角色以及事件处理/管理阶段，从检测到事件后审查。

如何在 Azure 安全中心内配置工作流自动化：https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有关构建您自己的安全事件响应流程的指导：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微软安全响应中心的事件剖析：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建他们自己的事件响应计划：https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 创建事件评分和优先级处理过程

**指南**：Azure 安全中心为每个警报分配严重性，以帮助您优先处理应首先调查哪些警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，清楚地标记订阅（对于前 生产，非 prod），并创建命名系统以明确识别和分类 Azure 资源。


**Azure 安全中心监视**：是

**责任**： 客户

### <a name="103-test-security-response-procedures"></a>10.3： 测试安全响应程序

**指导**：进行练习，以常规节奏测试系统的事件响应能力。 找出薄弱环节和差距，并根据需要修订计划。

请参阅 NIST 出版物：IT 计划和功能测试、培训和锻炼计划指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并配置安全事件的警报通知

**指南**：如果 Microsoft 安全响应中心 （MSRC） 发现客户的数据已被非法或未经授权的方访问，Microsoft 将使用安全事件联系信息与您联系。  事后查看事件，以确保问题得到解决。

如何设置 Azure 安全中心安全联系人：https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 将安全警报纳入事件响应系统

**指南**：使用"连续导出"功能导出 Azure 安全中心警报和建议。 "连续导出"允许您手动或持续、持续的方式导出警报和建议。 您可以使用 Azure 安全中心数据连接器来流式传输警报哨兵。

如何配置连续导出：https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure 安全中心监视**：不适用

**责任**： 客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，通过安全警报和建议上的"逻辑应用"自动触发响应。

如何配置工作流自动化和逻辑应用：https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure 安全中心监视**：不适用

**责任**： 客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在 60 天内补救所有关键安全发现

**指南**： 遵循 Microsoft 参与规则，确保您的渗透测试不违反 Microsoft 策略：https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有关 Microsoft 针对 Microsoft 管理的云基础架构、服务和应用程序进行红色团队和实时站点渗透测试的战略和执行的详细信息，如下所示：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure 安全中心监视**：不适用

**责任**： 共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线的更多信息](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
