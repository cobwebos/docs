---
title: 适用于 API 管理的 Azure 安全基线
description: 适用于 API 管理的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aa59a37f3a4413c92a483746ed4a08c363ab8457
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796520"
---
# <a name="azure-security-baseline-for-api-management"></a>适用于 API 管理的 Azure 安全基线

适用于 API 管理的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线取自[Azure 安全基准1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何在 Azure 上利用我们的最佳实践指南来保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指南**：可以在 Azure 虚拟网络（Vnet）中部署 Azure API 管理，以便它可以访问网络中的后端服务。 可以将开发人员门户和 API 管理网关配置为可以从 Internet （外部）或仅在 Vnet （内部）中访问。
- 外部：可以通过外部负载均衡器从公共 Internet 访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。
- 内部：只能通过内部负载均衡器从虚拟网络内部访问 API 管理网关和开发人员门户。 网关可以访问虚拟网络中的资源。

可以使用网络安全组控制入站和出站流量，其中部署了 API 管理的子网。

* [如何在虚拟网络中使用 Azure API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [将 Azure API 管理服务与内部虚拟网络配合使用](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [在包含应用程序网关的内部 VNET 中集成 API 管理](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指南**：可以使用网络安全组（nsg）控制在其中部署了 API 管理的子网的入站和出站流量。 将 NSG 部署到 API 管理子网，并启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解 Azure API 管理的 NSG 配置](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：若要保护关键 WEB/HTTP api，请在内部模式下配置虚拟网络（Vnet）中的 API 管理并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载平衡、路由、web 应用程序防火墙（WAF）和其他服务。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 Api 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源向外部使用者公开 Api 子集。
- 提供一种方法，用于从公共 Internet 开启和关闭对 API 管理的访问。

注意：此功能在 API 管理的高级层和开发人员层中提供。

* [如何在包含应用程序网关的内部 VNET 中集成 API 管理](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [了解 Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址的通信

**指南**：在内部模式下配置虚拟网络（Vnet）中的 API 管理并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载平衡、路由、web 应用程序防火墙（WAF）和其他服务。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 Api 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源向外部使用者公开 Api 子集。
- 提供一种方法，用于从公共 Internet 开启和关闭对 API 管理的访问。

注意：此功能在 API 管理的高级层和开发人员层中提供。

在与 API 管理部署关联的 Vnet 中启用 Azure DDoS 保护标准，以防止出现分布式拒绝服务（DDoS）攻击。

使用 Azure 安全中心集成威胁情报拒绝与已知的恶意或未使用的 Internet IP 地址的通信。

* [如何在包含应用程序网关的内部 VNET 中集成 API 管理](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [了解 Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/)

* [如何配置 Azure DDoS 保护标准](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 安全中心集成威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：可以使用网络安全组（NSG）控制在其中部署了 API 管理的子网的入站和出站流量。 将 NSG 部署到 API 管理子网，并启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解 Azure API 管理的 NSG 配置](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统（IDS/IPS）

**指南**：在内部模式下配置虚拟网络（Vnet）中的 API 管理并配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载平衡、路由、web 应用程序防火墙（WAF）和其他服务。 应用程序网关 WAF 提供对常见安全攻击和漏洞的保护，可以在以下两种模式下运行：
- 检测模式：监视并记录所有威胁警报。 可以在 "诊断" 部分打开应用程序网关的日志记录诊断。 您必须确保已选择并打开 WAF 日志。 在检测模式下运行时，Web 应用程序防火墙不会阻止传入的请求。
- 防护模式：阻止规则检测到的入侵和攻击。 攻击者会收到“403 未授权访问”异常，且连接会结束。 防护模式会在 WAF 日志中记录此类攻击。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 Api 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源向外部使用者公开 Api 子集。
- 提供一种方法，用于从公共 Internet 开启和关闭对 API 管理的访问。

注意：此功能在 API 管理的高级层和开发人员层中提供。

* [如何在包含应用程序网关的内部 VNET 中集成 API 管理](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [了解 Azure 应用程序网关 WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指南**：若要管理流向 WEB/HTTP api 的流量，请将 API 管理部署到与外部或内部模式下的应用服务环境关联的虚拟网络（Vnet）。

在内部模式下，在 API 管理之前配置 Azure 应用程序网关。 应用程序网关是一种 PaaS 服务。 它充当反向代理，并提供 L7 负载平衡、路由、web 应用程序防火墙（WAF）和其他服务。 应用程序网关 WAF 提供对常见安全攻击和漏洞的保护。

将内部 Vnet 中预配的 API 管理与应用程序网关前端相结合可实现以下方案：
- 使用单个 API 管理资源将所有 Api 公开给内部使用者和外部使用者。
- 使用单个 API 管理资源向外部使用者公开 Api 子集。
- 提供一种方法，用于从公共 Internet 开启和关闭对 API 管理的访问。

注意：此功能在 API 管理的高级层和开发人员层中提供。

* [如何向外部使用者公开专用 Api](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [如何在 Vnet 中使用 API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Azure 应用程序网关上的 Azure Web 应用程序防火墙](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [了解 Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络（Vnet）服务标记来定义 API 管理子网上使用的网络安全组（nsg）上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 ApiManagement），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

警告：在 API 管理子网上配置 NSG 时，需要打开一组端口。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。

* [了解和使用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [API 管理所需的端口](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure API 管理部署相关的网络设置的标准安全配置。 使用 "ApiManagement" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure API 管理部署和相关资源的网络配置。 你还可以使用 Azure 虚拟网络的内置策略定义，例如：
- 应启用 DDoS 防护标准版

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、基于角色的访问控制（RBAC）和策略）来简化大规模的 Azure 部署。 通过版本控制，可以轻松地将蓝图应用到新的订阅、环境和微调控制和管理。

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：对网络安全组（nsg）和与网络安全和通信流相关的其他资源使用标记。 对于单独的 NSG 规则，你可以使用 "说明" 字段来指定允许进出网络流量的任何规则的业务需求和/或持续时间（等等）。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何创建使用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测对与 Azure API 管理部署关联的网络资源所做的更改。 在 Azure Monitor 中创建当关键网络资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护 Azure API 管理的时间源。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：在 Azure Monitor 中，使用 Log Analytics 工作区来查询和执行分析、将日志发送到 azure 存储以进行长期/存档存储或脱机分析，或使用 Azure 事件中心将日志导出到 azure 上和其他位置的其他 Analytics 解决方案。 默认情况下，Azure API 管理会将日志和指标输出到 Azure Monitor。 可以在服务范围和每个 API 基础上配置日志记录的详细级别。

除了 Azure Monitor 之外，Azure API 管理还可与一个或多个 Azure 应用程序 Insights 服务集成。 可以根据每个服务或每个 API 来配置 Application Insights 的日志记录设置。

（可选）将和机载数据启用到 Azure Sentinel 或第三方安全事件和事件管理（SIEM）。

* [如何配置诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何开始 Azure Monitor 和第三方 SIEM 集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [如何创建自定义日志记录和分析管道](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [如何与 Azure 应用程序 Insights 集成](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对于控制平面审核日志记录，启用 Azure 活动日志诊断设置并将活动日志发送到 Log Analytics 工作区，以便进行报告和分析，将活动日志发送到 azure 存储以进行长期保护，并将其发送到 Azure 事件中心，以在 azure 和其他位置的其他分析解决方案中进行导出。 使用 Azure 活动日志数据，可以确定在 Azure API 管理服务的控制平面级别执行的任何写入操作（PUT、POST、DELETE）的 "操作内容、操作人员和操作时间"。

对于数据平面审核日志记录，诊断日志提供了有关操作和错误的丰富信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。 活动日志提供有关对 Azure 资源执行的操作的见解。 诊断日志提供资源执行的操作的深入信息。

* [如何对 Azure 活动日志启用诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何为 Azure API 管理启用诊断设置](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性法规设置 Log Analytics 工作区保持期。 将 Azure 存储帐户用于长期/存档存储。

* [如何为 Log Analytics 工作区设置日志保持参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [如何将日志存档到 Azure 存储帐户](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**： Azure API 管理不断地将日志和指标发送到 Azure Monitor，为你提供 api 状态和运行状况的近乎实时的可见性。 使用 Azure Monitor 和 Log Analytics 工作区，你可以查看、查询、可视化、路由、存档、配置警报，并对来自 API 管理和相关资源的指标和日志采取措施。 分析和监视日志中的异常行为，并定期检查结果。

（可选）将 API 管理与 Azure 应用程序 Insights 集成，并将其用作主要或辅助监视、跟踪、报告和警报工具。

* [如何监视和查看 Azure API 管理的日志](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [如何在 Azure Monitor 中执行自定义查询](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [了解 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何与 Azure 应用程序 Insights 集成](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：为异常活动启用警报

**指南**：启用 Azure 活动日志诊断设置以及 Azure API 管理实例的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，搜索术语，确定趋势，分析模式，并根据收集的数据提供许多其他见解。 您可以根据 Log Analytics 工作区查询来创建警报。

创建指标警报，以便在出现意外情况时通知您。 例如，当你的 Azure API 管理实例在某个时间段内超过预期的高峰容量，或在预定义的时间段内发生了特定数量的未经授权的网关请求或错误时，会收到通知。

（可选）将 API 管理与 Azure 应用程序 Insights 集成，并将其用作主要或辅助监视、跟踪、报告和警报工具。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。

* [如何对 Azure 活动日志启用诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何为 Azure API 管理启用诊断设置](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [如何针对未经授权的请求配置警报规则](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [如何查看 Azure API 管理实例的容量指标](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [如何与 Azure 应用程序 Insights 集成](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;Azure API 管理不处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;Azure API 管理不处理或产生用户可访问的与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**：维护对 Azure API 管理控制平面具有管理访问权限的帐户的清单（Azure 门户）。

Azure Active Directory （AD）具有内置角色，必须显式分配这些角色并可查询。 API 管理依赖于这些角色和基于角色的访问控制来启用 API 管理服务和实体的精细访问管理。

此外，API 管理包含 API 管理的用户系统中的内置 Administrators 组。 API 管理中的组控制开发人员门户中 Api 的可见性和 Administrators 组的成员可以查看所有 Api。

按照 Azure 安全中心提供的建议来管理和维护管理帐户。

* [如何在 Azure API 管理中使用基于角色的访问控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [如何获取 Azure API 管理实例下的用户列表](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何使用 PowerShell 在 Azure AD 中获取分配给目录角色的用户的列表](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [如何使用 PowerShell 在 Azure AD 中获取目录角色定义](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [了解 Azure 安全中心的标识和访问建议](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**： Azure API 管理没有默认密码/密钥的概念。

Azure API 管理订阅是保护对 Api 的访问的一种方法，但会附带一对已生成的订阅密钥。 客户可以随时重新生成这些订阅密钥。

* [了解 Azure API 管理订阅](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕使用专用管理帐户创建标准操作过程。 使用 Azure 安全中心的标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置 Azure 策略中的建议，例如：
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

* [如何使用 Azure 安全中心来监视标识和访问（预览）](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用带有 Azure Active Directory 的单一登录（SSO）

**指南**：可以将 Azure API 管理配置为利用 Azure Active Directory 作为标识提供者对开发人员门户上的用户进行身份验证，以便受益于 Azure AD 提供的 SSO 功能。 配置完成后，新的开发人员门户用户可以通过先通过 Azure AD 进行身份验证，然后在门户上完成注册过程后，选择按照现成的注册过程进行操作。

* [在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

此外，还可以通过委派进一步自定义登录/注册过程。 可以通过委派使用现有网站处理开发人员的登录/注册和产品订阅事项，不需使用开发人员门户中的内置功能。 这样就可以让网站拥有用户数据，并通过自定义方式对这些步骤进行验证。

* [如何委派用户注册和产品订阅](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 AZURE ACTIVE DIRECTORY （AD）多重身份验证（MFA）并遵循 Azure 安全中心的标识和访问管理建议。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心内监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：对配置为登录和配置 Azure 资源的多重身份验证（MFA）使用特权访问工作站（PAW）。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：在环境中发生可疑或不安全活动时，使用 AZURE ACTIVE DIRECTORY （AD） PRIVILEGED IDENTITY MANAGEMENT （PIM）来生成日志和警报。

此外，使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

* [如何部署 Privileged Identity Management （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，以仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：尽可能使用 Azure AD 作为中心身份验证和授权系统。 AAD 通过对静态数据和传输中的数据使用强加密来保护数据。 还 Azure AD salts、哈希和安全存储用户凭据。

将 Azure API 管理开发人员门户配置为使用 Azure Active Directory 对开发人员帐户进行身份验证。

将 Azure API 管理实例配置为使用 Azure Active Directory （AD）的 OAuth 2.0 协议来保护 Api。

* [如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [如何使用 OAuth 2.0 与 Azure Active Directory 和 API 管理配合使用来保护 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [如何创建和配置 AAD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指导**：Azure Active Directory 提供日志来帮助发现过时的帐户。 客户可以使用 Azure 标识访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续进行适当的访问。

客户可以根据需要维护 API 管理用户帐户的清单并协调访问。 在 API 管理中，开发人员是使用 API 管理公开的 Api 的使用者。 默认情况下，新创建的开发人员帐户是活动，与开发人员组相关联。 活动状态中的开发人员帐户可用于访问他们具有订阅的所有 API。

管理员可以创建自定义组或利用关联的 Azure Active Directory 租户中的外部组。 自定义组和外部组可与系统组一起使用为开发人员提供可见性并可以访问 API 产品。

* [如何在 Azure API 管理中管理用户帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [如何获取 API 管理用户的列表](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何创建和使用组来管理 Azure API 管理中的开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视器尝试访问停用的帐户

**指南**：通过在 Azure api 管理中使用 Azure Active Directory 作为标识提供者，将 Azure API 管理实例配置为对开发人员帐户进行身份验证。

将 Azure API 管理实例配置为使用 Azure Active Directory （AD）的 OAuth 2.0 协议来保护 Api。

将 JWT 验证策略配置为传入 API 请求，以帮助强制实施有效令牌的存在和有效性。

为 Azure AD 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区。 在 Log Analytics 中配置所需的警报。 此外，你可以将 Log Analytics 工作区载入 Azure Sentinel 或第三方 SIEM。

使用`log-to-eventhub`策略配置针对 API 管理的高级监视，捕获安全分析所需的任何其他上下文信息，并将其发送到 Azure Sentinel 或第三方 SIEM。

* [如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [如何使用 OAuth 2.0 与 Azure Active Directory 和 API 管理配合使用来保护 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [API 管理访问限制策略](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [如何将 Azure AD 日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何在板载 Azure 上操作](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Api 的高级监视](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帐户登录行为偏差的警报

**指南**：对于控制平面（Azure 门户）的帐户登录行为偏差，使用 AZURE ACTIVE DIRECTORY （AD） Identity Protection 和风险检测功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 以便进一步调查。

* [如何查看 Azure AD 有风险的登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用 Identity Protection 风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案中为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;Azure API 管理目前不支持客户密码箱。

* [客户密码箱支持的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的 Azure 资源。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure API 管理实例应由虚拟网络（VNet）/子网分隔并正确标记。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何在虚拟网络中使用 Azure API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止敏感信息的未授权传输

**指南**：当前不可用;数据标识、分类和丢失防护功能当前不适用于 Azure API 管理。

Microsoft 管理 Azure API 管理的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：通过 Azure 资源管理器通过 TLS 进行管理平面调用。 需要有效 JSON web 令牌 (JWT)。 可以通过 TLS 以及某个受支持的身份验证机制（例如，客户端证书或 JWT）对数据平面调用进行保护。

* [了解 Azure API 管理中的数据保护](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [在 Azure API 管理中管理 TLS 设置](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [通过 Azure Active Directory 在 Azure API 管理中保护 Api](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [通过 Azure Active Directory B2C 在 Azure API 管理中保护 Api](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Azure 安全中心监视**：是

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：当前不可用;数据标识、分类和丢失防护功能当前不适用于 Azure API 管理。 标记可能正在处理敏感信息的 Azure API 管理服务，并根据需要实现第三方解决方案，以实现符合性。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护一套可靠的数据保护控制和功能。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用基于角色的访问控制来控制对 Azure API 管理的访问。 Azure API 管理依赖于 Azure 基于角色的访问控制 (RBAC) 来为 API 管理服务和实体（例如，API 和策略）启用精细访问管理。

* [如何在 Azure API 管理中使用基于角色的访问控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此建议适用于计算资源。

Microsoft 管理 Azure API 管理的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：敏感数据（如证书、密钥和机密命名值）通过服务托管的每个服务实例密钥进行加密。 所有加密密钥都是基于服务实例的，也是通过服务托管的。

* [了解 Azure API 管理中的数据保护/静态加密](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建有关生产 Azure Functions 应用发生更改的警报，以及其他关键或相关资源。

* [如何为 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何在 Azure API 管理中使用 Azure Monitor 和 Azure 活动日志](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：当前不可用;Azure 安全中心的漏洞评估目前不适用于 Azure API 管理。

Microsoft 对基础平台进行了扫描和修补。 查看可用于降低与服务配置相关的漏洞的安全控制。

* [了解 Azure API 管理可用的安全控制](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**：当前不可用;Azure 安全中心的漏洞评估目前不适用于 Azure API 管理。

Microsoft 对基础平台进行了扫描和修补。 客户查看可用的安全控件，以减少与服务配置相关的漏洞。

* [了解 Azure API 管理可用的安全控制](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

*有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure 资源关系图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。 请确保租户中的相应（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何通过 Azure 资源关系图创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看你的 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure 资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure 策略将对可以使用以下内置策略定义在客户订阅中创建的资源类型进行限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 策略对可使用以下内置策略定义在订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

使用 Azure 资源关系图在其订阅中查询/发现资源。 确保环境中存在的所有 Azure 资源都已获得批准。

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何通过 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指导**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 策略拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，配置 Azure 条件性访问，以限制用户与 Azure 资源管理器的交互能力。

* [如何配置条件性访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Azure API 管理中基于角色的访问控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：不适用;此建议适用于在 Azure App Service 或计算资源上运行的 web 应用程序。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：定义和实现 Azure API 管理服务的标准安全配置。 使用 "ApiManagement" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure API 管理服务的配置。

* [如何查看可用的 Azure 策略别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指南**：通过 azure 策略为 Azure API 管理服务定义和实施标准安全配置。 使用 "ApiManagement" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure API 管理实例的配置。 使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure 策略影响](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地存储 Azure 资源的配置

**指南**：如果使用自定义 azure 策略定义，请使用 azure DevOps 或 Azure Repos 来安全地存储和管理 Azure API 管理服务配置。

* [如何在 Azure DevOps 中存储文件](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [了解 Azure API 管理 DevOps 资源工具包](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：通过 azure 策略为 Azure API 管理服务定义和实施标准安全配置。 使用 "ApiManagement" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure API 管理实例的配置。 使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在 Azure 资源中强制实施安全设置。

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure 策略影响](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指南**：使用 Azure Api Management DevOps 资源工具包对 Azure api 管理执行配置管理。

此外，通过 Azure 策略为 Azure API 管理服务定义和实现标准安全配置。 使用 "ApiManagement" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制执行 Azure API 管理实例的配置。 使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在 Azure 资源中强制实施安全设置。

* [了解 Azure API 管理 DevOps 资源工具包](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure 策略影响](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此建议适用于计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：使用 Key Vault 管理证书，并将其设置为 autorotate。 如果使用 Azure 密钥保管库来管理自定义域 SSL 证书，请确保该证书作为证书而不是机密插入到密钥保管库中。

* [如何设置自定义域名，并提供 Key Vault 密钥轮换指南](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Azure 安全中心监视**：是

**责任**： Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全、自动管理标识

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）生成的托管服务标识，以允许 API 管理实例轻松、安全地访问其他受 Azure AD 保护的资源，例如 Azure Key Vault。

* [如何创建 API 管理实例的托管标识](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [用于对托管标识进行身份验证的策略](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：不适用；此建议适用于计算资源。

在支持 Azure 服务（例如，Azure API 管理）的基础主机上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：不适用;此建议适用于设计用于存储数据的非计算资源。

在支持 Azure 服务（例如，Azure API 管理）的基础主机上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用;此建议适用于设计用于存储数据的非计算资源。

在支持 Azure 服务（例如，Azure API 管理）的基础主机上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：通过 Azure API 管理来发布和管理 api，你将充分利用容错和基础结构功能，你需要手动设计、实施和管理它们。 API 管理支持多区域部署，这使得数据平面受到地区性故障，而不会增加任何运营开销。

API 管理的服务备份和还原功能为实现灾难恢复策略提供必要的构建基块。 备份和还原操作可以手动或自动执行。

* [如何将 API 管理数据平面部署到多个区域](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [如何调用 API 管理备份操作](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

* [如何调用 API 管理还原操作](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**： Azure API 管理提供的备份和还原操作执行完整的系统备份和还原。

托管标识可用于从 API 管理自定义域名 Azure Key Vault 获取证书。 备份 Azure Key Vault 中存储的所有证书。

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [如何备份 Azure Key Vault 证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：通过从备份中执行服务和证书的测试还原来验证备份。

* [如何调用 API 管理还原操作](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

* [如何还原 Azure Key Vault 证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**： Azure API 管理将备份写入客户拥有的 azure 存储帐户。 遵循 Azure 存储安全建议来保护备份。

* [如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Blob 存储的安全建议](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

在 Key Vault 中启用“软删除”，以防止意外删除或恶意删除密钥。

* [如何在 Key Vault 中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指南**：构建组织的事件响应指南。 请确保有一些书面事件响应计划，这些计划定义了人员的所有角色，以及从检测到后事件检查的事件处理/管理阶段。

* [有关构建你自己的安全事件响应过程的指导](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产）使用标记并创建命名系统，以便清晰地识别和分类 Azure 资源，尤其是处理敏感数据的资源。 根据发生事件的 Azure 资源和环境的严重程度确定警报修正的优先级。

* [Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [使用标记来组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：执行试验以测试定期事件的事件响应功能，以帮助保护 Azure 资源。 识别弱点和差距，并根据需要修改计划。

* [NIST 发布指南，适用于 IT 计划和功能的测试、培训和试验计划](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并为安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心（MSRC）发现你的数据被非法或未授权的一方访问，microsoft 将使用安全事件联系人信息与你联系。 在事实后查看事件，以确保解决问题。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议，以帮助确定 azure 资源的风险。 使用连续导出，可以手动或以持续、持续的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：利用 Azure 安全中心的工作流自动化功能，在安全警报和建议上通过 "逻辑应用" 自动触发响应。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和 red 团队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：对 Azure 资源进行定期渗透测试，并确保在60天内修正所有关键安全发现

**指南**： *[请遵循 Microsoft 交往规则，以确保你的渗透测试不违反 Microsoft 策略](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [可在此处找到有关 Microsoft 托管云基础结构、服务和应用程序的 Microsoft 策略和执行红色组合和实时站点渗透测试的详细信息](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)的详细信息
