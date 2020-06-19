---
title: 适用于 Azure Databricks 的 Azure 安全基线
description: 适用于 Azure Databricks 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681694"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>适用于 Azure Databricks 的 Azure 安全基线

适用于 Azure Databricks 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线提取自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，该版本提供了有关如何在 Azure 上利用我们的最佳做法指南来保护云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源

**指南**：在自己的 Azure 虚拟网络 (VNet) 中部署 Azure Databricks。 Azure Databricks 的默认部署是 Azure 上的完全托管式服务：所有数据平面资源（包括与所有群集关联的 VNet）都部署到锁定的资源组。 但如果需要自定义网络，你可在自己的虚拟网络中部署 Azure Databricks 数据平面资源（VNet 注入），从而实现自定义网络配置。 你可以对自己的网络安全组 (NSG) 应用自定义规则，以实现特定的出口流量限制。

此外，你还可以配置 NSG 规则，以指定要将 Azure Databricks 实例部署到的子网上的出口流量限制。 可以为 VNET 注入的工作区配置 Azure 防火墙。

* [如何将 Azure Databricks 部署到自己的虚拟网络中](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [如何管理 NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NICS 的配置和流量

**指南**：在自己的 Azure 虚拟网络 (VNet) 中部署 Azure Databricks。 系统不会自动创建网络安全组 (NSG)。 你只需使用默认 Azure 规则创建基线 NSG。 部署工作区时，系统会添加 Databricks 所需的规则。 你还可以从空的 NSG 开始，系统将自动添加适当的规则。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的一些优点是能够用于直观呈现网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

* [如何将 Azure Databricks 部署到自己的虚拟网络中](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [如何启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：不适用；此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址通信

**指南**：在与 Azure Databricks 实例关联的 Azure 虚拟网络上启用 Azure DDoS 防护标准，防范分布式拒绝服务攻击。 使用 Azure 安全中心的集成式威胁情报功能拒绝与已知的恶意或未使用的公共 IP 地址通信。

* [使用 Azure 门户管理 Azure DDoS 防护标准](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 安全中心内 Azure 网络层的威胁防护](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：在自己的 Azure 虚拟网络 (VNet) 中部署 Azure Databricks。 系统不会自动创建网络安全组 (NSG)。 你只需使用默认 Azure 规则创建基线 NSG。 部署工作区时，系统会添加 Databricks 所需的规则。 启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的一些优点是能够用于直观呈现网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

* [如何将 Azure Databricks 部署到自己的虚拟网络中](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [如何启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：从 Azure 市场实现具有 IDP/IPS 功能的网络虚拟设备 (NVA)，以创建一个集成了虚拟网络的工作区，其中所有 Azure Databricks 群集都具有单一 IP 出站地址。 该单一 IP 地址可用作允许基于特定 IP 地址进行访问的其他 Azure 服务和应用程序的额外安全层。 你可以使用 Azure 防火墙或其他第三方工具（如 NVA）来管理入口和出口流量。

如果不需要基于有效负载检查的入侵检测和/或防护，则可以使用具有威胁情报功能的 Azure 防火墙。 基于 Azure 防火墙威胁情报的筛选功能可以发出警报，并拒绝传入和传出已知恶意 IP 地址和域的流量。 IP 地址和域源自 Microsoft 威胁智能源。

* [How to Assign a Single Public IP for VNet-Injected Workspaces Using Azure Firewall](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)（如何使用 Azure 防火墙为 VNet 注入工作区分配单个公共 IP）

* [如何创建 NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理到 Web 应用程序的流量

**指南**：不适用；此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度降低网络安全规则的复杂性和管理开销

**指南**：使用服务标记来定义网络安全组上连接到与 Azure Databricks 实例相关联的子网的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。 非注入的 VNET 工作区不支持服务标记。

* [了解服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：通过 Azure Policy 为 Azure Databricks 实例定义和实现网络安全配置。 你可以使用“Microsoft.Databricks”命名空间中的 Azure Policy 别名来定义自定义策略定义。 策略不会应用于受管理资源组中的资源。

你还可以使用 Azure 蓝图将关键环境项目（例如 Azure 资源管理模板、基于角色的访问控制 (RBAC) 和策略）打包到单个蓝图定义中，以简化大规模的 Azure 部署。 轻松地在新的订阅和环境中应用蓝图，并通过版本控制对控制和管理进行微调。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 别名和定义结构](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

**指南**：对 NSG 以及与 Azure Databricks 实例关联的其他与网络安全和流量流相关的资源使用标记。 对于各个 NSG 规则，可以使用“描述”字段为允许流量传入/传出网络的任何规则指定业务需求和/或持续时间（等等）。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具监视网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Databricks 实例相关的网络资源的更改。 在 Azure Monitor 中创建警报，使其在关键网络资源发生更改时触发。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：Microsoft 维护 Azure 资源的时间源，但你可以选择管理计算资源的时间同步设置。 由于 Azure Databricks 是一项 PaaS 服务，因此你无法直接访问 Azure Databricks 群集中的 VM，也无法设置时间同步设置。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置安全日志集中管理

**指南**：通过 Azure Monitor 引入日志来聚合 Azure Databricks 生成的安全数据。 在 Azure Monitor 中，可以查询配置为接收 Databricks 和诊断日志的 Log Analytics 工作区。 将 Azure 存储帐户用于长期/存档日志存储或事件中心，以便将数据导出到其他系统。 或者，可以启用数据并将其加入 Azure Sentinel 或第三方安全信息和事件管理 (SIEM)。

注意：Azure Databricks 诊断日志需要 Azure Databricks Premium 计划

* [如何配置诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何开始将 Azure Monitor 与第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：启用 Azure 活动日志诊断设置，并将日志发送到 Log Aalytics 工作区、Azure 事件中心或 Azure 存储帐户进行存档。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure 资源执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

对于审核日志记录，Azure Databricks 提供全面的由 Azure Databricks 用户所执行活动的端到端诊断日志，使企业能够监视详细的 Azure Databricks 使用模式。

注意：Azure Databricks 诊断日志需要 Azure Databricks Premium 计划

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何启用 Azure Databricks 的诊断设置](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：Azure Databricks 提供全面的 Azure Databricks 用户所执行活动的端到端诊断日志，使企业能够监视详细的 Azure Databricks 使用模式。

注意：Azure Databricks 诊断日志需要 Azure Databricks Premium 计划。 OS 安全日志记录不可用。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何启用 Azure Databricks 的诊断设置](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留

**指南**：启用 Azure Databricks 的诊断设置。 如果选择将日志存储在 Log Analytics 工作区中，请根据组织的合规性规则来设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期/存档存储。 在 Databricks 审核日志中跟踪与安全相关的活动。

注意：Azure Databricks 诊断日志需要 Azure Databricks Premium 计划

* [如何在 Azure Databricks 中启用诊断设置](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [如何为 Log Analytics 工作区设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和检查日志

**指南**：启用 Azure Databricks 的诊断设置，并将日志发送到 Log Analytics 工作区。 使用 Log Analytics 工作区分析和监视 Azure Databricks 日志中是否存在异常行为，并定期查看结果。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。

注意：Azure Databricks 诊断日志需要 Azure Databricks Premium 计划

* [如何在 Azure Databricks 中启用诊断设置](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [如何查询发送到 Log Analytics 工作区的 Azure Databricks 日志](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：启用针对异常活动的警报

**指南**：配置 Azure Databricks 实例的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 工作区中，配置发生一组预定义的条件时要触发的警报。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。

注意：Azure Databricks 诊断日志需要 Azure Databricks Premium 计划

* [如何将 Azure Databricks 日志发送到 Log Analytics 工作区](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [如何在 Log Analytics 工作区中配置警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中进行反恶意软件日志记录

**指南**：不适用。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用；Azure Databricks 不会处理或生成用户可访问的与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此指南适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：可使用 Azure Databricks SCIM API 来管理 Azure Databricks 工作区中的用户，并向指定的用户授予管理权限。 还可以通过 Azure Databricks UI 来管理它们。

* [如何使用 SCIM API](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [如何在 Azure Databricks 中添加和删除用户](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：更改默认密码（如果适用）

**指南**：Azure Databricks 使用 Azure Active Directory (AD) 提供对 Azure 门户以及 Azure Databricks 管理控制台的访问权限。 Azure AD 没有默认密码的概念，但你有责任更改或不允许使用任何自定义或第三方应用程序的默认密码。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：可以在 Azure Databricks 中使用 Azure Databricks SCIM API 添加具有管理员权限的用户。 还可以通过 Azure Databricks UI 来管理它们。

* [如何使用 SCIM API](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [如何在 Azure Databricks 中添加和删除用户](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用 Azure Active Directory 的单一登录 (SSO)

**指南**：Azure Databricks 会自动设置为使用 Azure Active Directory 单一登录对用户进行身份验证。 组织外的用户必须完成邀请过程并添加到 Active Directory 租户后，才能通过单一登录登录 Azure Databricks。 你可以实现 SCIM，在工作区中自动预配和取消预配用户。

* [如何使用 SCIM API](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [了解 Azure Databricks 的单一登录](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [如何邀请用户加入 Azure AD 租户](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证。

**指南**：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心内监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将 PAW（特权访问工作站）与为登录和配置 Azure 资源而配置的 MFA 结合使用。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录管理帐户的可疑活动并发出警报

**指南**：使用 Azure Active Directory 安全报告在环境中发生可疑活动或不安全的活动时生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。 此外，还可以利用 Azure Databricks 诊断日志。

* [如何确定标记为存在风险活动的 Azure AD 用户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 安全中心内监视用户的标识和访问活动](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：Azure Databricks 会自动设置为使用 Azure Active Directory 单一登录对用户进行身份验证。 组织外的用户必须完成邀请过程并添加到 Active Directory 租户后，才能通过单一登录登录 Azure Databricks。

* [了解 Azure Databricks 的单一登录](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [如何邀请用户加入 Azure AD 租户](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期查看和协调用户访问权限

**指南**：Azure AD 提供有助于发现陈旧帐户的日志。 此外，使用 Azure 标识访问评审还可有效管理组成员身份、对企业应用程序的访问权限以及角色分配。 可定期评审用户访问权限，确保相应人员持续拥有访问权限。 还可实现 SCIM API 和 Azure Databricks 诊断日志来查看用户访问权限。 还可使用 SCIM API 和 Azure Databricks 诊断日志来查看用户访问权限。

此外，应在 Azure Databricks 管理控制台中定期查看和管理用户访问权限。

* [Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [如何在 Azure Databricks 管理控制台中管理用户访问权限](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视尝试访问已停用帐户的行为

**指南**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。 此外，还可使用 Azure Databricks 诊断日志来查看用户访问活动。

你可以为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区以简化上述过程。 可在 Log Analytics 工作区中配置所需的警报。

* [如何使用 SCIM API](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：对帐户登录行为偏差发出警报

**指南**：可使用 Azure AD 风险和标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。 此外，还可使用 Azure Databricks 诊断日志来查看用户访问活动。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案期间为 Microsoft 提供对相关客户数据的访问权限

**指南**：当支持票证处于未结状态时，客服和支持工程师将征求你的同意，以便访问相关客户数据。

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记可以帮助跟踪处理敏感信息的 Azure Databricks 实例。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：为开发、测试和生产采用单独的订阅和/或管理组。 Azure Databricks 的默认部署是一种部署在其自己的虚拟网络中的完全托管式服务。 如需自定义网络，你可在自己的虚拟网络中部署 Azure Databricks。 最佳做法是为不同的业务团队或部门创建单独的 Azure Databricks 工作区。

* [如何将 Azure Databricks 部署到自己的虚拟网络中](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输。

**指南**：遵循 Databricks 的泄露保护体系结构，降低数据泄露的可能性。

* [Azure Databricks 的数据泄露保护](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft 会管理 Azure Databricks 的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：默认情况下，在通过 Azure 门户或 Azure Databricks 控制台管理 Azure Databricks 实例时，Microsoft 将协商 TLS 1.2。 Databricks Web 应用支持 TLS 1.3。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：当前不可用；数据标识、分类和丢失防护功能目前不可用于 Azure Databricks。 标记可能正在处理此类敏感信息的 Azure Databricks 实例和相关资源，并根据需要实施第三方解决方案以实现合规性。

Databricks 平台仅用于计算，所有数据都存储在其他 Azure 数据服务中。 对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容都视为敏感信息，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：在 Azure Databricks 中，可以使用访问控制列表 (ACL) 来配置访问数据表、群集、池、作业和工作区对象（如笔记本、试验和文件夹）的权限。 所有管理员用户都可以管理访问控制列表，被授予访问控制列表委托管理权限的用户也可以进行此类管理。 可使用 Azure RBAC 设置对 Azure Databricks 工作区的权限。

注意：只能在 Azure Databricks Premium 计划中对表、群集、池、作业和工作区进行访问控制

* [如何管理 Azure Databricks 中的访问控制](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护功能来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

Microsoft 会管理 Azure Databricks 的底层基础结构，并实施了严格的控制措施来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态的敏感信息

**指南**：Azure Databricks 工作区包含一个托管在 Azure Databricks 管理的虚拟网络中的管理平面，还包含一个部署在客户管理的虚拟网络中的数据平面。 控制平面将所有用户的笔记本和关联的笔记本结果存储在数据库中。 默认情况下，系统使用不同的加密密钥对所有笔记本和结果进行静态加密。

Databricks 文件系统 (DBFS) 是一个装载到 Azure Databricks 工作区的分布式文件系统，可以在 Azure Databricks 群集上使用。 DBFS 以 Azure Databricks 工作区受管理资源组中的存储帐户的形式实现。 默认情况下，存储帐户使用 Microsoft 管理的密钥进行加密。 你的数据存储在你拥有的 Azure 数据服务中，你可视情况对其进行加密。 不建议使用 DBFS 存储生产数据

注意：这些功能并不适用于所有 Azure Databricks 订阅。 请联系 Microsoft 或 Databricks 客户代表，以申请访问权限。

* [如何为 Azure Databricks 笔记本启用客户管理的密钥](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [如何为 Azure Databricks 文件系统启用客户管理的密钥](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，创建要在关键 Azure Databricks 工作区发生更改时触发的警报。

* [如何为 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动化漏洞扫描工具

**指南**：实施第三方漏洞管理解决方案。

如果有漏洞管理平台订阅，则可以使用 Azure Databricks 初始化脚本在 Azure Databricks 群集节点上安装漏洞评估代理，并通过相应的门户管理节点。 注意，每个第三方解决方案的工作方式都有所不同。

* [如何手动安装 Rapid7 代理](https://insightagent.help.rapid7.com/docs/install)

* [如何手动安装 Qualys 代理](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 初始化脚本](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署操作系统修补程序自动化管理解决方案

**指南**：Microsoft 负责维护 Azure Databricks 群集节点基础映像，而你负责确保群集节点得到修补。 要将维护更新添加到正在运行的现有群集，必须重启该群集。

* [了解 Azure Databricks 的运行时维护更新](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方软件修补程序自动化管理解决方案

**指南**：Microsoft 负责维护 Azure Databricks 群集节点基础映像，而你负责确保安装的任何第三方应用程序都得到修补。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续的漏洞扫描

**指南**：实施可以将一段时间内的漏洞扫描进行比较的第三方漏洞管理解决方案。 如果你有漏洞管理订阅，则可以使用该供应商的门户来查看和比较连续的漏洞扫描。 注意，每个第三方解决方案的工作方式都有所不同。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险分级流程确定所发现漏洞的修正优先级。

**指南**：使用常见的风险分级程序（例如“常见漏洞评分系统”）或第三方扫描工具提供的默认风险分级功能。

**Azure 安全中心监视**：空值

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。

虽然可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后创建和使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用于 Azure 资源，从而将元数据按逻辑组织到分类中。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪 Azure 资源。 定期协调清单，并确保及时从订阅中删除未经授权的资源。

此外，使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单。

**指南**：为计算资源定义已批准的 Azure 资源和软件。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未经批准的 Azure 资源

**指南**：使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 可查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未经批准的软件应用程序

**指南**：不适用；Azure Databricks 是一项 PaaS 服务，客户无法直接访问 Azure Databricks 群集中的 VM。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未经批准的 Azure 资源和软件应用程序

**指南**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等），包括 Azure Databricks 实例。 删除发现的任何未经批准的 Azure 资源。 对于 Azure Databricks 群集节点，请实施第三方解决方案，以删除未经批准的软件或对其发出警报。

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：只使用已批准的应用程序

**指南**：不适用；Azure Databricks 是一项 PaaS 服务，你无法直接访问 Azure Databricks 群集中的 VM。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：只使用已批准的 Azure 服务

**指南**：使用 Azure Policy 对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Policy 拒绝特定资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实现已批准的应用程序列表

**指南**：不适用；Azure Databricks 是一项 PaaS 服务，你无法直接访问 Azure Databricks 群集中的 VM。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制用户通过脚本与 Azure 资源管理器进行交互的能力</div>

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指南**：不适用；这不适用于 Azure Databricks，因为群集的用户（非管理员）不需要访问各个节点即可运行作业。 默认情况下，群集管理员对所有群集节点具有根级访问权限。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用；基准适用于 Azure 应用服务或托管 Web 应用程序的计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure Policy 为 Azure Databricks 实例定义和实现标准安全配置。 使用“Microsoft.Databricks”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Databricks 实例的配置。 请注意，应用的任何策略都不适用于 Databricks 受管理资源组。

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；Azure Databricks 是一项 PaaS 服务，你无法直接访问 Azure Databricks 群集中的 VM。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：通过 Azure Policy 为 Azure Databricks 实例定义和实现标准安全配置。 使用“Microsoft.Databricks”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Databricks 实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：Azure Databricks 操作系统映像由 Microsoft 管理和维护。 你负责实现 OS 级别的状态配置。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用的是自定义 Azure 策略定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：如果为 Azure Databricks 群集节点使用自定义映像，请将自定义基础映像推送到 Docker 注册表，如 Azure 容器注册表 (ACR)。

* [如何使用 Databricks 容器服务自定义容器](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [了解 Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：通过 Azure Policy 为 Azure Databricks 实例定义和实现标准安全配置。 使用“Microsoft.Databricks”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Databricks 实例的配置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用；Azure Databricks 是一项 PaaS 服务，你无法直接访问 Azure Databricks 群集中的 VM。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：针对 Azure 服务实现自动化配置监视

**指南**：通过 Azure Policy 为 Azure Databricks 实例定义和实现标准安全配置。 使用“Microsoft.Databricks”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure Databricks 实例的配置。

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：针对操作系统实现自动化配置监视

**指南**：实现第三方解决方案，以监视 Azure Databricks 群集节点操作系统的状态。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 机密

**指南**：将 Azure Key Vault 与 Azure Databricks 机密作用域结合使用，以安全地管理和使用机密。

* [如何将 Azure Key Vault 与 Azure Databricks 配合使用](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自动地管理标识

**指南**：当前不可用；托管标识当前不可用于 Azure Databricks

* [支持 Azure 资源托管标识的服务](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure 安全中心监视**：目前不可用

**责任**：目前不可用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据暴露

**指南**：实现凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据移动到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：如果有漏洞管理平台订阅，则可以使用 Azure Databricks 初始化脚本在 Azure Databricks 群集节点上安装漏洞评估代理，并通过相应的门户管理节点。 注意，每个第三方解决方案的工作方式都有所不同。

* [如何手动安装 Rapid7 代理](https://insightagent.help.rapid7.com/docs/install)

* [如何手动安装 Qualys 代理](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 初始化脚本](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预扫描要上传到非计算 Azure 资源的文件

**指南**：Microsoft Antimalware 会在支持 Azure 服务（例如 Azure 应用服务）的基础主机上启用，但不会对客户内容运行。

预扫描上传到 Azure Databricks 群集节点或相关资源的任何文件。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：如果有漏洞管理平台订阅，则可以使用 Azure Databricks 初始化脚本在 Azure Databricks 群集节点上安装漏洞评估代理，并通过相应的门户管理节点。 注意，每个第三方解决方案的工作方式都有所不同。

* [如何手动安装 Rapid7 代理](https://insightagent.help.rapid7.com/docs/install)

* [如何手动安装 Qualys 代理](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 初始化脚本](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：对于 Azure Databricks 数据源，请确保为用例配置了适当的数据冗余级别。 例如，如果对 Azure Databricks 数据存储使用 Azure 存储帐户，请选择适当的冗余选项（LRS、ZRS、GRS、RA-GRS）。

* [Azure Databricks 的数据源](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Azure Databricks 群集的区域性灾难恢复](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份所有客户管理的密钥

**指南**：备份 Azure Key Vault 中与 Azure Databricks 实现相关的任何客户管理的密钥。 还可使用 REST API 和 CLI 创建 Databricks 配置的每日备份。 还可使用 REST API/CLI 创建 Databricks 配置的每日备份。

* [如何在 Azure 中备份密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户端管理的密钥

**指南**：测试与 Azure Databricks 实现相关的已备份客户管理的密钥的还原。

* [如何在 Azure 中还原密钥保管库密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：确保在 Key Vault 中启用软删除，以防意外或恶意删除密钥。

* [如何在 Key Vault 中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指南**：为组织生成事件响应指南。 确保制定书面事件响应计划，其中定义了人员的所有角色，以及从检测到事件后审查的事件处理/管理阶段。

* [如何在 Azure 安全中心内配置工作流自动化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [有关如何生成自己的安全事件响应过程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [你还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级确定过程

**指南**：安全中心向每个警报分配一个严重性，以帮助你确定应首先调查哪个警报。 严重性取决于安全中心对调查结果或用于发出警报的分析的置信度，以及对导致警报的活动背后存在恶意意图的可信级别。

此外，应清楚地标记订阅（如 生产、非生产），并创建命名系统来明确标识和分类 Azure 资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期练习以测试系统的事件响应能力。 明确薄弱点和差距，并根据需要修订计划。

* [请参阅 NIST 出版物：适用于 IT 计划和功能的测试、培训和练习计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现客户数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 在事后审查事件，以确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出功能可手动或以连续不断的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，通过“逻辑应用”对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源进行渗透测试，确保在 60 天内修正所有关键的安全发现。

**指南**：*[遵循 Microsoft Rules of Engagement，确保渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
