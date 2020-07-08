---
title: API 管理的 Azure 安全基线
description: API 管理的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ceaa44429d105c2a6a1d0883ab29ed11b63ad497
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736069"
---
# <a name="azure-security-baseline-for-api-management"></a>API 管理的 Azure 安全基线

API 管理的 Azure 安全基线包含的建议可帮助你改进部署的安全状况。

此服务的基线提取自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，该版本提供了有关如何在 Azure 上利用我们的最佳做法指南来保护云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源

**指南**：可以将 Azure API 管理部署到虚拟网络 (Vnet) 内部，以便它可以访问该网络中的后端服务。 可以将开发人员门户和 API 管理网关配置为可以从 Internet（外部）访问或只能在 Vnet（内部）内访问。
- 外部：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。
- 内部：只能通过内部负载均衡器从虚拟网络内部访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

可以使用网络安全组控制其中部署了 API 管理的子网的入站和出站流量。

* [如何在虚拟网络中使用 Azure API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [将 Azure API 管理服务与内部虚拟网络配合使用](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [将内部 VNET 中的 API 管理与应用程序网关集成](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置和流量

**指南**：可以使用网络安全组 (NSG) 控制其中部署了 API 管理的子网的入站和出站流量。 将 NSG 部署到 API 管理子网，然后启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析具有以下优势：能够直观显示网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解 Azure API 管理的 NSG 配置](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键的 Web 应用程序

**指南**：若要保护关键的 Web/HTTP API，请在内部模式下的虚拟网络 (Vnet) 中配置 API 管理，并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 的子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

* [如何将内部 VNET 中的 API 管理与应用程序网关集成](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [了解 Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址通信

**指南**：在内部模式下的虚拟网络 (Vnet) 中配置 API 管理，并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 的子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

在与 API 管理部署相关联的 Vnet 上启用 Azure DDoS 防护标准，以防受到分布式拒绝服务 (DDoS) 攻击。

使用 Azure 安全中心的集成式威胁情报功能拒绝与已知的恶意或未使用的 Internet IP 地址通信。

* [如何将内部 VNET 中的 API 管理与应用程序网关集成](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [了解 Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)

* [如何配置 Azure DDoS 防护标准](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 安全中心的集成式威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：可以使用网络安全组 (NSG) 控制其中部署了 API 管理的子网的入站和出站流量。 将 NSG 部署到 API 管理子网，然后启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析具有以下优势：能够直观显示网络活动、识别热点、识别安全威胁、了解流量流模式以及查明网络配置错误。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解 Azure API 管理的 NSG 配置](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指南**：在内部模式下的虚拟网络 (Vnet) 中配置 API 管理，并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。 应用程序网关 WAF 可防止常见安全攻击和漏洞，可以在以下两种模式下运行：
- 检测模式：监视和记录所有威胁警报。 可以在“诊断”部分打开应用程序网关的日志记录诊断。 必须确保已选择并打开 WAF 日志。 在检测模式下运行时，Web 应用程序防火墙不会阻止传入的请求。
- 防护模式：阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 阻止模式会在 WAF 日志中记录此类攻击。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 的子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

* [如何将内部 VNET 中的 API 管理与应用程序网关集成](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [了解 Azure 应用程序网关 WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理到 Web 应用程序的流量

**指南**：若要管理流向 Web/HTTP API 的流量，请在外部或内部模式下将 API 管理部署到与应用服务环境关联的虚拟网络 (Vnet)。

在内部模式下，在 API 管理前配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载均衡、路由、Web 应用程序防火墙 (WAF) 和其他服务。 应用程序网关 WAF 可防止常见安全攻击和漏洞。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 API 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源将 API 的子集公开给外部使用者。
- 提供一种方式让客户启用和禁用通过公共 Internet 对 API 管理的访问。

注意：此功能在 API 管理的“高级”和“开发人员”层中可用。

* [如何将专用 API 公开给外部使用者](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [如何使用 Vnet 中的 API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Azure 应用程序网关上的 Azure Web 应用程序防火墙](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [了解 Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络 (Vnet) 服务标记在 API 管理子网中使用的网络安全组 (NSG) 上定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解和使用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [API 管理所需的端口](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：为与 Azure API 管理部署相关的网络设置定义和实施标准安全配置。 使用“Microsoft.ApiManagement”和“Microsoft.Network”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理部署和相关实例的网络配置。 还可使用 Azure 虚拟网络的内置策略定义，例如：
- 应启用 DDoS 防护标准版

你还可以使用 Azure 蓝图将关键环境项目（例如 Azure 资源管理器模板、基于角色的访问控制 (RBAC) 和策略）打包到单个蓝图定义，以简化大规模的 Azure 部署。 你可以通过版本控制轻松地在新的订阅和环境中应用蓝图并对控制和管理进行微调。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：记录流量配置规则

**指南**：为网络安全组 (NSG) 和其他与网络安全和流量流相关的资源使用标记。 对于单个 NSG 规则，可以使用“描述”字段为允许流量进出网络的任何规则指定业务需求和/或持续时间等。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何创建具有安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具监视网络资源配置并检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure API 管理部署相关的网络资源的更改。 在 Azure Monitor 中创建警报，使其在对关键网络资源进行更改时触发。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**：Microsoft 可以维护 Azure API 管理的时间源。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置安全日志集中管理

**指南**：在 Azure Monitor 中，使用 Log Analytics 工作区查询和执行分析，将日志发送到 Azure 存储以进行长期/存档存储或离线分析，或者通过 Azure 事件中心将日志导出到 Azure 和其他位置中的其他分析解决方案。 默认情况下，Azure API 管理会将日志和指标输出到 Azure Monitor。 可以根据服务范围和按 API 配置日志记录的详细程度。

除 Azure Monitor 以外，Azure API 管理还可以与一项或多项 Azure Application Insights 服务集成。 可以按服务或按 API 配置 Application Insights 的日志记录设置。

或者，启用数据并将其传输到 Azure Sentinel 或第三方安全信息和事件管理 (SIEM)。

* [如何配置诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何将 Azure Monitor 与第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [如何创建自定义日志记录和分析管道](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [如何与 Azure Application Insights 集成](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对于控制平面审核日志记录，可启用 Azure 活动日志诊断设置，并将活动日志发送到 Log Analytics 工作区以供报告和分析，发送到 Azure 存储以供长期安全保存，以及发送到 Azure 事件中心以导出到 Azure 和其他位置中的其他分析解决方案。 使用 Azure 活动日志数据，可以确定在控制平面级别针对 Azure API 管理服务执行的任何写入操作（PUT、POST、DELETE）的“操作内容、操作人员和操作时间”。

对于数据平面审核日志记录，诊断日志提供大量有关操作和错误的信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。 活动日志提供有关对 Azure 资源执行的操作的见解。 诊断日志提供资源执行的操作的深入信息。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何启用 Azure API 管理的诊断设置](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规则设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期/存档存储。

* [如何为 Log Analytics 工作区设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [如何将日志存档到 Azure 存储帐户](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和检查日志

**指南**：Azure API 管理不断将日志和指标发送到 Azure Monitor，使你能够准实时地了解 API 的状态和运行状况。 使用 Azure Monitor 和 Log Analytics 工作区，你可以查看、查询、可视化、路由、存档、配置警报，并对来自 API 管理和相关资源的指标和日志执行操作。 分析和监视日志中是否有异常行为，并定期查看结果。

或者，将 API 管理与 Azure Application Insights 集成，并将其用作进行监视、跟踪、报告和通知的主要或辅助工具。

* [如何监视和查看 Azure API 管理的日志](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [如何在 Azure Monitor 中执行自定义查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [了解 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何与 Azure Application Insights 集成](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：启用针对异常活动的警报

**指南**：启用 Azure 活动日志诊断设置以及 Azure API 管理实例的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询以搜索术语、识别趋势、分析模式以及基于收集的数据提供其他许多见解。 可以根据 Log Analytics 工作区查询来创建警报。

创建指标警报，以便在发生意外的情况时收到通知。 例如，Azure API 管理实例在某个时间段内超过了其预期的峰值容量时，或者未经授权的网关请求或错误在预定义的时间段内达到了一定数量时，会收到通知。

或者，将 API 管理与 Azure Application Insights 集成，并将其用作进行监视、跟踪、报告和通知的主要或辅助工具。

或者，可以启用数据并将其加入 Azure Sentinel 或第三方 SIEM。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何启用 Azure API 管理的诊断设置](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [如何针对未经授权的请求配置警报规则](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [如何查看 Azure API 管理实例的容量指标](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [如何与 Azure Application Insights 集成](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中进行反恶意软件日志记录

**指南**：不适用；Azure API 管理不会处理或生成与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用；Azure API 管理不会处理或生成用户可访问的与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护对 Azure API 管理控制平面（Azure 门户）具有管理访问权限的帐户的清单。

Azure Active Directory (AD) 包含内置角色，这些角色必须进行显式分配且可查询。 API 管理依赖于这些角色和基于角色的访问控制来为 API 管理服务和实体启用精细访问管理。

此外，API 管理在 API 管理的用户系统中包含内置 Administrator 组。 API 管理中的组可以控制 API 在开发人员门户中的显示，并且 Administrator 组的成员可以查看所有 API。

请遵循 Azure 安全中心的建议来管理和维护管理帐户。

* [如何在 Azure API 管理中使用基于角色的访问控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [如何获取 Azure API 管理实例下的用户列表](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何使用 PowerShell 获取 Azure AD 中分配给目录角色的用户的列表](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [如何使用 PowerShell 获取 Azure AD 中的目录角色定义](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [了解 Azure 安全中心的标识和访问建议](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：更改默认密码（如果适用）

**指南**：Azure API 管理没有默认密码/密钥的概念。

Azure API 管理订阅可以保护对 API 的访问，不过会生成一对订阅密钥。 客户可以随时重新生成这些订阅密钥。

* [了解 Azure API 管理订阅](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：根据专用管理帐户的用法创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为帮助跟踪专用管理帐户，可以使用 Azure 安全中心或内置 Azure Policy 中的建议，例如：
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

* [如何使用 Azure 安全中心监视标识和访问（预览）](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用 Azure Active Directory 的单一登录 (SSO)

**指南**：为了从 Azure AD 提供的 SSO 功能中获益，可以配置 Azure API 管理，使其将 Azure Active Directory 用作标识提供者来对开发人员门户中的用户进行身份验证。 配置完成后，新的开发人员门户用户可以选择遵循开箱即用的注册过程，即先通过 Azure AD 进行身份验证，身份验证完成后再在门户中完成注册过程。

* [在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

或者，可以通过委派进一步自定义登录/注册过程。 可以通过委派使用现有网站处理开发人员的登录/注册和产品订阅事项，不需使用开发人员门户中的内置功能。 这样就可以让网站拥有用户数据，并通过自定义方式对这些步骤进行验证。

* [如何委派用户注册和产品订阅](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 Azure Active Directory (AD) 多重身份验证 (MFA)，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将特权访问工作站 (PAW) 与为登录和配置 Azure 资源而配置的多重身份验证 (MFA) 结合使用。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录管理帐户的可疑活动并发出警报

**指南**：可以使用 Azure Active Directory (AD) Privileged Identity Management (PIM) 在环境中发生可疑活动或不安全的活动时生成日志和警报。

此外，还可使用 Azure AD 风险检测查看有关风险用户行为的警报和报告。

* [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：尽可能使用 Azure AD 作为中央身份验证和授权系统。 AAD 通过对静态数据和传输中的数据使用强加密来保护数据。 Azure AD 还会进行加盐操作、哈希操作并安全地存储用户凭据。

配置 Azure API 管理开发人员门户，使其通过 Azure Active Directory 对开发人员帐户进行身份验证。

配置 Azure API 管理实例，使其结合使用 Azure Active Directory (AD) 和 OAuth 2.0 协议来保护 API。

* [如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [如何结合使用 OAuth 2.0 和 Azure Active Directory 与 API 管理来保护 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [如何创建和配置 AAD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期查看和协调用户访问权限

**指南**：Azure Active Directory 提供有助于发现陈旧帐户的日志。 客户可以使用 Azure 标识访问评审有效地管理组成员身份、对企业应用程序的访问权限，以及角色分配。 可以定期查看用户访问权限，确保只有适当的用户持续拥有适当的访问权限。

客户可以维护 API 管理用户帐户的清单，并根据需要协调访问。 在 API 管理中，开发人员是使用 API 管理公开的 API 的使用者。 默认情况下，新创建的开发人员帐户处于“活动”状态，并且与“开发人员”组相关联。 处于“活动”状态的开发人员帐户可用于访问它们具有订阅的所有 API。

管理员可以创建自定义组或利用关联 Azure Active Directory 租户中的外部组。 自定义组和外部组可与系统组一起使用为开发人员提供可见性并可以访问 API 产品。

* [如何在 Azure API 管理中管理用户帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [如何获取 API 管理用户列表](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何创建和使用组来管理 Azure API 管理中的开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视访问已停用帐户的尝试

**指南**：配置 Azure API 管理实例，使其将 Azure Active Directory 用作 Azure API 管理中的标识提供者来对开发人员帐户进行身份验证。

配置 Azure API 管理实例，使其结合使用 Azure Active Directory (AD) 和 OAuth 2.0 协议来保护 API。

将 JWT 验证策略配置为传入的 API 请求，以帮助强制实施有效令牌的存在和有效性。

为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需警报。 此外，还可以将 Log Analytics 工作区加入 Azure Sentinel 或第三方 SIEM。

使用 `log-to-eventhub` 策略配置 API ​​管理的高级监视，捕获安全分析所需的其他所有上下文信息，并将其发送到 Azure Sentinel 或第三方 SIEM。

* [如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [如何结合使用 OAuth 2.0 和 Azure Active Directory 与 API 管理来保护 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [API 管理访问限制策略](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [如何将 Azure AD 日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [API 的高级监视](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：对帐户登录行为偏差发出警报

**指南**：对于控制平面（Azure门户）中帐户登录行为的偏差，可使用 Azure Active Directory (AD) 标识保护和风险检测功能进行配置，使其在检测到与用户标识相关的可疑操作时自动进行响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案期间为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用；Azure API 管理目前不支持客户密码箱。

* [客户密码箱支持的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记可帮助跟踪存储或处理敏感信息的 Azure 资源。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指南**：为开发、测试和生产实现单独的订阅和/或管理组。 Azure API 管理实例应由虚拟网络 (VNet)/子网进行分隔并适当标记。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何在虚拟网络中使用 Azure API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指南**：当前不可用；数据标识、分类和丢失防护功能目前不可用于 Azure API 管理。

Microsoft 会管理 Azure API 管理的底层基础结构，并已实施严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：管理平面调用通过 Azure 资源管理器基于 TLS 进行。 需要有效 JSON web 令牌 (JWT)。 可以使用 TLS 和受支持的身份验证机制之一（例如客户端证书或 JWT）保护数据平面调用。

* [了解 Azure API 管理中的数据保护](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [管理 Azure API 管理中的 TLS 设置](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [使用 Azure Active Directory 保护 Azure API 管理中的 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [使用 Azure Active Directory B2C 保护 Azure API 管理中的 API](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：当前不可用；数据标识、分类和丢失防护功能目前不可用于 Azure API 管理。 将可能正在处理敏感信息的 Azure API 管理服务标记为此类，并根据需要实施第三方解决方案以实现合规性。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容都视为敏感内容，竭尽全力防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护一套可靠的数据保护控制和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用基于角色的访问控制来控制对 Azure API 管理的访问。 Azure API 管理依赖于 Azure 基于角色的访问控制 (RBAC) 来为 API 管理服务和实体（例如，API 和策略）启用精细访问管理。

* [如何在 Azure API 管理中使用基于角色的访问控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用；此建议适用于计算资源。

Microsoft 会管理 Azure API 管理的底层基础结构，并已实施严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态的敏感信息

**指南**：使用由服务管理的服务实例密钥对敏感数据（如证书、密钥和使用机密命名的值）进行加密。 所有加密密钥均基于服务实例，并由服务管理。

* [了解 Azure API 管理中的数据保护/静态加密](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在 Azure Functions 应用的生产实例和其他关键或相关资源发生更改时发出的警报。

* [如何为 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何在 Azure API 管理中使用 Azure Monitor 和 Azure 活动日志](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动化漏洞扫描工具

**指南**：当前不可用；Azure 安全中心的漏洞评估目前不可用于 Azure API 管理。

Microsoft 对基础平台进行了扫描和修补。 查看可用于减少服务配置相关漏洞的安全控制。

* [了解 Azure API 管理可用的安全控制](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure 安全中心监视**：当前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署操作系统修补程序自动化管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方软件修补程序自动化管理解决方案

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续的漏洞扫描

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评分流程确定所发现漏洞的修正优先级

**指南**：当前不可用；Azure 安全中心的漏洞评估目前不可用于 Azure API 管理。

Microsoft 对基础平台进行了扫描和修补。 客户可以查看可用于减少服务配置相关漏洞的安全控制。

* [了解 Azure API 管理可用的安全控制](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure 安全中心监视**：当前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保你在租户中拥有适当（读取）权限，并且能够枚举所有 Azure 订阅以及订阅中的资源。

虽然可以通过 Resource Graph 发现经典的 Azure 资源，但强烈建议创建和使用 Azure 资源管理器资源以供后续使用。

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

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure Policy 对可使用以下内置策略定义在订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure Resource Graph 可查询/发现订阅中的资源。 确保环境中的所有 Azure 资源均已获得批准。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

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

### <a name="68-use-only-approved-applications"></a>6.8：只使用已批准的应用程序

**指南**：不适用；此建议适用于计算资源。

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

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Azure API 管理中基于角色的访问控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用；此建议适用于在 Azure 应用服务或计算资源上运行的 Web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：通过 Azure Policy 为 Azure API 管理服务定义和实现标准安全配置。 使用“Microsoft.ApiManagement”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理服务的配置。

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：通过 Azure Policy 为 Azure API 管理服务定义和实现标准安全配置。 使用“Microsoft.ApiManagement”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用自定义 Azure 策略定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理 Azure API 管理服务配置。

* [如何在 Azure DevOps 中存储文件](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [了解 Azure API 管理 DevOps 资源工具包](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：通过 Azure Policy 为 Azure API 管理服务定义和实现标准安全配置。 使用“Microsoft.ApiManagement”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：针对 Azure 服务实现自动化配置监视

**指南**：使用 Azure API 管理 DevOps 资源工具包对 Azure API 管理执行配置管理。

此外，通过 Azure Policy 为 Azure API 管理服务定义和实现标准安全配置。 使用“Microsoft.ApiManagement”命名空间中的 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure API 管理实例的配置。 使用 Azure Policy [拒绝] 和 [不存在时部署] 在 Azure 资源中强制实施安全设置。

* [了解 Azure API 管理 DevOps 资源工具包](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：针对操作系统实现自动化配置监视

**指南**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 机密

**指南**：使用 Key Vault 管理证书，并将其设置为自动更新。 如果使用 Azure Key Vault 管理自定义域 SSL 证书，请确保将该证书作为证书（而不是机密）插入 Key Vault。

* [如何借助 Key Vault 密钥更新指南设置自定义域名](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Azure 安全中心监视**：是

**责任**：Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自动地管理标识

**指南**：借助 Azure Active Directory (AD) 生成的托管服务标识，API 管理实例可以轻松、安全访问其他受 Azure AD 保护的资源（如 Azure Key Vault）。

* [如何为 API 管理实例创建托管标识](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [使用托管标识进行身份验证的策略](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据暴露

**指南**：实现凭据扫描器来识别代码中的凭据。 凭据扫描器还建议将发现的凭据迁移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用；此建议适用于计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure API 管理）的基础主机上启用，但它不会对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预扫描要上传到非计算 Azure 资源的文件

**指南**：不适用；此建议适用于旨在存储数据的非计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure API 管理）的基础主机上启用，但它不会对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用；此建议适用于旨在存储数据的非计算资源。

Microsoft 反恶意软件会在支持 Azure 服务（例如，Azure API 管理）的基础主机上启用，但它不会对客户内容运行。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：通过 Azure API 管理来发布和管理 API，即可充分利用容错和基础结构功能，否则需手动设计、实现和管理这些功能。 API 管理支持多区域部署，这可以在不增加任何操作开销的情况下使数据平面不受区域故障的影响。

API 管理的服务“备份和还原”功能为实现灾难恢复策略提供了必要的构建基块。 可以手动或自动执行备份和还原操作。

* [如何将 API 管理数据平面部署到多个区域](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [如何调用 API 管理备份操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [如何调用 API 管理还原操作](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份所有客户托管密钥

**指南**：Azure API 管理提供的备份和还原操作可以执行完整的系统备份和还原。

托管标识可用于从 Azure Key Vault 为 API 管理自定义域名获取证书。 备份 Azure Key Vault 中存储的所有证书。

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [如何备份 Azure Key Vault 证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户端托管密钥

**指南**：通过对备份中的服务和证书执行测试还原来验证备份。

* [如何调用 API 管理还原操作](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [如何还原 Azure Key Vault 证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户托管密钥

**指南**：Azure API 管理将备份写入客户拥有的 Azure 存储帐户。 遵循 Azure 存储安全建议来保护备份。

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [有关 Blob 存储的安全建议](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

在 Key Vault 中启用软删除，以防意外或恶意删除密钥。

* [如何启用 Key Vault 中的软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指南**：为组织构建事件响应指南。 确保制定书面事件响应计划，包括定义人员的所有角色，以及从检测到事件后审查的事件处理/管理阶段。

* [有关生成自己的安全事件响应过程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级确定过程

**指南**：安全中心向每个警报分配一个严重性，以帮助你优先考虑应首先调查的警报。 严重性取决于安全中心对用于发出警报的调查结果或分析的置信度，以及对导致警报的活动背后存在恶意意图的可信度。

此外，使用标记清楚地标记订阅（例如 生产或非生产），并创建命名系统以清楚地标识和分类 Azure 资源，尤其是那些用于处理敏感数据的资源。 你要保证根据 Azure 资源以及事件的发生环境的重要性来设置警报修正的优先级。

* [Azure 安全中心中的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [使用标记组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指南**：定期执行练习来测试系统的事件响应功能，以帮助保护 Azure 资源。 识别薄弱点和缺口，并根据需要修订计划。

* [NIST 出版物 - 适用于 IT 计划和功能的测试、培训和练习计划指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现数据被某方非法访问或未经授权访问，Microsoft 会使用安全事件联系信息联系用户。 在事后审查事件，以确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助识别 Azure 资源的风险。 使用连续导出功能可手动或以连续不断的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，确保修正 60 天内的所有关键安全调查结果

**指南**：* [请遵循 Microsoft Rules of Engagement 以确保渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [对于针对 Microsoft 托管云基础结构、服务和应用程序的红队和实时站点渗透测试的 Microsoft 策略和执行，可在此处找到有关详细信息](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
