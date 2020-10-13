---
title: Azure 负载均衡器的 azure 安全基线
description: Azure 负载均衡器安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6d03f0057e657933f4b0f38c48f2a2cdbe1ba2a4
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940321"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure 负载均衡器的 azure 安全基线

Microsoft Azure 负载均衡器的 Azure 安全基线包含的建议可帮助你提高部署的安全状况。 此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。 有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全性

[有关详细信息，请参阅 *Azure 安全基线：* 网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指南**：使用内部 Azure 负载均衡器，仅允许从特定虚拟网络内部到后端资源的流量，或对等互连虚拟网络的流量，而无需向 internet 公开。 使用源网络地址转换来实现外部负载均衡器 (SNAT) 来伪装后端资源的 IP 地址，以防止直接 internet 暴露。

Azure 提供两种类型的负载均衡器产品/服务：标准版和基本版。 将标准负载均衡器用于所有生产工作负荷。 实现网络安全组，只允许访问应用程序的受信任端口和 IP 地址范围。 如果没有为后端虚拟机的后端子网或 NIC 分配网络安全组，则不允许流量从负载均衡器发送到这些资源。 使用标准负载均衡器，提供出站规则，以使用网络安全组定义出站 NAT。 查看这些出站规则以优化出站连接的行为。 

建议为生产工作负荷使用标准负载均衡器，通常，基本负载均衡器仅用于测试，因为默认情况下基本类型已开放到来自 internet 的连接，并且不需要网络安全组即可执行操作。 

- [Azure 中的出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#outboundrule)

- [升级 Azure 公共负载均衡器](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指南**：负载均衡器是一种直通服务，因为它依赖于应用于后端资源的网络安全组规则和配置的出站规则来控制 internet 访问。

通过负载均衡器的 "出站规则" 边栏选项卡和 "负载均衡规则" 边栏选项卡（可在其中启用了隐式出站规则）查看为你的标准负载均衡器配置的出站规则。

监视出站连接数，以跟踪资源到达 internet 的频率。 

使用安全中心并按照网络保护建议来帮助保护 Azure 网络资源。

遵循后端资源的安全性建议，启用网络安全组流日志，并将日志发送到 Azure 存储帐户进行审核。

同时将流日志发送到 Log Analytics 工作区，然后使用流量分析提供对 Azure 云中流量模式的见解。 流量分析的优点包括：直观显示网络活动、识别热点和安全威胁、了解流量流模式以及查明网络配置错误。

- [如何启用网络安全组流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [了解 Azure 安全中心提供的网络安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [如何实现检查我的出站连接统计信息](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：使用负载均衡器，通过出站规则和网络安全组显式定义 internet 连接和有效源 ip，以使用 Microsoft 的威胁情报来保护 web 应用程序。

- [集成 Azure 防火墙](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在 Azure 虚拟网络上启用 Azure 分布式拒绝服务 (DDoS) 标准保护，防止 DDoS 攻击。 

在组织的每个网络边界上部署 Azure 防火墙，启用基于威胁情报的筛选并将其配置为针对恶意网络流量执行“发出警报并拒绝”操作。

 

使用安全中心威胁防护来检测与已知的恶意 IP 地址的通信。 

默认情况下，标准负载均衡器是安全的，并且是专用和隔离的虚拟网络的一部分。 除非由网络安全组打开以明确允许允许的流量，并且不允许已知的恶意 IP 地址，否则会关闭入站流。 除非虚拟机资源的子网或 NIC 上的网络安全组位于负载均衡器后面，否则不允许流量到达此资源。 

在组织的每个网络边界上部署 Azure 防火墙，并为恶意网络流量启用基于威胁智能的筛选并将其配置为 "警报并拒绝"，以防止恶意 IP 地址遭受攻击。 

实现将 Azure 防火墙与负载均衡器集成的指导。

使用安全中心威胁防护功能检测与已知的恶意 IP 地址的通信。 

安全中心 (标准层) 提供实时虚拟机访问，并将允许的源 IP 地址配置为仅允许来自已批准的 IP 地址/范围的访问。
 

使用安全中心的自适应网络强化功能推荐网络安全组配置，这些配置基于实际流量和威胁智能限制端口和源 Ip。
 

- [使用 Azure 门户管理 Azure DDoS 防护标准](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [基于 Azure 防火墙威胁智能的筛选](https://docs.microsoft.com/azure/firewall/threat-intel)

- [Azure 安全中心的威胁防护](https://docs.microsoft.com/azure/security-center/threat-protection)

- [使用实时访问保护管理端口](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Azure 安全中心内的自适应网络强化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [将 Azure 防火墙与负载均衡器集成](https://docs.microsoft.com/azure/firewall/overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指南**：启用网络观察程序数据包捕获来调查异常活动。

- [如何创建网络观察程序实例](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：从 Azure Marketplace 实现一个产品/服务，该产品支持具有负载检查功能的 ID/IPS 功能到负载均衡器的环境。 

如果不需要进行负载检查，请使用 Azure 防火墙威胁情报。 使用基于 Azure 防火墙威胁情报的筛选功能，针对进出已知恶意 IP 地址和域的流量发出警报并/或阻止该流量。 IP 地址和域源自 Microsoft 威胁智能源。

在组织的每个网络边界上部署所选的防火墙解决方案，以检测并/或阻止恶意流量。

- [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [如何配置 Azure 防火墙警报](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：使用负载均衡器，通过出站规则和网络安全组显式定义 internet 连接和有效源 ip，以使用 Microsoft 的威胁情报功能来保护 web 应用程序。

- [集成 Azure 防火墙](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：创建安全规则时，使用服务标记代替特定的 IP 地址。 在规则的 "源" 或 "目标" 字段中指定服务标记名称，以允许或拒绝相应服务的流量。 

Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 

默认情况下，每个网络安全组都包含服务标记 AzureLoadBalancer，以允许运行状况探测流量。 

请参阅 Azure 文档，了解可在网络安全组规则中使用的所有服务标记。

- [可用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现 Azure 策略的网络资源的标准安全配置。

使用 Azure 蓝图可以通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure RBAC 控件和策略）来简化大规模的 Azure 部署。 

将蓝图应用到新的订阅，并通过版本控制来微调控制和管理。

- [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [用于网络的 Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对网络安全组和与网络安全和流量流相关的其他资源使用资源标记。 

使用 "说明" 字段记录允许来自网络的流量和网络安全组规则的规则。

实现任何与标记相关的内置 Azure 策略定义，如 "需要标记和值"，这可确保所有资源都用标记创建，并通知所有现有未标记的资源。

使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找或执行对资源的操作。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何创建 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何使用网络安全组规则筛选网络流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 azure 活动日志监视资源配置，并检测对 Azure 资源所做的更改。 

在 Azure Monitor 中创建警报，以便在关键资源发生更改时通知你。

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：查看订阅中的活动日志，查看负载均衡器的出站规则和网络安全组更改。 

查看内部主机日志，以确保后端资源是安全的。

将这些日志导出到 Log Analytics 或其他存储平台。 在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析，并使用 Azure 存储帐户进行长期存档存储。

基于组织业务要求，启用此数据并将其基于你的 Azure Sentinel 或第三方 SIEM。

- [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何使用 Azure Monitor 收集平台日志和指标](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [如何使用 Azure Monitor 收集 Azure 虚拟机内部主机日志](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [平台活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：查看在基本负载均衡器的活动日志中捕获的控制和管理平面日志记录和审核信息。 默认情况下，将启用这些捕获设置。 

使用活动日志监视资源的操作，以查看所有活动及其状态。 

确定对包含活动日志的订阅中的资源执行了哪些操作： 

- 谁启动了该操作
- 操作何时发生
- 操作的状态

- 其他可能有助于研究操作的属性的值

通过 Azure PowerShell、Azure 命令行接口 (CLI) 、Azure REST API 或 Azure 门户从活动日志中检索信息。 

为 Azure Monitor 的标准负载均衡器配置功能实现多维诊断。  其中包括适用于安全的指标，其中包括有关源网络地址转换 (SNAT) 连接、端口的信息。 还提供有关 SYN (同步) 数据包和数据包计数器的其他指标。 

通过 Api 以编程方式检索多维指标，并通过 "所有指标" 选项将它们写入存储帐户。

使用 Azure 审核日志内容包和 Microsoft Power BI 通过预配置的仪表板分析数据，或者根据需求自定义视图。

将日志流式传输到事件中心或 Log Analytics 工作区。 它们还可以从 Azure blob 存储提取，并在不同的工具（如 Excel 和 Power BI）中查看。 

根据你的业务需求，将和机载数据启用到 Azure Sentinel 或第三方 SIEM。

- [请参阅此文，其中包含使用资源管理器审核操作中详细说明的每个方法的分步说明。](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [公共基本负载均衡器的 Azure Monitor 日志](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [查看活动日志以监视对资源的操作](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [通过 API 以编程方式检索多维指标](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [如何开始将 Azure Monitor 与第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：默认情况下启用活动日志，并在 Azure 的事件日志存储中保留90天。 根据组织的 Azure Monitor 中的符合性法规设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期存储和存档存储。

- [查看活动日志以监视资源的操作一文](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

- [更改 Log Analytics 中的数据保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [如何为 Azure 存储帐户日志配置保留策略](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：使用 "Azure 门户中的" 负载均衡器 "页和 Azure Monitor 下的资源运行状况页中的" 负载均衡器 "页监视、管理和解决标准负载均衡器资源。 可用的安全指标包括有关源网络地址转换 (SNAT) 连接、端口的信息。 此外还提供了有关 SYN (同步) 数据包和数据包计数器的指标。 

使用 Azure Monitor 可通过标准、外部和内部负载均衡器的多维指标来查看终结点运行状况探测状态。 通过 Api 以编程方式收集这些指标，并通过 "所有指标" 选项写入存储帐户。

Azure Monitor 日志不适用于内部基本负载均衡器。 

使用 Azure Monitor 查看基本外部负载均衡器的每个后端池的运行状况探测状态，例如，由于运行状况探测失败，后端池中的实例数未收到负载均衡器发出的请求。 

通过 Azure Operational Insights 实现日志记录，以提供有关负载均衡器运行状况的统计信息。 

使用活动日志查看有关 Azure 订阅中的资源及其状态的警报和监视操作。 活动日志默认情况下启用，并且可以在 Azure 门户中查看。 

结合使用 Microsoft Power BI 与 Azure 审核日志内容包，并使用预配置的仪表板分析数据。 根据业务需求自定义视图以满足你的需求。 

将日志流式传输到事件中心或 Log Analytics 工作区。 它们还可以从 Azure blob 存储提取，并在不同的工具（如 Excel 和 Power BI）中查看。 可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。

- [负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

- [Azure 监视器 REST API](https://docs.microsoft.com/rest/api/monitor)

- [如何通过 REST API 检索度量值](https://docs.microsoft.com/rest/api/monitor/metrics/list)

- [标准负载均衡器诊断以及指标、警报和资源运行状况](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)

- [公共基本负载均衡器的 Azure Monitor 日志](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [在 Azure 门户中查看负载均衡器指标](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指南**：将安全中心与 Log Analytics 工作区结合使用，以便在安全日志和事件中与负载均衡器相关的异常活动上进行监视和警报。

启用和板数据到 Azure Sentinel 或第三方 SIEM 工具。

- [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何在 Azure 安全中心管理警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [如何针对 Log Analytics 日志数据发出警报](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用于 Azure 负载均衡器。 此建议用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用于 Azure 负载均衡器是一种核心网络服务，不会进行 DNS 查询。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：此建议适用于计算资源，不适用于 Azure 负载均衡器。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： azure RBAC) 的基于角色的访问控制 (允许通过角色分配管理对 azure 资源（例如负载均衡器）的访问权限。 将这些角色分配给用户、组服务主体和托管标识。 

Azure CLI、Azure PowerShell 或 Azure 门户等工具为某些资源列出了预定义和内置角色。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 管理对资源的访问

**指南**：使用 Azure RBAC 控制对负载均衡器资源的访问。

- [如何在 Azure 中配置 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：负载均衡器是一种传递服务，不会存储客户数据。 它是由 Microsoft 管理的底层平台的一部分。 

Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 

为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护一套可靠的数据保护控件和功能。 

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以便在对关键 Azure 资源进行更改时（例如用于重要生产工作负荷的负载均衡器）创建警报。

- [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指南**：使用 Azure 资源关系图可查询和发现订阅中的所有资源 (如计算、存储、网络、端口、协议等) 。 建议使用 Azure 资源管理器来创建和使用当前资源。 

确保适当的 (读取租户中的) 权限，并枚举订阅中的所有 Azure 订阅和资源。

- [如何使用 Azure Resource Graph 浏览器创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：按照分类将标记应用于 Azure 资源，以逻辑方式组织数据。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指导**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪资产。 

定期协调清点，并确保以及时方式从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准 Azure 资源的清单

**指南**：根据你的组织需求创建已批准的 Azure 资源列表，你可以将其用作允许列表机制。 这样，你的组织便可以在你的组织的典型安全评估流程正式查看和批准后，载入任何新提供的 Azure 服务。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

在拥有的订阅中查询和发现 Azure 资源图中的资源。 

确保环境中存在的所有 Azure 资源都已获得批准。

- [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure Resource Graph 浏览器创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：通过为“Microsoft Azure 管理”应用配置“阻止访问”，使用 Azure AD 条件访问来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：业务运营所需的软件，但可能会对组织造成更高的风险，应在其自己的虚拟机和/或虚拟网络中隔离，并使用 Azure 防火墙或网络安全组进行充分的保护。

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建具有安全配置的网络安全组](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

[有关详细信息，请参阅 *Azure 安全基线：* 安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的配置。 使用内置的 Azure 策略定义。

Azure 资源管理器能够以 JavaScript 对象表示法 (JSON) 导出模板，应该对其进行检查，以确保配置满足组织的安全要求。

将 Azure 资源管理器模板导出到 JavaScript 对象表示法 (JSON) 格式，并定期查看这些模板以确保配置符合组织的安全要求。 

作为 Azure 资源的安全配置基线，实现安全中心提供的建议。 

- [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施符合性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [在 Azure 门户中将单资源和多资源导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [安全建议 - 参考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。  此外，还可以使用 Azure 资源管理器模板来维护组织所需的 Azure 资源的安全配置。 

- [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [创建和管理策略以强制实施符合性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 资源管理器模板概述](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：使用 Azure DevOps 来安全地存储和管理代码，如自定义 azure 策略定义、Azure 资源管理器模板和所需状态配置脚本。 

在 Azure Active Directory) Azure AD (中定义的特定用户、内置安全组或组授予或拒绝权限（如果该用户已与 Azure DevOps 集成）或在 Active Directory （如果与 TFS 集成）中定义。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：使用 Azure Policy 为 Azure 资源定义和实施标准安全配置。  使用 Azure Policy 别名创建自定义策略，审核或强制实施 Azure 资源的网络配置。 实现与特定 Azure 负载均衡器资源相关的内置策略定义。  同时，使用 Azure 自动化来部署配置更改。 Azure 负载均衡器资源。

- [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用别名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指南**：使用安全中心对 azure 资源执行基线扫描，并使用 azure 策略对资源配置进行警报和审核。

- [如何在 Azure 安全中心修正建议](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

[有关详细信息，请参阅 *Azure 安全基线：* 事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 

严重性取决于安全中心在查找或用于发出警报的分析中的置信度，以及导致警报的活动的恶意意图的置信度。

使用标记来标记订阅，并创建命名系统来标识和分类 Azure 资源，尤其是处理敏感数据的资源。  

你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

- [Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用标记整理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出安全中心警报和建议，以帮助确定 Azure 资源的风险。 

使用安全中心的连续导出功能，可以手动或以持续、持续的方式导出警报和建议。 

利用安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用安全中心中的工作流自动化功能可以自动触发对安全警报和建议的响应，以保护 Azure 资源。

- [如何在安全输入中配置工作流自动化](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

[有关详细信息，请参阅 *Azure 安全基线：* 渗透测试和红队演练](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**：请遵循 Microsoft 云渗透测试互动规则，确保你的渗透测试不违反 Microsoft 政策。 使用 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试。 

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
