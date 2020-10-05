---
title: API 管理的 Azure 安全基线
description: API 管理的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4db7727558d0b7f4c26aba3ffa823bebb07862f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713785"
---
# <a name="azure-security-baseline-for-api-management"></a>API 管理的 Azure 安全基线

API 管理的 Azure 安全基线包含可帮助你改善部署安全状态的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](../security/benchmarks/overview.md)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>网络安全性

有关详细信息，请参阅[安全控制：网络安全](../security/benchmarks/security-control-network-security.md)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指南**：可以将 Azure API 管理部署到 Azure 虚拟网络 (Vnet) 内部，以便它可以访问该网络中的后端服务。 可以将开发人员门户和 API 管理网关配置为可以从 Internet（外部）访问或只能在 Vnet（内部）内访问。
- 外部：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。
- 内部：只能通过内部负载均衡器从虚拟网络内部访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

可以使用网络安全组控制其中部署了 API 管理的子网的入站和出站流量。

* [如何在虚拟网络中使用 Azure API 管理](./api-management-using-with-vnet.md)

* [将 Azure API 管理服务与内部虚拟网络配合使用](./api-management-using-with-internal-vnet.md)

* [在包含应用程序网关的内部 VNET 中集成 API 管理](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指南**：可以使用网络安全组 (NSG) 控制其中部署了 API 管理的子网的入站和出站流量。 将 NSG 部署到 API 管理子网，启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解 Azure API 管理的 NSG 配置](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：若要保护关键 Web/HTTP API，请在内部模式下的虚拟网络 (Vnet) 中配置 API 管理，并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

* [如何在包含应用程序网关的内部 VNET 中集成 API 管理](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [了解 Azure 应用程序网关](../application-gateway/index.yml)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指南**：在内部模式下的虚拟网络 (Vnet) 中配置 API 管理，并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

在与 API 管理部署关联的 Vnet 中启用 Azure DDoS 防护标准，以防受到分布式拒绝服务 (DDoS) 攻击。

根据 Azure 安全中心集成的威胁情报进行判断，拒绝与已知恶意的或未使用过的 Internet IP 地址通信。

* [如何在包含应用程序网关的内部 VNET 中集成 API 管理](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [了解 Azure 应用程序网关](../application-gateway/index.yml)

* [如何配置 Azure DDoS 防护标准](../virtual-network/manage-ddos-protection.md)

* [了解 Azure 安全中心的集成式威胁情报](../security-center/threat-protection.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：可以使用网络安全组 (NSG) 控制其中部署了 API 管理的子网的入站和出站流量。 将 NSG 部署到 API 管理子网，启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解 Azure API 管理的 NSG 配置](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [如何启用 NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何启用和使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：在内部模式下的虚拟网络 (Vnet) 中配置 API 管理，并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。 应用程序网关 WAF 提供对常见安全攻击和漏洞的保护，可以在以下两种模式下运行：
- 检测模式：监视并记录所有威胁警报。 可在“诊断”部分为应用程序网关启用日志记录诊断。 必须确保已选择并启用 WAF 日志。 在检测模式下运行时，Web 应用程序防火墙不会阻止传入的请求。
- 防护模式：阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 阻止模式会在 WAF 日志中记录此类攻击。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

* [如何在包含应用程序网关的内部 VNET 中集成 API 管理](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [了解 Azure 应用程序网关 WAF](../web-application-firewall/ag/ag-overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：若要管理流向 Web/HTTP API 的流量，请将 API 管理部署到与外部或内部模式下的应用服务环境关联的虚拟网络 (Vnet)。

在内部模式下，在 API 管理前面配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。 应用程序网关 WAF 提供对常见安全攻击和漏洞的保护。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

* [如何向外部使用者公开专用 API](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [如何在 Vnet 中使用 API 管理](./api-management-using-with-vnet.md)

* [Azure 应用程序网关上的 Azure Web 应用程序防火墙](../web-application-firewall/ag/ag-overview.md)

* [了解 Azure 应用程序网关](../application-gateway/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：在 API 管理子网上使用的网络安全组 (NSG) 中，使用虚拟网络 (Vnet) 服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解并使用服务标记](../virtual-network/service-tags-overview.md)

* [API 管理所需的端口](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义并实现与 Azure API 管理部署相关的网络设置的标准安全配置。 在“Microsoft.ApiManagement”和“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理部署及其相关资源的网络配置。 还可以利用 Azure 虚拟网络的内置策略定义，例如：
- 应启用 DDoS 防护标准版

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 可以轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何创建 Azure 蓝图](../governance/blueprints/create-blueprint-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：将标记用于网络安全组 (NSG) 以及其他与网络安全和流量流有关的资源。 对于单个 NSG 规则，可以使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何创建虚拟网络](../virtual-network/quick-create-portal.md)

* [如何创建采用安全配置的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure API 管理部署关联的网络资源的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-activity-log.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：由 Microsoft 维护 Azure API 管理的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，将日志发送到 Azure 存储以进行长期/存档存储或脱机分析，或者使用 Azure 事件中心将日志导出到 Azure 上和其他位置的其他分析解决方案。 默认情况下，Azure API 管理会将日志和指标输出到 Azure Monitor。 可以根据服务范围和每个 API 来配置日志记录的详细程度。

除 Azure Monitor 之外，Azure API 管理还可与一个或多个 Azure Application Insights 服务集成。 可以根据每个服务或每个 API 来配置 Application Insights 的日志记录设置。

（可选）启用并将数据加入 Azure Sentinel 或第三方安全事件和事件管理 (SIEM)。

* [如何配置诊断设置](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [如何开始使用 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [如何创建自定义日志记录和分析管道](./api-management-howto-log-event-hubs.md)

* [如何与 Azure Application Insights 集成](./api-management-howto-app-insights.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对于控制平面审核日志记录，启用 Azure 活动日志诊断设置并将活动日志发送到 Log Analytics 工作区进行报告和分析，将其发送到 Azure 存储以进行长期保护，将其发送到 Azure 事件中心以导出到 Azure 和其他位置的其他分析解决方案。 使用 Azure 活动日志数据，可以确定在 Azure API 管理服务的控制平面级别执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

对于数据平面审核日志记录，诊断日志提供大量有关操作和错误的信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。 活动日志提供有关对 Azure 资源执行的操作的见解。 诊断日志提供资源执行的操作的深入信息。

* [如何启用 Azure 活动日志的诊断设置](../azure-monitor/platform/activity-log.md)

* [如何启用 Azure API 管理的诊断设置](./api-management-howto-use-azure-monitor.md#activity-logs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 使用 Azure 存储帐户进行长期/存档存储。

* [如何为 Log Analytics 工作区设置日志保留参数](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [如何将日志存档到 Azure 存储帐户](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：Azure API 管理不断地将日志和指标发送到 Azure Monitor，以便你能够以准实时的速度了解 API 的状态和运行状况。 通过 Azure Monitor 和 Log Analytics 工作区，你可以审查、查询、可视化、路由、存档、配置警报，并对来自 API 管理和相关资源的指标和日志采取措施。 分析和监视日志中的异常行为，并定期审查结果。

（可选）将 API 管理与 Azure Application Insights 集成，并将其用作主要或辅助监视、跟踪、报告和警报工具。

* [如何监视和审查 Azure API 管理的日志](./api-management-howto-use-azure-monitor.md)

* [如何在 Azure Monitor 中执行自定义查询](../azure-monitor/log-query/get-started-queries.md)

* [了解 Log Analytics 工作区](../azure-monitor/log-query/get-started-portal.md)

* [如何与 Azure Application Insights 集成](./api-management-howto-app-insights.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：针对异常活动启用警报

**指南**：启用 Azure 活动日志诊断设置以及 Azure API 管理实例的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的数据提供许多其他见解。 可以根据 Log Analytics 工作区查询来创建警报。

设置指标警报，以便在发生意外的情况时收到通知。 例如，当 Azure API 管理实例在某个时间段内超过预期的峰值容量，或在预定义的时间段内发生了特定数量的未经授权的网关请求或错误时，你会收到通知。

（可选）将 API 管理与 Azure Application Insights 集成，并将其用作主要或辅助监视、跟踪、报告和警报工具。

（可选）可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何启用 Azure 活动日志的诊断设置](../azure-monitor/platform/activity-log.md)

* [如何启用 Azure API 管理的诊断设置](./api-management-howto-use-azure-monitor.md#activity-logs)

* [如何针对未经授权的请求配置警报规则](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule-for-unauthorized-request)

* [如何查看 Azure API 管理实例的容量指标](./api-management-capacity.md)

* [如何与 Azure Application Insights 集成](./api-management-howto-app-insights.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用；Azure API 管理不会处理或生成与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用；Azure API 管理不会处理或生成与用户可访问的 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../security/benchmarks/security-control-identity-access-control.md)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护对 Azure API 管理控制平面（Azure 门户）具有管理访问权限的帐户的清单。

Azure Active Directory (AD) 具有必须显式分配且可查询的内置角色。 API 管理依赖于这些角色和基于角色的访问控制来为 API 管理服务和实体启用细化访问管理。

此外，API 管理包含 API 管理的用户系统中的内置管理员组。 API 管理中的组控制开发人员门户中 API 的可见性，而且管理员组的成员可以查看所有 API。

按照 Azure 安全中心提供的建议来管理和维护管理帐户。

* [如何在 Azure API 管理中使用基于角色的访问控制](./api-management-role-based-access-control.md)

* [如何获取 Azure API 管理实例下的用户列表](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何使用 PowerShell 获取 Azure AD 中分配到目录角色的用户列表](/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色定义](/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [了解 Azure 安全中心提供的标识和访问管理建议](../security-center/recommendations-reference.md#recs-identity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：Azure API 管理没有默认密码/密钥。

Azure API 管理订阅是保护对 API 的访问的一种方法，但会附带一对已生成的订阅密钥。 客户可以随时重新生成这些订阅密钥。

* [了解 Azure API 管理订阅](./api-management-subscriptions.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

* [如何使用 Azure 安全中心监视标识和访问（预览）](../security-center/security-center-identity-access.md)

* [如何使用 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指南**：可以将 Azure API 管理配置为利用 Azure Active Directory 作为标识提供者对开发人员门户上的用户进行身份验证，以便受益于 Azure AD 提供的 SSO 功能。 配置完成后，新的开发人员门户用户可以选择按照现成的注册过程进行操作，先通过 Azure AD 进行身份验证，然后在身份验证完成后在门户上完成注册过程。

* [在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](./api-management-howto-aad.md)

此外，还可以通过委派进一步自定义登录/注册过程。 可以通过委派使用现有网站处理开发人员的登录/注册和产品订阅事项，不需使用开发人员门户中的内置功能。 这样就可以让网站拥有用户数据，并通过自定义方式对这些步骤进行验证。

* [如何委派用户注册和产品订阅](./api-management-howto-setup-delegation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理的建议。

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 安全中心监视标识和访问](../security-center/security-center-identity-access.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：使用配置了多重身份验证 (MFA) 的特权访问工作站 (PAW) 来登录并配置 Azure 资源。

* [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：当环境中出现可疑或不安全的活动时，可使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 生成日志和警报。

此外，还可使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

* [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure AD 风险检测](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

* [如何在 Azure 中配置命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：请尽可能使用 Azure AD 作为中心身份验证和授权系统。 AAD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

将 Azure API 管理开发人员门户配置为使用 Azure Active Directory 对开发人员帐户进行身份验证。

结合 Azure Active Directory (AD) 使用 OAuth 2.0 协议配置 Azure API 管理实例，以保护 API。

* [如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](./api-management-howto-aad.md)

* [如何结合 Azure Active Directory 和 API 管理使用 OAuth 2.0 保护 API](./api-management-howto-protect-backend-with-aad.md)

* [如何创建和配置 AAD 实例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure Active Directory 提供有助于发现陈旧帐户的日志。 客户可以使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才能继续拥有适当的访问权限。

客户可以维护 API 管理用户帐户的清单并根据需要协调访问。 在 API 管理中，开发人员是使用 API 管理公开的 API 的使用者。 默认情况下，新创建的开发人员帐户处于“活动”状态，并且与“开发人员”组相关联。 处于“活动”状态的开发人员帐户可用于访问他们具有订阅的所有 API。

管理员可以创建自定义组或利用关联 Azure Active Directory 租户中的外部组。 可以将自定义组和外部组与系统组一起使用，来允许开发人员查看和访问 API 产品。

* [如何在 Azure API 管理中管理用户帐户](./api-management-howto-create-or-invite-developers.md)

* [如何获取 API 管理用户的列表](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何创建和使用组来管理 Azure API 管理中的开发人员帐户](./api-management-howto-create-groups.md)

* [如何使用 Azure 标识访问评审](../active-directory/governance/access-reviews-overview.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的企图

**指南**：将 Azure API 管理实例配置为在 Azure API 管理中使用 Azure Active Directory 作为标识提供者来对开发人员帐户进行身份验证。

结合 Azure Active Directory (AD) 使用 OAuth 2.0 协议配置 Azure API 管理实例，以保护 API。

对传入的 API 请求配置 JWT 验证策略，以帮助确保存在有效令牌，并具有有效性。

为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需的警报。 此外，还可以将 Log Analytics 工作区加入 Azure Sentinel 或第三方 SIEM。

使用 `log-to-eventhub` 策略通过 API 管理配置高级监视，捕获进行安全分析所需的任何其他上下文信息，并将其发送到 Azure Sentinel 或第三方 SIEM。

* [如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](./api-management-howto-aad.md)

* [如何结合 Azure Active Directory 和 API 管理使用 OAuth 2.0 保护 API](./api-management-howto-protect-backend-with-aad.md)

* [API 管理访问限制策略](./api-management-access-restriction-policies.md)

* [如何将 Azure AD 日志集成到 Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

* [API 的高级监视](./api-management-log-to-eventhub-sample.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指南**：对于控制平面（Azure 门户）上的帐户登录行为偏差，请使用 Azure Active Directory (AD) 标识保护和风险检测功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

* [如何查看 Azure AD 风险登录](../active-directory/identity-protection/overview-identity-protection.md)

* [如何配置和启用标识保护风险策略](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何加入 Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指南**：目前不可用；Azure API 管理目前不支持客户密码箱。

* [支持客户密码箱的服务列表](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../security/benchmarks/security-control-data-protection.md)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure API 管理实例应由虚拟网络 (VNet)/子网分隔并正确标记。

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

* [如何在虚拟网络中使用 Azure API 管理](./api-management-using-with-vnet.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：目前不可用；数据标识、分类和丢失防护功能尚不适用于 Azure API 管理。

Microsoft 管理 Azure API 管理的底层基础结构，并实施了严格的控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：通过 Azure 资源管理器经 TLS 进行管理平面调用。 需要有效 JSON web 令牌 (JWT)。 可以使用 TLS 和受支持的身份验证机制之一（例如客户端证书或 JWT）保护数据平面调用。

* [了解 Azure API 管理中的数据保护](./api-management-security-controls.md#data-protection)

* [在 Azure API 管理中管理 TLS 设置](./api-management-howto-manage-protocols-ciphers.md)

* [使用 Azure Active Directory 在 Azure API 管理中保护 API](./api-management-howto-protect-backend-with-aad.md)

* [使用 Azure Active Directory B2C 在 Azure API 管理中保护 API](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：目前不可用；数据标识、分类和丢失防护功能尚不适用于 Azure API 管理。 标记可能正在处理敏感信息的 Azure API 管理服务，如果需要出于合规性目的使用这些功能，请实施第三方解决方案。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用基于角色的访问控制来控制对 Azure API 管理的访问。 Azure API 管理依赖于 Azure 基于角色的访问控制 (Azure RBAC) 为 API 管理服务和 (实体（例如，Api 和策略) ）启用精细访问管理。

* [如何在 Azure API 管理中使用基于角色的访问控制](./api-management-role-based-access-control.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

Microsoft 管理 Azure API 管理的底层基础结构，并实施了严格的控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](../security/fundamentals/protection-customer-data.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：敏感数据（例如证书、密钥以及使用机密命名的值）使用服务托管的、基于服务实例的密钥进行加密。 所有加密密钥均基于服务实例，并由服务管理。

* [了解 Azure API 管理中的静态数据保护/加密](./api-management-security-controls.md#data-protection)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，在生产 Azure Functions 应用以及其他关键或相关资源发生更改时，创建警报。

* [如何针对 Azure 活动日志事件创建警报](../azure-monitor/platform/alerts-activity-log.md)

* [如何在 Azure API 管理中使用 Azure Monitor 和 Azure 活动日志](./api-management-howto-use-azure-monitor.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](../security/benchmarks/security-control-vulnerability-management.md)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：目前不可用；Azure 安全中心的漏洞评估目前不适用于 Azure API 管理。

Microsoft 对基础平台进行了扫描和修补。 查看可用的安全控制，以减少与服务配置相关的漏洞。

* [了解 Azure API 管理可用的安全控制](./api-management-security-controls.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：目前不可用；Azure 安全中心的漏洞评估目前不适用于 Azure API 管理。

Microsoft 对基础平台进行了扫描和修补。 客户查看他们可用的安全控制，以减少与服务配置相关的漏洞。

* [了解 Azure API 管理可用的安全控制](./api-management-security-controls.md)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](../governance/resource-graph/first-query-portal.md)

* [如何查看 Azure 订阅](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](../cost-management-billing/manage/create-subscription.md)

* [如何创建管理组](../governance/management-groups/create.md)

* [如何创建和使用标记](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在你的订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 可查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 创建查询](../governance/resource-graph/first-query-portal.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](../role-based-access-control/conditional-access-azure-management.md)

* [Azure API 管理中基于角色的访问控制](./api-management-role-based-access-control.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：不适用；此建议适用于 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](../security/benchmarks/security-control-secure-configuration.md)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：使用 Azure Policy 为 Azure API 管理服务定义和实施标准安全配置。 在“Microsoft.ApiManagement”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理服务的配置。

* [如何查看可用的 Azure Policy 别名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：使用 Azure Policy 为 Azure API 管理服务定义和实施标准安全配置。 在“Microsoft.ApiManagement”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用自定义的 Azure 策略定义，请使用 Azure DevOps 或 Azure Repos 来安全存储和管理 Azure API 管理服务配置。

* [如何在 Azure DevOps 中存储文件](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](/azure/devops/repos/index?view=azure-devops)

* [了解 Azure API 管理 DevOps 资源工具包](./api-management-security-controls.md#configuration-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：使用 Azure Policy 为 Azure API 管理服务定义和实施标准安全配置。 在“Microsoft.ApiManagement”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指南**：使用 Azure API 管理 DevOps 资源工具包对 Azure API 管理执行配置管理。

此外，使用 Azure Policy 为 Azure API 管理服务定义和实施标准安全配置。 在“Microsoft.ApiManagement”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [了解 Azure API 管理 DevOps 资源工具包](./api-management-security-controls.md#configuration-management)

* [如何配置和管理 Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure Policy 效果](../governance/policy/concepts/effects.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：使用 Key Vault 来管理证书并将其设置为“自动轮换”。 如果使用 Azure Key Vault 来管理自定义域 SSL 证书，请确保该证书作为证书而不是机密插入到 Key Vault 中。

* [如何通过 Key Vault 密钥轮换指南设置自定义域名](./configure-custom-domain.md)

**Azure 安全中心监视**：是

**责任**：Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指南**：使用 Azure Active Directory (AD) 生成的托管服务标识，API 管理实例可以轻松、安全访问其他受 Azure AD 保护的资源（如 Azure Key Vault）。

* [如何为 API 管理实例创建托管标识](./api-management-howto-use-managed-service-identity.md)

* [使用托管标识进行身份验证的策略](./api-management-authentication-policies.md#ManagedIdentity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](../security/benchmarks/security-control-malware-defense.md)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

在支持 Azure 服务（例如 Azure API 管理）的底层主机上已启用 Microsoft 反恶意软件，但是，该软件不会针对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：不适用；此建议适用于设计用于存储数据的非计算资源。

在支持 Azure 服务（例如 Azure API 管理）的底层主机上已启用 Microsoft 反恶意软件，但是，该软件不会针对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于设计用于存储数据的非计算资源。

在支持 Azure 服务（例如 Azure API 管理）的底层主机上已启用 Microsoft 反恶意软件，但是，该软件不会针对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](../security/benchmarks/security-control-data-recovery.md)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：通过 Azure API 管理来发布和管理 API，即可充分利用容错和基础结构功能，否则需手动设计、实现和管理这些功能。 API 管理支持多区域部署，这使得数据平面免受区域性故障影响，从而无需增加任何运营开销。

API 管理的服务备份和还原功能为实现灾难恢复策略提供必要的构建基块。 备份和还原操作可以手动或自动执行。

* [如何将 API 管理数据平面部署到多个区域](./api-management-howto-deploy-multi-region.md)

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [如何调用 API 管理备份操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [如何调用 API 管理还原操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：Azure API 管理提供的备份和还原操作执行完整的系统备份和还原。

托管标识可用于从 Azure Key Vault 为 API 管理自定义域名获取证书。 备份 Azure Key Vault 中存储的所有证书。

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [如何备份 Azure Key Vault 证书](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：通过对备份中的服务和证书执行测试还原来验证备份。

* [如何调用 API 管理还原操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [如何还原 Azure Key Vault 证书](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：Azure API 管理将备份写入客户拥有的 Azure 存储帐户。 请遵循 Azure 存储安全建议来保护备份。

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [适用于 Blob 存储的安全建议](../storage/blobs/security-recommendations.md)

在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

* [如何在 Key Vault 中启用“软删除”](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](../security/benchmarks/security-control-incident-response.md)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的“计算机安全事件处理指南”，帮助制定自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产）并创建命名系统来对 Azure 资源进行明确标识和分类，特别是处理敏感数据的资源。 你的责任是根据发生事件的 Azure 资源和环境的关键性确定修正警报的优先级。

* [Azure 安全中心中的安全警报](../security-center/security-center-alerts-overview.md)

* [使用标记整理 Azure 资源](../azure-resource-manager/management/tag-resources.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行演练来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [NIST 发布 - IT 计划和功能的测试、训练和演练计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指导**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](../security-center/security-center-provide-security-contact-details.md)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 Azure 资源的风险。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](../security-center/continuous-export.md)

* [如何将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](../security-center/workflow-automation.md)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：定期对 Azure 资源执行渗透测试，确保在 60 天内修正所有发现的关键安全问题

**指导**：[请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [在以下网页中可以找到有关 Microsoft 红队演练策略和执行，以及针对 Microsoft 托管云基础结构、服务和应用程序执行现场渗透测试的详细信息](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../security/benchmarks/security-baselines-overview.md)
