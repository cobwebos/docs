---
title: 适用于 Azure Functions 的 Azure 安全基线
description: 适用于 Azure Functions 的 Azure 安全基线
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 040eeda3edc8aa1165915a157cb7e1bdd1594740
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796494"
---
# <a name="azure-security-baseline-for-azure-functions"></a>适用于 Azure Functions 的 Azure 安全基线

适用于 Azure Functions 的 Azure 安全基线包含有助于改进部署安全状况的建议。

此服务的基线取自[Azure 安全基准1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供了有关如何在 Azure 上利用我们的最佳实践指南来保护云解决方案的建议。

有关详细信息，请参阅[Azure 安全基线概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>网络安全

*有关详细信息，请参阅[安全控制：网络安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虚拟网络中使用网络安全组或 Azure 防火墙保护资源

**指南**：将 Azure Functions 应用与 Azure 虚拟网络集成。 在高级计划中运行的函数应用与 Azure App Service 中的 web 应用具有相同的托管功能，其中包括 "VNet 集成" 功能。  使用 azure 虚拟网络可以将许多 Azure 资源（如 Azure Functions）放置在非 internet 可路由网络中。

- [如何将函数与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/azure-functions/functions-create-vnet)

- [了解 Azure Functions 和 Azure App Service 的 Vnet 集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：监视和记录 VNet、子网和 NIC 的配置与流量

**指南**：使用 Azure 安全中心并按照网络保护建议来帮助保护与 Azure Functions 应用相关的网络资源和网络配置。

如果通过 Azure Functions 实现使用网络安全组（Nsg），请启用 NSG 流日志，并将日志发送到 Azure 存储帐户以进行流量审核。 还可以将 NSG 流日志发送到 Log Analytics 工作区，并使用流量分析来提供对 Azure 云中的流量流的见解。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [了解 Azure 安全中心提供的网络安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="13-protect-critical-web-applications"></a>1.3：保护关键 Web 应用程序

**指南**：若要在生产环境中全面保护 Azure 函数终结点，应考虑实现以下 Function 应用级安全选项之一：
- 为 function app 启用应用服务身份验证/授权，
- 使用 Azure API 管理（APIM）对请求进行身份验证，或
- 将函数应用部署到 Azure 应用服务环境。

此外，请确保已为生产 Azure Functions 禁用了远程调试。 此外，跨域资源共享（CORS）不应允许所有域访问 Azure Function app。 仅允许所需域与 Azure Function app 交互。

考虑将 Azure Web 应用程序防火墙（WAF）部署为网络配置的一部分，以便对传入流量进行额外检查。 启用 WAF 的诊断设置，并将日志引入存储帐户、事件中心或 Log Analytics 工作区。 

- [如何在生产环境中保护 Azure 函数终结点](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒绝与已知的恶意 IP 地址的通信

**指南**：在与函数应用相关联的虚拟网络上启用 ddos 保护，以防止 ddos 攻击。 使用 Azure 安全中心集成威胁情报拒绝与已知的恶意或未使用的公共 IP 地址的通信。
此外，配置前端网关（如 Azure Web 应用程序防火墙），对所有传入的请求进行身份验证并筛选出恶意流量。 Azure Web 应用程序防火墙可以通过检查入站 Web 流量来阻止 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击，从而帮助保护 Azure Function app。 引入 WAF 需要应用服务环境或使用专用终结点（预览）。 在将专用终结点用于生产工作负荷之前，请确保专用终结点不再处于（预览）。

- [Azure Functions 网络选项](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 高级计划](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [应用服务环境简介](https://docs.microsoft.com/azure/app-service/environment/intro)

- [应用服务环境的网络注意事项](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [如何配置 DDoS 保护](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [如何部署 Azure 防火墙](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [了解 Azure 安全中心集成威胁情报](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [了解 Azure 安全中心自适应网络强化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [了解 Azure 安全中心实时网络访问控制](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [使用私有终结点进行 Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：记录网络数据包和流日志

**指南**：如果将网络安全组（nsg）与 Azure Functions 实现一起使用，请启用网络安全组流日志，并将日志发送到存储帐户以进行流量审核。 你还可以将流日志发送到 Log Analytics 工作区，并使用流量分析来深入了解 Azure 云中的流量流。 流量分析的优势包括能够可视化网络活动、识别热点、识别安全威胁、了解流量流模式，以及查明网络不当配置。

- [如何启用 NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何启用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [如何启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署基于网络的入侵检测/入侵防护系统（IDS/IPS）

**指南**：配置前端网关（如 Azure Web 应用程序防火墙）来验证所有传入的请求并筛选出恶意流量。 Azure Web 应用程序防火墙可以通过检查入站 Web 流量来阻止 SQL 注入、跨站点脚本、恶意软件上传和 DDoS 攻击，从而帮助保护函数应用。 引入 WAF 需要应用服务环境或使用专用终结点（预览）。 在将专用终结点用于生产工作负荷之前，请确保专用终结点不再处于（预览）。

另外，还可以使用多种 marketplace 选项，例如 azure Marketplace 中提供的 Barracuda WAF for Azure，其中包括 ID/IPS 功能。

- [Azure Functions 网络选项](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 高级计划](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [应用服务环境简介](https://docs.microsoft.com/azure/app-service/environment/intro)

- [应用服务环境的网络注意事项](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [了解 Azure Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [使用私有终结点进行 Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

- [了解 Barracuda WAF 云服务](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理发往 Web 应用程序的流量

**指导**：为网络配置前端网关，如使用端对端 TLS 加密的 Azure Web 应用程序防火墙。 引入 WAF 需要应用服务环境或使用专用终结点（预览）。 在将专用终结点用于生产工作负荷之前，请确保专用终结点不再处于（预览）。

- [Azure Functions 网络选项](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 高级计划](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [应用服务环境简介](https://docs.microsoft.com/azure/app-service/environment/intro)

- [应用服务环境的网络注意事项](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [了解 Azure Web 应用程序防火墙](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [如何通过门户使用应用程序网关配置端到端 TLS](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

- [使用私有终结点进行 Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：最大程度地降低网络安全规则的复杂性和管理开销

**指南**：使用虚拟网络服务标记定义网络安全组或 Azure 防火墙上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "源" 或 "目标" 字段中指定服务标记名称（例如 AzureAppService），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。

- [有关使用服务标记的详细信息](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：维护网络设备的标准安全配置

**指南**：定义和实现与 Azure Functions 相关的网络设置的标准安全配置。 使用 "Microsoft Web" 和 "Microsoft 网络" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Functions 的网络配置。 你还可以使用 Azure Functions 的内置策略定义，例如：
- CORS 不应允许所有资源访问函数应用
- 应该只能通过 HTTPS 访问函数应用
- 应在函数应用中使用最新的 TLS 版本

你还可以使用 Azure 蓝图通过在单个蓝图定义中打包关键环境项目（例如 Azure 资源管理器模板、基于角色的访问控制（RBAC）和策略）来简化大规模的 Azure 部署。 通过版本控制，可以轻松地将蓝图应用到新的订阅、环境和微调控制和管理。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何创建 Azure 蓝图](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="110-document-traffic-configuration-rules"></a>1.10：阐述流量配置规则

**指南**：如果将网络安全组（nsg）与 Azure Functions 实现一起使用，请使用 nsg 以及与网络安全和通信流相关的其他资源的标记。 对于单独的 NSG 规则，请使用 "说明" 字段来指定允许进出网络流量的任何规则的业务需求和/或持续时间（等等）。

使用与标记相关的任何内置 Azure 策略定义（如 "需要标记和值"）来确保使用标记创建所有资源并通知现有未标记资源。

您可以使用 Azure PowerShell 或 Azure CLI 基于其标记对资源进行查找或执行操作。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自动化工具来监视网络资源配置和检测更改

**指南**：使用 Azure 活动日志监视网络资源配置，并检测与 Azure Functions 部署相关的网络设置和资源的更改。 在 Azure Monitor 中创建警报，以便在对关键网络设置或资源发生更改时触发。 

- [如何查看和检索 Azure 活动日志事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure Monitor 中创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="logging-and-monitoring"></a>日志记录和监视

*有关详细信息，请参阅[安全控制：日志记录和监视](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用批准的时间同步源

**指南**： Microsoft 维护用于 Azure 资源的时间源，如日志中的时间戳 Azure Functions。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：配置中心安全日志管理

**指南**：对于控制平面审核日志记录，启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、azure 事件中心或 azure 存储帐户以进行存档。 使用 Azure 活动日志数据，可以确定在 Azure 资源的控制平面级别执行的任何写入操作（PUT、POST、DELETE）的 "操作内容、操作人员和操作时间"。

Azure Functions 还提供与 Azure 应用程序 Insights 的内置集成，以监视函数。 Application Insights 收集日志、性能和错误数据。 它会自动检测性能异常，并提供强大的分析工具来帮助你诊断问题并了解函数的使用方式。

如果在 Azure Function app 中具有内置的自定义安全/审核日志记录，请启用诊断设置 "FunctionAppLogs"，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 

- [如何对 Azure 活动日志启用诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何设置 Azure 应用程序 Insights Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [如何启用 Azure Functions 的诊断设置（用户生成的日志）](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**：对于控制平面审核日志记录，启用 Azure 活动日志诊断设置，并将日志发送到 Log Analytics 工作区、azure 事件中心或 azure 存储帐户以进行存档。 使用 Azure 活动日志数据，可以确定在 Azure 资源的控制平面级别执行的任何写入操作（PUT、POST、DELETE）的 "操作内容、操作人员和操作时间"。

如果在 Azure Function app 中具有内置的自定义安全/审核日志记录，请启用诊断设置 "FunctionAppLogs"，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 

- [如何对 Azure 活动日志启用诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何启用 Azure Functions 的诊断设置（用户生成的日志）](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：从操作系统收集安全日志

**指南**：不适用;此准则适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="25-configure-security-log-storage-retention"></a>2.5：配置安全日志存储保留期

**指南**：在 Azure Monitor 中，根据组织的符合性规定，设置与 Azure Functions 应用相关联的 Log Analytics 工作区的日志保持期。

- [如何设置日志保留参数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="26-monitor-and-review-logs"></a>2.6：监视和审查日志

**指南**：启用 Azure 活动日志诊断设置以及 Azure Functions 应用的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，搜索术语，确定趋势，分析模式，并根据收集的数据提供许多其他见解。

为 Azure Functions 应用启用 Application Insights 以收集日志、性能和错误数据。 您可以查看 Azure 门户中 Application Insights 收集的遥测数据。

如果在 Azure Function app 中具有内置的自定义安全/审核日志记录，请启用诊断设置 "FunctionAppLogs"，并将日志发送到 Log Analytics 工作区、Azure 事件中心或 Azure 存储帐户以进行存档。 

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 

- [如何对 Azure 活动日志启用诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何启用 Azure Functions 的诊断设置](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [如何设置 Azure 应用程序 Insights 和查看遥测数据的 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：为异常活动启用警报

**指南**：启用 Azure 活动日志诊断设置以及 Azure Functions 应用的诊断设置，并将日志发送到 Log Analytics 工作区。 在 Log Analytics 中执行查询，搜索术语，确定趋势，分析模式，并根据收集的数据提供许多其他见解。 您可以根据 Log Analytics 工作区查询来创建警报。

为 Azure Functions 应用启用 Application Insights 以收集日志、性能和错误数据。 你可以查看 Application Insights 收集的遥测数据，并在 Azure 门户中创建警报。

或者，你可以将和机载数据启用到 Azure Sentinel 或第三方 SIEM。 

- [如何对 Azure 活动日志启用诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何启用 Azure Functions 的诊断设置](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [如何为 Azure Functions 启用 Application Insights](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#enable-application-insights-integration)

- [如何在 Azure 中创建警报](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中管理反恶意软件日志记录

**指南**：不适用;Azure Functions 应用不会处理或产生与反恶意软件相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="29-enable-dns-query-logging"></a>2.9：启用 DNS 查询日志记录

**指南**：不适用;Azure Functions 应用不会处理或产生用户可访问的与 DNS 相关的日志。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="210-enable-command-line-audit-logging"></a>2.10：启用命令行审核日志记录

**指南**：不适用;此准则适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="identity-and-access-control"></a>标识和访问控制

*有关详细信息，请参阅[安全控制：标识和访问控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

**指南**： AZURE ACTIVE DIRECTORY （AD）具有内置角色，必须显式分配这些角色并可查询。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组成员的帐户。 

- [如何使用 PowerShell 在 Azure AD 中获取目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中获取目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

**指南**：通过 AZURE ACTIVE DIRECTORY （AD）控制对 Azure Functions 的控制平面访问。 Azure AD 没有默认密码的概念。

可以通过多种方式控制数据平面访问，包括授权密钥、网络限制和验证 AAD 标识。 授权密钥由连接到 Azure Functions HTTP 终结点的客户端使用，可以随时重新生成。 默认情况下，将为新的 HTTP 终结点生成这些密钥。

函数应用提供多种部署方法，其中一些部署方法可能利用一组生成的凭据。 查看将用于应用程序的部署方法。

- [保护 HTTP 终结点](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [如何获取和重新生成授权密钥](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Azure Functions 中的部署技术](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕使用专用管理帐户创建标准操作过程。 使用 Azure 安全中心的标识和访问管理来监视管理帐户的数量。

此外，为了帮助你跟踪专用管理帐户，你可以使用 Azure 安全中心或内置 Azure 策略中的建议，例如：应将多个所有者分配给你的订阅，并且应从订阅中删除具有所有者权限的不推荐使用帐户

- [如何使用 Azure 安全中心来监视标识和访问（预览）](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [如何使用 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用带有 Azure Active Directory 的单一登录（SSO）

**指南**：若有可能，请使用 Azure Active Directory SSO，而不是配置单独的独立凭据来访问函数应用的数据。 使用 Azure 安全中心的标识和访问管理建议。 使用应用服务身份验证/授权功能实现 Azure Functions 应用的单一登录。

- [了解 Azure Functions 中的身份验证和授权](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [了解 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

**指南**：启用 AZURE ACTIVE DIRECTORY （AD）多重身份验证（MFA）并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 安全中心内监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：对配置为登录和配置 Azure 资源的多重身份验证（MFA）使用特权访问工作站（PAW）。

- [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

**指南**：在环境中发生可疑或不安全活动时，使用 AZURE ACTIVE DIRECTORY （AD） PRIVILEGED IDENTITY MANAGEMENT （PIM）来生成日志和警报。

此外，使用 Azure AD 风险检测来查看警报和报告有风险的用户行为。

- [如何部署 Privileged Identity Management （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [了解 Azure AD 风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

**指南**：使用条件访问命名位置，以仅允许从 IP 地址范围或国家/地区的特定逻辑分组访问 Azure 门户。

- [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）作为 Azure Functions 应用的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据。 还 Azure AD salts、哈希和安全存储用户凭据。

- [如何将 Azure Functions 应用配置为使用 Azure AD 登录](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [如何创建和配置 AAD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

**指南**： AZURE ACTIVE DIRECTORY （AD）提供日志以帮助你发现陈旧的帐户。 此外，使用 Azure 标识访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续访问。 

- [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：监视器尝试访问停用的帐户

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）作为 Azure Function app 的中央身份验证和授权系统。 Azure AD 通过对静态数据和传输中的数据使用强加密来保护数据。 还 Azure AD salts、哈希和安全存储用户凭据。

你有权访问 Azure AD 登录活动、审核和风险事件日志源，从而允许你与 Azure Sentinel 或第三方 SIEM 集成。

可以通过创建 Azure AD 用户帐户的诊断设置并将审核日志和登录日志发送到 Log Analytics 工作区来简化此过程。 可以在 Log Analytics 中配置所需的日志警报。

- [如何将 Azure Functions 应用配置为使用 Azure AD 登录](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何在板载 Azure 上操作](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帐户登录行为偏差的警报

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）作为 Azure Functions 应用的中央身份验证和授权系统。 对于控制平面上的帐户登录行为偏差（Azure 门户），请使用 Azure Active Directory （AD）标识保护和风险检测功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 以便进一步调查。

- [如何查看 Azure AD 有风险的登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用 Identity Protection 风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何载入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持方案中为 Microsoft 提供对相关客户数据的访问权限

**指南**：当前不可用;Azure Functions 当前不支持客户密码箱。

- [客户密码箱支持的服务列表](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-protection"></a>数据保护

*有关详细信息，请参阅[安全控制：数据保护](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

**指南**：使用标记帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

**指导**：为开发、测试和生产实施单独的订阅和/或管理组。 Azure Function apps 应由虚拟网络（VNet）/子网分隔并正确标记。

你还可以使用专用终结点来执行网络隔离。 Azure 专用终结点是一个网络接口，用于将你私下并安全地连接到由 Azure 专用链接提供支持的服务（例如： Azure Functions 应用 HTTPs 终结点）。 专用终结点使用 VNet 中的专用 IP 地址将服务有效地引入 VNet 中。 专用终结点处于高级计划中运行的函数应用的（预览版）中。 在将专用终结点用于生产工作负荷之前，请确保专用终结点不再处于（预览）。

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Functions 网络选项](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 高级计划](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [了解专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

- [使用私有终结点进行 Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止敏感信息的未授权传输

**指南**：在网络外围部署自动化工具，该工具监视敏感信息的未授权传输并在通知信息安全专业人员时阻止此类传输。 

Microsoft 管理 Azure Functions 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：不适用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

**指南**：在 Azure Function app 的 Azure 门户中，在 "平台功能：网络： SSL" 下，启用 "仅 HTTPs" 设置，并将最低 TLS 版本设置为1.2。

**Azure 安全中心监视**：是

**责任**：客户

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

**指南**：当前不可用;数据标识、分类和丢失防护功能当前不可用于 Azure Functions。 标记函数应用程序可能会以这种方式处理敏感信息，并在需要时实现第三方解决方案以实现符合性。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护一套可靠的数据保护控制和功能。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

**指南**：使用 AZURE ACTIVE DIRECTORY （AD）基于角色的访问控制（RBAC）来控制对 Azure Function control 平面（Azure 门户）的访问。 

- [如何在 Azure 中配置 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

**指南**：不适用;此建议适用于 IaaS 计算资源。

Microsoft 管理 Azure Functions 的底层基础结构，并实施了严格控制来防止客户数据丢失或泄露。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

**指南**：创建 function app 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 这是因为函数依赖 Azure 存储进行操作，例如管理触发器和记录函数执行。 Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了进一步控制加密密钥，你可以提供客户管理的密钥来加密 blob 和文件数据。 这些密钥必须存在于 Azure Key Vault 的函数应用才能访问存储帐户。

- [了解 Azure Functions 的存储注意事项](https://docs.microsoft.com/azure/azure-functions/storage-considerations)

- [了解静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure 安全中心监视**：不适用

**责任**：共享

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与 azure 活动日志结合使用，以便为生产 Azure Function app 以及其他关键或相关资源的发生更改创建警报。

- [如何为 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="vulnerability-management"></a>漏洞管理

*有关详细信息，请参阅[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：运行自动漏洞扫描工具

**指南**：采用 DevSecOps 做法，确保 Azure Functions 的应用程序安全，并在其生命周期的整个持续时间内保持尽可能安全。 DevSecOps 将你的组织的安全团队及其功能合并到你的 DevOps 做法，使安全成为团队中每个人的责任。

此外，请遵循 Azure 安全中心的建议，以帮助保护 Azure Function app。

- [如何将持续安全验证添加到 CI/CD 管道](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [如何实现 Azure 安全中心漏洞评估建议](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2：部署自动操作系统修补程序管理解决方案

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署第三方自动软件修补管理解决方案

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比较连续进行的漏洞扫描

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用风险评级过程来确定已发现漏洞的修正措施的优先级

**指南**： Microsoft 对支持 Azure Functions 的基础系统执行漏洞管理，但你可以使用 Azure 安全中心内的建议的严重性以及安全分数来衡量你的环境中的风险。 安全分数以您已缓解的安全中心建议的数量为基础。 若要确定首先要解决的建议的优先级，请考虑每个建议的严重性。

- [安全建议参考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 安全中心监视**：是

**责任**：共享

## <a name="inventory-and-asset-management"></a>库存和资产管理

*有关详细信息，请参阅[安全控制：清单和资产管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

**指南**：使用 Azure 资源关系图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。  请确保租户中的相应（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

尽管可以通过 Resource Graph 发现经典 Azure 资源，但我们强烈建议你今后还是创建并使用 Azure 资源管理器资源。

- [如何通过 Azure 资源关系图创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何查看你的 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指南**：将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

**指南**：使用标记、管理组和单独的订阅（如果适用）来组织和跟踪 Azure 资源。 定期核对清单，确保及时地从订阅中删除未经授权的资源。

此外，使用 Azure 策略将限制添加到可使用以下内置策略定义在客户订阅中创建的资源类型：不允许的资源类型允许的资源类型

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

**指南**：定义已批准的 Azure 资源和用于计算资源的批准的软件。

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 策略对可在订阅中创建的资源类型施加限制。 

使用 Azure 资源关系图在其订阅中查询/发现资源。  确保环境中存在的所有 Azure 资源都已获得批准。 

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何通过 Azure Graph 创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未批准的软件应用程序

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未批准的 Azure 资源和软件应用程序

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="68-use-only-approved-applications"></a>6.8：仅使用已批准的应用程序

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="69-use-only-approved-azure-services"></a>6.9：仅使用已批准的 Azure 服务

**指南**：使用 Azure 策略对可使用以下内置策略定义在客户订阅中创建的资源类型施加限制：不允许的资源类型允许的资源类型

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 策略拒绝特定的资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="610-implement-approved-application-list"></a>6.10：实施已批准的应用程序列表

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

**指南**：通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，配置 Azure 条件性访问，以限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件性访问以阻止访问 Azure 资源管理器](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的功能

**指南**：不适用;此建议适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以物理或逻辑方式隔离高风险应用程序

**指南**：对于敏感或高风险的 Azure Function app，实现单独的订阅和/或管理组以提供隔离。

将高风险 Azure Function app 部署到自己的虚拟网络（VNet）中。 Azure Functions 中的外围安全性通过 Vnet 实现。 高级计划或应用服务环境（ASE）中运行的功能可以与 Vnet 集成。 为用例选择最佳体系结构。

- [Azure Functions 网络选项](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 高级计划](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [应用服务环境的网络注意事项](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [如何创建外部 ASE](https://docs.microsoft.com/azure/app-service/environment/create-external-ase)

如何创建内部 ASE：

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建使用安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="secure-configuration"></a>安全配置

*有关详细信息，请参阅[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

**指南**：定义和实现 azure Function App 和 azure 策略的标准安全配置。 使用 "System.web" 命名空间中的 Azure 策略别名创建自定义策略，以便审核或强制实施 Azure Functions 应用的配置。 你还可以使用内置的策略定义，例如：
- 应在函数应用中使用托管标识
- 应当为函数应用禁用远程调试
- 应该只能通过 HTTPS 访问函数应用

- [如何查看可用的 Azure 策略别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

**指南**：不适用;此准则适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

**指导**：使用 Azure 策略“[拒绝]”和“[不存在则部署]”对不同的 Azure 资源强制实施安全设置。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [了解 Azure 策略影响](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

**指南**：不适用;尽管可以部署本地功能，但此准则适用于 IaaS 计算资源。 在本地运行时，您需要负责环境的安全配置。

- [了解本地函数](https://docs.microsoft.com/azure/azure-functions/functions-runtime-install)

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地存储 Azure 资源的配置

**指南**：在源代码管理中安全地存储和管理 ARM 模板和自定义 Azure 策略定义。

- [什么是基础结构即代码](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

- [作为代码工作流的设计策略](https://docs.microsoft.com/azure/governance/policy/concepts/policy-as-code)

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

**指南**：不适用;此准则适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

**指南**：使用内置的 azure 策略定义以及 "Microsoft" 命名空间中的 azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 另外，开发一个用于管理策略例外的流程和管道。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：为操作系统部署系统配置管理工具

**指南**：不适用;此准则适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：为 Azure 服务实施自动配置监视

**指南**：使用内置的 azure 策略定义以及 "Microsoft" 命名空间中的 azure 策略别名创建自定义策略，以对系统配置进行警报、审核和强制执行。 使用 Azure 策略 [audit]、[拒绝] 和 [部署（如果不存在））自动强制执行 Azure 资源的配置。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实施自动配置监视

**指南**：不适用;此准则适用于 IaaS 计算资源。

**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密

**指南**：将托管标识与 Azure Key Vault 结合使用，为你的云应用程序简化和安全密钥管理。 通过托管标识，函数应用可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Key Vault，而无需在代码中包含任何凭据。

- [如何创建 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [如何使用应用服务和 Azure Functions 的托管标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

- [如何使用托管标识提供 Key Vault 身份验证](https://docs.microsoft.com/azure/key-vault/managed-identity)

- [使用应用服务和 Azure Functions 的 Key Vault 引用](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全、自动管理标识

**指南**：使用托管标识，通过 Azure AD 中的自动托管标识提供 Azure Function app。 通过托管标识，你可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Key Vault，而无需在代码中包含任何凭据。

- [如何使用应用服务和 Azure Functions 的托管标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据透露

**指导**：实施凭据扫描程序来识别代码中的凭据。 凭据扫描程序还会建议将发现的凭据转移到更安全的位置，例如 Azure Key Vault。 

- [如何设置凭据扫描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="malware-defense"></a>恶意软件防护

*有关详细信息，请参阅[安全控制：恶意软件防护](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反恶意软件

**指南**：不适用;此准则适用于 IaaS 计算资源。

在支持 Azure 服务的基础主机（例如 Azure Functions）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**： Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：预先扫描要上传到非计算 Azure 资源的文件

**指南**：不适用;此建议适用于设计用于存储数据的非计算资源。


**Azure 安全中心监视**：不适用

**责任**：不适用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>步骤 8.3：确保反恶意软件和签名已更新

**指南**：不适用;此建议适用于设计用于存储数据的非计算资源。

在支持 Azure 服务的基础主机（例如 Azure Functions）上启用了 Microsoft 反恶意软件，但它不会在客户内容上运行。

**Azure 安全中心监视**：不适用

**责任**：不适用

## <a name="data-recovery"></a>数据恢复

*有关详细信息，请参阅[安全控制：数据恢复](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期执行自动备份

**指南**：使用备份和还原功能来计划应用的定期备份。 在高级计划中运行的函数应用与 Azure App Service 中的 web 应用具有相同的托管功能，其中包括 "备份和还原" 功能。

还利用源代码管理解决方案（如 Azure Repos 和 Azure DevOps）来安全地存储和管理代码。 Azure DevOps Services 利用了许多 Azure 存储功能，以确保在发生硬件故障、服务中断或区域灾难时能够实现数据的可用性。 此外，Azure DevOps 团队按照过程来保护数据免遭意外或恶意删除。

- [在 Azure 中备份应用](https://docs.microsoft.com/azure/app-service/manage-backup)

- [了解 Azure DevOps 中的数据可用性](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整系统备份，并备份客户管理的所有密钥

**指南**：使用备份和还原功能来计划应用的定期备份。 在高级计划中运行的函数应用与 Azure App Service 中的 web 应用具有相同的托管功能，其中包括 "备份和还原" 功能。 Azure Key Vault 中的备份客户托管密钥。

还利用源代码管理解决方案（如 Azure Repos 和 Azure DevOps）来安全地存储和管理代码。 Azure DevOps Services 利用了许多 Azure 存储功能，以确保在发生硬件故障、服务中断或区域灾难时能够实现数据的可用性。 此外，Azure DevOps 团队按照过程来保护数据免遭意外或恶意删除。

- [在 Azure 中备份应用](https://docs.microsoft.com/azure/app-service/manage-backup)

- [如何在 Azure 中备份 key vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [了解 Azure DevOps 中的数据可用性](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文档](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证所有备份，包括客户管理的密钥

**指南**：确保能够定期从备份和还原功能执行还原。 如果使用另一个脱机位置来备份代码，则会定期确保执行完整还原。 测试已备份客户托管密钥的还原。

- [从备份中还原 Azure 中的应用](https://docs.microsoft.com/azure/app-service/web-sites-restore)

- [在 Azure 中从快照还原应用](https://docs.microsoft.com/azure/app-service/app-service-web-restore-snapshots)

- [如何在 Azure 中还原 key vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

**指南**：备份和还原功能中的备份使用订阅中的 Azure 存储帐户。 Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了进一步控制加密密钥，你可以提供客户管理的密钥来加密存储数据。

如果使用客户管理的密钥，请确保已启用 Key Vault 中的软删除功能，以防止意外或恶意删除密钥。

- [Azure 存储静态加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [如何在 Key Vault 中启用软删除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 安全中心监视**：是

**责任**：共享

## <a name="incident-response"></a>事件响应

*有关详细信息，请参阅[安全控制：事件响应](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：创建事件响应指南

**指南**：构建组织的事件响应指南。 请确保有一些书面事件响应计划，这些计划定义了人员的所有角色，以及从检测到后事件检查的事件处理/管理阶段。

- [如何在 Azure 安全中心内配置 Workflow 自动化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [有关构建你自己的安全事件响应过程的指导](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全响应中心事件分析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客户还可以利用 NIST 的计算机安全事件处理指南来帮助创建自己的事件响应计划](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：创建事件评分和优先级设定过程

**指导**：安全中心为每条警报分配严重性，以帮助你优先处理应该最先调查的警报。 严重性取决于安全中心在发出警报时所依据的检测结果和分析结果的置信度，以及导致发出警报的活动的恶意企图的置信度。

此外，请明确标记订阅（例如 生产、非生产），并创建命名系统来对 Azure 资源进行明确标识和分类。

**Azure 安全中心监视**：是

**责任**：共享

### <a name="103-test-security-response-procedures"></a>10.3：测试安全响应过程

**指导**：执行试验，以定期测试系统的事件响应功能。 识别弱点和差距，并根据需要修改计划。

- [请参阅 NIST 发布：针对 IT 计划和功能的测试、培训和运用计划指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件联系人详细信息并为安全事件配置警报通知

**指南**：如果 Microsoft 安全响应中心（MSRC）发现客户数据被非法或未授权的一方访问，microsoft 将使用安全事件联系人信息与你联系。  在事实后查看事件，以确保解决问题。

- [如何设置 Azure 安全中心安全联系人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 安全中心监视**：是

**责任**：客户

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：将安全警报合并到事件响应系统中

**指南**：使用连续导出功能导出 Azure 安全中心警报和建议。 使用连续导出，可以手动或以持续、持续的方式导出警报和建议。 可以使用 Azure 安全中心数据连接器将警报流式传输到 Azure Sentinel。

- [如何配置连续导出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何将警报流式传输到 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 安全中心监视**：不适用

**责任**：客户

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自动响应安全警报

**指南**：使用 Azure 安全中心的工作流自动化功能，可自动触发对逻辑应用的安全警报和建议的响应。

- [如何配置工作流自动化和逻辑应用](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 安全中心监视**：不适用

**责任**：客户

## <a name="penetration-tests-and-red-team-exercises"></a>渗透测试和红队练习

*有关详细信息，请参阅[安全控制：渗透测试和 red 团队练习](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：对 Azure 资源进行定期渗透测试，并确保所有关键安全发现的修正

**指南**：遵循 Microsoft 订婚规则确保你的渗透测试不违反 Microsoft 政策。 针对 Microsoft 托管的云基础结构、服务和应用程序，使用 Microsoft 的战略和对红色组合和活动站点渗透测试的执行。

- [参与的渗透测试规则](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 云红色组合](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 安全中心监视**：不适用

**责任**：共享

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 了解有关[Azure 安全基线](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)的详细信息
