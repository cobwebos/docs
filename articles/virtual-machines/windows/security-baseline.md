---
title: 适用于 Linux 虚拟机 Windows 虚拟机的 Azure 安全基线
description: Windows 虚拟机安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 11b532b5ece10c8a9ac6dad61e4ca64a9f19f901
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445203"
---
# <a name="azure-security-baseline-for-windows-virtual-machines"></a>适用于 Windows 虚拟机的 Azure 安全基线

适用于 Windows 虚拟机的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：创建 Azure 虚拟机（VM）时，必须创建虚拟网络（VNet）或使用现有的 VNet，并使用子网配置 VM。 确保所有部署的子网均具有一个网络安全组，该安全组与特定于应用程序的受信任端口和源的网络访问控制一起应用。

或者，如果你有用于集中式防火墙的特定用例，还可以使用 Azure 防火墙来满足这些要求。

* [Azure 中的虚拟网络和虚拟机](./network-overview.md)

* [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署和配置 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：监视和记录虚拟网络、子网和网络接口的配置和流量

**指南**：使用 Azure 安全中心来识别和遵循网络保护建议，帮助保护 azure 中的 Azure 虚拟机（VM）资源。 启用 NSG 流日志，并将日志发送到存储帐户，以便对 Vm 进行异常活动的流量审核。

* [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [了解 Azure 安全中心提供的网络安全](../../security-center/security-center-network-recommendations.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：如果使用虚拟机（VM）来托管 web 应用程序，请使用 VM 子网上的网络安全组（NSG）来限制允许通信的网络流量、端口和协议。 将 Nsg 配置为仅允许应用程序所需的流量时，请遵循最低权限网络方法。

你还可以将 Azure Web 应用程序防火墙（WAF）部署在关键 Web 应用程序的前面，以便对传入流量进行额外检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。

* [使用 Azure 门户创建具有 Web 应用程序防火墙的应用程序网关](../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

* [有关网络安全组的信息](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意 IP 地址的通信

**指南**：对虚拟网络启用分布式拒绝服务（DDoS）标准保护，以防止 DDoS 攻击。 使用 Azure 安全中心的威胁智能，你可以监视与已知的恶意 IP 地址的通信。 在每个虚拟网络段上配置适当的 Azure 防火墙，启用威胁情报，并将其配置为针对恶意网络流量 "警报和拒绝"。

你可以使用 Azure 安全中心的实时网络访问权限，在有限期限内限制 Windows 虚拟机暴露给批准的 IP 地址。 此外，使用 Azure 安全中心的自适应网络强化建议基于实际流量和威胁智能限制端口和源 IPs 的 NSG 配置。

* [如何配置 DDoS 保护](../../virtual-network/manage-ddos-protection.md)

* [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

* [了解 Azure 安全中心集成的威胁情报](../../security-center/threat-protection.md)

* [了解 Azure 安全中心自适应网络强化](../../security-center/security-center-adaptive-network-hardening.md)

* [了解 Azure 安全中心实时网络访问控制](../../security-center/security-center-just-in-time.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：可以将 NSG 流日志记录到存储帐户，以便为 Azure 虚拟机生成流记录。 调查异常活动时，可以启用网络观察程序数据包捕获，以便查看网络流量的异常和意外活动。

* [如何启用 NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用网络观察程序](../../network-watcher/network-watcher-create.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：通过组合网络观察程序提供的数据包捕获和开源 id 工具，你可以针对各种威胁执行网络入侵检测。 此外，你可以根据需要在虚拟网络段上部署 Azure 防火墙，并为恶意网络流量启用了威胁情报并将其配置为 "警报和拒绝"。

* [通过网络观察程序和开源工具执行网络入侵检测](../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置 Azure 防火墙警报](../../firewall/threat-intel.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：你可以为受信任证书启用 HTTPS/SSL 的 web 应用程序部署 Azure 应用程序网关。 使用 Azure 应用程序网关，可将应用程序 web 流量定向到特定资源，方法是将侦听器分配给端口，创建规则，并将资源添加到后端池（如 Windows 虚拟机）。

* [如何部署应用程序网关](../../application-gateway/quick-create-portal.md)

* [如何将应用程序网关配置为使用 HTTPS](../../application-gateway/create-ssl-portal.md)

* [了解 Azure Web 应用程序网关的第七层负载均衡](../../application-gateway/overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义为 azure 虚拟机配置的网络安全组或 azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

* [了解和使用服务标记](../../virtual-network/service-tags-overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：使用 azure 策略为 Azure 虚拟机（VM）定义和实施标准安全配置。 你还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、角色分配和 Azure 策略分配）来简化大规模的 Azure VM 部署。 可以将蓝图应用于订阅，并通过蓝图版本控制启用资源管理。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network)

* [如何创建 Azure 蓝图](../../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对于网络安全组（NSG）以及为 Windows 虚拟机配置的网络安全和流量流相关的其他资源，你可以使用标记。 对于单独的 NSG 规则，请使用 "说明" 字段来指定允许进出网络流量的任何规则的业务需求和/或持续时间。

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视对虚拟机相关的网络资源配置的更改。 在 Azure Monitor 中创建警报，在对关键网络设置或资源进行更改时将触发此警报。

使用 Azure 策略来验证（和/或修正）与 Windows 虚拟机相关的网络资源的配置。

* [如何查看和检索 Azure 活动日志事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../../azure-monitor/platform/alerts-activity-log.md)

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [用于网络的 Azure Policy 示例](/azure/governance/policy/samples/#network)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 Azure 资源的时间源，但你可以选择管理 Windows 虚拟机的时间同步设置。

* [如何配置 Azure 计算资源的时间同步](./time-sync.md)

**Azure 安全中心监视**：不可用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**： Azure 安全中心为 Windows 虚拟机提供安全事件日志监视。 给定安全事件日志生成的数据量，默认情况下不存储它。

* [如果你的组织想要从虚拟机中保留安全事件日志数据，则可以将其存储在数据收集层中，此时可在 Log Analytics 中查询该数据。以下链接中详细说明了不同的层：最小、最常见和全部：](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：是

责任：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：活动日志可用于审核虚拟机资源上执行的操作和操作。 活动日志包含资源的所有写入操作（PUT、POST、DELETE），读取操作除外（GET）。 活动日志可用于在故障排除时查找错误，或监视组织中的用户如何修改资源。

通过在虚拟机（VM）上部署诊断扩展，启用来宾 OS 诊断数据的收集。 可以使用诊断扩展从 Azure 虚拟机收集诊断数据，例如应用程序日志或性能计数器。

若要深入了解虚拟机支持的应用程序和服务，可以同时启用用于 VM 的 Azure Monitor 和 Application insights。 利用 Application Insights，你可以监视应用程序并捕获遥测数据（如 HTTP 请求、异常等），以便你可以将 Vm 与应用程序之间的问题关联起来。

此外，为访问审核和活动日志（包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素）启用 Azure Monitor。

* [如何使用 Azure Monitor 收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)

* [Log analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)

* [适用于 Windows 的 Log analytics 虚拟机扩展](../extensions/oms-windows.md)

* [查看和检索 Azure 活动日志事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Application Insights 概述](../../azure-monitor/app/app-insights-overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：使用 Azure 安全中心为 Azure 虚拟机提供安全事件日志监视。 给定安全事件日志生成的数据量，默认情况下不存储它。

如果你的组织想要从虚拟机中保留安全事件日志数据，则可以将其存储在在 Azure 安全中心内配置的所需数据收集层上的 Log Analytics 工作区中。

* [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)

* [若要捕获用于监视的 Syslog 数据，您将需要启用 Log Analytics 扩展](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Azure 安全中心监视**：是

责任：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：确保用于存储虚拟机日志的任何存储帐户或 Log Analytics 工作区都具有根据你的组织的符合性规定设置的日志保持期。

* [如何监视 Azure 中的虚拟机](./monitor.md)

* [如何配置 Log Analytics 工作区保持期](../../azure-monitor/platform/manage-cost-storage.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和查看日志

**指南**：启用 Log Analytics 代理（也称为 MICROSOFT MONITORING AGENT （MMA）），并将其配置为将日志发送到 Log Analytics 工作区。 Windows 代理将不同源中收集的数据发送到 Azure Monitor 中的 Log Analytics 工作区，以及监视解决方案中定义的任何唯一日志或指标。

分析和监视日志中的异常行为，并定期查看结果。 使用 Azure Monitor 查看日志数据并执行查询。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM 来监视和查看日志。

* [Log analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)

* [适用于 Windows 的 Log analytics 虚拟机扩展](../extensions/oms-windows.md)

* [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [了解 Log Analytics 工作区](../../azure-monitor/log-query/get-started-portal.md)

* [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：使用配置了 "Log Analytics" 工作区的 Azure 安全中心来监视和发出有关 Azure 虚拟机的安全日志和事件中的异常活动的警报。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM，为异常活动设置警报。

* [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [如何在 Azure 安全中心管理警报](../../security-center/security-center-managing-and-responding-alerts.md)

* [如何针对 Log Analytics 日志数据发出警报](../../azure-monitor/learn/tutorial-response.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：你可以使用适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件，并配置虚拟机以将事件记录到 Azure 存储帐户。 配置 Log Analytics 工作区以从存储帐户中引入事件，并在适当的位置创建警报。 遵循 Azure 安全中心中的建议： "计算 &amp; 应用"。

* [如何为云服务和虚拟机配置 Microsoft Antimalware](../../security/fundamentals/antimalware.md)

* [如何为虚拟机启用来宾级别监视](../../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：根据组织的需要，从 Azure MARKETPLACE 实现 DNS 日志记录解决方案的第三方解决方案。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**： Azure 安全中心为 Azure 虚拟机（VM）提供安全事件日志监视。 安全中心在所有受支持的 Azure Vm 以及在启用自动预配时创建的任何新的 Azure Vm 以及手动安装代理时，预配 Microsoft Monitoring Agent。 该代理可启用进程创建事件 4688 和事件 4688 内的 CommandLine 字段。 VM 上创建的新进程由事件日志记录，并由安全中心检测服务监视。

* [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：尽管 Azure Active Directory 是用于管理用户访问权限的建议方法，但 Azure 虚拟机可能具有本地帐户。 应查看和管理本地和域帐户，通常只需最小的占用空间。 此外，对用于访问虚拟机资源的管理帐户使用 Azure Privileged Identity Management。

* [本地帐户的信息可在](../../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [特权标识管理器的信息](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Windows 虚拟机和 Azure Active Directory 没有默认密码的概念。 负责使用默认密码的第三方应用程序和 marketplace 服务的客户。

Azure 安全中心监视：不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：创建有关使用可访问虚拟机的专用管理帐户的标准操作过程。 使用 Azure 安全中心的标识和访问管理来监视管理帐户的数量。 用于访问 Azure 虚拟机资源的任何管理员帐户还可以由 Azure Privileged Identity Management （PIM）进行管理。 Azure Privileged Identity Management 提供多个选项，例如，在假设角色之前需要多重身份验证，并提供委派选项，使权限仅适用于特定的时间范围并需要审批者。

* [了解 Azure 安全中心的标识和访问](../../security-center/security-center-identity-access.md)

* [特权标识管理器的信息](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 单一登录（SSO）

**指导**：尽可能使用 SSO 和 Azure Active Directory，而不是为每个服务配置单独的单独凭据。 请使用 Azure 安全中心标识和访问管理建议。

* [单一登录到 Azure Active Directory 中的应用程序](../../active-directory/manage-apps/what-is-single-sign-on.md)

* [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：对管理任务使用 Azure 托管的安全工作站

**指南**：将 PAW（特权访问工作站）与为登录和配置 Azure 资源而配置的 MFA 结合使用。

* [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：在环境中发生可疑或不安全活动时，使用 AZURE AD PRIVILEGED IDENTITY MANAGEMENT （PIM）生成日志和警报。 使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。 客户可以选择将 Azure 安全中心风险检测警报引入 Azure Monitor，并使用操作组配置自定义警报/通知。

* [如何部署 Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure 安全中心风险检测（可疑活动）](../../active-directory/identity-protection/overview-identity-protection.md)

* [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何为自定义警报和通知配置操作组](../../azure-monitor/platform/action-groups.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用 Azure Active Directory 条件访问策略和命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问。

* [如何在 Azure 中配置命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (Azure AD) 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。 你可以使用托管标识向支持 Azure AD 身份验证的任何服务进行身份验证，包括 Key Vault，而无需在代码中使用任何凭据。 在虚拟机上运行的代码可以使用其托管标识来请求支持 Azure AD 身份验证的服务的访问令牌。

* [如何创建和配置 Azure AD 实例](../../active-directory-domain-services/tutorial-create-instance.md)

* [Azure 资源的托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，还可以使用 Azure Active Directory 标识访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期查看用户的访问权限，以确保只有正确的用户才能继续访问。 使用 Azure 虚拟机时，你将需要查看本地安全组和用户，以确保不存在可能危及系统安全的意外帐户。

* [如何使用 Azure 标识访问评审](../../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指南**：配置 Azure Active Directory 的诊断设置，以便将审核日志和登录日志发送到 Log Analytics 工作区。 此外，还可以使用 Azure Monitor 查看日志，并对 Azure 虚拟机中的日志数据执行查询。

* [了解 Log Analytics 工作区](../../azure-monitor/log-query/get-started-portal.md)

* [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何在 Azure Monitor 中执行自定义查询](../../azure-monitor/log-query/get-started-queries.md)

* [如何监视 Azure 中的虚拟机](./monitor.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帐户登录行为偏差时发出警报

**指南**：使用 Azure Active Directory 的风险和标识保护功能配置对检测到的与存储帐户资源相关的可疑操作的自动响应。 应通过 Azure Sentinel 启用自动响应，以实现组织的安全响应。

* [如何查看 Azure AD 风险登录](../../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：在第三方需要访问客户数据（例如在支持请求期间）的情况下，请使用 Azure 虚拟机客户密码箱查看和批准或拒绝客户数据访问请求。

* [了解客户密码箱](../../security/fundamentals/customer-lockbox-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的 Azure 虚拟机。

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实现单独的订阅和/或管理组。 资源应由虚拟网络/子网分隔、相应标记并在网络安全组（NSG）或 Azure 防火墙内受保护。 对于存储或处理敏感数据的虚拟机，请实施相应的策略和过程，以在不使用这些虚拟机时将其关闭。

* [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../../governance/management-groups/create.md)

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火墙](../../firewall/tutorial-firewall-deploy-portal.md)

* [如何配置警报或警报，以及如何拒绝 Azure 防火墙](../../firewall/threat-intel.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：在网络外围实现第三方解决方案，该解决方案监视敏感信息的未授权传输并在通知信息安全专业人员时阻止此类传输。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感数据，以防客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](../../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：通过多种方式对运行 Windows 的虚拟机（VM）之间的传输到、来源和之间的数据进行加密，具体取决于连接的性质，如连接到 RDP 或 SSH 会话中的 VM 时。

Microsoft 使用传输层安全性（TLS）协议在云服务和客户之间传输数据时对数据进行保护。

* [VM 中的传输中加密](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Azure 安全中心监视**：不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：使用第三方活动发现工具来确定由组织的技术系统存储、处理或传输的所有敏感信息，包括位于现场或远程服务提供商的信息，以及更新组织的敏感信息清单。

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用基于角色的访问控制（RBAC），可以在团队中对职责进行隔离，只向 VM 上的用户授予执行作业所需的访问权限量。 可以仅允许某些操作，而不是向 VM 上的每个人授予不受限制的权限。 可以使用 Azure CLI 或 Azure PowerShell 为 Azure 门户中的 VM 配置访问控制。

* [Azure 资源的基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)

* [Azure 内置角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：实现第三方工具（例如自动基于主机的数据丢失防护解决方案）来强制实施访问控制，以缓解数据泄露的风险。

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：使用服务器端加密或 Azure 磁盘加密（ADE）加密 WINDOWS 虚拟机（VM）上的虚拟磁盘。 Azure 磁盘加密利用 Windows 的 BitLocker 功能，通过来宾 VM 中的客户托管密钥来加密托管磁盘。 使用客户托管密钥的服务器端加密改进了 ADE，它通过加密存储服务中的数据使你可以为 VM 使用任何 OS 类型和映像。

* [Azure 托管磁盘的服务器端加密](./disk-encryption.md)

* [适用于 Windows VM 的 Azure 磁盘加密](./disk-encryption-overview.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，为虚拟机和相关资源的更改时间创建警报。

* [如何针对 Azure 活动日志事件创建警报](../../azure-monitor/platform/alerts-activity-log.md)

* [如何针对 Azure 活动日志事件创建警报](../../azure-monitor/platform/alerts-activity-log.md)

* [Azure 存储分析日志记录](../../storage/common/storage-analytics-logging.md)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：根据 Azure 安全中心的建议，执行 Azure 虚拟机上的漏洞评估。 使用 Azure 安全建议的或第三方解决方案来执行虚拟机的漏洞评估。

* [如何实现 Azure 安全中心漏洞评估建议](../../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：使用 Azure 更新管理解决方案来管理虚拟机的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 System Center Updates Publisher （Updates Publisher）等工具允许你将自定义更新发布到 Windows Server Update Services （WSUS）。 此方案允许更新管理将使用 Configuration Manager 作为其更新存储库的计算机与第三方软件配合使用。

* [Azure 中的更新管理解决方案](../../automation/update-management/update-mgmt-overview.md)

* [管理 Vm 的更新和修补程序](../../automation/update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指南**：你可以使用第三方修补程序管理解决方案。 可以使用 Azure 更新管理解决方案来管理虚拟机的更新和修补程序。 更新管理依赖于本地配置的更新存储库来修补受支持的 Windows 系统。 System Center Updates Publisher （Updates Publisher）等工具允许你将自定义更新发布到 Windows Server Update Services （WSUS）。 此方案允许更新管理将使用 Configuration Manager 作为其更新存储库的计算机与第三方软件配合使用。

* [Azure 中的更新管理解决方案](../../automation/update-management/update-mgmt-overview.md)

* [管理 Vm 的更新和修补程序](../../automation/update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：按一致的间隔导出扫描结果，并比较结果以验证是否已修正了漏洞。 使用 Azure 安全中心建议的漏洞管理建议时，客户可能会透视到所选解决方案的门户以查看历史扫描数据。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：使用 Azure 安全中心提供的默认风险评级（安全功能分数）。

* [了解 Azure 安全中心安全功能分数](../../security-center/secure-score-security-controls.md)

**Azure 安全中心监视**：是

责任：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有资源（包括虚拟机）。 确保你在租户中拥有适当的（读取）权限，并且可以枚举所有 Azure 订阅，以及订阅中的资源。

* [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据根据分类逻辑组织它们。

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不可用

责任：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪虚拟机及相关资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

* [如何创建其他 Azure 订阅](../../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../../governance/management-groups/create.md)

* [如何创建和使用标记](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义和维护已批准的 Azure 资源的清单

**指南**：应为计算资源创建已批准的 Azure 资源和批准的软件的清单。 你还可以使用 Azure 安全中心的自适应应用程序控件，来帮助你定义一组允许在已配置的计算机上运行的应用程序。 此功能适用于 Azure 和非 Azure Windows（所有版本，不管是经典部署模型还是 Azure 资源管理器部署模型）和 Linux 计算机。

         

* [如何启用自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：在 Azure 策略中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

此外，请使用 Azure Resource Graph 来查询/发现订阅中的资源。 这可以帮助实现基于高安全性的环境，如具有存储帐户的环境。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 创建查询](../../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 利用 Azure 虚拟机库存自动收集有关虚拟机上所有软件的信息。 注意：可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取安装日期和其他信息的访问权限，客户需要启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。

除了使用更改跟踪来监视软件应用程序外，Azure 安全中心的自适应应用程序控件还可以使用机器学习来分析计算机上运行的应用程序，并通过此智能创建允许列表。 此功能极大地简化了配置和维护应用程序允许列表策略的过程，使你能够避免在你的环境中使用不需要的软件。 你可以配置审核模式或强制模式。 审核模式只审核受保护 Vm 上的活动。 强制模式确实强制执行这些规则，并确保阻止不允许运行的应用程序。

* [Azure 自动化简介](../../automation/automation-intro.md)

* [如何启用 Azure VM 清单](../../automation/automation-tutorial-installed-software.md)

* [了解自适应应用程序控件](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：可以通过 Azure 自动化在工作负荷和资源的部署、操作和解除授权过程中进行完全的控制。 你可以使用更改跟踪来确定在虚拟机上安装的所有软件。 你可以实现自己的进程，或使用 Azure 自动化状态配置来删除未经授权的软件。

* [Azure 自动化简介](../../automation/automation-intro.md)

* [使用更改跟踪解决方案跟踪环境中的更改](../../automation/change-tracking.md)

* [Azure 自动化 State Configuration 概述](../../automation/automation-dsc-overview.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：使用 Azure 安全中心自适应应用程序控制，以确保仅执行授权的软件，并且阻止所有未经授权的软件在 Azure 虚拟机上执行。

* [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略将限制添加到可使用以下内置策略定义在客户订阅中创建的资源类型：-不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：是

责任：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指南**：自适应应用程序控制是 Azure 安全中心的一个智能、自动化的端到端解决方案，可帮助你控制哪些应用程序可在 azure 和非 Azure 计算机（Windows 和 Linux）上运行。 如果这不能满足组织的要求，请实现第三方解决方案。

* [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：使用 Azure 条件访问可通过为“Microsoft Azure 管理”应用配置“阻止访问”，限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：根据脚本类型，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。 还可以利用 Azure 安全中心自适应应用程序控制来确保仅执行已授权软件，并阻止所有未授权软件在 Azure 虚拟机上执行。

* [如何在 Windows 环境中控制 PowerShell 脚本的执行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [如何使用 Azure 安全中心自适应应用程序控制](../../security-center/security-center-adaptive-application.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：可以使用虚拟网络、子网、订阅、管理组等隔离部署在 azure 环境中的高风险应用程序，并使用 Azure 防火墙、Web 应用程序防火墙（WAF）或网络安全组（NSG）对其进行充分的保护。

* [Azure 中的虚拟网络和虚拟机](./network-overview.md)

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

**指导**：使用 Azure Policy 或 Azure 安全中心来维护所有 Azure 资源的安全配置。 此外，Azure 资源管理器可以在 JavaScript 对象表示法（JSON）中导出模板，应查看该模板以确保配置满足/超过公司的安全要求。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [有关如何下载 VM 模板的信息](./download-template.md)

**Azure 安全中心监视**：不可用

责任：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：使用 Azure 安全中心建议 [更正虚拟机上的安全配置漏洞] 来维护所有计算资源的安全配置。

* [如何监视 Azure 安全中心建议](../../security-center/security-center-recommendations.md)

* [如何修正 Azure 安全中心建议](../../security-center/security-center-remediate-recommendations.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 azure 资源管理器模板和 azure 策略安全配置与虚拟机关联的 azure 资源。 Azure 资源管理器模板是基于 JSON 的文件，用于将虚拟机和 Azure 资源一起部署，并且需要维护自定义模板。 Microsoft 对基本模板执行维护。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [有关创建 Azure 资源管理器模板的信息](./ps-template.md)

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：对于用于维护部署的 Azure 虚拟机（VM），有几个选项可供选择：

1-Azure 资源管理器模板：这些是用于从 Azure 门户部署 VM 的基于 JSON 的文件，需要维护自定义模板。 Microsoft 对基本模板执行维护。

2-自定义虚拟硬盘（VHD）：在某些情况下，可能需要使用自定义 VHD 文件，如处理无法通过其他方式管理的复杂环境时。

3-Azure 自动化状态配置：部署基本操作系统后，可使用此功能对设置进行更细致的控制，并通过自动化框架强制执行。

在大多数情况下，与 Azure 自动化所需状态配置结合的 Microsoft 基本 VM 模板可以帮助满足和维护安全要求。

* [有关如何下载 VM 模板的信息](./download-template.md)

* [有关创建 ARM 模板的信息](./ps-template.md)

* [如何将自定义 VM VHD 上传到 Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure Repos 安全地存储和管理代码，如自定义 azure 策略、azure 资源管理器模板、所需状态配置脚本等。若要访问在 Azure DevOps 中管理的资源（例如代码、生成和工作跟踪），必须拥有这些特定资源的权限。 大多数权限通过内置安全组授予，如 "权限和访问" 中所述。 如果与 Azure DevOps 集成，则可以授予或拒绝特定用户、内置安全组或 Azure Active Directory （Azure AD）中定义的组，如果与 TFS 集成，则可以为 Active Directory 授予或拒绝权限。

* [如何在 Azure DevOps 中存储代码](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [关于 Azure DevOps 中的权限和组](/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：如果使用自定义映像（例如虚拟硬盘），请使用 Azure 基于角色的访问控制，以确保只有经过授权的用户才能访问映像。

* [了解 Azure 中的 RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

* [如何在 Azure 中配置 RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指南**：利用 Azure 策略为虚拟机提供警报、审核和强制系统配置。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指南**： Azure 自动化状态配置是适用于任何云或本地数据中心内的所需状态配置（DSC）节点的配置管理服务。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](../../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：利用 Azure 安全中心对 Azure 虚拟机执行基线扫描。 使用 Azure 自动化状态配置的其他自动配置方法包括。

* [如何在 Azure 安全中心修正建议](../../security-center/security-center-remediate-recommendations.md)

* [Azure Automation State Configuration 入门](../../automation/automation-dsc-getting-started.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**： Azure 自动化状态配置是适用于任何云或本地数据中心内的所需状态配置（DSC）节点的配置管理服务。 它可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

* [加入 Azure Automation State Configuration 管理的计算机](../../automation/automation-dsc-onboarding.md)

**Azure 安全中心监视**：不可用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：将托管服务标识与 Azure Key Vault 结合使用，以便简化和保护云应用程序的机密管理。

* [如何与 Azure 托管标识集成](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何创建 Key Vault](../../key-vault/secrets/quick-create-portal.md)

* [如何使用托管标识提供 Key Vault 身份验证](../../key-vault/general/managed-identity.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

* [如何配置托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 安全中心监视**：不可用

责任：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：使用适用于 Azure Windows 虚拟机的 Microsoft 反恶意软件持续监视和保护你的资源。

* [如何为云服务和虚拟机配置 Microsoft Antimalware](../../security/fundamentals/antimalware.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：不适用于 Azure 虚拟机，因为它是计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：部署后，默认情况下，适用于 Azure 的 Microsoft 反恶意软件将自动安装最新的签名、平台和引擎更新。 请遵循 Azure 安全中心中的建议：“计算和应用”用于确保所有终结点都具有最新的签名。 Windows OS 可以通过附加的安全性进行进一步保护，以通过与 Azure 安全中心集成的 Microsoft Defender 高级威胁防护服务来限制受病毒或恶意软件攻击的风险。

* [如何为 Azure 云服务和虚拟机部署 Microsoft Antimalware](../../security/fundamentals/antimalware.md)

* [Microsoft Defender 高级威胁防护](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 安全中心监视**：是

责任：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期自动备份

**指南**：启用 azure 备份并配置 azure 虚拟机（VM），以及自动备份所需的频率和保留期。

* [概要了解 Azure VM 备份](../../backup/backup-azure-vms-introduction.md)

* [通过 VM 设置备份 Azure VM](../../backup/backup-azure-vms-first-look-arm.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用 POWERSHELL 或 REST Api 创建 Azure 虚拟机或附加到这些实例的托管磁盘的快照。 在 Azure Key Vault 中备份任何客户管理的密钥。

启用 Azure 备份和目标 Azure 虚拟机（VM），以及所需的频率和保留期。 这包括完整的系统状态备份。 如果使用 Azure 磁盘加密，Azure VM 备份会自动处理客户托管密钥的备份。

* [使用加密的 Azure Vm 上的备份](../../backup/backup-azure-vms-encryption.md)

* [Azure VM 备份概述](../../backup/backup-azure-vms-introduction.md)

* [概要了解 Azure VM 备份](../../backup/backup-azure-vms-introduction.md)

* [如何在 Azure 中备份密钥保管库密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：是

责任：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：确保能够定期执行 Azure 备份中的内容数据还原。 如有必要，请测试将内容还原到隔离的虚拟网络或订阅。 用于测试已备份客户托管密钥的还原的客户。

如果你使用的是 Azure 磁盘加密，则可以使用磁盘加密密钥还原 Azure VM。 使用磁盘加密时，可以使用磁盘加密密钥还原 Azure VM。

* [使用加密的 Azure Vm 上的备份](../../backup/backup-azure-vms-encryption.md)

* [如何从 Azure 虚拟机备份恢复文件](../../backup/backup-azure-restore-files-from-vm.md)

* [如何在 Azure 中还原密钥保管库密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [如何备份和还原已加密的 VM](../../backup/backup-azure-vms-encryption.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：在 azure 备份中备份 azure 托管磁盘时，会将 vm 静态加密存储服务加密（SSE）。 Azure 备份还可以备份使用 Azure 磁盘加密进行加密的 Azure VM。 Azure 磁盘加密与在 Key Vault 中作为机密受到保护的 BitLocker 加密密钥 (BEK) 相集成。 Azure 磁盘加密还与 Azure Key Vault 密钥加密密钥 (KEK) 相集成。 在 Key Vault 中启用软删除，以防意外或恶意删除密钥。

* [VM 的软删除](../../backup/soft-delete-virtual-machines.md)

* [Azure Key Vault 软删除概述](../../key-vault/general/soft-delete-overview.md)

**Azure 安全中心监视**：是

责任：客户

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

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心对用于发出警报的调查结果或分析的置信度，以及导致警报的活动背后存在恶意意图的可信度。

此外，使用标记清楚地标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

责任：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不可用

责任：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能，通过“逻辑应用”针对安全警报和建议自动触发响应，以保护 Azure 资源。

* [如何配置工作流自动化和逻辑应用](../../security-center/workflow-automation.md)

**Azure 安全中心监视**：不可用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指南**：遵循 Microsoft 订婚规则确保你的渗透测试不违反 Microsoft 政策。 针对 Microsoft 托管的云基础结构、服务和应用程序，使用 Microsoft 的战略和对红色组合和活动站点渗透测试的执行。

* [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../security/benchmarks/security-baselines-overview.md)
