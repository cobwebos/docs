---
title: 适用于 Linux 虚拟机的 Azure 安全基线
description: Linux 虚拟机安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 627c0c77cc206cd1b3210fcfc7b2a9bbc3c6daea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401089"
---
# <a name="azure-security-baseline-for-linux-virtual-machines"></a>适用于 Linux 虚拟机的 Azure 安全基线

适用于 Linux 虚拟机的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：创建 Azure 虚拟机 (VM) 时，必须创建虚拟网络 (VNet) 或使用现有 VNet，并使用子网来配置 VM。 确保所有部署的子网都应用了网络安全组，且具有特定于应用程序受信任端口和源的网络访问控制。

或者，如果拥有集中式防火墙的特定用例，则还可以使用 Azure 防火墙来满足这些要求。

* [Azure 中的虚拟网络和虚拟机](../windows/network-overview.md)

* [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署和配置 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视并记录虚拟网络、子网和网络接口的配置与流量

**指导**：使用 Azure 安全中心来确定并遵循网络保护建议，以帮助保护 Azure 中的 Azure 虚拟机 (VM) 资源。 启用 NSG 流日志，并将日志发送到存储帐户，以针对 VM 进行异常活动流量审核。

* [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [了解 Azure 安全中心提供的网络安全](../../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：如果使用虚拟机 (VM) 承载 Web 应用程序，请在 VM 子网中使用网络安全组 (NSG) 来限制允许进行通信的网络流量、端口和协议。 将 NSG 配置为仅允许所需流量进入应用程序时，请遵循最小特权网络方法。

还可以在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。

* [使用 Azure 门户创建具有 Web 应用程序防火墙的应用程序网关](../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

* [Azure 中的虚拟网络和虚拟机](../windows/network-overview.md)

* [有关网络安全组的信息](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：在虚拟网络上启用分布式拒绝服务 (DDoS) 标准保护，以防范 DDoS 攻击。 使用 Azure 安全中心的集成式威胁情报功能，可以监视与已知恶意的 IP 地址进行的通信。 在每个虚拟网络段上配置 Azure 防火墙，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

你可以使用 Azure 安全中心的实时网络访问权限，在有限期限内限制 Linux 虚拟机暴露给批准的 IP 地址。 此外，请使用 Azure 安全中心的自适应网络强化功能，建议基于实际流量和威胁情报限制端口和源 IP 的 NSG 配置。

* [如何配置 DDoS 防护](../../virtual-network/manage-ddos-protection.md)

* [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

* [了解 Azure 安全中心集成的威胁情报](../../security-center/threat-protection.md)

* [了解 Azure 安全中心自适应网络强化](../../security-center/security-center-adaptive-network-hardening.md)

* [了解 Azure 安全中心实时网络访问控制](../../security-center/security-center-just-in-time.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：你可以将 NSG 流日志记录到存储帐户中，以生成 Azure 虚拟机的流记录。 调查异常活动时，可以启用网络观察程序数据包捕获，以便可以检查网络流量中是否存在异常活动和意外活动。

* [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用网络观察程序](../../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：通过将网络观察程序提供的数据包捕获与开源 IDS 工具相结合，可以针对各种威胁执行网络入侵检测。 此外，还可以在虚拟网络段上适当部署 Azure 防火墙，启用威胁情报并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

* [使用网络观察程序和开源工具执行网络入侵检测](../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置 Azure 防火墙警报](../../firewall/threat-intel.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：你可以为 Web 应用程序部署 Azure 应用程序网关，并为受信任的证书启用 HTTPS/SSL。 使用 Azure 应用程序网关，可将应用程序 web 流量定向到特定资源，方法是将侦听器分配给端口，创建规则，并将资源添加到后端池（如 Linux 虚拟机）。

* [如何部署应用程序网关](../../application-gateway/quick-create-portal.md)

* [如何将应用程序网关配置为使用 HTTPS](../../application-gateway/create-ssl-portal.md)

* [了解 Azure Web 应用程序网关的第七层负载均衡](../../application-gateway/overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在为 Azure 虚拟机配置的网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

* [了解并使用服务标记](../../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：使用 Azure Policy 为 Azure 虚拟机 (VM) 定义和实现标准安全配置。 还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、角色分配和 Azure Policy 分配）来简化大规模的 Azure VM 部署。 可以将蓝图应用于订阅，并通过蓝图版本控制启用资源管理。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network)

* [如何创建 Azure 蓝图](../../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：你可以使用 nsg 和其他与为 Azure 虚拟机配置的网络安全和流量流相关的资源的标记。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间。

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志来监视与虚拟机相关的网络资源配置的更改。 在 Azure Monitor 中创建警报，这些警报将在对关键网络设置或资源进行更改时触发。

使用 Azure 策略来验证 (和/或修正与 Linux 虚拟机相关的网络资源) 配置。

* [如何查看和检索 Azure 活动日志事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../../azure-monitor/platform/alerts-activity-log.md)

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 Azure 资源的时间源，但你可以选择管理 Linux 虚拟机的时间同步设置。

* [如何为 Azure 计算资源配置时间同步](./time-sync.md)

**Azure 安全中心监视**：不可用

**责任**：共享

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**： Azure 安全中心为 Linux 虚拟机提供安全事件日志监视。

* [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)

* [若要捕获用于监视的 Syslog 数据，需启用 Log Analytics 扩展](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：活动日志可用于审核对虚拟机资源执行的操作。 活动日志包含资源的所有写入操作（PUT、POST、DELETE），但读取操作 (GET) 除外。 活动日志可用于在进行故障排除时查找错误，或监视组织中的用户如何对资源进行修改。

通过在虚拟机 (VM) 上部署诊断扩展，启用来宾 OS 诊断数据收集。 可以使用诊断扩展从 Azure 虚拟机收集诊断数据，例如应用程序日志或性能计数器。

若要获得虚拟机支持的应用程序和服务的高级可见性，可以同时启用 Azure Monitor（用于 VM）和 Application Insights。 利用 Application Insights，你可以监视应用程序并捕获遥测数据（如 HTTP 请求、异常等），以便你可以将 Vm 与应用程序之间的问题关联起来。

此外，请允许 Azure Monitor 访问你的审核和活动日志，其中包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用的元素。

* [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)

* [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)

* [适用于 Linux 的 Log analytics 虚拟机扩展](../extensions/oms-linux.md)

* [查看和检索 Azure 活动日志事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Application Insights 概述](../../azure-monitor/app/app-insights-overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：使用 Azure 安全中心为 Azure 虚拟机提供安全事件日志监视。 考虑到安全事件日志生成的数据量，默认情况下不会存储它。

如果组织想要保留来自虚拟机的安全事件日志数据，则可以将其存储在 Azure 安全中心内配置的所需数据集合层的 Log Analytics 工作区中。

* [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)

* [若要捕获用于监视的 Syslog 数据，需启用 Log Analytics 扩展](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指导**：确保用于存储虚拟机日志的任何存储帐户或 Log Analytics 工作区都具有根据组织的符合性规定设置的日志保留期。

* [如何监视 Azure 中的虚拟机](../../azure-monitor/insights/monitor-vm-azure.md)

* [如何配置 Log Analytics 工作区保留期](../../azure-monitor/platform/manage-cost-storage.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：启用 Log Analytics 代理，也称为 MICROSOFT MONITORING AGENT (MMA) 或 OMS Linux 代理，并将其配置为将日志发送到 Log Analytics 工作区。 Linux 代理将不同源中收集的数据发送到 Azure Monitor 中的 Log Analytics 工作区，以及监视解决方案中定义的任何唯一日志或指标。

分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 查看日志并对日志数据执行查询。

或者，可以启用数据并将其载入 Azure Sentinel 或第三方 SIEM，以监视和查看日志。

* [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)

* [适用于 Linux 的 Log analytics 虚拟机扩展](../extensions/oms-linux.md)

* [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [了解 Log Analytics 工作区](../../azure-monitor/log-query/get-started-portal.md)

* [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：使用 Azure 安全中心和配置的 Log Analytics 工作区，监视安全日志和事件中发现的 Azure 虚拟机异常活动并发出警报。

或者，可以启用数据并将其载入 Azure Sentinel 或第三方 SIEM，以针对异常活动设置警报。

* [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [如何在 Azure 安全中心管理警报](../../security-center/security-center-managing-and-responding-alerts.md)

* [如何针对 Log Analytics 日志数据发出警报](../../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：你将需要第三方工具，用于在 Linux OS 内检测反恶意软件漏洞。

* [将 Linux 服务器载入 Azure 安全中心的说明](../../security-center/quick-onboard-linux-computer.md)

* [以下链接提供 Microsoft 建议的安全准则，可用作所选漏洞软件的标准列表](./security-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：根据组织的需求，从 Azure MARKETPLACE 实现 DNS 日志记录解决方案的第三方解决方案。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：可以基于每个节点手动配置控制台日志记录，并使用一种方法来存储数据。 同时，使用 Azure Monitor 的 Log Analytics 工作区查看日志并对 Azure 虚拟机中的 syslog 数据执行查询。

* [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

* [Azure Monitor 中的 Syslog 数据源](../../azure-monitor/platform/data-sources-syslog.md)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：虽然建议使用 Azure Active Directory 管理用户访问，但 Azure 虚拟机可能具有本地帐户。 通常应以最少使用量原则来查看和管理本地帐户及域帐户。 此外，对用于访问虚拟机资源的管理帐户使用 Azure Privileged Identity Management。

* [本地帐户的信息可在](../../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [有关 Privileged Identity Manager 的信息](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Linux 虚拟机和 Azure Active Directory 没有默认密码的概念。 客户对可能使用默认密码的第三方应用程序和市场服务负责。

Azure 安全中心监视：不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：围绕可以访问虚拟机的专用管理帐户的使用，创建标准操作过程。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 用于访问 Azure 虚拟机资源的所有管理员帐户还可以由 Azure Privileged Identity Management (PIM) 进行管理。 Azure Privileged Identity Management 提供实时提升（例如，要求在承担某个角色前进行多重身份验证）等多个选项，以及委派选项（以便权限仅适用于特定时间范围并需要审批者）。

* [了解 Azure 安全中心标识和访问](../../security-center/security-center-identity-access.md)

* [有关 Privileged Identity Manager 的信息](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录 (SSO)

**指导**：客户应尽可能使用 Azure Active Directory SSO，而不是为每个服务配置单个独立凭据。 请使用 Azure 安全中心标识和访问管理建议。

* [了解 Azure AD 的 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

* [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：将 PAW（特权访问工作站）与为登录和配置 Azure 资源而配置的 MFA 结合使用。

* [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure AD Privileged Identity Management (PIM) 在环境中发生可疑或不安全的活动时生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。 （可选）客户可以将 Azure 安全中心风险检测警报引入 Azure Monitor，并使用操作组配置自定义警报/通知。

* [如何部署 Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure 安全中心风险检测 (可疑活动) ](../../active-directory/identity-protection/overview-identity-protection.md)

* [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何为自定义警报和通知配置操作组](../../azure-monitor/platform/action-groups.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指导**：使用 Azure Active Directory 条件访问策略和命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑组进行访问。

* [如何在 Azure 中配置命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 可以使用托管标识向支持 Azure AD 身份验证的任何服务（包括密钥保管库）进行身份验证，无需在代码中包含任何凭据。 在虚拟机上运行的代码可以使用其托管标识来请求支持 Azure AD 身份验证的服务的访问令牌。

* [如何创建和配置 Azure AD 实例](../../active-directory-domain-services/tutorial-create-instance.md)

* [Azure 资源的托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure Active Directory 标识访问评审来高效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保仅适当的用户持续拥有访问权限。 使用 Azure 虚拟机时，需要查看本地安全组和用户，以确保没有可能危及系统的非预期帐户。

* [如何使用 Azure 标识访问评审](../../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：配置 Azure Active Directory 的诊断设置，以将审核日志和登录日志发送到 Log Analytics 工作区。 此外，还可以使用 Azure Monitor 查看日志，并对 Azure 虚拟机中的身份验证 syslog 数据执行查询。

* [了解 Log Analytics 工作区](../../azure-monitor/log-query/get-started-portal.md)

* [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

* [Azure Monitor 中的 Syslog 数据源](../../azure-monitor/platform/data-sources-syslog.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：使用 Azure Active Directory 的风险和标识保护功能配置对检测到的与存储帐户资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

* [如何查看 Azure AD 风险登录](../../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：如果第三方需要访问客户数据（例如在响应支持请求期间这样做），请使用 Azure 虚拟机客户密码箱来审核和批准/拒绝客户数据访问请求。

* [Microsoft Azure 客户密码箱](../../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 虚拟机。

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 资源应当按虚拟网络/子网进行分隔，相应地进行标记，并由网络安全组 (NSG) 或 Azure 防火墙提供保护。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以在不使用这些虚拟机时将其关闭。

* [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../../governance/management-groups/create.md)

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

* [如何通过 Azure 防火墙配置“警报”或“发出警报并拒绝”](../../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：在网络边界实现第三方解决方案，用于监视敏感信息的未授权传输并阻止此类传输，同时向信息安全专业人员发出警报。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](../../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：根据连接的性质（例如连接到 RDP 或 SSH 会话中的 VM 时），以多种方式对运行 LINUX (vm) 的传输中的数据进行加密。

Microsoft 使用传输层安全性 (TLS) 协议，在云服务和客户之间传输数据时对数据进行保护。

* [VM 中的传输中加密](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Azure 安全中心监视**：不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：使用第三方主动发现工具来确定组织的技术系统（包括现场或远程服务提供商处的技术系统）存储、处理或传输的所有敏感信息，并更新组织的敏感信息清单。

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC)，可以在团队中对职责进行分配，仅将执行作业所需的最低访问权限授予 VM 上的用户。 可以仅允许某些操作，而不是向 VM 上的每个人授予不受限制的权限。 可以使用 Azure CLI 或 Azure PowerShell 为 Azure 门户中的 VM 配置访问控制。

* [Azure RBAC](../../role-based-access-control/overview.md)

* [Azure 内置角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：实现第三方工具（例如基于主机的自动数据丢失预防解决方案），以强制执行访问控制来减轻数据泄露风险。

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**： (VM) Linux 虚拟机上的虚拟磁盘使用服务器端加密或 Azure 磁盘加密 (ADE) 进行静态加密。 Azure 磁盘加密利用 Linux 的 DM-Crypt 功能，通过来宾 VM 中的客户托管密钥对托管磁盘进行加密。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

* [Azure 托管磁盘的服务器端加密](../windows/disk-encryption.md)

* [适用于 Linux VM 的 Azure 磁盘加密](./disk-encryption-overview.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志配合使用，以创建在虚拟机和相关资源发生更改时发出的警报。

* [如何针对 Azure 活动日志事件创建警报](../../azure-monitor/platform/alerts-activity-log.md)

* [如何针对 Azure 活动日志事件创建警报](../../azure-monitor/platform/alerts-activity-log.md)

* [Azure 存储分析日志记录](../../storage/common/storage-analytics-logging.md)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：你将需要第三方工具，用于在 Linux OS 内检测反恶意软件漏洞。

* [将 Linux 服务器载入 Azure 安全中心的说明](../../security-center/quick-onboard-linux-computer.md)

* [Microsoft 推荐的安全指南](./security-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：使用 Azure 更新管理解决方案来管理虚拟机的更新和补丁。 更新管理依赖于本地配置的更新存储库来修补受支持的系统。

* [Azure 中的更新管理解决方案](../../automation/update-management/update-mgmt-overview.md)

* [管理 VM 的更新和修补程序](../../automation/update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：可以使用第三方补丁管理解决方案。 可以使用 Azure 更新管理解决方案来管理虚拟机的更新和补丁。 更新管理依赖于本地配置的更新存储库来修补受支持的系统。

* [Azure 中的更新管理解决方案](../../automation/update-management/update-mgmt-overview.md)

* [管理 VM 的更新和修补程序](../../automation/update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：以一致的间隔导出扫描结果，并比较结果以验证漏洞是否已修复。 使用 Azure 安全中心建议的漏洞管理建议时，客户可以转到所选解决方案的门户查看历史扫描数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

* [了解 Azure 安全中心安全功能分数](../../security-center/secure-score-security-controls.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现所有资源 (包括订阅中) 的虚拟机。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地根据分类组织元数据。

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：使用标记、管理组和单独订阅（如果适用）来组织和跟踪虚拟机和相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

* [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../../governance/management-groups/create.md)

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：你应当为计算资源创建已批准的 Azure 资源和已批准的软件的清单。 还可以使用自适应应用程序控制（Azure 安全中心的一项功能）来定义一组应用程序，允许这些应用程序在所配置的计算机组上运行。 此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

         

* [如何启用自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。 这可以在基于高安全性的环境（例如具有存储帐户的环境）中提供帮助。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 利用 Azure 虚拟机清单自动收集有关虚拟机上的所有软件的信息。 注意：可从 Azure 门户获得软件名称、版本、发布者和刷新时间。 若要获取对指标和其他信息的访问权限，客户需要启用来宾级别诊断，并可以将 syslog 信息发送到指定的存储帐户。

除了使用更改跟踪来监视软件应用程序外，Azure 安全中心的自适应应用程序控制还可以使用机器学习来分析计算机上运行的应用程序，并通过此智能创建允许列表。 此功能大大简化了配置和维护应用程序允许列表策略的过程，让你可以避免在环境中使用不需要的软件。 你可以配置审核模式或强制模式。 审核模式只审核受保护 VM 上的活动。 强制模式强制执行规则，确保阻止不允许运行的应用程序。

* [适用于 Linux 虚拟机的诊断扩展](../extensions/diagnostics-linux.md?toc=/azure/azure-monitor/toc.json)

* [Azure 自动化简介](../../automation/automation-intro.md)

* [如何启用 Azure VM 清单](../../automation/automation-tutorial-installed-software.md)

* [了解自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 可以使用更改跟踪来识别虚拟机上安装的所有软件。 可以实现自己的过程，也可以使用 Azure Automation State Configuration 来删除未经授权的软件。

* [Azure 自动化简介](../../automation/automation-intro.md)

* [了解更改跟踪](../../automation/change-tracking.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：使用 Azure 安全中心自适应应用程序控制确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：自适应应用程序控制是 Azure 安全中心提供的智能、自动化、端到端的解决方案，有助于控制可在 Azure 和非 Azure 计算机（Windows 和 Linux）上运行的应用程序。 如果此方案不满足组织的要求，则实现第三方解决方案。

* [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：根据脚本的类型，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。 还可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：你的 azure 环境中部署的高风险应用程序可使用虚拟网络、子网、订阅和管理组进行隔离，并使用 Azure 防火墙、Web 应用程序防火墙 (WAF) 或网络安全组 (NSG) 进行充分保护。

* [Azure 中的虚拟网络和虚拟机](../windows/network-overview.md)

* [Azure 防火墙概述](../../firewall/overview.md)

* [Web 应用程序防火墙概述](../../web-application-firewall/overview.md)

* [网络安全概述](../../virtual-network/security-overview.md)

* [Azure 虚拟网络概述](../../virtual-network/virtual-networks-overview.md)

* [使用 Azure 管理组来组织资源](../../governance/management-groups/overview.md)

* [订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 或 Azure 安全中心来维护所有 Azure 资源的安全配置。 此外，Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足/超出公司的安全要求。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [有关如何下载 VM 模板的信息](../windows/download-template.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：使用 Azure 安全中心建议[修复虚拟机上安全配置中的漏洞]，维护所有计算资源上的安全配置。

* [如何监视 Azure 安全中心建议](../../security-center/security-center-recommendations.md)

* [如何修正 Azure 安全中心建议](../../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 资源管理器模板和 Azure 策略安全地配置与虚拟机关联的 Azure 资源。 Azure 资源管理器模板是基于 JSON 的文件，用于与 Azure 资源一起部署虚拟机，并且需要维护自定义模板。 Microsoft 对基本模板进行维护。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [有关创建 Azure 资源管理器模板的信息](../windows/ps-template.md)

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：可通过多个选项维护用于部署的 Azure 虚拟机 (VM) 的安全配置：

1- Azure 资源管理器模板：这些是基于 JSON 的文件，用于从 Azure 门户部署 VM，并且需要维护自定义模板。 Microsoft 对基本模板进行维护。

2- 自定义虚拟硬盘 (VHD)：在某些情况下，可能需要使用自定义 VHD 文件，例如在处理无法通过其他方式管理的复杂环境时。

3- Azure Automation State Configuration：部署基本 OS 后，可以将其用于更精细的设置控制，并通过自动化框架强制执行。

对于大部分方案，Microsoft 基本 VM 模板与 Azure Automation Desired State Configuration 相结合可以帮助满足和维护安全要求。

* [有关如何下载 VM 模板的信息](../windows/download-template.md)

* [有关创建 ARM 模板的信息](../windows/ps-template.md)

* [如何将自定义 VM VHD 上传到 Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps/存储库来安全地存储和管理代码，如自定义 azure 策略定义、azure 资源管理器模板、所需状态配置脚本和其他代码。 若要访问在 Azure DevOps 中管理的资源（例如代码、生成和工作跟踪），必须拥有这些特定资源的权限。 大多数权限通过内置安全组授予，如“权限和访问”中所述。 你可以向特定用户、内置安全组或者 Azure Active Directory (Azure AD)（如果与 Azure DevOps 集成）或 Active Directory（如果与 TFS 集成）中定义的组授予权限，或拒绝向其授予权限。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：如果使用自定义映像（例如，虚拟硬盘），请使用 Azure 基于角色的访问控制 (Azure RBAC) 来确保仅授权用户才能访问映像。

* [了解 Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

* [如何配置 Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：利用 Azure Policy 发出警报、执行审核和强制执行虚拟机的系统配置。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure 策略](../../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](../../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：利用 Azure 安全中心对 Azure 虚拟机执行基线扫描。 其他自动配置方法包括使用 Azure Automation State Configuration。

* [如何在 Azure 安全中心修正建议](../../security-center/security-center-remediate-recommendations.md)

* [Azure Automation State Configuration 入门](../../automation/automation-dsc-getting-started.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](../../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

* [如何与 Azure 托管标识集成](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何创建 Key Vault](../../key-vault/secrets/quick-create-portal.md)

* [如何向 Key Vault 进行身份验证](../../key-vault/general/authentication.md)

* [如何分配 Key Vault 访问策略](../../key-vault/general/assign-access-policy-portal.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

* [如何配置托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：你将需要第三方工具在 Azure Linux 虚拟机中提供反恶意软件保护。

* [如何为云服务和虚拟机配置 Microsoft Antimalware](./security-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：不适用于 Azure 虚拟机，因为它是计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：你将需要第三方工具在 Azure Linux 虚拟机中提供反恶意软件保护。

* [如何为云服务和虚拟机配置 Microsoft Antimalware](./security-recommendations.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：启用 Azure 备份并为自动化备份配置 Azure 虚拟机 (VM) 以及所需的频率和保留期。

* [概要了解 Azure VM 备份](../../backup/backup-azure-vms-introduction.md)

* [通过 VM 设置备份 Azure VM](../../backup/backup-azure-vms-first-look-arm.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：使用 PowerShell 或 REST API 创建 Azure 虚拟机或附加到这些实例的托管磁盘的快照。 备份 Azure 密钥保管库中所有客户管理的密钥。

启用 Azure 备份和目标 Azure 虚拟机 (VM)，以及所需的频率和保留期。 这包括完整的系统状态备份。 如果使用 Azure 磁盘加密，则 Azure VM 备份会自动处理客户管理密钥的备份。

* [使用加密的 Azure VM 上的备份](../../backup/backup-azure-vms-encryption.md)

* [Azure VM 备份概述](../../backup/backup-azure-vms-introduction.md)

* [了解自动备份的工作原理](../../backup/backup-azure-vms-encryption.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：确保能够定期在 Azure 备份中执行内容数据还原。 如有必要，在隔离的虚拟网络或订阅中对还原内容进行测试。 客户对还原备份的客户管理密钥进行测试。

如果你使用 Azure 磁盘加密，可以使用磁盘加密密钥还原 Azure VM。 使用磁盘加密时，可以使用磁盘加密密钥还原 Azure VM。

* [使用加密的 Azure VM 上的备份](../../backup/backup-azure-vms-encryption.md)

* [如何从 Azure 虚拟机备份恢复文件](../../backup/backup-azure-restore-files-from-vm.md)

* [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [如何备份和还原加密的 VM](../../backup/backup-azure-vms-encryption.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 azure 备份中备份 azure vm 时，将静态加密 vm，并) 存储服务加密 (SSE。 Azure 备份还可以备份使用 Azure 磁盘加密进行加密的 Azure VM。 Azure 磁盘加密还与 Azure Key Vault 密钥加密密钥 (KEK) 相集成。 在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。 

* [VM 的软删除](../../backup/soft-delete-virtual-machines.md)

* [Azure Key Vault 软删除概述](../../key-vault/general/soft-delete-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。 此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。

* [确定薄弱点和间隙，并根据需要修改计划。请参阅 NIST 发布：针对 IT 计划和功能的测试、培训和运用计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](../../security-center/workflow-automation.md)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行的现场渗透测试。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../security/benchmarks/security-baselines-overview.md)
