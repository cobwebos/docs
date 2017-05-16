---
title: "Azure Application Insights 常见问题解答 | Microsoft Docs"
description: "有关 Application Insights 的常见问题。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 65c199064273930f66db580b6c01dabb4b906e7a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights：常见问题

## <a name="configuration-problems"></a>配置问题
*我在设置以下内容时遇到问题：*

* [.NET 应用](app-insights-asp-net-troubleshoot-no-data.md)
* [监视已经在运行的应用](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure 诊断](app-insights-azure-diagnostics.md)
* [Java Web 应用](app-insights-java-troubleshoot.md)

*我无法从服务器获取任何数据*

* [设置防火墙异常](app-insights-ip-addresses.md)
* [设置 ASP.NET 服务器](app-insights-monitor-performance-live-website-now.md)
* [设置 Java 服务器](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>是否可以将 Application Insights 用于...？

* [IIS 服务器上的 Web 应用 - 本地或在 VM 中](app-insights-asp-net.md)
* [Java Web 应用](app-insights-java-get-started.md)
* [Node.js 应用](app-insights-nodejs.md)
* [Azure 上的 Web 应用](app-insights-azure-web-apps.md)
* [Azure 上的云服务](app-insights-cloudservices.md)
* [在 Docker 中运行的应用服务器](app-insights-docker.md)
* [单页 Web 应用](app-insights-javascript.md)
* [Sharepoint](app-insights-sharepoint.md)
* [Windows 桌面应用](app-insights-windows-desktop.md)
* [其他平台](app-insights-platforms.md)

## <a name="is-it-free"></a>是否免费？

是的，对于实验用途。 在基本定价计划中，应用程序可以发送一些每月免费数据量。 免费的限额足以满足开发和为少量用户发布应用的需求。 可以设置上限，以防止所使用的数据量超出指定数据量。

较大的遥测量按 Gb 收费。 我们提供了一些有关如何[限制费用](app-insights-pricing.md)的提示。

企业计划对每个 Web 服务器节点每天发送的遥测进行收费。 如果要大规模使用连续导出，此计划非常合适。

[阅读定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="how-much-is-it-costing"></a>花费是多少？

* 在 Application Insights 资源中打开“功能 + 定价”页面。 此处提供了一个最近使用情况的图表。 如果愿意，可以设置数据上限。
* 打开 [Azure 计费边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)查看所有资源的帐单。

## <a name="q14"></a>Application Insights 在我的项目中修改哪些内容？
详细信息取决于项目类型。 对于 Web 应用程序：

* 将这些文件添加到项目：

  * ApplicationInsights.config。
  * ai.js
* 安装以下 NuGet 包：

  * *Application Insights API* - 核心 API
  * *适用于 Web 应用程序 的 Application Insights API* - 用于从服务器发送遥测
  * *适用于 JavaScript 应用程序的 Application Insights API* - 用于从客户端发送遥测

    这些包包括以下程序集：
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 将项目插入：

  * Web.config
  * packages.config
* （仅限新项目 - 如果[将 Application Insights 添加到现有项目][start]，则必须手动执行此操作。）在客户端和服务器代码中插入代码片段，以使用 Application Insights 资源 ID 初始化它们。 例如，在 MVC 应用中，代码插入到主页 Views/Shared/_Layout.cshtml 中

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何从较早的 SDK 版本升级？
请参阅[发行说明](app-insights-release-notes.md)了解对应于应用程序类型的 SDK。

## <a name="update"></a>如何更改项目向哪个 Azure 资源发送数据？
在解决方案资源管理器中，右键单击 `ApplicationInsights.config` 并选择“更新 Application Insights”。 可在 Azure 中将数据发送到现有或新资源。 更新向导更改 ApplicationInsights.config 中的检测密钥，该密钥确定服务器 SDK 将数据发送到何处。 除非取消选中“更新全部”，否则它还将在网页中出现密钥的位置更改密钥。

## <a name="what-is-status-monitor"></a>什么是状态监视器？

它是可以在 IIS Web 服务器中使用的桌面应用，用于帮助在 Web 应用中配置 Application Insights。 它不会收集遥测：不配置应用时，可以停止此监视器。 

[了解详细信息](app-insights-monitor-performance-live-website-now.md#questions)。

## <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights 收集哪些遥测？

从服务器 Web 应用：

* HTTP 请求
* [依赖项](app-insights-asp-net-dependencies.md)。 调用: SQL 数据库; 对外部服务的 HTTP 调用; Azure Cosmos DB, 表, blob 存储和队列。 
* [异常](app-insights-asp-net-exceptions.md)和堆栈跟踪。
* [性能计数器](app-insights-performance-counters.md) - 如果使用[状态监视器](app-insights-monitor-performance-live-website-now.md)、Azure 监视 (app-insights-azure-web-apps.md) 或 [Application Insights 收集编写器](app-insights-java-collectd.md)。
* [自定义编码的事件和指标](app-insights-api-custom-events-metrics.md)。
* 如果配置相应的收集器，则会[跟踪日志](app-insights-asp-net-trace-logs.md)。

从[客户端网页](app-insights-javascript.md)：

* [页面视图计数](app-insights-web-track-usage.md)
* [AJAX 调用](app-insights-asp-net-dependencies.md)，从运行脚本发出的请求。
* 页面视图加载数据
* 用户和会话计数
* [已经过身份验证的用户 ID](app-insights-api-custom-events-metrics.md#authenticated-users)

从其他源中（如果有配置）：

* [Azure 诊断](app-insights-azure-diagnostics.md)
* [Docker 容器](app-insights-docker.md)
* [将表导入到分析](app-insights-analytics-import.md)
* [OMS (Log Analytics)](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>是否可以筛选或修改某些遥测？

可以，可在服务器中编写：

* 用于在从应用发送属性前，对属性进行筛选并将其添加到所选遥测项的遥测处理器。
* 用于将属性添加到所有遥测项的遥测初始化器。

深入了解 [ASP.NET](app-insights-api-filtering-sampling.md) 或 [Java](app-insights-java-filter-telemetry.md)。

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>如何计算市/县/区、国家/地区和其他地理位置数据？

使用 [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/) 查找 Web 客户端的 IP 地址（IPv4 或 IPv6）。

* 浏览器遥测：收集发件人的 IP 地址。
* 服务器遥测：Application Insights 模块收集客户端 IP 地址。 如果设置了 `X-Forwarded-For`，则不会进行收集。

可以配置 `ClientIpHeaderTelemetryInitializer`，从不同的标头获取 IP 地址。 例如，在某些系统中，代理、负载均衡器或 CDN 会将其移动到 `X-Originating-IP`。 [了解详细信息](http://apmtips.com/blog/2016/07/05/client-ip-address/)。

可以[使用 Power BI](app-insights-export-power-bi.md) 在映射中显示请求遥测。


## <a name="data"></a>数据在门户中保留多长时间？ 是否安全？
请参阅[数据保留和隐私][data]。

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>遥测中有可能发送个人身份信息 (PII) 吗？

如果代码发送了此类数据，则有可能。 如果堆栈跟踪中的变量包括 PII，也可能发生这种情况。 开发团队应进行风险评估，确保妥善处理 PII。 [深入了解数据保留和隐私](app-insights-data-retention-privacy.md)。

客户端 Web 地址的最后一个八进制数在门户引入后始终设置为 0。

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>在网页源中可见我的 iKey。 

* 这是监视解决方案的常见做法。
* 它不能用于窃取数据。
* 但可用于倾斜数据或触发警报。
* 我们未曾听说有任何客户遇到了此类问题。

可以：

* 将两个单独 iKey（独立的 Application Insights 资源）分别用于客户端和服务器数据。 或
* 编写一个在服务器中运行的代理，并使 Web 客户端通过该代理发送数据。

## <a name="post"></a>如何在诊断搜索中查看 POST 数据？
我们不自动记录 POST 数据，但你可以使用 TrackTrace 调用：将数据放置在消息参数中。 相比对字符串属性的限制，这具有更长的大小限制，尽管你无法对其进行筛选。

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>应使用单个 Application Insights 资源还是多个 Application Insights 资源？

* [请参阅此处的讨论](app-insights-separate-resources.md)
* [示例 - 辅助角色和 Web 角色的云服务](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>如何动态更改检测密钥？

* [在此处讨论](app-insights-separate-resources.md)
* [示例 - 辅助角色和 Web 角色的云服务](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>什么是用户和会话计数？

* JavaScript SDK 在 Web 客户端上设置了用于识别返回用户的用户 cookie，以及用于对活动进行分组的会话 cookie。
* 如果没有客户端脚本，可以[在服务器设置 cookie](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)。
* 如果一个真实的用户在不同的浏览器中使用站点，或者使用私密/隐身浏览，或使用不同的计算机，则会进行多次计数。
* 若要识别跨计算机和浏览器登录的用户，请添加对 [setAuthenticatedUserContect()](app-insights-api-custom-events-metrics.md#authenticated-users) 的调用。

## <a name="q17"></a> 我是否已在 Application Insights 中启用所有内容？
| 你应看到 | 如何获取 | 为何需要它 |
| --- | --- | --- |
| 可用性图表 |[Web 测试](app-insights-monitor-web-app-availability.md) |知道 Web 应用已启动 |
| 服务器应用性能：响应时间、... |[将 Application Insights 添加到项目](app-insights-asp-net.md)或[在服务器上安装 AI 状态监视器](app-insights-monitor-performance-live-website-now.md)（或编写你自己的代码以[跟踪依赖项](app-insights-api-custom-events-metrics.md#trackdependency)） |检测性能问题 |
| 依赖项遥测 |[在服务器上安装 AI 状态监视器](app-insights-monitor-performance-live-website-now.md) |诊断数据库或其他外部组件问题 |
| 获取异常的堆栈跟踪 |[在代码中插入 TrackException 调用](app-insights-asp-net-exceptions.md)（但有些会自动报告） |检测和诊断异常 |
| 搜索日志跟踪 |[添加日志记录适配器](app-insights-asp-net-trace-logs.md) |诊断异常、性能问题 |
| 客户端使用基础知识：页面视图、会话、... |[网页中的 JavaScript 初始值设定项](app-insights-javascript.md) |使用情况分析 |
| 客户端自定义指标 |[在网页中跟踪调用](app-insights-api-custom-events-metrics.md) |增强用户体验 |
| 服务器自定义指标 |[在服务器中跟踪调用](app-insights-api-custom-events-metrics.md) |商业智能 |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>为什么搜索图表和指标图表中的计数不一致？

[采样](app-insights-sampling.md)减少了实际从应发送到门户的遥测项（请求、自定义事件等）数量。 在搜索中，看到的是实际接收到的项数。 在显示事件计数的指标图表中，看到的是发生的原始事件数。 

传输的每个项都携带一个 `itemCount` 属性，此属性显示该项表示的原始事件数量。 若要观察操作中的采样，可以在分析中运行此查询：

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>自动化

### <a name="configuring-application-insights"></a>配置 Application Insights

可以使用 Azure 资源监视器[编写 PowerShell 脚本](app-insights-powershell.md)：

* 创建并更新 Application Insights 资源。
* 设置定价计划。
* 获取检测密钥。
* 添加指标警报。
* 添加可用性测试。

无法设置指标资源管理器报表，或设置连续导出。

### <a name="querying-the-telemetry"></a>查询遥测

使用 [REST API](https://dev.applicationinsights.io/) 运行 [Analytics](app-insights-analytics.md) 查询。

## <a name="how-can-i-set-an-alert-on-an-event"></a>如何设置事件警报？

Azure 警报仅出现在指标上。 创建一个每当事件发生时都跨越值阈值的自定义指标。 然后在该指标上设置警报。 请注意：指标在任一方向跨越阈值时，都会收到通知；无论初始值高低，都不会在首次跨越前收到通知；总是有几分钟的延迟。

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure Web 应用和 Application Insights 之间是否有数据传输费用？

* 如果 Azure Web 应用托管在具有 Application Insights 集合终结点的数据中心内，则不收取费用。 
* 如果主机数据中心没有集合终结点，那么应用的遥测将会产生 [ Azure 传出费用](https://azure.microsoft.com/pricing/details/bandwidth/)。

这与 Application Insights 资源的托管位置无关。 它只取决于终结点的分布情况。

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>是否可以将遥测数据发送到 Application Insights 门户？

建议使用 SDK，并且使用 SDK API (app-insights-api-custom-events-metrics.md)。 为各种[平台](app-insights-platforms.md)提供了相应的 SDK 变体。 这些 SDK 可处理缓冲、压缩、限制以及重试等。 但是，[引入架构](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema)和[终结点协议](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)是公共的。

## <a name="can-i-monitor-an-intranet-web-server"></a>是否可以监视 Intranet Web 服务器？

以下提供了两种方法：

### <a name="firewall-door"></a>防火墙门

允许 Web 服务器将遥测发送到终结点 https://dc.services.visualstudio.com:443 和 https://rt.services.visualstudio.com:443。 

### <a name="proxy"></a>代理

通过在 ApplicationInsights.config 中进行设置，将流量从服务器路由到 Intranet 上的网关：

```XML
<TelemetryChannel>
    <EndpointAddress>your gateway endpoint</EndpointAddress>
</TelemetryChannel>
```

网关应将流量路由到 https://dc.services.visualstudio.com:443/v2/track

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>是否可以在 Intranet 服务器上运行可用性 Web 测试？

我们的 [Web 测试](app-insights-monitor-web-app-availability.md)可在遍布全球的各个接入点上运行。 可运用以下两种解决方案：

* 防火墙门 - 允许从[长且可更改的 Web 测试代理列表](app-insights-ip-addresses.md)中请求自己的服务器。
* 编写自己的代码，从 Intranet 内部向服务器发送定期请求。 可以为此运行 Visual Studio Web 测试。 测试人员可以使用 TrackAvailability() API 将结果发送到 Application Insights。

## <a name="more-answers"></a>更多答案
* [Application Insights 论坛](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

