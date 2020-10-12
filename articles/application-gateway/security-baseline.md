---
title: 适用于 Azure 应用程序网关的 Azure 安全基线
description: 适用于 Azure 应用程序网关的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b470fa72518f805c10403931280bdec96bda0fbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226533"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>适用于 Azure 应用程序网关的 Azure 安全基线

Azure 应用程序网关的 Azure 安全基线包含可帮助你改善部署安全状况的建议。

此服务的基线摘自 [Azure 安全基准版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何根据我们的最佳做法指导保护 Azure 上的云解决方案的建议。

有关详细信息，请参阅 [Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保护虚拟网络中的 Azure 资源

**指导**：请确保所有虚拟网络 Azure 应用程序网关子网部署都应用了网络安全组 (NSG)，且具有针对应用程序受信任端口和源的网络访问控制。 虽然 Azure 应用程序网关上支持网络安全组，但必须遵守某些限制和要求，以使 NSG 和 Azure 应用程序网关按预期运行。

* [了解有关将 NSG 与 Azure 应用程序网关结合使用的限制和要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：监视和记录虚拟网络、子网和 NIC 的配置与流量

**指导**：对于与 Azure 应用程序网关子网关联的网络安全组 (NSG)，启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

注意：在某些情况下，与 Azure 应用程序网关子网关联的 NSG 流日志不会显示已允许的流量。 如果配置与以下情况匹配，则 NSG 流日志中不会显示允许的流量：
- 已部署了应用程序网关 v2
- 应用程序网关子网上有 NSG
- 已在该 NSG 上启用了 NSG 流日志

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure 应用程序网关的诊断和日志记录常见问题解答](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 Web 应用程序防火墙 (WAF) 是（Azure 应用程序网关的）一项服务功能，可以对 Web 应用程序进行集中保护，避免其受到常见攻击和漏洞的伤害。 Azure WAF 通过检查入站 Web 流量来阻止攻击（例如 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击），从而帮助保护 Azure 应用服务 Web 应用的安全。 WAF 基于 OWASP（开放 Web 应用程序安全项目）核心规则集 3.1（仅限 WAF_v2）、3.0 和 2.2.9 中的规则。

* [了解 Azure 应用程序网关功能](https://docs.microsoft.com/azure/application-gateway/features)

* [了解 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知恶意的 IP 地址进行通信

**指导**：在与 Azure 应用程序网关的生产实例相关联的 Azure 虚拟网络上启用 DDoS 标准保护，以防范 DDoS 攻击。 使用 Azure 安全中心的集成式威胁情报功能拒绝与已知的恶意 IP 地址通信。

* [如何配置 DDoS 防护](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 安全中心集成的威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets"></a>1.5：记录网络数据包

**指导**：对于与 Azure 应用程序网关子网关联的网络安全组 (NSG)，启用 NSG 流日志，并将日志发送到存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供有关 Azure 云中流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

注意：在某些情况下，与 Azure 应用程序网关子网关联的 NSG 流日志不会显示已允许的流量。 如果配置与以下情况匹配，则 NSG 流日志中不会显示允许的流量：
- 已部署了应用程序网关 v2
- 应用程序网关子网上有 NSG
- 已在该 NSG 上启用了 NSG 流日志

* [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure 应用程序网关的诊断和日志记录常见问题解答](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统 (IDS/IPS)

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF)，以对传入的流量进行额外的检查。 Web 应用程序防火墙 (WAF) 是（Azure 应用程序网关的）一项服务功能，可以对 Web 应用程序进行集中保护，避免其受到常见攻击和漏洞的伤害。 Azure WAF 通过检查入站 Web 流量来阻止攻击（例如 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击），从而帮助保护 Azure 应用服务 Web 应用的安全。 WAF 基于 OWASP（开放 Web 应用程序安全项目）核心规则集 3.1（仅限 WAF_v2）、3.0 和 2.2.9 中的规则。

另外，Azure 市场上还提供了多种市场选项，它们均包含 IDS/IPS 功能，例如 Barracuda WAF for Azure。

* [了解 Azure 应用程序网关功能](https://docs.microsoft.com/azure/application-gateway/features)

* [了解 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [了解 Barracuda WAF 云服务](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：为 Web 应用程序部署 Azure 应用程序网关，并为受信任的证书启用 HTTPS/SSL。

* [如何部署应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [如何将应用程序网关配置为使用 HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [了解 Azure Web 应用程序网关的第七层负载均衡](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指导**：在网络安全组或 Azure 防火墙中使用虚拟网络服务标记来定义网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GatewayManager），可以允许或拒绝相应服务的流量。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

对于与 Azure 应用程序网关子网关联的网络安全组 (NSG)，必须为应用程序网关 v1 SKU 在 TCP 端口 65503-65534 上允许传入 Internet 流量，为 v2 SKU 在 TCP 端口 65200-65535 上允许传入 Internet 流量，并将目标子网设为 Any，将源设为 GatewayManager 服务标记。 此端口范围是进行 Azure 基础结构通信所必需的。 这些端口受 Azure 证书的保护（处于锁定状态）。 外部实体（包括这些网关的客户）无法在这些终结点上通信。

* [了解并使用服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Azure 应用程序网关配置概述](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指导**：定义并实现与 Azure 应用程序网关部署相关的网络设置的标准安全配置。 在“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 应用程序网关、Azure 虚拟网络和网络安全组的网络配置。 还可以利用内置策略定义。

还可以使用 Azure 蓝图，通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、Azure 基于角色的访问控制 (Azure RBAC) 和策略）来简化大规模的 Azure 部署。 可以轻松将蓝图应用到新的订阅和环境，并通过版本控制来微调控制措施和管理。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指导**：对与 Azure 应用程序网关子网关联的网络安全组 (NSG) 以及与网络安全和流量流相关的任何其他资源使用标记。 对于单个 NSG 规则，请使用“说明”字段针对允许流量传入/传出网络的任何规则指定业务需求和/或持续时间等。

使用标记相关的任何内置 Azure 策略定义（例如“需要标记及其值”）来确保使用标记创建所有资源，并在有现有资源不带标记时发出通知。

可以使用 Azure PowerShell 或 Azure CLI 根据资源的标记查找资源或对其执行操作。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指导**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure 应用程序网关部署相关的网络设置和资源的更改。 在 Azure Monitor 中创建当关键网络设置或资源发生更改时触发的警报。

* [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指导**：Microsoft 维护用于 Azure 资源的时间源，如 Azure 应用服务。

**Azure 安全中心监视**：不适用

**责任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户。 使用 Azure 活动日志数据，可以确定任何写入操作（PUT、POST、DELETE）的“内容、对象和时间”，这些写入操作在控制平面级别上针对 Azure 应用程序网关以及用于保护 Azure 应用程序网关子网的相关资源执行。

除活动日志外，还可以配置 Azure 应用程序网关部署的诊断设置。 诊断设置用于将资源的平台日志和指标流式导出配置到你选择的目标位置（存储帐户、事件中心和 Log Analytics）。

Azure 应用程序网关还提供与 Azure Application Insights 的内置集成。 Application Insights 可收集日志、性能和错误数据。 Application Insights 可自动检测性能异常，并且包含了强大的分析工具来帮助你诊断问题并了解 Web 应用的使用情况。 可以启用连续导出，将遥测从 Application Insights 导出到一个集中位置，以使数据保留的时间长于标准保持期。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何 Azure 应用程序网关启用诊断设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何启用 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [如何配置连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：对于控制平面审核日志记录，请启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户。 使用 Azure 活动日志数据，可以确定任何写入操作（PUT、POST、DELETE）的“内容、对象和时间”，这些写入操作在控制平面级别上针对 Azure 应用程序网关以及用于保护 Azure 应用程序网关子网的相关资源执行。

除活动日志外，还可以配置 Azure 应用程序网关部署的诊断设置。 诊断设置用于将资源的平台日志和指标流式导出配置到你选择的目标位置（存储帐户、事件中心和 Log Analytics）。

Azure 应用程序网关还提供与 Azure Application Insights 的内置集成。 Application Insights 可收集日志、性能和错误数据。 Application Insights 可自动检测性能异常，并且包含了强大的分析工具来帮助你诊断问题并了解 Web 应用的使用情况。 可以启用连续导出，将遥测从 Application Insights 导出到一个集中位置，以使数据保留的时间长于标准保持期。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何 Azure 应用程序网关启用诊断设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何启用 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [如何配置连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的合规性规章设置 Log Analytics 工作区保留期。 使用 Azure 存储帐户进行长期/存档存储。

* [如何为 Log Analytics 工作区设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指导**：启用 Azure 活动日志诊断设置以及 Azure 应用程序网关的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的数据提供许多其他见解。

使用 Azure 网络监视器以全面了解所有已部署网络资源（包括 Azure 应用程序网关）的运行状况和指标。

（可选）可以启用将数据加入 Azure Sentinel 或第三方 SIEM 的功能。

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何 Azure 应用程序网关启用诊断设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何使用 Azure 网络监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：针对异常活动启用警报

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF) v2 SKU，以对传入的流量进行额外的检查。 Web 应用程序防火墙 (WAF) 是（Azure 应用程序网关的）一项服务功能，可以对 Web 应用程序进行集中保护，避免其受到常见攻击和漏洞的伤害。 Azure WAF 通过检查入站 Web 流量来阻止攻击（例如 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击），从而帮助保护 Azure 应用服务 Web 应用的安全。 WAF 基于 OWASP（开放 Web 应用程序安全项目）核心规则集 3.1（仅限 WAF_v2）、3.0 和 2.2.9 中的规则。

启用 Azure 活动日志诊断设置以及 Azure WAF 的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，以搜索字词、识别趋势、分析模式，并根据收集的数据提供许多其他见解。 可以根据 Log Analytics 工作区查询来创建警报。

使用 Azure 网络监视器以全面了解所有已部署网络资源（包括 Azure 应用程序网关）的运行状况和指标。 在 Azure 网络监视器控制台中，可以查看和创建 Azure 应用程序网关的警报。

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [如何启用 Azure 活动日志的诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何 Azure 应用程序网关启用诊断设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何使用 Azure 网络监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

* [如何在 Azure 中创建警报](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF) v2，以对传入的流量进行额外的检查。 Web 应用程序防火墙 (WAF) 是（Azure 应用程序网关的）一项服务功能，可以对 Web 应用程序进行集中保护，避免其受到常见攻击和漏洞的伤害。 Azure WAF 通过检查入站 Web 流量来阻止攻击（例如 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击），从而帮助保护 Azure 应用服务 Web 应用的安全。

配置 Azure 应用程序网关部署的诊断设置。 诊断设置用于将资源的平台日志和指标流式导出配置到你选择的目标位置（存储帐户、事件中心和 Log Analytics）。

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [如何配置 Azure WAF 的诊断设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指导**：不适用；Azure 应用程序网关不会处理或生成与用户可访问的 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指导**：不适用；此项指导适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指导**：Azure Active Directory (AD) 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。

* [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指导**：通过 Azure Active Directory (AD) 控制对 Azure 应用程序网关的控制平面访问。 Azure AD 没有默认密码。

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置的 Azure 策略提供的建议，例如：
- 应该为你的订阅分配了多个所有者
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户

* [如何使用 Azure 安全中心监视标识和访问（预览）](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

**指导**：使用 Azure 应用注册（服务主体）来检索令牌，该令牌可用于通过 API 调用与 Azure 应用程序网关进行交互。

* [如何调用 Azure REST API](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何将客户端应用程序（服务主体）注册到 Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure 恢复服务 API 信息](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指导**：启用 Azure AD MFA，并遵循 Azure 安全中心标识和访问管理建议。

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 安全中心监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将 PAW（特权访问工作站）与为登录和配置 Azure 资源而配置的 MFA 结合使用。

* [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指导**：使用 Azure Active Directory 安全报告在环境中发生可疑活动或不安全的活动时生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

* [如何确定标记为存在风险活动的 Azure AD 用户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 安全中心内监视用户的标识和访问活动](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

* [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指导**：使用 Azure Active Directory (AAD) 作为中心身份验证和授权系统。 AAD 通过对静态数据和传输中数据使用强加密来保护数据。 AAD 还会对用户凭据进行加盐、哈希处理和安全存储。

* [如何创建和配置 AAD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**：Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。

* [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

**指导**：你有权访问 Azure AD 登录活动、审核和风险事件日志源，以便与任何 SIEM/监视工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

* [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

**指导**：可使用 Azure AD 标识保护和风险检测功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可将数据引入 Azure Sentinel 以做进一步调查。

* [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

**指导**：不适用；客户密码箱不适用于 Azure 应用程序网关。

* [支持客户密码箱的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：目前不可用

**责任**：不适用

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指导**：使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 请确保所有虚拟网络 Azure 应用程序网关子网部署都应用了网络安全组 (NSG)，且具有针对应用程序受信任端口和源的网络访问控制。 虽然 Azure 应用程序网关上支持网络安全组，但必须遵守某些限制和要求，以使 NSG 和 Azure 应用程序网关按预期运行。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [了解有关将 NSG 与 Azure 应用程序网关结合使用的限制和要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

**指导**：请确保所有虚拟网络 Azure 应用程序网关子网部署都应用了网络安全组 (NSG)，且具有针对应用程序受信任端口和源的网络访问控制。 将出站流量仅限于受信任的位置，以帮助缓解数据泄露的威胁。 虽然 Azure 应用程序网关上支持网络安全组，但必须遵守某些限制和要求，以使 NSG 和 Azure 应用程序网关按预期运行。

* [了解有关将 NSG 与 Azure 应用程序网关结合使用的限制和要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指导**：为 Azure 应用程序网关配置具有 TLS 的端到端加密。

* [如何使用 Azure 应用程序网关配置端到端 TLS](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

**Azure 安全中心监视**：目前不可用

**责任**：共享

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指导**：不适用，Azure 应用程序网关不会存储静态客户数据。

Microsoft 会管理 Azure 应用程序网关的底层基础结构，并实现了严格的控制来防止客户数据丢失或泄露。

* [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制对 Azure 应用程序网关控制平面（Azure 门户）的访问。

* [如何配置 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指导**：不适用；Azure 应用程序网关不会存储客户数据。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：将 Azure Monitor 与 Azure 活动日志结合使用，以创建在生产 Azure 应用程序网关实例以及其他关键或相关资源发生更改时触发的警报。

* [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指导**：当前不可用；Azure 安全中心的漏洞评估暂不适用于 Azure 应用程序网关。

Microsoft 对基础平台进行了扫描和修补。 查看 Azure 应用程序网关可用的安全控制，以减少与服务配置相关的漏洞。

* [Azure PaaS 服务的功能范围（包括漏洞评估）](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补管理解决方案

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：为第三方软件部署自动修补程序管理解决方案

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指导**：暂不可用；Azure 安全中心的漏洞评估暂不适用于 Azure 应用程序网关。

Microsoft 对基础平台进行了扫描和修补。 查看 Azure 应用程序网关可用的安全控制，以减少与服务配置相关的漏洞。

* [Azure PaaS 服务的功能范围（包括漏洞评估）](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指导**：暂不可用；Azure 安全中心的漏洞评估暂不适用于 Azure 应用程序网关。

Microsoft 对基础平台进行了扫描和修补。 查看 Azure 应用程序网关可用的安全控制，以减少与服务配置相关的漏洞。

* [Azure PaaS 服务的功能范围（包括漏洞评估）](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自动化资产发现解决方案

**指导**：使用 Azure Resource Graph 查询/发现订阅中的所有资源（例如计算、存储、网络、端口和协议等）。 确保租户中具有适当的（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

* [如何使用 Azure Resource Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用于 Azure 资源，从而将元数据按逻辑组织到分类中。

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：在适用的情况下，请使用标记、管理组和单独的订阅来组织和跟踪 Azure 资产。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指导**：定义已批准的 Azure 资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

使用 Azure Resource Graph 查询/发现订阅中的资源。 确保环境中存在的所有 Azure 资源已获得批准。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：在 Azure Policy 中使用以下内置策略定义，对可以在客户订阅中创建的资源类型施加限制：
- 不允许的资源类型
- 允许的资源类型

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Policy 拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：维护已获批软件的清单

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器进行交互的能力

**指南**：配置 Azure 条件访问，使其通过为“Microsoft Azure 管理”应用配置“阻止访问”，来限制用户与 Azure 资源管理器进行交互的能力。

* [如何配置条件访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 请确保所有虚拟网络 Azure 应用程序网关子网部署都应用了网络安全组 (NSG)，且具有针对应用程序受信任端口和源的网络访问控制。 虽然 Azure 应用程序网关上支持网络安全组，但必须遵守某些限制和要求，以使 NSG 和 Azure 应用程序网关按预期运行。

* [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [了解有关将 NSG 与 Azure 应用程序网关结合使用的限制和要求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何创建采用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="secure-configuration"></a>安全配置

有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指导**：定义并实现与 Azure 应用程序网关部署相关的网络设置的标准安全配置。 在“Microsoft.Network”命名空间中使用 Azure Policy 别名创建自定义策略，以审核或强制实施 Azure 应用程序网关、Azure 虚拟网络和网络安全组的网络配置。 还可以利用内置策略定义。

* [如何查看可用的 Azure Policy 别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全存储 Azure 资源的配置

**指南**：如果使用的是自定义 Azure 策略定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署 Azure 资源的配置管理工具

**指导**：在“Microsoft.Network”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 另外，开发一个用于管理策略例外的流程和管道。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署操作系统的配置管理工具

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：为 Azure 资源实施自动配置监视

**指导**：在“Microsoft.Network”命名空间中使用内置的 Azure Policy 定义和 Azure Policy 别名创建自定义策略，以审核、强制实施系统配置并为其发出警报。 使用 Azure Policy“[审核]”、“[拒绝]”和“[不存在则部署]”自动强制实施 Azure 资源的配置。

* [如何配置和管理 Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指导**：不适用；此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指导**：使用托管标识在 Azure Active Directory (AD) 中为 Azure 应用程序网关提供一个自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

使用 Azure Key Vault 安全地存储证书。 Azure Key Vault 是由平台管理的机密存储，可以用来保证机密、密钥和 SSL 证书的安全。 Azure 应用程序网关支持与密钥保管库集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 此支持仅限于应用程序网关 v2 SKU。

* [如何使用 Azure PowerShell 通过 Key Vault 证书配置 SSL 终止](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全自动管理标识

**指导**：使用托管标识在 Azure Active Directory (AD) 中为 Azure 应用程序网关提供一个自动托管标识。 使用托管标识可以向支持 Azure AD 身份验证的任何服务（包括 Key Vault）进行身份验证，无需在代码中放入任何凭据。

使用 Azure Key Vault 安全地存储证书。 Azure Key Vault 是由平台管理的机密存储，可以用来保证机密、密钥和 SSL 证书的安全。 Azure 应用程序网关支持与密钥保管库集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 此支持仅限于应用程序网关 v2 SKU。

* [如何使用 Azure PowerShell 通过 Key Vault 证书配置 SSL 终止](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指南**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。

* [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指导**：在关键 Web 应用程序前部署 Azure Web 应用程序防火墙 (WAF) v2，以对传入的流量进行额外的检查。 Web 应用程序防火墙 (WAF) 是（Azure 应用程序网关的）一项服务功能，可以对 Web 应用程序进行集中保护，避免其受到常见攻击和漏洞的伤害。 Azure WAF 通过检查入站 Web 流量来阻止攻击（例如 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击），从而帮助保护 Azure 应用服务 Web 应用的安全。

配置 Azure 应用程序网关部署的诊断设置。 诊断设置用于将资源的平台日志和指标流式导出配置到你选择的目标位置（存储帐户、事件中心和 Log Analytics）。

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [如何配置 Azure WAF 的诊断设置](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指导**：不适用；Azure 应用程序网关不会存储客户数据。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指导**：使用 Azure Web 应用程序防火墙 (WAF) 时，可以配置 WAF 策略。 WAF 策略包含两种类型的安全规则：由客户创作的自定义规则，以及托管规则集（即由 Azure 托管的预配置规则组的集合）。 Azure 托管的规则集可轻松针对一组常见的安全威胁来部署保护。 由于此类规则集由 Azure 托管，因此这些规则会根据需要进行更新以预防新的攻击签名。

* [了解 Azure 托管的 WAF 规则集](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy)

**Azure 安全中心监视**：目前不可用

**责任**：共享

## <a name="data-recovery"></a>数据恢复

有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指导**：Azure 应用程序网关不会存储客户数据。 但是，如果使用自定义的 Azure Policy 定义，请使用 Azure DevOps 或 Azure Repos 安全地存储和管理代码。

Azure DevOps Services 利用许多 Azure 存储功能，确保在发生硬件失败、服务中断或区域灾难时保持数据可用性。 此外，Azure DevOps 团队按照标准程序来保护数据免遭意外或恶意删除。

* [了解 Azure DevOps 中的数据可用性](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指导**：在 Azure Key Vault 中备份客户托管证书。

* [如何在 Azure 中备份密钥保管库证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指导**：测试已备份客户托管证书的还原。

* [如何还原密钥保管库证书](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指导**：请确保为 Azure Key Vault 启用软删除。 软删除允许恢复已删除的密钥保管库和保管库对象，例如密钥、机密和证书。

* [如何使用 Azure Key Vault 软删除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 安全中心监视**：是

**责任**：客户

## <a name="incident-response"></a>事件响应

有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指导

**指南**：为组织制定事件响应指南。 确保在书面的事件响应计划中定义人员职责，以及事件处理/管理从检测到事件后审查的各个阶段。

* [如何在 Azure 安全中心配置工作流自动化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [关于建立自己的安全事件响应流程的指南](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客户还可以利用 NIST 的“计算机安全事件处理指南”来制定他们自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指南**：安全中心向每个警报分配一个严重性，帮助你优先处理应首先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：定期执行演练来测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

* [请参阅 NIST 的刊物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)（IT 规划和功能的测试、培训与演练计划指南）

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息，并针对安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心 (MSRC) 发现非法或未经授权的某方访问了客户的数据，Microsoft 将使用安全事件联系人信息与你取得联系。 事后审查事件，确保问题得到解决。

* [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报整合到事件响应系统中

**指导**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出可以手动导出或者持续导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Sentinel。

* [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指导**：使用 Azure 安全中心内的工作流自动化功能可以通过“逻辑应用”针对安全警报和建议自动触发响应。

* [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

有关详细信息，请参阅[安全控制：渗透测试和红队演练](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期对 Azure 资源执行渗透测试，确保修正所有发现的关键安全问题

**指导**： 

* [请遵循 Microsoft 互动规则，确保你的渗透测试不违反 Microsoft 政策](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [可在此处详细了解如何针对 Microsoft 托管云基础结构、服务和应用程序执行红队测试和实时站点渗透测试，以及 Microsoft 的相关策略](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
