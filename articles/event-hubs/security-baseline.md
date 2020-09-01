---
title: 事件中心的 Azure 安全基线
description: 事件中心的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a6beb1023b3c934cdbb4b7dc479d87e23a7c96be
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230358"
---
# <a name="azure-security-baseline-for-event-hubs"></a>事件中心的 Azure 安全基线

事件中心的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全性](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指导**：通过将事件中心与虚拟网络服务终结点集成，可从绑定到虚拟网络的工作负载（例如虚拟机）安全地访问消息传递功能，同时在两端保护网络流量路径。

绑定到至少一个虚拟网络子网服务终结点后，相应的事件中心命名空间将不再接受来自经授权的虚拟网络子网以外的任何位置的流量。 从虚拟网络的角度来看，通过将事件中心命名空间绑定到服务终结点，可配置从虚拟网络子网到消息传递服务的独立网络隧道。 

也可创建一个专用终结点，该终结点是一个网络接口，可以使用 Azure 专用链接服务将你以私密且安全的方式连接到 Azure 事件中心服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 

还可以使用防火墙保护 Azure 事件中心命名空间。 Azure 事件中心支持使用基于 IP 的访问控制来提供入站防火墙支持。 可以通过 Azure 门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell 设置防火墙规则。

如何将虚拟网络服务终结点与 Azure 事件中心配合使用： https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

有关详细信息，请参阅“将 Azure 事件中心与 Azure 专用链接集成”： https://docs.microsoft.com/azure/event-hubs/private-link-service 。

在事件中心命名空间中启用虚拟网络集成和防火墙： https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

如何为 Azure 事件中心命名空间配置 IP 防火墙规则： https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指导**：使用 Azure 安全中心并遵循网络保护建议来帮助保护 Azure 中的事件中心资源。 如果使用 Azure 虚拟机来访问事件中心，请启用网络安全组 (NSG) 流日志，并将日志发送到存储帐户进行流量审核。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

了解 Azure 安全中心提供的网络安全： https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：为了防范分布式拒绝服务 (DDoS) 攻击，请在与事件中心关联的虚拟网络上启用 DDoS 保护标准。 根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

如何配置 DDoS 防护：[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

详细了解 Azure 安全中心集成的威胁情报： https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指导**：如果使用 Azure 虚拟机来访问事件中心，请启用网络安全组 (NSG) 流日志，并将日志发送到存储帐户进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

启用网络观察程序数据包捕获（如果调查异常活动时有此需要）。

如何启用 NSG 流日志： https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何启用和使用流量分析： https://docs.microsoft.com/azure/network-watcher/traffic-analytics

如何启用网络观察程序： https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：如果使用 Azure 虚拟机访问事件中心，请从 Azure 市场中选择一种产品/服务，该产品/服务应支持包含有效负载检查功能的 ID/IPS 功能。 如果你的组织不需要基于有效负载检查的入侵检测和/或防护，你可以使用 Azure 事件中心的内置防火墙功能。 可以使用防火墙规则，将对事件中心命名空间的访问限制为有限的 IP 地址范围或特定的 IP 地址。

Azure 市场：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

如何在事件中心内为指定 IP 地址添加防火墙规则：

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：不适用。此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为与 Azure 事件中心命名空间关联的网络资源定义和实施标准安全配置。 在“Microsoft.EventHub”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施事件中心命名空间的网络配置。 还可以使用与 Azure 事件中心相关的内置策略定义，例如：

- 事件中心应使用虚拟网络服务终结点。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

适用于事件中心命名空间的 Azure 内置策略： https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



适用于网络的 Azure Policy 示例： https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



如何创建 Azure 蓝图： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与你的事件中心关联的、与网络安全和流量流相关的虚拟网络和其他资源使用标记。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 事件中心相关的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

如何查看和检索 Azure 活动日志事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure Monitor 中创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：不适用；Microsoft 会为日志中的时间戳维护用于 Azure 资源（例如 Azure 事件中心）的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：在 Azure Monitor 中，配置与活动日志和事件中心诊断设置中的事件中心相关的日志，以将日志发送到要查询的 Log Analytics 工作区或要进行长期存档存储的存储帐户。

如何配置 Azure 事件中心的诊断设置： https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

了解 Azure 活动日志： https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：启用 Azure 事件中心命名空间的诊断设置。 有三种类别的 Azure 事件中心诊断设置：存档日志、操作日志和自动缩放日志。 启用操作日志可捕获事件中心操作期间发生的事件的相关信息，具体而言，就是操作类型（包括事件中心创建操作）、所使用的资源和操作状态。

此外，你可以启用 Azure 活动日志诊断设置并将其发送到 Azure 存储帐户、事件中心或 Log Analytics 工作区。 可以通过活动日志了解在 Azure 事件中心和其他资源上执行的操作。 可以通过活动日志确定对 Azure 事件中心命名空间执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

如何启用 Azure 事件中心的诊断设置： https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何启用 Azure 活动日志的诊断设置： https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 安全中心监视**：是

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期，以捕获和查看与事件中心相关的事件。

如何为 Log Analytics 工作区设置日志保留参数： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：分析和监视日志中的异常行为，并定期查看与事件中心相关的结果。 使用 Azure Monitor 的 Log Analytics 检查日志并对日志数据执行查询。 或者，可以将数据启用并加入 Azure Sentinel 或第三方 SIEM。
 

有关 Log Analytics 工作区的详细信息： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure Monitor 中执行自定义查询： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报

**指导**：在 Azure Monitor 中，配置与活动日志和事件中心诊断设置中的 Azure 事件中心相关的日志，以将日志发送到要查询的 Log Analytics 工作区或要进行长期存档存储的存储帐户。 使用 Log Analytics 工作区针对安全日志和事件中的异常活动创建警报。

或者，你可以将和机载数据启用到 Azure Sentinel。 

了解 Azure 活动日志： https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

如何配置 Azure 事件中心的诊断设置： https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

如何针对 Log Analytics 工作区日志数据发出警报： https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：不适用；事件中心不处理反恶意软件日志记录。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；事件中心不会处理或生成与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此指南适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。 

如何使用 PowerShell 获取 Azure AD 中的目录角色： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 获取 Azure AD 中目录角色的成员： https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：通过 Azure Active Directory (AD) 控制对事件中心的控制平面访问。 Azure AD 没有默认密码。

可以使用托管标识或应用注册和共享访问签名通过 Azure AD 控制数据平面对事件中心的访问。 共享访问签名由连接到事件中心的客户端使用，可以随时重新生成。

了解事件中心的共享访问签名： https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：

- 应该为你的订阅分配了多个所有者

- 应从订阅中删除拥有所有者权限的已弃用帐户

- 应从订阅中删除拥有所有者权限的外部帐户

如何使用 Azure 安全中心监视标识和访问（预览版）： https://docs.microsoft.com/azure/security-center/security-center-identity-access

如何使用 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：Microsoft Azure 基于 Azure Active Directory (AD) 针对资源和应用程序提供了集成的访问控制管理功能。 将 Azure AD 与 Azure 事件中心配合使用的主要优势在于，不再需要将凭据存储在代码中。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 用于请求令牌的资源名称为 \//eventhubs.azure.net/。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 事件中心资源请求授权。

如何使用 Azure AD 对访问事件中心资源的应用程序进行身份验证： https://docs.microsoft.com/azure/event-hubs/authenticate-application

了解 Azure AD 的 SSO： https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure Active Directory 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议，以便保护启用了事件中心的资源。

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心监视标识和访问： https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指导**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 进行登录并配置启用了事件中心的资源。

了解特权访问工作站： https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中启用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。 如需其他日志记录，请将 Azure 安全中心风险检测警报发送到 Azure Monitor 中，并使用操作组配置自定义警报/通知。

如何部署 Privileged Identity Management (PIM)： https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

了解 Azure AD 风险检测： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

如何为自定义警报和通知配置操作组： https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。



如何在 Azure 中配置命名位置： https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (AD) 作为 Azure 资源（例如事件中心）的中心身份验证和授权系统。 这样就可以对用于管理的敏感资源进行基于角色的访问控制 (RBAC)。

 如何创建和配置 Azure AD 实例： https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

若要了解 Azure 事件中心如何与 Azure Active Directory (AAD) 集成，请参阅“使用 Azure Active Directory 授予对事件中心资源的访问权限”： https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory (AD) 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

另外，还需定期轮换事件中心的共享访问签名。

了解 Azure AD 报告： https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 标识访问评审： https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

了解事件中心的共享访问签名： https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的企图

**指导**：你有权访问 Azure Active Directory (AD) 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。

可以通过为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

如何将 Azure 活动日志集成到 Azure Monitor： https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

使用 Azure Active Directory 授予对事件中心资源的访问权限： https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：使用 Azure Active Directory 的标识保护和风险检测功能来配置对检测到的与已启用事件中心相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

如何查看 Azure AD 风险登录： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和启用 Identity Protection 风险策略： https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何加入 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;事件中心尚不支持客户密码箱。

客户密码箱支持的服务列表： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：对与事件中心相关的资源使用标记，以便跟踪那些存储或处理敏感信息的 Azure 资源。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 事件中心命名空间应由启用了服务终结点的虚拟网络分隔并进行相应的标记。

还可以使用防火墙保护 Azure 事件中心命名空间。 Azure 事件中心支持使用基于 IP 的访问控制来提供入站防火墙支持。 可以通过 Azure 门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell 设置防火墙规则。

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

为 Azure 事件中心命名空间配置 IP 防火墙规则： https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何创建虚拟网络： https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：使用虚拟机访问事件中心时，请利用虚拟网络、服务终结点、事件中心防火墙、网络安全组和服务标记来降低数据外泄的可能性。

Microsoft 会管理 Azure 事件中心的底层基础结构，并实施严格的控制措施来防止客户数据丢失或泄露。

为 Azure 事件中心命名空间配置 IP 防火墙规则： https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

了解 Azure 事件中心提供的虚拟网络服务终结点： https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

将 Azure 事件中心与 Azure 专用链接集成： https://docs.microsoft.com/azure/event-hubs/private-link-service

了解网络安全组和服务标记： https://docs.microsoft.com/azure/virtual-network/security-overview

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，Azure 事件中心强制实施 TLS 加密的通信。 目前支持 TLS 版本 1.0、1.1 和 1.2。 但是，TLS 1.0 和 TLS 1.1 即将在全行业范围内弃用，因此，请尽可能使用 TLS 1.2。

若要了解事件中心的安全功能，请参阅网络安全：  https://docs.microsoft.com/azure/event-hubs/network-security

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：数据标识、分类和丢失防护功能尚不适用于 Azure 事件中心。 如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：Azure 事件中心支持使用 Azure Active Directory (AD) 对事件中心资源请求进行授权。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户或应用程序服务主体。

了解 Azure 事件中心的 Azure AD RBAC 和可用角色： https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此项指导适用于计算资源。

Microsoft 会管理事件中心的底层基础结构，并实施严格的控制措施来防止客户数据丢失或泄露。

了解 Azure 中的客户数据保护： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： Azure 事件中心支持通过 Microsoft 管理的密钥或客户托管的密钥来加密静态数据。 此功能使你能够创建、轮换、禁用和撤消对用于静态 Azure 事件中心数据加密的客户托管密钥的访问权限。

如何配置用于加密 Azure 事件中心的客户托管密钥： https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure 事件中心的生产实例和其他关键或相关资源发生更改时发出的警报。

如何针对 Azure 活动日志事件创建警报： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理。](../security/benchmarks/security-control-vulnerability-management.md)

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：不适用；Microsoft 对支持 Azure 事件中心的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；Microsoft 对支持事件中心的基础系统执行修补程序管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指导**：不适用；基准适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：不适用；Microsoft 对支持 Azure 事件中心的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：不适用；Microsoft 对支持 Azure 事件中心的基础系统执行漏洞管理。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

## <a name="inventory-and-asset-management"></a>清单和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure Resource Graph 查询和发现订阅中的所有资源（包括 Azure 事件中心命名空间）。 确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。

如何使用 Azure Resource Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅： https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪 Azure 事件中心命名空间和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

如何创建其他 Azure 订阅： https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组： https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准 Azure 资源和软件标题的清单

**指南**：不适用；此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 创建查询： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源和整个 Azure。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：

- 不允许的资源类型

- 允许的资源类型

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Policy 拒绝特定的资源类型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制用户通过脚本与 Azure 资源管理器进行交互的能力</div>

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

如何配置条件访问以阻止访问 Azure 资源管理器： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：定义和实施适用于 Azure 事件中心部署的标准安全配置。 在“Microsoft.EventHub”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施配置。 还可以为 Azure 事件中心利用内置策略定义，例如：

- 应启用事件中心内的诊断日志

- 事件中心应使用虚拟网络服务终结点

适用于事件中心命名空间的 Azure 内置策略： https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

如何查看可用的 Azure Policy 别名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure Policy“[拒绝]”和“[不存在则部署]”对支持事件中心的资源强制实施安全设置。 

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
有关 Azure Policy 效果的详细信息： https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指导**：如果对事件中心或相关资源使用自定义 Azure Policy 定义，请使用 Azure Repos 安全地存储和管理代码。

如何在 Azure DevOps 中存储代码： https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文档： https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指导**：在“Microsoft.EventHub”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指导**：在“Microsoft.EventHub”命名空间中使用 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并对其发出警报。 使用 Azure Policy“[审核]”、“[拒绝]”和“[不存在则部署]”为 Azure 事件中心部署和相关资源自动强制实施配置。

如何配置和管理 Azure Policy： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：对于在 Azure 应用服务上运行的用于访问事件中心的 Azure 虚拟机或 Web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 事件中心部署的共享访问签名管理。 请确保启用 Key Vault 软删除。

使用 Azure Active Directory 对托管标识进行身份验证，以便访问事件中心资源： https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

为事件中心配置客户托管的密钥： https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供 Key Vault 身份验证： https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：对于在 Azure 应用服务上运行的用于访问事件中心的 Azure 虚拟机或 Web 应用程序，请将托管服务标识与 Azure Key Vault 结合使用，以简化和保护 Azure 事件中心。 请确保启用 Key Vault 软删除。

使用托管标识在 Azure Active Directory (AD) 中为 Azure 服务提供一个自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Azure Key Vault）进行身份验证，无需在代码中放入任何凭据。

使用 Azure Active Directory 对托管标识进行身份验证，以便访问事件中心资源： https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

为事件中心配置客户托管的密钥： https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

如何配置托管标识： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure 应用服务）的基础主机上启用，但不会对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：预扫描要上传到非计算 Azure 资源的任何内容，例如 Azure 事件中心、应用服务、Data Lake Storage、Blob 存储、Azure Database for PostgreSQL 等。Microsoft 无法访问这些实例中的数据。

Microsoft 反恶意软件已在支持 Azure 服务（例如 Azure Cache for Redis）的基础主机上启用，但它不会针对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：配置 Azure 事件中心的异地灾难恢复。 当整个 Azure 区域或数据中心（如果未使用可用性区域）遭遇停机时，在不同区域或数据中心中继续进行数据处理就显得至关重要。 在这种情况下，异地灾难恢复和异地复制对于任何企业而言都是至关重要的功能。 Azure 事件中心支持命名空间级别的异地灾难恢复和异地复制。 

了解 Azure 事件中心的异地灾难恢复： https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**： Azure 事件中心通过 azure 存储服务加密 (azure SSE) 提供静态数据的加密。 事件中心依赖于 Azure 存储来存储数据，默认情况下，使用 Microsoft 托管密钥对存储在 Azure 存储中的所有数据进行加密。 如果使用 Azure Key Vault 来存储客户管理的密钥，请确保定期自动备份密钥。

请确保通过以下 PowerShell 命令定期自动备份 Key Vault 机密： AzKeyVaultSecret

如何为静态 Azure 事件中心数据配置客户管理的密钥： https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

如何备份 Key Vault 机密： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：测试已备份客户托管密钥的还原。

 

如何在 Azure 中还原密钥保管库密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 Key Vault 中启用软删除，以防止意外或恶意删除密钥。 Azure 事件中心需要客户管理的密钥才能进行软删除，并且不会清除已配置的密钥。

为用于捕获事件中心数据的 Azure 存储帐户配置软删除。 请注意，Azure Data Lake Storage 第2代尚不支持此功能。

如何在 Key Vault 中启用软删除： https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

使用密钥设置密钥保管库： https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Azure 存储 blob 的软删除： https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指导**：确保在书面的事件响应计划中定义人员职责，以及事件处理/管理的各个阶段。

如何在 Azure 安全中心内配置工作流自动化： https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心将为警报分配严重性来帮助你确定每条警报的处理优先顺序，以便在资源泄密时可以立即采取措施。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

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

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

如何配置连续导出： https://docs.microsoft.com/azure/security-center/continuous-export

如何将警报流式传输到 Azure Sentinel： https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

指南：使用 Azure 安全中心的工作流自动化功能，通过“逻辑应用”对安全警报和建议自动触发响应。

如何配置工作流自动化和逻辑应用： https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 。
对于 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序的实时站点渗透测试，可在此处找到详细信息： https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心监视**：是

**责任**：客户

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
