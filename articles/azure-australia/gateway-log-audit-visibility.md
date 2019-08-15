---
title: Azure 澳大利亚的网关日志记录、审核和可见性
description: 如何在澳大利亚地区配置日志记录、审核和可见性, 以满足澳大利亚政府政策、法规和法规的特定要求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: b7a9f28d06b5e921b5f1b8defa151641bb039940
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990213"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Azure 澳大利亚的网关日志记录、审核和可见性

检测和响应网络安全威胁依赖于生成、收集和分析与系统操作相关的数据。

Microsoft 在 Azure 中提供了内置工具, 可帮助你实现日志记录、审核和可见性, 以管理 Azure 中部署的系统的安全。 还有一种参考体系结构, 它与澳大利亚网络安全中心 (ACSC) 使用者指南和信息安全手册 (ISM) 的意图相符。

网关充当网络层的信息流控制机制, 还可以在开放系统互连 (OSI) 模型的更高层中控制信息。 需要使用网关来控制安全域之间的数据流, 并防止未经授权访问外部网络。 考虑到控制安全域间信息流的网关的重要程度, 任何失败, 特别是在更高的分类上, 都可能产生严重后果。 因此, 在可能导致网络安全事件的情况下, 用于向人员发出警报的强大机制对于网关尤其重要。

为网关实现日志记录和警报功能有助于检测网络安全事件、尝试入侵和异常使用模式。 此外, 在单独的安全日志服务器上存储事件日志会增加攻击者删除日志记录信息以破坏目标网络入侵的证据的难度。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>澳大利亚网络安全中心 (ACSC) 要求

ACSC 信息安全手册 (ISM) 中定义了英联邦系统的整体安全要求。 为了帮助用户在 Azure 中满足这些要求, *ACSC 使用者指南– MICROSOFT AZURE 受保护*和*ACSC 认证报告– Microsoft Azure*发布详细说明了以下相关特定要求日志记录、审核和可见性:

1. 为了降低使用共享底层云资源引起的风险, 英的实体必须选择加入 Microsoft Azure 提供的功能, 包括 Azure 安全中心、Azure Monitor、Azure 策略和 Azure 顾问, 以帮助实体执行实时监视其 Azure 工作负荷

2. ACSC 还建议联邦实体将所有强制的安全日志转发到 ACSC, 以实现澳大利亚政府的所有监视

3. 为了帮助缓解风险, 可以在其 Azure 订阅中配置英联邦实体:

    * 启用 Azure 安全中心
    * 升级到标准层
    * 启用 Microsoft Monitoring Agent 自动预配到受支持的 Azure Vm
    * 在安全中心仪表板上定期查看、prioritise 和缓解安全建议和警报

4. 政府实体必须启用从其 Azure 订阅到 ACSC 的日志和事件转发, 以便向 ACSC 提供与本指南不相容的可见性。 Azure 事件中心提供将外部日志流式处理到英 ACSC 或由英的实体拥有的内部系统的功能

5. 英联邦实体应将在 Azure 中启用的日志记录与 ISM 中指定的要求进行对齐

6. Microsoft 会在 Azure 中保留90天的日志。 客户实体必须实现日志存档体制, 以确保可以在 NAA AFDA 下的七年中保留日志。

7. 基于本地或基于 Azure 的安全信息和事件管理 (SIEM) 功能的无人管理实体还可以将日志转发到这些系统

8. 英联邦实体应为网络安全组 (Nsg) 和虚拟机实现网络观察程序流日志。 这些日志应存储在仅包含安全日志的专用存储帐户中, 并且应使用基于角色的访问控制来保护对存储帐户的访问

9. 英联邦实体必须实现 ACSC 使用者指南, 以确保 Azure 工作负载满足 ISM 进行日志记录和监视的意图。 英联邦实体还必须选择加入 Azure 功能, 以帮助 ACSC 接收与澳大利亚政府使用 Azure 关联的实时监控、警报和日志

## <a name="architecture"></a>体系结构

若要自信地了解进入和离开 Azure 环境的网络流量, 必须在正确的组件集上启用必要的日志记录。 这可确保环境的完整可见性并提供必要的数据来进行分析。

![Azure 监视体系结构](media/visibility.png)

## <a name="components"></a>组件

上面所示的体系结构由离散组件构成, 这些组件提供日志源、日志收集、日志保留、日志分析或事件响应的功能。 此体系结构包括单个组件, 这些组件通常涉及到可通过 internet 访问的 Azure 部署。

|函数|组件|
|---|---|
|日志源|<ul><li>应用程序网关</li><li>VPN 网关</li><li>Azure 防火墙</li><li>网络虚拟设备</li><li>Azure 负载均衡器</li><li>虚拟机</li><li>域命名系统 (DNS) 服务器</li><li>Syslog 和/或日志收集服务器</li><li>NSG</li><li>Azure 活动日志</li><li>Azure 诊断日志</li><li>Azure Policy</li></ul>|
|日志收集|<ul><li>事件中心</li><li>网络观察程序</li><li>Log Analytics</li></ul>|
|日志保留期|<ul><li>Azure 存储</li></ul>|
|Log Analytics|<ul><li>Azure 安全中心 (ASC)</li><li>Azure Advisor</li><li>Log Analytics 解决方案<ul><li>流量分析</li><li>DNS Analytics (预览版)</li><li>Activity Log Analytics</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|事件响应|<ul><li>Azure 警报</li><li>Azure 自动化</li></ul>|
|

该体系结构的工作原理是首先从所需的源生成日志, 然后将其收集到集中式存储库。 收集日志后, 可以执行以下操作:

* 由 Azure analysis services 用于了解,
* 转到外部系统, 或
* 存档到存储以进行长期保留。

若要响应由分析工具确定的关键事件或事件, 可以配置警报, 并开发自动化, 以采取必要的主动管理和响应操作。

## <a name="general-guidance"></a>通用指南

在实现本文中列出的组件时, 以下常规指南适用:

* 验证服务的区域可用性, 确保所有数据都保留在授权位置, 并将其部署到 AU Central 或 AU 中心 2, 作为受保护工作负荷的第一个首选项

* 请参阅*AZURE ACSC 认证报告-受保护的 2018*发布, 了解各个服务的认证状态, 并根据*ACSC 使用者指南在未包括在报告中的任何相关组件上执行自我评估–受保护 Microsoft Azure*

* 对于本文中未提到的组件, 无需的实体应遵循有关生成、捕获、分析和保留日志的相关原则

* 识别并 prioritise 对高价值系统以及 Azure 中托管的系统的所有网络入口和出口点进行日志记录、审核和查看

* 合并日志, 并将日志工具 (例如存储帐户、Log Analytics 工作区和事件中心) 的实例数降至最低

* 通过基于角色的访问控制限制管理权限

* 使用多重身份验证 (MFA) 在 Azure 中管理或配置资源

* 当跨多个订阅 centralising 日志收集时, 请确保管理员在每个订阅中具有必要的权限

* 为虚拟机 (包括网络虚拟设备 (Nva)、日志收集服务器和 DNS 服务器) 确保网络连接和任何所需的代理配置, 以连接到必要的 Azure 服务 (如 Log Analytics 工作区、事件中心)和存储

* 将 Microsoft Monitoring Agent (MMA) 配置为利用 TLS 版本1。2

* 使用 Azure 策略来监视和强制实施符合性要求

* 在所有数据存储库 (例如存储和数据库) 上强制加密

* 使用本地冗余存储 (LRS) 和快照实现存储帐户和相关数据的可用性

* 考虑异地冗余存储 (GRS) 或异地存储, 以与灾难恢复策略一致

|Resource|URL|
|---|---|
|澳大利亚法规和政策符合性文档|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 产品-澳大利亚地区和非区域|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 澳大利亚中部、澳大利亚中部-2、澳大利亚-东部、澳大利亚-东南部](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Microsoft Azure 安全和审核日志管理白皮书|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Microsoft Monitoring Agent 配置|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>组件指南

本部分提供有关每个组件及其在总体日志记录、审核和可见性体系结构中的作用的信息。 提供了其他链接来访问有用的资源, 例如参考文档、指南和教程。

## <a name="log-sources"></a>日志源

在进行任何分析、警报或报告之前, 必须生成必需的日志。 Azure 日志分为控制/管理日志、数据平面日志和处理事件。

|类型|描述|
|---|---|
|控制/管理日志|提供有关 Azure 资源管理器操作的信息|
|数据平面日志|提供有关作为 Azure 资源使用的一部分引发的事件的信息, 例如虚拟机中的日志和通过 Azure Monitor 提供的诊断日志|
|处理的事件|提供有关已由 Azure 处理的并非事件/警报的信息, 例如 Azure 安全中心已处理并并非订阅以提供安全警报|
|

### <a name="application-gateway"></a>应用程序网关

Azure 应用程序网关是 Azure 环境中可能存在的入口点之一, 因此你需要捕获与 web 应用程序通信的传入连接相关的信息。 应用程序网关可提供与 web 应用程序使用相关的重要信息, 并帮助检测网络安全事件。 应用程序网关会将元数据发送到 Azure Monitor 中的活动日志和诊断日志中, 以便可以在 Log Analytics 中能否或将其分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|应用程序网关文档|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|应用程序网关快速入门指南|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN 网关

VPN 网关是将各种通信引入到 Azure 环境中的潜在入口点, 如到本地环境和管理流量的连接。 登录 VPN 网关可为与 Azure 环境建立的连接提供见解和跟踪性。 日志记录可提供审核和分析, 还有助于检测或调查恶意或异常连接。 VPN 网关日志将发送到 Azure Monitor 活动日志中, 这些日志可以在 Log Analytics 中能否或分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|VPN 网关文档|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|澳大利亚政府特定的 VPN 网关指南|[Azure VPN 网关配置](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure 防火墙

Azure 防火墙在 Azure 环境中提供受控的出口点, 生成的日志 (包括有关已尝试和成功的出站连接的信息) 是日志记录策略中的重要元素。 这些日志可以验证系统是否按设计方式运行, 并有助于检测恶意代码或尝试连接到未经授权外部系统的执行组件。 Azure 防火墙将日志写入到活动日志和诊断日志中, Azure Monitor 可以在 Log Analytics 中使用它, 或将其分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|Azure 防火墙文档|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|教程：监视 Azure 防火墙日志和指标|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>网络虚拟设备 (NVA)

Nva 可用于对 Azure 中本机可用的安全功能进行补充。 在 Nva 上生成的日志在检测网络安全事件时可能是宝贵的资源, 并且是总体日志记录、审核和可见性策略的重要组成部分。 若要从 Nva 捕获日志, 请利用 Microsoft Monitoring Agent (MMA)。 对于不支持 MMA 安装的 Nva, 请考虑使用 Syslog 或其他日志收集服务器中继日志。

|资源|链接|
|---|---|
|网络虚拟设备概述|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|NVA 文档|请参阅供应商文档, 以获得 Azure 中相关 NVA 的实现|
|

### <a name="azure-load-balancer"></a>Azure 负载均衡器

Azure 负载均衡器日志用于获取有关连接和与在 Azure 中部署的系统相关的使用情况的有用信息。 这可以用于运行状况和可用性监视, 还可以在获取通信流量的必要深入了解并检测恶意或异常流量模式时形成另一个关键组成部分。 Azure 负载均衡器记录到 Azure Monitor 中的活动日志和诊断日志, 可将其能否 Log Analytics 或分发到事件中心或存储帐户。

|资源|链接|
|---|---|
|Azure 负载均衡器文档|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|标准负载均衡器的指标和运行状况诊断|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>虚拟机

虚拟机是发送和接收网络通信、处理数据和提供服务的终结点。 由于虚拟机可以托管数据或关键系统服务, 因此确保它们正常运行并检测网络安全事件可能至关重要。 虚拟机收集各种事件和审核日志, 这些日志可跟踪系统的操作以及对该系统执行的操作。 可以通过 Azure 安全中心和适用的 Log Analytics 解决方案将虚拟机上收集的日志转发到 Log Analytics 的工作 Microsoft Monitoring Agent 区。 虚拟机还可以直接或通过日志收集服务器直接与 Azure 事件中心或 SIEM 进行集成。

|资源|链接|
|---|---|
|虚拟机|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|从虚拟机收集数据|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|将虚拟机日志流式传输到事件中心|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>域名服务 (DNS) 服务器

DNS 服务器日志提供与系统在内部或外部尝试访问的服务相关的关键信息。 捕获 DNS 日志有助于确定网络安全事件, 并提供事件类型和可能受影响的系统的见解。 可以在 DNS 服务器上使用 Microsoft 管理代理 (MMA) 将日志转发到 Log Analytics 以便在 DNS Analytics (预览版) 中使用。

|资源|链接|
|---|---|
|虚拟网络的 Azure 名称解析|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog 和日志收集服务器

若要从网络虚拟设备接收日志或从其他系统中使用自定义安全日志以供在 SIEM 中使用, 可以在 Azure Vnet 中部署专用服务器。 可以在 Syslog 服务器上收集 syslog 日志, 并中继到 Log Analytics 进行分析。 日志收集服务器是集中式监视系统或 Siem 使用的任何日志聚合和分发功能的通用术语。 这些功能可用于简化网络体系结构和安全性, 以及在分发到集中式功能之前筛选和聚合日志。

|资源|链接|
|---|---|
|Log Analytics 中的 Syslog 数据源|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|日志收集服务器|有关监视和 SIEM 体系结构的详细信息, 请参阅供应商文档|
|

### <a name="network-security-groups-nsgs"></a>网络安全组 (NSG)

Nsg 控制进出 Azure 中的虚拟网络的流量。 Nsg 应用允许或拒绝的流量的规则, 其中包括 Azure 中的流量以及 Azure 和外部网络 (如本地或 Internet) 之间的流量。 Nsg 应用于虚拟网络中的子网或单个网络接口。 若要捕获有关在 Azure 中进入和退出系统的流量的信息, 可通过网络观察程序 NSG Flow 日志功能启用 NSG 日志。 这些日志用于形成系统标准操作的基准, 是流量分析的数据源, 它可详细了解 Azure 中托管的系统的流量模式。

|资源|链接|
|---|---|
|网络安全组文档|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|针对网络安全组进行流日志记录简介|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|教程：使用 Azure 门户记录进出虚拟机的网络流量|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Azure 活动日志

Azure 活动日志是 Azure Monitor 的一部分, 它是一种订阅日志, 可用于深入了解 Azure 中发生的订阅级别事件。 活动日志可帮助确定对订阅中的资源执行的任何写入操作 (PUT、POST、DELETE) 的 "操作内容、操作人员和操作时间"。 活动日志对于跟踪 Azure 环境中所做的配置更改至关重要。 Azure 活动日志可在 Log Analytics 解决方案中自动使用, 并且可以发送到事件中心或 Azure 存储进行处理或保留。

|资源|链接|
|---|---|
|Azure 活动日志文档|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|将 Azure 活动日志流式传输到事件中心|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Azure 诊断日志

Azure Monitor 诊断日志是由 Azure 服务发出的日志, 可提供有关该服务的操作的丰富、频繁的数据。 诊断日志可深入了解资源在详细级别的操作, 并可用于各种要求, 例如审核或故障排除。 Azure 诊断日志可在 Log Analytics 解决方案中自动使用, 并且可以发送到事件中心或 Azure 存储进行处理或保留。

|资源|链接|
|---|---|
|Azure 诊断日志文档|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|诊断日志的支持服务|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure 策略强制实施有关如何部署资源的规则, 例如类型、位置和配置。 可以配置 Azure 策略, 以确保仅当资源符合要求时才能部署这些资源。 Azure 策略是维护 Azure 环境完整性的核心组件。 与 Azure 策略相关的事件会记录到 Azure 活动日志中, 并可自动用于 Log Analytics 解决方案中, 也可发送到事件中心或 Azure 存储进行处理或保留。

|资源|链接|
|---|---|
|Azure Policy 文档|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|使用 azure 蓝图利用 Azure 策略和资源管理器模板|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>日志收集

从多个日志源生成后, 需要将日志存储在集中式位置, 以便进行日常访问和分析。 Azure 为日志收集提供多种方法和选项, 可以根据日志类型和要求能否。

### <a name="event-hubs"></a>事件中心

事件中心的用途是聚合各种源的日志数据以进行分发。 在事件中心, 可将日志数据发送到 SIEM, 并将其发送到 ACSC 以实现符合性, 并保存到存储以进行长期保留。

|资源|链接|
|---|---|
|事件中心文档|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|事件中心和外部工具指南|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics 是 Azure Monitor 的一部分, 用于日志分析。 Log Analytics 使用工作区作为存储机制, 其中日志数据可用于在 Azure 中提供的各种分析工具和解决方案。 Log Analytics 通过 Microsoft Monitoring Agent 直接与各种 Azure 组件以及虚拟机集成。

|资源|链接|
|---|---|
|Log Analytics 文档|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|教程：分析 Log Analytics 中的数据|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>网络观察程序

ACSC 建议使用网络观察程序, 以帮助了解并捕获 Azure 订阅中的网络流量。 NSG Flow 日志提供 Log Analytics 中的流量分析解决方案的输入, 它通过 Azure 以本地方式提供更高的可见性、分析和报告。 网络观察程序还可以直接从 Azure 门户提供数据包捕获功能, 而无需登录到虚拟机。 数据包捕获允许您创建数据包捕获会话, 以跟踪进出虚拟机的流量。

|资源|链接|
|---|---|
|网络观察程序|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|数据包捕获概述|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>日志保留期

对于澳大利亚政府组织, 在 Azure 中捕获的日志必须按照澳大利亚[管理功能处置机构 (AFDA)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx)的国家存档进行保留, 这将保留日志的时间最长为七年。

|日志位置|保持期|
|---|---|
|Azure 活动日志|最长90天|
|Log Analytics 工作区|最多两年|
|事件中心|最长7天|
|

你需要确保正确存档日志以遵守 AFDA 和其他法律要求。

### <a name="azure-storage"></a>Azure 存储

Azure 存储是用于长期保留在 Azure 中的日志的存储库。 Azure 存储空间可用于将日志从 Azure 存档, 包括事件中心、Azure 活动日志和 Azure 诊断日志。 存储中数据的保留期可以设置为零, 也可以指定为天数。 保留期为0天表示永久保留日志, 否则该值可以是介于1到2147483647之间的任意天数。

|资源|链接|
|---|---|
|Azure 存储文档|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|教程：使用 Azure 存储将 Azure 指标和日志数据存档|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Azure 存储复制|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|创建 Blob 的快照|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Log Analytics

一旦生成并存储在集中式位置, 就必须并非日志, 以帮助检测尝试或成功的安全事件。 如果检测到安全事件, 代理需要能够响应这些事件, 并跟踪、包含并修正任何威胁。

### <a name="azure-security-center-asc"></a>Azure 安全中心 (ASC)

Azure 安全中心提供统一的安全管理和高级威胁防护。 Azure 安全中心可跨工作负荷应用安全策略、限制对威胁的暴露以及检测和响应攻击。 Azure 安全中心跨各种 Azure 组件提供仪表板和分析。 使用 Azure 安全中心是在 ACSC 使用者指南中指定的要求。

|资源|链接|
|---|---|
|Azure 安全中心文档|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|快速入门：将 Azure 订阅加入安全中心标准层|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>流量分析

流量分析是一种基于云的解决方案, 可用于查看 Azure 中的用户和应用程序活动。 流量分析分析网络观察程序 NSG 流日志, 以便深入了解 Azure 中的流量流。 流量分析用于提供与跨虚拟网络查看的网络通信相关的仪表板、报告、分析和事件响应功能。 流量分析提供了大量见解, 有助于识别和解决网络安全事件。

|资源|链接|
|---|---|
|流量分析文档|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure 顾问分析资源配置和其他数据, 以帮助提高资源的性能、安全性和高可用性, 同时寻找机会减少 Azure 总支出。 ACSC 建议使用 azure Advisor, 并提供有关 Azure 环境配置的易于访问的详细建议。

|资源|链接|
|---|---|
|Azure 顾问文档|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Azure 顾问入门|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (预览版)

DNS Analytics 是一种 Log Analytics 解决方案, 用于收集、分析和关联 Windows DNS 分析和审核日志以及其他相关数据。 DNS Analytics 标识尝试解析恶意域名、过时资源记录、经常查询的域名和请求频繁 DNS 客户端的客户端。 DNS Analytics 还可以深入了解 DNS 服务器上的请求负载和动态 DNS 注册失败。 DNS Analytics 用于提供与在 Azure 环境中进行的 DNS 查询相关的仪表板、报告、分析和事件响应功能。 DNS Analytics 提供了大量见解, 有助于识别和解决网络安全事件。

|资源|链接|
|---|---|
|DNS Analytics 文档|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Activity Log Analytics

Activity Log Analytics 是一种 Log Analytics 的解决方案, 可帮助分析和搜索跨多个 Azure 订阅的 Azure 活动日志。 Activity Log Analytics 用于提供集中式仪表板、报告、分析和事件响应功能, 这些功能与在整个 Azure 环境中对资源执行的操作相关。 Activity Log Analytics 可以帮助进行审核和调查。

|资源|链接|
|---|---|
|收集和分析 Log Analytics 中的 Azure 活动日志|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>安全信息和事件管理 (SIEM)

SIEM 是一种系统, 它提供了集中式存储、审核和分析安全日志, 并具有定义的机制用于引入各种日志数据, 以及用于分析、报告和事件检测和响应的智能工具。 你可以使用包含 Azure 日志记录信息的 SIEM 功能来补充在 Azure 中以本机方式提供的安全功能。 根据特定要求, 可利用的实体可以在 Azure、本地或作为服务型软件 (SaaS) 功能的虚拟机上托管的 SIEM。

|资源|链接|
|---|---|
|Azure Sentinel (预览版)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM 文档|请参阅供应商文档, 了解 SIEM 体系结构和指南|
|使用 Azure Monitor 与 SIEM 工具集成|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>澳大利亚网络安全中心

澳大利亚网络安全中心 (ACSC) 是澳大利亚政府在国家网络安全方面的领先者。 它将跨澳大利亚政府的网络安全功能汇集在一起, 以提高澳大利亚社区的网络复原能力, 并在数字时代提供对澳大利亚经济和社会兴盛的支持。 ACSC 建议英联邦实体将所有强制系统生成的日志文件、事件和日志转发到 ACSC, 以供整个澳大利亚政府监视。

|资源|链接|
|---|---|
|澳大利亚网络安全中心网站|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>事件响应

生成相应的日志, 将其收集到集中式存储库, 执行分析可以提高系统的理解, 并提供检测网络安全事件的机制。 检测到事件或事件后, 下一步是响应这些事件, 并执行操作以维护系统运行状况, 并保护服务和数据免遭损害。 Azure 提供了一系列服务来有效地响应发生的任何事件。

### <a name="azure-alerts"></a>Azure 警报

Azure 警报可用于通知支持人员和安全人员响应特定事件。 这允许一个英的实体主动响应本文中列出的 analysis services 所引发的相关事件。

|资源|链接|
|---|---|
|Microsoft Azure 中的警报概述|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|管理和响应 Azure 安全中心的安全警报|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|借助 Azure Monitor 警报对事件做出响应|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure 自动化

Azure 自动化可让你在响应事件时触发操作, 以实现可触发的操作。 这可能是在虚拟机上启动数据包捕获、运行工作流、停止或启动虚拟机或服务, 或者其他一系列任务。 自动化可让你无需手动干预即可快速响应警报, 从而减少事件或事件的响应时间和严重性。

|资源|链接|
|---|---|
|Azure 自动化文档|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|操作方法指南:使用警报触发 Azure 自动化 Runbook|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>后续步骤

有关安全管理 Azure 中的网关环境的详细信息, 请查看有关[网关安全远程管理](gateway-secure-remote-administration.md)的文章。
