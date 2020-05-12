---
title: Azure Monitor 常见问题 |Microsoft Docs
description: 有关 Azure Monitor 的常见问题的解答。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/11/2020
ms.openlocfilehash: 471ccddd31fd6c9f332bdaa8ea76b7bda25ac191
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117778"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor 常见问题

此 Microsoft FAQ 列出了有关 Azure Monitor 常见问题的列表。

## <a name="general"></a>常规

### <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
[Azure Monitor](overview.md)是 azure 中的一项服务，它为 azure、其他云环境或本地中的应用程序和服务提供性能和可用性监视。 Azure Monitor 将多个源中的数据收集到一个公共数据平台，可以在其中分析趋势和异常。 Azure Monitor 中的丰富功能可帮助你快速识别和响应可能影响你的应用程序的关键情况。

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Monitor、Log Analytics 和 Application Insights 之间有何区别？
2018年9月，Microsoft 将 Azure Monitor、Log Analytics 和 Application Insights 组合到一个服务中，以便为应用程序及其依赖的组件提供强大的端到端监视功能。 Log Analytics 和 Application Insights 中的功能未发生更改，但某些功能已更名到 Azure Monitor，以便更好地反映其新范围。 Log Analytics 的日志数据引擎和查询语言现在称为 Azure Monitor 日志。 请参阅[Azure Monitor 术语更新](terminology.md)。

### <a name="what-does-azure-monitor-cost"></a>Azure Monitor 费用是什么？
自动启用的 Azure Monitor 功能，如指标和活动日志的收集，免费提供。 存在与其他功能（如日志查询和警报）相关的成本。 请参阅[Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)获取详细定价信息。

### <a name="how-do-i-enable-azure-monitor"></a>如何实现启用 Azure Monitor？
当你创建新的 Azure 订阅，并且自动收集[活动日志](platform/activity-logs-overview.md)和平台[指标](platform/data-platform-metrics.md)时，会启用 Azure Monitor。 创建[诊断设置](platform/diagnostic-settings.md)以收集有关 Azure 资源操作的更多详细信息，并添加[监视解决方案](insights/solutions.md)和[见解](insights/insights-overview.md)，为特定服务的收集数据提供额外分析。 

### <a name="how-do-i-access-azure-monitor"></a>如何实现访问 Azure Monitor？
从 Azure 门户中的 "**监视**" 菜单访问所有 Azure Monitor 功能和数据。 对于不同的 Azure 服务，菜单的 "**监视**" 部分提供对相同工具的访问权限，并将数据筛选到特定资源。 使用 CLI、PowerShell 和 REST API 的各种情况下，也可以访问 Azure Monitor 数据。

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>是否有 Azure Monitor 的本地版本？
不是。 Azure Monitor 是一种可缩放的云服务，可处理和存储大量数据，不过 Azure Monitor 可以监视本地和其他云中的资源。

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>能否 Azure Monitor 监视本地资源？
是的，除了收集 Azure 资源的监视数据以外，Azure Monitor 还可以从其他云和本地的虚拟机和应用程序中收集数据。 请参阅[Azure Monitor 的监视数据源](platform/data-sources.md)。

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor 是否与 System Center Operations Manager 集成？
可以将现有 System Center Operations Manager 管理组连接到 Azure Monitor，以便将代理中的数据收集到 Azure Monitor 日志中。 这使你可以使用日志查询和解决方案来分析从代理收集的数据。 你还可以配置现有 System Center Operations Manager 代理，以将数据直接发送到 Azure Monitor。 请参阅[将 Operations Manager 连接到 Azure Monitor](platform/om-agents.md)。

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Monitor 使用哪些 IP 地址？
请参阅[Application Insights 和 Log Analytics 使用的 ip 地址](app/ip-addresses.md)，以获取代理和其他外部资源访问 Azure Monitor 所需的 ip 地址和端口的列表。 

## <a name="monitoring-data"></a>监视数据

### <a name="where-does-azure-monitor-get-its-data"></a>Azure Monitor 从何处获取数据？
Azure Monitor 从各种来源（包括来自 Azure 平台的日志和指标，以及在虚拟机上运行的自定义应用程序和代理）收集数据。 其他服务（例如 Azure 安全中心和网络观察程序）会将数据收集到 Log Analytics 工作区中，以便能够使用 Azure Monitor 数据进行分析。 你还可以使用日志或指标的 REST API 将自定义数据发送到 Azure Monitor。 请参阅[Azure Monitor 的监视数据源](platform/data-sources.md)。

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Monitor 收集哪些数据？ 
Azure Monitor 将各种源中的数据收集到[日志](platform/data-platform-logs.md)或[指标](platform/data-platform-metrics.md)中。 每种数据类型都有其自身的相对优点，每种数据都支持 Azure Monitor 中的一组特定功能。 每个 Azure 订阅都有一个指标数据库，可以根据需要创建多个 Log Analytics 工作区来收集日志。 请参阅[Azure Monitor 数据平台](platform/data-platform.md)。

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>是否有可在 Azure Monitor 中收集的最大数据量？
可以收集的指标数据量没有限制，但该数据的存储时间最长为93天。 请参阅[指标保留](platform/data-platform-metrics.md#retention-of-metrics)。 可以收集的日志数据量没有限制，但可能受到您为 Log Analytics 工作区选择的定价层的影响。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>如何实现访问 Azure Monitor 收集的数据？
见解和解决方案为处理存储在 Azure Monitor 中的数据提供了自定义体验。 您可以使用以 Kusto 查询语言（KQL）编写的日志查询直接处理日志数据。 在 Azure 门户中，可以编写和运行查询，并使用 Log Analytics 以交互方式分析数据。 用指标资源管理器分析 Azure 门户中的度量值。 请参阅[在 Azure Monitor 中分析日志数据](log-query/log-query-overview.md)和[Azure 指标资源管理器](platform/metrics-getting-started.md)入门。

## <a name="solutions-and-insights"></a>解决方案和见解

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Monitor 中有哪些见解？
Insights 提供针对特定 Azure 服务的自定义监视体验。 它们使用与 Azure Monitor 中的其他功能相同的指标和日志，但可能会收集更多的数据，并在 Azure 门户中提供独一无二的体验。 请参阅[Azure Monitor 中的见解](insights/insights-overview.md)。

若要查看 Azure 门户中的见解，请参阅 "**监视**" 菜单或服务菜单的 "**监视**" 部分的 "**见解**" 部分。

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Monitor 中的解决方案是什么？
监视解决方案是一组打包的逻辑，用于基于 Azure Monitor 功能监视特定的应用程序或服务。 它们 Azure Monitor 收集日志数据，并使用 Azure 门户中的常见体验为分析提供日志查询和视图。 请参阅[Azure Monitor 中的监视解决方案](insights/solutions.md)。

若要查看 Azure 门户中的解决方案，请单击 "**监视**" 菜单的 "**见解**" 部分中的 "**更多**"。 单击 "**添加**"，将其他解决方案添加到工作区。

## <a name="logs"></a>日志

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Monitor 日志和 Azure 数据资源管理器之间的区别是什么？
Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure Monitor 日志建立在 Azure 数据资源管理器之上，并使用相同的 Kusto 查询语言（KQL），但有一些细微的差异。 请参阅[Azure Monitor 日志查询语言差异](log-query/data-explorer-difference.md)。

### <a name="how-do-i-retrieve-log-data"></a>如何实现检索日志数据？
所有数据都是使用使用 Kusto 查询语言（KQL）编写的日志查询从 Log Analytics 工作区中检索的。 您可以编写自己的查询，或使用包括特定应用程序或服务的日志查询的解决方案和见解。 请参阅[Azure Monitor 中的日志查询概述](log-query/log-query-overview.md)。

### <a name="what-is-a-log-analytics-workspace"></a>Log Analytics 工作区是什么？
Azure Monitor 收集的所有日志数据都存储在 Log Analytics 工作区中。 工作区实质上是从各种源收集日志数据的容器。 您可以为所有监视数据使用单个 Log Analytics 工作区，也可以有多个工作区的要求。 请参阅[设计 Azure Monitor 日志部署](platform/design-logs-deployment.md)。

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>是否可以将现有 Log Analytics 工作区移动到另一个 Azure 订阅？
你可以在资源组或订阅之间移动工作区，但不能在不同的区域之间移动。 请参阅[将 Log Analytics 工作区移动到不同的订阅或资源组](platform/move-workspace.md)。

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>为什么看不到 Log Analytics 中的查询资源管理器和保存按钮？

当[查询范围](log-query/scope.md)设置为特定资源时，"**查询资源管理器**"、"**保存**" 和 "**新建警报规则**" 按钮不可用。 若要创建警报，请保存或加载查询，Log Analytics 必须作用于工作区。 若要打开工作区上下文中 Log Analytics，请从 " **Azure Monitor** " 菜单中选择 "**日志**"。 选择的是上次使用的工作区，但可以选择任何其他的工作区。 请参阅[Azure Monitor 中的日志查询范围和时间范围 Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>为什么会收到错误： "注册此订阅的资源提供程序 ' Microsoft Insights ' 以便在从 VM 打开 Log Analytics 时启用此查询？ 
将自动注册许多资源提供程序，但你可能需要手动注册某些资源提供程序。 注册的作用域始终是订阅。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>从 VM 打开 Log Analytics 时，为什么我不会收到访问错误消息？ 
若要查看 VM 日志，需获得存储 VM 日志的工作区的读取权限。 在这些示例中，管理员必须在 Azure 中为你授予相关权限。

## <a name="alerts"></a>警报

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Monitor 中的警报是什么？
在监视数据中发现重要情况时，警报会以主动的方式通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有多种类型的警报：

- 指标指标值超过阈值。
- 日志查询-日志查询的结果匹配定义的条件。
- 活动日志-活动日志事件与定义的条件匹配。
- Web 测试-可用性测试匹配定义的条件的结果。


请参阅[Microsoft Azure 中的警报概述](platform/alerts-overview.md)。


### <a name="what-is-an-action-group"></a>操作组是什么？
操作组是可以由警报触发的通知和操作的集合。 多个警报可使用单个操作组，使你能够利用常见的通知和操作集。 请参阅[在 Azure 门户中创建和管理操作组](platform/action-groups.md)。


### <a name="what-is-an-action-rule"></a>什么是操作规则？
操作规则允许您修改符合特定条件的一组警报的行为。 这允许您执行在维护时段内禁用警报操作等要求。 你还可以将操作组应用于一组警报，而不是将其直接应用于警报规则。 请参阅[操作规则](platform/alerts-action-rules.md)。

## <a name="agents"></a>代理

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor 需要代理吗？
代理只需要从操作系统和虚拟机中的工作负荷收集数据。 虚拟机可以位于 Azure、其他云环境或本地。 请参阅[Azure Monitor 代理概述](platform/agents-overview.md)。


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Monitor 代理之间有何区别？
Azure 诊断扩展适用于 Azure 虚拟机，可将数据收集到 Azure Monitor 指标、Azure 存储和 Azure 事件中心。 Log Analytics 代理适用于 Azure 中的虚拟机、其他云环境或本地，并将数据收集到 Azure Monitor 日志。 依赖关系代理需要 Log Analytics 代理，并收集进程详细信息和依赖项。 请参阅[Azure Monitor 代理概述](platform/agents-overview.md)。


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>代理通讯是否使用 ExpressRoute 连接？
要 Azure Monitor 的流量使用 Microsoft 对等互连 ExpressRoute 线路。 有关 ExpressRoute 流量的不同类型的说明，请参阅[expressroute 文档](../expressroute/expressroute-faqs.md#supported-services)。 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>如何确认 Log Analytics 代理是否能够与 Azure Monitor 通信？
在代理计算机上的 "控制面板" 中，选择 "**安全 & 设置**"， **Microsoft Monitoring Agent** "。 在 " **Azure Log Analytics （OMS）** " 选项卡下，绿色复选标记图标确认代理能够与 Azure Monitor 通信。 黄色警告图标表示代理存在问题。 一个常见原因是**Microsoft Monitoring Agent**服务已停止。 请使用服务控制管理器重启该服务。

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>如何实现阻止 Log Analytics 代理与 Azure Monitor 通信？
对于连接到 Log Analytics 的代理，请打开 "控制面板"，然后选择 "**安全 & 设置**" 和 " **Microsoft Monitoring Agent**"。 在 " **Azure Log Analytics （OMS）** " 选项卡下，删除列出的所有工作区。 在 System Center Operations Manager 中，从 Log Analytics 托管的计算机列表中删除该计算机。 Operations Manager 会更新该代理的配置以便不再向 Log Analytics 进行报告。 

### <a name="how-much-data-is-sent-per-agent"></a>每个代理发送多少数据？
每个代理发送的数据量取决于：

* 已启用的解决方案
* 正在收集的日志和性能计数器的数量
* 日志中的数据量

有关详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)。

对于能够运行 WireData 代理的计算机，可以使用以下查询了解正在发送的数据量：

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>将数据发送到 Azure Monitor 时，Microsoft 管理代理（MMA）使用多少网络带宽？
带宽是已发送数据量的一个函数。 数据通过网络发送时会被压缩。


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>当 Log Analytics 代理中的数据收集停止时，如何收到通知？

当数据收集停止时，可以使用[新建日志警报](platform/alerts-metric.md)中所述的步骤收到通知。 将以下设置用于警报规则：

- **定义警报条件**：将 Log Analytics 工作区指定为资源目标。
- **警报条件** 
   - **信号名称**：*自定义日志搜索*
   - **搜索查询**：`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **警报逻辑**：**基于***结果数*，**条件***大于*，**阈值** *0*
   - **计算依据**：**周期（分钟）** *30*，**频率（分钟）** *10*
- **定义警报详细信息** 
   - **名称**：*数据收集已停止*
   - **严重性**：*警告*

指定一个现有的或新的[操作组](platform/action-groups.md)，以便当日志警报符合条件时，如果检测信号丢失超过15分钟，会收到通知。


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Monitor 代理的防火墙要求是什么？
有关防火墙要求的详细信息，请参阅[网络防火墙要求](platform/log-analytics-agent.md#network-requirements)。


## <a name="visualizations"></a>可视化效果

### <a name="why-cant-i-see-view-designer"></a>为什么看不到视图设计器？

视图设计器仅适用于在 Log Analytics 工作区中分配有 "参与者" 权限或更高权限的用户。

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>配置问题
*我在设置以下内容时遇到问题：*

* [.NET 应用](app/asp-net-troubleshoot-no-data.md)
* [监视已经在运行的应用](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 诊断](platform/diagnostics-extension-to-application-insights.md)
* [Java web 应用](app/java-troubleshoot.md)

*我从服务器中获取了没有数据：*

* [设置防火墙异常](app/ip-addresses.md)
* [设置 ASP.NET 服务器](app/monitor-performance-live-website-now.md)
* [设置 Java 服务器](app/java-agent.md)

*应该部署多少个 Application Insights？：*

* [如何设计 Application Insights 部署：一个与多个 Application Insights 资源？](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>是否可以将 Application Insights 用于...？

* [Azure VM 或 Azure 虚拟机规模集中 IIS 服务器上的 Web 应用](app/azure-vm-vmss-apps.md)
* [IIS 服务器上的 Web 应用 - 本地或在 VM 中](app/asp-net.md)
* [Java web 应用](app/java-get-started.md)
* [Node.js 应用](app/nodejs.md)
* [Azure 上的 Web 应用](app/azure-web-apps.md)
* [Azure 上的云服务](app/cloudservices.md)
* [在 Docker 中运行的应用服务器](app/docker.md)
* [单页 Web 应用](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows 桌面应用](app/windows-desktop.md)
* [其他平台](app/platforms.md)

### <a name="is-it-free"></a>是否免费？

是的，对于实验用途。 在基本定价计划中，应用程序可以发送一些每月免费数据量。 免费的限额足以满足开发和为少量用户发布应用的需求。 可以设置上限，以防止所使用的数据量超出指定数据量。

较大的遥测量按 Gb 收费。 我们提供了一些有关如何[限制费用](app/pricing.md)的提示。

企业计划对每个 Web 服务器节点每天发送的遥测进行收费。 如果要大规模使用连续导出，此计划非常合适。

[阅读定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

### <a name="how-much-does-it-cost"></a>费用是多少？

* 在 Application Insights 资源中打开“使用情况和估算成本”页面。**** 此处提供了一个最近使用情况的图表。 如果愿意，可以设置数据上限。
* 打开 [Azure 计费边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)查看所有资源的帐单。

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights 在我的项目中修改哪些内容？
详细信息取决于项目类型。 对于 Web 应用程序：

* 将这些文件添加到项目：
  * ApplicationInsights.config
  * ai.js
* 安装以下 NuGet 包：
  * *Application Insights API* - 核心 API
  * *适用于 Web 应用程序 的 Application Insights API* - 用于从服务器发送遥测
  * *适用于 JavaScript 应用程序的 Application Insights API* - 用于从客户端发送遥测
* 这些包包括以下程序集：
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 将项目插入：
  * Web.config
  * packages.config
* （仅限新项目-如果[将 Application Insights 添加到现有项目][start]，则必须手动执行此操作。）将代码片段插入客户端和服务器代码中，以将其初始化为 Application Insights 资源 ID。 例如，在 MVC 应用程序中，代码将插入到母版页视图/共享/布局中 \_ 。 cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何从较早的 SDK 版本升级？
请参阅[发行说明](app/release-notes.md)了解对应于应用程序类型的 SDK。

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>如何更改项目要将数据发送到的 Azure 资源？
在解决方案资源管理器中，右键单击 `ApplicationInsights.config` 并选择“更新 Application Insights”****。 可在 Azure 中将数据发送到现有或新资源。 更新向导更改 ApplicationInsights.config 中的检测密钥，该密钥确定服务器 SDK 将数据发送到何处。 除非取消选中“更新全部”，否则它还将在网页中出现密钥的位置更改密钥。

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>能否 `providers('Microsoft.Insights', 'components').apiVersions[0]` 在 Azure 资源管理器部署中使用？

不建议使用此方法来填充 API 版本。 最新版本可以表示可能包含重大更改的预览版本。 即使使用较新的非预览版本，API 版本也并不总是向后兼容现有的模板，或者在某些情况下，API 版本可能不适用于所有订阅。

### <a name="what-is-status-monitor"></a>什么是状态监视器？

它是可以在 IIS Web 服务器中使用的桌面应用，用于帮助在 Web 应用中配置 Application Insights。 它不会收集遥测：不配置应用时，可以停止此监视器。 

[了解详细信息](app/monitor-performance-live-website-now.md#questions)。

### <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights 收集哪些遥测？

从服务器 Web 应用：

* HTTP 请求
* [依赖项](app/asp-net-dependencies.md)。 调用: SQL 数据库; 对外部服务的 HTTP 调用; Azure Cosmos DB, 表, blob 存储和队列。 
* [异常](app/asp-net-exceptions.md)和堆栈跟踪。
* [性能计数器](app/performance-counters.md)-如果你使用[状态监视器](app/monitor-performance-live-website-now.md)、[适用于应用服务的 AZURE 监视](app/azure-web-apps.md)、适用[于 VM 或虚拟机规模集的 azure 监视](app/azure-vm-vmss-apps.md)或[Application Insights collectd 编写器](app/java-collectd.md)。
* [自定义编码的事件和指标](app/api-custom-events-metrics.md)。
* [跟踪日志](app/asp-net-trace-logs.md)（如果配置相应的收集器）。

从[客户端网页](app/javascript.md)：

* [页面视图计数](app/usage-overview.md)
* [AJAX 调用](app/asp-net-dependencies.md)，从运行脚本发出的请求。
* 页面视图加载数据
* 用户和会话计数
* [已经过身份验证的用户 ID](app/api-custom-events-metrics.md#authenticated-users)

从其他源中（如果有配置）：

* [Azure 诊断](platform/diagnostics-extension-to-application-insights.md)
* [导入到 Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>是否可以筛选或修改某些遥测？

可以，可在服务器中编写：

* 用于在从应用发送属性前，对属性进行筛选并将其添加到所选遥测项的遥测处理器。
* 用于将属性添加到所有遥测项的遥测初始化器。

深入了解 [ASP.NET](app/api-filtering-sampling.md) 或 [Java](app/java-filter-telemetry.md)。

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>如何计算市/县/区、国家/地区和其他地理位置数据？

使用 [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 查找 Web 客户端的 IP 地址（IPv4 或 IPv6）。

* 浏览器遥测：收集发件人的 IP 地址。
* 服务器遥测：Application Insights 模块收集客户端 IP 地址。 如果设置了 `X-Forwarded-For`，则不会进行收集。
* 若要详细了解如何在中收集 IP 地址和地理位置数据 Application Insights 参阅此[文](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)。


可以配置 `ClientIpHeaderTelemetryInitializer`，从不同的标头获取 IP 地址。 例如，在某些系统中，代理、负载均衡器或 CDN 会将其移动到 `X-Originating-IP`。 [了解详细信息](https://apmtips.com/blog/2016/07/05/client-ip-address/)。

可以[使用 Power BI](app/export-power-bi.md ) 在映射中显示请求遥测。


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>数据在门户中保留多长时间？ 是否安全？
请参阅[数据保留和隐私][data]。

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>当服务器或设备失去 Azure 连接时，应用程序见解的遥测会发生什么情况？

所有 Sdk （包括 web SDK）都包含 "可靠传输" 或 "可靠传输"。 当服务器或设备失去与 Azure 的连接时，遥测数据将以[本地方式存储在文件系统](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage)（服务器 sdk）或 HTML5 会话存储（Web SDK）中。 SDK 会定期重试发送此遥测数据，直到引入服务将其视为 "过时" （48小时的日志，指标为30分钟）。 将删除过时的遥测。 在某些情况下，例如本地存储已满时，将不会重试。


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>是否可能会在遥测中发送个人数据？

如果代码发送了此类数据，则有可能。 如果堆栈跟踪中的变量包括个人数据，也可能发生这种情况。 开发团队应进行风险评估，确保妥善处理个人数据。 [深入了解数据保留和隐私](app/data-retention-privacy.md)。

**所有**八进制数的客户端 web 地址始终设置为 0，查找异地位置属性。

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>在网页源中可见我的检测密钥。 

* 这是监视解决方案的常见做法。
* 它不能用于窃取数据。
* 但可用于倾斜数据或触发警报。
* 我们未曾听说有任何客户遇到了此类问题。

可以：

* 将两个单独检测密钥（独立的 Application Insights 资源）分别用于客户端和服务器数据。 或
* 编写一个在服务器中运行的代理，并使 Web 客户端通过该代理发送数据。

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>如何在诊断搜索中查看 POST 数据？
我们不自动记录 POST 数据，但可以使用 TrackTrace 调用：将数据放置在消息参数中。 相比对字符串属性的限制，这具有更长的大小限制，尽管你无法对其进行筛选。

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>应使用单个 Application Insights 资源还是多个 Application Insights 资源？

对单个业务系统中的所有组件或角色使用单个资源。 对开发、测试和发布版本以及独立应用程序使用单独的资源。

* [请参阅此处的讨论](app/separate-resources.md)
* [示例 - 辅助角色和 Web 角色的云服务](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>如何动态更改检测密钥？

* [在此处讨论](app/separate-resources.md)
* [示例 - 辅助角色和 Web 角色的云服务](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>什么是用户和会话计数？

* JavaScript SDK 在 Web 客户端上设置了用于识别返回用户的用户 cookie，以及用于对活动进行分组的会话 cookie。
* 如果没有客户端脚本，可以[在服务器设置 cookie](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)。
* 如果一个真实的用户在不同的浏览器中使用站点，或者使用私密/隐身浏览，或使用不同的计算机，则会进行多次计数。
* 若要识别跨计算机和浏览器登录的用户，请添加对 [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users) 的调用。

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> 我是否已在 Application Insights 中启用所有内容？
| 你应该看到的结果 | 如何获取 | 为何需要它 |
| --- | --- | --- |
| 可用性图表 |[Web 测试](app/monitor-web-app-availability.md) |知道 Web 应用已启动 |
| 服务器应用性能：响应时间、... |[将 Application Insights 添加到项目](app/asp-net.md)或[在服务器上安装 AI 状态监视器](app/monitor-performance-live-website-now.md)（或编写自己的代码以[跟踪依赖项](app/api-custom-events-metrics.md#trackdependency)） |检测性能问题 |
| 依赖项遥测 |[在服务器上安装 AI 状态监视器](app/monitor-performance-live-website-now.md) |诊断数据库或其他外部组件问题 |
| 获取异常的堆栈跟踪 |[在代码中插入 TrackException 调用](app/asp-net-exceptions.md)（但有些会自动报告） |检测和诊断异常 |
| 搜索日志跟踪 |[添加日志记录适配器](app/asp-net-trace-logs.md) |诊断异常、性能问题 |
| 客户端使用基础知识：页面视图、会话、... |[网页中的 JavaScript 初始值设定项](app/javascript.md) |使用情况分析 |
| 客户端自定义指标 |[在网页中跟踪调用](app/api-custom-events-metrics.md) |增强用户体验 |
| 服务器自定义指标 |[在服务器中跟踪调用](app/api-custom-events-metrics.md) |商业智能 |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>为什么搜索图表和指标图表中的计数不一致？

[采样](app/sampling.md)减少了实际从应发送到门户的遥测项（请求、自定义事件等）数量。 在搜索中，看到的是实际接收到的项数。 在显示事件计数的指标图表中，看到的是发生的原始事件数。 

传输的每个项都携带一个 `itemCount` 属性，此属性显示该项表示的原始事件数量。 若要观察操作中的采样，可以在分析中运行此查询：

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>自动化

#### <a name="configuring-application-insights"></a>配置 Application Insights

可以使用 Azure 资源监视器[编写 PowerShell 脚本](app/powershell.md)：

* 创建并更新 Application Insights 资源。
* 设置定价计划。
* 获取检测密钥。
* 添加指标警报。
* 添加可用性测试。

无法设置指标资源管理器报表，或设置连续导出。

#### <a name="querying-the-telemetry"></a>查询遥测

使用 [REST API](https://dev.applicationinsights.io/) 运行 [Analytics](app/analytics.md) 查询。

### <a name="how-can-i-set-an-alert-on-an-event"></a>如何设置事件警报？

Azure 警报仅出现在指标上。 创建一个每当事件发生时都跨越值阈值的自定义指标。 然后在该指标上设置警报。 当指标在任一方向跨越阈值时，你将收到通知;直到第一次交叉时才会收到通知，而无论初始值是高还是低;始终存在几分钟的延迟。

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure Web 应用和 Application Insights 之间是否有数据传输费用？

* 如果 Azure Web 应用托管在具有 Application Insights 集合终结点的数据中心内，则不收取费用。 
* 如果主机数据中心没有集合终结点，那么应用的遥测会产生 [ Azure 传出费用](https://azure.microsoft.com/pricing/details/bandwidth/)。

这与 Application Insights 资源的托管位置无关。 它只取决于终结点的分布情况。

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>是否可以将遥测数据发送到 Application Insights 门户？

建议使用我们的 SDK 并使用 [SDK API](app/api-custom-events-metrics.md)。 为各种[平台](app/platforms.md)提供了相应的 SDK 变体。 这些 SDK 可处理缓冲、压缩、限制以及重试等。 但是，[引入架构](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema)和[终结点协议](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)是公共的。

### <a name="can-i-monitor-an-intranet-web-server"></a>是否可以监视 Intranet Web 服务器？

可以，但你需要通过防火墙例外或代理重定向来允许流量到我们的服务。
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


在[此处](app/ip-addresses.md)查看服务和 IP 地址的完整列表。

#### <a name="firewall-exception"></a>防火墙例外

允许 Web 服务器将遥测发送到我们的终结点。 

#### <a name="gateway-redirect"></a>网关重定向

通过重写配置中的终结点，将流量从服务器路由到 Intranet 上的网关。 如果配置中不存在这些“终结点”属性，则这些类将使用下面示例 ApplicationInsights.config 中显示的默认值。 

你的网关应将流量路由到我们的终结点的基址。 在你的配置中，将默认值替换为 `http://<your.gateway.address>/<relative path>`。


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>使用默认终结点的示例 ApplicationInsights.config：
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider 可从 v 2.6.0 开始使用。



#### <a name="proxy-passthrough"></a>代理透传

可以通过配置计算机级代理或应用程序级代理来实现代理透传。
有关详细信息，请参阅 dotnet 关于 [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings) 的文章。
 
 示例 Web.config：
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>是否可以在 Intranet 服务器上运行可用性 Web 测试？

我们的 [Web 测试](app/monitor-web-app-availability.md)可在遍布全球的各个接入点上运行。 可运用以下两种解决方案：

* 防火墙门 - 允许从[长且可更改的 Web 测试代理列表](app/ip-addresses.md)中请求自己的服务器。
* 编写自己的代码，从 Intranet 内部向服务器发送定期请求。 可以为此运行 Visual Studio Web 测试。 测试人员可以使用 TrackAvailability() API 将结果发送到 Application Insights。

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>收集遥测数据需要多长时间？

大多数 Application Insights 数据的延迟小于 5 分钟。 有些数据可能需要更长的时间，通常是较大的日志文件。 有关详细信息，请参阅 [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)。



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor

本 Microsoft 常见问题解答列出了用于容器的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

### <a name="what-does-other-processes-represent-under-the-node-view"></a>*其他进程*在节点视图下表示什么？

**其他过程**旨在帮助你清楚地了解节点上资源使用率较高的根本原因。 这使你可以区分容器化流程与非容器化流程之间的使用情况。

这些**其他过程**有哪些？ 

这些是在节点上运行的非容器化进程。  

如何计算这一点？

**其他进程**  = CAdvisor 中的*总使用量*  - *容器化过程中的用法*

**其他过程**包括：

- 自行管理或托管的 Kubernetes 非容器化进程 

- 容器运行时进程  

- Kubelet  

- 节点上运行的系统进程 

- 节点硬件或虚拟机上运行的其他非 Kubernetes 工作负荷 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>我看不到在查询 ContainerLog 表时填充的图像和名称属性值。

对于代理版本 ciprod12042019 和更高版本，默认情况下，不会为每个日志行填充这两个属性，这样是为了最大程度地减少收集日志数据时产生的成本。 有两个可用于查询表的选项，其中包含这些属性及其值：

#### <a name="option-1"></a>选项 1 

联接其他表以在结果中包含这些属性值。

通过在 ContainerID 属性上进行联接，将查询修改为包含 ```ContainerInventory``` 表中的 Image 和 ImageTag 属性。 通过在 ContainerID 属性上进行联接，可以包含 KubepodInventory 表的 ContaineName 字段中的 Name 属性（与以前在 ```ContainerLog``` 表中显示的相同）。建议使用此选项。

下面的示例是一个示例详细查询，说明如何使用联接获取这些字段值。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>方法 2

为每个容器日志行的这些属性重新启用收集。

如果第一个选项因涉及到查询更改而不方便，则可通过在代理配置映射中启用设置 ```log_collection_settings.enrich_container_logs``` 来重新启用收集这些字段的功能，如[数据收集配置设置](insights/container-insights-agent-config.md)中所述。

> [!NOTE]
> 对于包含50个以上节点的大型群集，不建议使用第二个选项，因为它将从群集中的每个节点生成 API 服务器调用以执行此扩充。 此选项还会增加收集的每个日志行的数据大小。

### <a name="can-i-view-metrics-collected-in-grafana"></a>能否查看在 Grafana 中收集的指标？

用于容器的 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的度量值。 我们提供了一个模板。你可以从 Grafana 的[仪表板存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下载该模板来开始操作，并可通过该模板来了解如何从受监视的群集查询更多数据，以便在自定义 Grafana 仪表板中进行可视化。 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>是否可以使用用于容器的 Azure Monitor 监视 AKS-engine 群集？

用于容器的 Azure Monitor 支持监视部署到 Azure 上托管的 AKS-engine（以前称为 ACS-engine）群集的容器工作负荷。 有关为此方案启用监视所需步骤的进一步详细信息和概述，请参阅[将用于容器的 Azure Monitor 用于 AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为何 Log Analytics 工作区中不显示数据？

如果在每天的某个时间，在 Log Analytics 工作区中看不到任何数据，则可能已达到 500 MB 的默认限制或为了控制每天要收集的数据量而指定的每日上限。 如果当天的限制已达到，则数据收集将停止，并且仅在第二天恢复。 若要查看数据使用情况，并根据预期的使用模式更新为不同的定价层，请参阅[日志数据使用情况和成本](platform/manage-cost-storage.md)。 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定的容器状态是什么？

ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解决“缺少订阅注册”** 错误？

如果收到“缺少 Microsoft.OperationsManagement 的订阅注册”**** 错误，则可以通过在定义了工作区的订阅中注册资源提供程序 **Microsoft.OperationsManagement** 来解决该错误。 可以在[此处](../azure-resource-manager/templates/error-register-resource-provider.md)找到介绍如何执行此操作的文档。

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否支持启用了 RBAC 的 AKS 群集？

容器监视解决方案不支持 RBAC，但对于容器 Azure Monitor 支持。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？

默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅[容器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)GitHub 的 Azure Monitor 页。 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何将 omsagent 更新为最新发布的版本？

若要了解如何升级代理，请参阅[代理管理](insights/container-insights-manage-agent.md)。

### <a name="how-do-i-enable-multi-line-logging"></a>如何启用多行日志记录？

当前 Azure Monitor 容器不支持多行日志记录，但有一些可用的解决方法。 你可以将所有服务配置为以 JSON 格式进行写入，然后 Docker/Moby 会在单个行上写入它们。

例如，可以将你的日志包装为一个 JSON 对象，如示例 node.js 应用程序的以下示例中所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

在你查询时，此数据将类似于用于日志的 Azure Monitor 中的以下示例：

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细信息，请查看以下[GitHub 链接](https://github.com/moby/moby/issues/22920)。

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>如何解决启用实时日志时遇到的 Azure AD 错误？ 

你可能会看到以下错误：**请求中指定的回复 url 与为应用程序配置的回复 url "<应用程序 ID \> " 不匹配**。 解决方法的解决方案可以在[如何与 Azure Monitor 容器的实时查看容器数据](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)一文中找到。 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>为何在载入后不能升级群集？

如果为 AKS 群集启用用于容器的 Azure Monitor 后，删除了该群集将其数据发送到的 Log Analytics 工作区，则尝试升级该群集时，该操作将会失败。 若要解决这一问题，必须禁用监视，然后重新启用该监视，同时引用订阅中的另一个有效工作区。 当你尝试再次升级群集时，该升级操作应进行处理并成功完成。  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>需要为代理打开哪些端口和域，或将哪些端口和域加入允许列表？

请参阅 Azure、Azure 美国政府和 Azure 中国世纪互联代理所需的代理和防火墙配置信息的[网络防火墙要求](insights/container-insights-onboard.md#network-firewall-requirements)。

## <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor
本 Microsoft 常见问题解答列出了用于 VM 的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

### <a name="can-i-onboard-to-an-existing-workspace"></a>是否可以载入到现有工作区？
如果虚拟机已连接到 Log Analytics 工作区，则在载入到用于 VM 的 Azure Monitor 时，可以继续使用该工作区，前提是该工作区位于[此处](insights/vminsights-enable-overview.md#prerequisites)所列的某个受支持区域中。


### <a name="can-i-onboard-to-a-new-workspace"></a>是否可以载入到新工作区？ 
如果 VM 当前未连接到现有的 Log Analytics 工作区，则你需要创建一个新工作区来存储数据。 如果通过 Azure 门户为用于 VM 的 Azure Monitor 配置了单个 Azure VM，则系统会自动创建新的默认工作区。

如果选择使用基于脚本的方法，请在 "[使用 Azure PowerShell 或资源管理器模板启用用于 VM 的 Azure Monitor](insights/vminsights-enable-at-scale-powershell.md)中介绍这些步骤。 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果 VM 已开始向现有工作区报告，我该怎么做？
如果你已开始从虚拟机收集数据，则可能已将虚拟机配置为向现有的 Log Analytics 工作区报告数据。  只要该工作区在某个受支持区域中，就可以在该现有工作区中启用用于 VM 的 Azure Monitor。  如果你已在使用的工作区不在我们支持的某个区域，此时你将无法加入用于 VM 的 Azure Monitor。  我们将力求支持更多的区域。


### <a name="why-did-my-vm-fail-to-onboard"></a>为何我的 VM 无法载入？
从 Azure 门户载入 Azure VM 时，将执行以下步骤：

* 创建默认的 Log Analytics 工作区（如果已选择该选项）。
* 使用 VM 扩展在 Azure VM 上安装 Log Analytics 代理（如果确定有必要）。  
* 使用某个扩展在 Azure VM 上安装用于 VM 的 Azure Monitor 映射依赖项代理（如果确定有必要）。 

在载入过程中，我们会检查上述每个步骤的状态，并在门户中返回通知状态。 配置工作区和安装代理通常需要 5 到 10 分钟。 在门户中查看监视数据需要额外的5到10分钟。  

如果在启动载入后有消息指出 VM 需要载入，则 VM 最长需要花费 30 分钟来完成该过程。 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我在 VM 的性能图表中看不到部分或全部数据
性能图表已更新为使用存储在*InsightsMetrics*表中的数据。  若要查看这些图表中的数据，需要升级才能使用新的 VM Insights 解决方案。  有关其他信息，请参阅我们的[GA 常见问题解答](insights/vminsights-ga-release-faq.md)。

如果在磁盘表或某些性能图表中看不到性能数据，则可能不会在工作区中配置性能计数器。 若要解决此问题，请运行以下 [PowerShell 脚本](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)。


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>用于 VM 的 Azure Monitor 映射功能与服务映射有何不同？
用于 VM 的 Azure Monitor 映射功能基于服务映射，但存在以下差异：

* 可以从“VM”边栏选项卡，或者 Azure Monitor 中的“用于 VM 的 Azure Monitor”访问“映射”视图。
* “映射”中的连接现在可单击，所选连接的边侧面板中会显示连接指标数据的视图。
* 可以使用一个新 API 创建映射，以便更好地支持更复杂的映射。
* 受监视 VM 现在包含在客户端组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 受监视虚拟机现在包含在服务器端口组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 映射样式已更新，与 Application Insights 中的应用映射更一致。
* 侧面板已更新，但并未包含服务映射中支持的整套集成功能 -“更新管理”、“更改跟踪”、“安全性”和“服务台”。 
* 用于选择要映射的组和计算机的选项已更新，现在支持订阅、资源组、Azure 虚拟机规模集和云服务。
* 无法在用于 VM 的 Azure Monitor 映射功能中创建新的服务映射计算机组。  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>为何我的性能图表显示虚线？
这有几个原因。  如果数据集合中存在间隙，则我们会绘制虚线。  如果为启用的性能计数器修改了数据采样频率（默认设置为每隔 60 秒收集数据），并且为图表选择较窄的时间范围，而采样频率小于图表中使用的桶大小（例如，采样频率为每隔 10 分钟，图表上的每个桶为 5 分钟），则可能会在图表中看到虚线。  在这种情况下，选择查看更宽的时间范围应该可使图表线条显示为实线而不是虚线。

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 是否支持组？
是，一旦安装依赖项代理，我们就会从 VM 中收集信息，以根据订阅、资源组、虚拟机规模集和云服务显示组。  如果你一直在使用服务映射并创建了计算机组，则也会显示这些组。  如果你为正在查看的工作区创建了计算机组，那么这些组也会显示在组筛选器中。 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何查看聚合性能图表中驱动第 95 百分位线条的详细信息？
默认情况下，列表已经过排序，会显示所选指标的第 95 百分位值最大的 VM，但“可用内存”图表除外，其中显示第 5 百分位值最小的计算机。  单击图表会打开“前 N 项列表”视图，其中已选择相应的指标。****

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>“映射”功能如何处理不同 VNet 和子网中的重复 IP？
如果跨子网和 VNet 复制 VM 或 Azure 虚拟机规模集的 IP 范围，可能导致用于 VM 的 Azure Monitor 映射显示错误的信息。 这是一个已知问题，我们正在研究可改进此体验的选项。

### <a name="does-map-feature-support-ipv6"></a>“映射”功能是否支持 IPv6？
“映射”功能目前仅支持 IPv4，我们正在研究 IPv6 的支持。 我们还支持 IPv6 隧道内部的 IPv4。

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>加载资源组或其他大型组的映射时，难以查看映射
尽管我们已对“映射”做出改进，使其能够处理大型和复杂的配置，但我们认识到，映射中可能包含大量以群集方式工作的节点和连接。  我们将持续致力增强支持以提高可伸缩性。   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>为何“性能”选项卡上的网络图表不同于 Azure VM“概述”页上的网络图表？

Azure VM 的概述页基于来宾 VM 中的活动的主机度量值显示图表。  Azure VM“概述”中的网络图表仅显示计费的网络流量。  这不包括虚拟网络之间的流量。  为用于 VM 的 Azure Monitor 显示的数据和图表基于来宾 VM 中的数据，而网络图表显示所有入站和出站的 TCP/IP 流量（包括虚拟网络之间的流量）。

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>如何测量存储在 VMConnection 中并显示在连接面板和工作簿中的数据的响应时间？

响应时间是近似值。 由于我们没有检测应用程序的代码，因此我们并不真正知道请求何时开始以及响应何时到达。 但是，我们观察到通过连接发送数据，然后通过该连接返回数据。 我们的代理跟踪这些发送和接收操作并尝试对它们进行配对：一个发送序列后跟一个接收序列将解释为一个请求/响应对。 这些操作之间的时间间隔就是响应时间。 它将包括网络延迟和服务器处理时间。

这种近似适用于基于请求/响应的协议：单个请求在连接上发出，并且单个响应到达。 这是 HTTP(S) 的情况（没有流水线操作），但对于其他协议不太适合。

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>如果我使用的是 Log Analytics 免费定价计划，它们有何限制？
如果已经为 Azure Monitor 配置了使用*免费*定价层的 Log Analytics 工作区，则用于 VM 的 Azure Monitor 的映射功能将仅支持已连接到工作区的五台计算机。 如果已有五台 VM 连接到免费工作区，你断开了其中一台 VM 的连接，然后连接到一台新的 VM，则新的 VM 将不受监视并且不会反映在“映射”页上。  

在这种情况下，在打开 VM 时，系统将提示你 "**立即试用**" 选项，然后从左侧窗格中选择 "**见解**"，即使已在 VM 上安装了它也是如此。  但是，如果此 VM 未加入到用于 VM 的 Azure Monitor，则不会像通常情况下那样通过选项向你发出提示。 


## <a name="next-steps"></a>后续步骤
如果此处未解答你的问题，你可以参考以下论坛来了解其他问题和答案。

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

有关 Azure Monitor 的一般反馈，请访问[反馈论坛](https://feedback.azure.com/forums/34192--general-feedback)。
