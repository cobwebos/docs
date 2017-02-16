---
title: "使用 Azure Application Insights 管理应用程序性能 | Microsoft Docs"
description: "跟踪实时 web 应用程序的的性能和使用情况。  检测、会审和诊断问题。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 3e9476f8edc4186af026495bc575b8203c35c619
ms.openlocfilehash: b01de7724ec116e5e27369f7c0f852f82ab0348b


---
# <a name="application-performance-management-with-application-insights"></a>使用 Application Insights 管理应用程序性能
Application Insights 是面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它会自动检测性能异常。 其中包含强大的分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。  它旨在帮助持续提高性能与可用性。 它适用于本地或云中托管的各种平台（包括 .NET、Node.js 和 J2EE）中的应用。 它还可以与 devOps 流程集成，与其他各种工具建立连接。

![将用户活动统计信息绘制成图表，或钻取到特定事件。](./media/app-insights-overview/00-sample.png)

[查看简介动画](https://www.youtube.com/watch?v=fX2NtGrh-Y0)。

## <a name="how-does-it-work"></a>工作原理
在应用程序中安装小检测包，并在 Microsoft Azure 门户中设置 Application Insights 资源。 检测会监视应用，并将遥测数据发送到门户。 门户将显示统计图表，并提供功能强大的搜索工具来帮助诊断问题。

![应用中的 Application Insights 检测将遥测发送到 Azure 门户中的 Application Insights 资源。](./media/app-insights-overview/01-scheme.png)

Application Insights 具有多个[标准检测模块](app-insights-configuration-with-applicationinsights-config.md)，这些模块收集不同类型的遥测，例如请求响应时间、异常和依赖项调用。 还可以[编写代码以将自定义遥测发送](app-insights-api-custom-events-metrics.md)到门户。

### <a name="whats-the-overhead"></a>什么是开销？
应用性能受到的影响非常小。 跟踪调用是非阻塞式的，以批量方式进行并在单独的线程中发送。

## <a name="what-does-it-do"></a>它有什么作用？
Application Insights 主要面向开发团队，旨在帮助用户了解应用的运行性能和使用方式。 它提供：

遥测类型：

* HTTP 请求率、响应时间、成功率。
* 依赖项（HTTP 和 SQL）调用率、响应时间、成功率。
* 来自服务器和客户端的异常跟踪。
* 诊断日志跟踪。
* 页面查看计数、用户和会话计数、浏览器加载时间、异常数。
* AJAX 调用率、响应时间和成功率。
* 服务器性能计数器。
* 自定义客户端和服务器遥测。
* 按客户端位置、浏览器版本、操作系统版本、服务器实例、自定义维度等的分段。
* 可用性测试

诊断和分析工具：

* 针对故障率和其他指标以及可用性的智能和手动警报。
* 随时间变化的汇总指标的图表。
* 针对请求实例、异常、自定义事件、日志跟踪、页面视图、依赖项和 AJAX 调用的诊断搜索。
* 分析 - 针对遥测的功能强大的查询语言
* 仪表板 - 构成监视所有应用组件时所需的图表。

## <a name="how-do-i-use-it"></a>如何使用它？
### <a name="monitor"></a>监视
在 Web 应用中安装 Application Insights，设置可用性 Web 测试，然后：

* 为团队聊天室设置仪表板，以便监视负载、响应能力和依赖项性能、页面加载和 AJAX 调用。
* 发现最慢和多数情况下失败的请求。
* 在部署新版本时查看实时流，快速掌握任何降级情况。

### <a name="diagnose"></a>诊断
当收到警报，或发现问题时：

* 将失败与异常、依赖项调用和跟踪关联起来。
* 检查堆栈转储和跟踪日志。

### <a name="assess"></a>评估
测量部署的每个新功能的有效性。

* 规划以衡量客户使用新的 UX 或业务功能的方式。
* 将自定义遥测写入代码以记录使用情况。
* 使每个开发周期基于遥测的硬性证据。

## <a name="get-started"></a>入门
Application Insights 是 Microsoft Azure 中托管的众多服务之一，遥测会发送到此处进行分析和展示。 因此在执行任何其他操作之前，需要有 [Microsoft Azure](http://azure.com) 订阅。 可免费注册该订阅，如果选择 Application Insights 的基本[pricing plan](https://azure.microsoft.com/pricing/details/application-insights/)（定价计划），不会产生费用，但如果应用的使用量大幅增加，将会产生费用。 如果你的组织已有订阅，则组织可向其中添加你的 Microsoft 帐户。

可通过多种方法开始使用。 可从最适合你的方法开始。 可在以后添加其他方法。

* **运行时：检测服务器上的 Web 应用。** 避免任何代码更新。 需要服务器的管理员访问权限。
  * [**本地或 VM 上的 IIS**](app-insights-monitor-performance-live-website-now.md)
  * [Azure Web 应用或 VM](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **开发时：将 Application Insights 添加到代码。** 可以编写自定义遥测以及检测后端和桌面应用。
  * [Visual Studio](app-insights-asp-net.md) 2013 update 2 或更高版本。
  * [Eclipse](app-insights-java-eclipse.md) 或[其他工具](app-insights-java-get-started.md)中的 Java
  * [Node.js](app-insights-nodejs.md)
  * [其他平台](app-insights-platforms.md)
* 针对页面视图、AJAX 和其他客户端遥测**[检测网页](app-insights-javascript.md)**。
* **[可用性测试](app-insights-monitor-web-app-availability.md)** - 从我们的服务器定期对网站执行 ping 操作。

> [!NOTE]
> 这时，你可能会想要尝试使用和试验一下。 但是，如果想了解 Application Insights 的功能，请继续阅读...
>
>

## <a name="explore-metrics"></a>探索指标
运行应用 - 在开发计算机上以调试模式运行或通过将其部署到服务器运行 - 并使用一段时间。 然后登录到 [Azure 门户](https://portal.azure.com)。

导航到应用的 Application Insights 概述边栏选项卡：

![概述边栏选项卡](./media/app-insights-overview/01-find.png)

通过概述可快速查看应用的运行性能。 可以将负载（请求率）和应用响应请求的时间进行比较。 如果负载增加时响应时间以不相称的方式增加，可能会希望给应用分配更多资源。 如果在部署新版本后显示出现更多失败响应，可能会希望进行回退。

#### <a name="get-more-detail"></a>获取更多详细信息
单击任何图表以获取更详细的图表集。 例如，通过服务器响应时间图表，可获得显示请求率、响应时间和依赖项（即应用调用的服务）响应时间的图表。  

![服务器边栏选项卡](./media/app-insights-overview/04.png)

依赖项图表非常有用，因为通过它可以查看应用使用的数据库和 REST API 是否响应正常，或是否引起延迟。

#### <a name="customize-a-chart"></a>自定义图表
尝试编辑其中一个图表。 例如，如果在服务器实例的集合上运行 Web 应用，可比较不同服务器实例上的响应时间：

![编辑图表](./media/app-insights-overview/05.png)

1. 将鼠标悬停在图表并单击“编辑”。
2. 选择一个指标。 图表上可显示多个指标，但只能以特定组合形式显示：用户需要先取消选择一个指标，然后才能选择所需的指标。
3. 使用 Group-By 按属性对指标分段。 在此示例中，我们为不同的响应时间显示了单独的行。

    请注意，需要选择指标的有效属性，不然图表不会显示任何数据。
4. 选择图表类型。 当聚合类型为“Sum”时，区域图和条形图会显示一个适合的堆栈显示区。

[指标浏览详细信息](app-insights-metrics-explorer.md)。

## <a name="search-instance-data"></a>搜索实例数据
要调查问题，检查特定事件实例会非常有用。

请单击指标图表，按相关筛选器和时间范围搜索实例数据。 例如，单击服务器请求计数以查看单个请求报告。

也可以在概述页上从“搜索”直接转到实例数据：

![搜索实例](./media/app-insights-overview/06.png)

使用筛选器，将重点放在特定类型的事件和选定的属性值上：

![筛选属性](./media/app-insights-overview/07.png)

单击“...”以查看属性完整列表，或打开与相同请求关联的其他事件。 在此示例中，失败请求具有相关联的异常报表：

![相关的项和属性详细信息](./media/app-insights-overview/08.png)

打开事件（在此示例中是相关异常），然后可创建工作项（如果使用 Visual Studio Team Services 跟踪任务）。

![创建工作项](./media/app-insights-overview/09.png)

## <a name="analytics"></a>分析
[分析](app-insights-analytics.md)是更强大的搜索和分析功能，可用于编写针对遥测数据的类似 SQL 的查询，以查找特定问题或编译统计信息。

![分析](./media/app-insights-overview/10.png)

请打开教程窗口，查看并运行数据查询示例，或阅读较长的[教程演练](app-insights-analytics-tour.md)。 Intellisense 会向用户提示可用查询，并且还有[完整语言引用](app-insights-analytics-reference.md)。

查询的开头通常是遥测流（如请求、异常或依赖项）的名称。 弹开左侧的架构栏以查看可用遥测流列表。 查询是一个[查询操作](app-insights-analytics-reference.md#queries-and-operators)的管道，如 `where`（布尔筛选器）或 `project`（用于计算新属性）。 `summarize` 聚合实例，通过定义的函数对其进行分组，然后对分组的数据应用聚合函数。

结果可以[表格形式或各种类型的图表形式呈现](app-insights-analytics-tour.md#charting-the-results)。

## <a name="custom-telemetry"></a>自定义遥测
使用通过安装 Application Insights 获得的内置遥测，可以分析针对应用的 Web 请求以及依赖项（即应用对 SQL、REST API 的调用）的计数、成功率和响应时间。 还可以获取异常跟踪，以及（通过服务器上的状态监视器）获取系统性能计数器。 如果将客户端代码段添加到网页，可获取页面查看次数计数、加载时间、客户端异常和 AJAX 调用成功率和响应率。

通过分析所有这些遥测可了解有关应用性能和使用情况的详细信息。 但有时这还不够。 用户可能想要监视队列的长度以便优化性能；或想要对销售计数并根据位置对其分段；或者想要了解在客户端用户单击特定按钮的频率以便优化用户体验。

[Application Insights API](app-insights-api-custom-events-metrics.md) 提供 `TrackEvent(name)` 和 `TrackMetric(name, value)` 调用，以便用户可发送自定义事件和指标。 存在客户端的等效调用。

例如，如果网页是单页游戏应用，可在适当位置插入行，以在使用者赢得游戏或游戏失败时进行记录：

    appInsights.trackEvent("WinGame");
    ...
    appInsights.trackEvent("LoseGame");

然后可绘制自定义事件计数的图表，按事件名称对其分段：

![分析](./media/app-insights-overview/11.png)

### <a name="log-traces"></a>日志跟踪
出于诊断目的，设有一个自定义事件 `TrackTrace(message)`，可用于执行跟踪。 在“搜索”和“分析”功能中，用户可搜索消息内容，其长度可超过事件名称。

如果已经使用 Log4Net、NLog、Log4J 或 System.Diagnostic.Trace 等日志记录框架，则 Application Insights 可捕获这些跟踪调用，并将其与其他遥测一起显示。 Visual Studio 工具会自动添加适当的 SDK 模块。

## <a name="profiling-your-live-site"></a>分析实时站点

不知道时间花到哪去了？ Application Insights 探查器将跟踪对实时站点的 HTTP 调用，并显示代码中有哪些函数花费了最长的时间。 探查器目前受限预览版提供 - 欢迎[注册试用](https://aka.ms/AIProfilerPreview)。

## <a name="dashboards"></a>仪表板
许多应用程序包含多个组件，如 Web 服务和一个或多个后端处理器。 每个组件将由不同的单个 Application Insights 资源进行监视。 如果在 Azure 上运行系统，还可能使用（和监视）服务，如事件中心和机器学习。

若要监视整个系统，可从各种不同应用中选择最相关的图表，并将其固定到 Azure [仪表板](app-insights-dashboards.md)，通过此操作用户可持续监视整个系统。

![仪表板](./media/app-insights-overview/12.png)

事实上，用户可以创建多个仪表板，例如，用于监视总体系统运行状况的团队聊天室仪表板；重点关注不同功能使用情况的设计仪表板；用于测试中组件的单独仪表板等等。  

与资源一样，仪表板可在团队成员之间共享。

## <a name="development-in-visual-studio"></a>在 Visual Studio 中开发
如果要使用 Visual Studio 开发应用，用户将会发现它包含多个内置的 Application Insights 工具。

### <a name="diagnostic-search"></a>诊断搜索
“搜索”窗口显示已记录的事件。 （如果在设置 Application Insights 时登录 Azure，将能够在门户中搜索相同的事件。）

![右键单击项目，然后依次选择“Application Insights”、“搜索”](./media/app-insights-overview/34.png)

自定义文本搜索适用于事件中的任何字段。 例如，搜索页面的 URL 的一部分；或客户城市的属性值；或跟踪日志中的特定字词。

单击任何事件即可查看其详细属性。

还可以打开“相关项目”选项卡，帮助诊断失败的请求或异常。

![](./media/app-insights-overview/41.png)

### <a name="diagnostics-hub"></a>诊断中心
诊断中心（在 Visual Studio 2015 或更高版本中）显示其生成的 Application Insights 服务器遥测。 即使选择仅安装 SDK，而不连接到 Azure 门户中的资源，这也同样适用。

![打开“诊断工具”窗口，然后检查 Application Insights 事件。](./media/app-insights-overview/31.png)

### <a name="exceptions"></a>异常
如果已 [设置异常监视](app-insights-asp-net-exceptions.md)，异常报告将在“搜索”窗口中显示。

单击异常可获得堆栈跟踪。 如果应用的代码在 Visual Studio 中打开，可从堆栈跟踪单击浏览到相关代码行。

![异常堆栈跟踪](./media/app-insights-overview/17.png)

此外，在每个方法上的 Code Lens 行中，将看到过去 24 小时内由 Application Insights 记录的异常的计数。

![异常堆栈跟踪](./media/app-insights-overview/21.png)

### <a name="local-monitoring"></a>本地监视
（从 Visual Studio 2015 Update 2 开始）如果未将 SDK 配置为将遥测发送到 Application Insights 门户（以便让 ApplicationInsights.config 中没有检测密钥），“诊断”窗口会显示来自最新调试会话的遥测。

如果已发布过应用的以前版本，这会是比较好的做法。 你不会希望来自调试会话的遥测与 Application Insights 门户中来自已发布应用的遥测混在一起。

如果在将遥测发送到门户之前有一些想要调试的 [自定义遥测](app-insights-api-custom-events-metrics.md) ，它会很有用。

* *最初，将 Application Insights 完全配置为将遥测发送到门户。但现在只想查看 Visual Studio 中的遥测。*

  * 在“搜索”窗口的“设置”中，即使应用将遥测发送到门户，也有选项可供搜索本地诊断。
  * 若要停止将遥测发送到门户，请注释禁止 ApplicationInsights.config 的 `<instrumentationkey>...` 行。 准备好再次将遥测发送到门户时，请取消注释。

### <a name="trends"></a>趋势
“趋势”是 Visual Studio 中的一种工具，用于对应用行为随着时间变化的情况进行可视化。

从 Application Insights 工具栏按钮或 Application Insights“搜索”窗口选择“浏览遥测趋势”  。 选择五种常见查询中的一个，以便开始使用。 可以根据遥测类型、时间范围和其他属性分析不同的数据集。

若要查找数据中的异常情况，请选择“视图类型”下拉列表下的其中一个异常情况选项。 使用窗口底部的筛选选项，可轻松地全力探索遥测的特定子集。

![趋势](./media/app-insights-overview/51.png)

## <a name="releasing-a-new-build"></a>发布新版本
### <a name="live-metrics-stream"></a>实时指标流
实时指标流说明应用程序指标正处于此非常时刻，接近实时的延迟为 1 秒。 发布新版本并想要确保一切按预期运行，或者要实时调查某个事件时，这非常有用。

![在“概览”边栏选项卡中单击“实时流”](./media/app-insights-overview/live-stream.png)

不同于指标资源管理器，实时指标流显示一组固定指标。 数据只会在图表上显示时才会得到保留，不显示时将被丢弃。

### <a name="annotations"></a>批注
指标图表上的[版本批注](app-insights-annotations.md)会显示新版本的部署位置。 通过该批注可轻松查看更改是否对应用程序的性能产生了任何影响。 [Visual Studio Team Services 生成系统](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs)和 Visual Studio 的 Web 部署均可自动创建批注。 还可[从 PowerShell 进行创建](#create-annotations-from-powershell)。

![显示与服务器响应时间相关联的批注示例](./media/app-insights-overview/00.png)

版本批注是 Visual Studio Team Services 的基于云的生成和发布服务的一项功能。



## <a name="alerts"></a>警报
当应用出现问题时，用户会希望快速了解问题。

Application Insights 提供三种类型的警报，均通过电子邮件发送。

### <a name="proactive-diagnostics"></a>主动诊断
将自动设置[主动诊断](app-insights-proactive-failure-diagnostics.md)，无需对其进行配置。 假设网站有足够的流量，如果失败的请求数增多（对于当天的时间或请求率而言不寻常），用户会收到一封电子邮件。 警报包含诊断信息。

下面是一个示例警报。

![围绕失败显示群集分析的示例智能警报](./media/app-insights-overview/proactive-alert.png)

第二种类型的主动检测用于发现失败和某些因素（如位置、客户端 OS 或浏览器类型）之间的相关性。

### <a name="metric-alerts"></a>指标警报
通过配置[指标警报](app-insights-alerts.md)，当任一指标（例如失败计数、内存或页面查看数）在一段时间内超过某个阈值时，会通知用户。

![在指标资源管理器中选择“警报规则”，然后选择“添加警报”](./media/app-insights-overview/appinsights-413setMetricAlert.png)

### <a name="availability"></a>可用性
[可用性 Web 测试](app-insights-monitor-web-app-availability.md)从我们位于世界各地的服务器将请求发送到用户站点。 当站点在 Internet 上不可用或响应缓慢时，它会向用户发送通知。

![Web 测试示例](./media/app-insights-overview/appinsights-10webtestresult.png)

## <a name="export-and-api"></a>导出和 API
可通过多种方法从 Application Insights 门户获取遥测数据：

* [数据访问 REST API](https://dev.applicationinsights.io/) 可用于搜索和提取数据，包括运行 Analytics 查询。 
* 将 [Analytics 查询导出到 Power BI](app-insights-export-power-bi.md) 仪表板并在 Power BI 可视化效果（可以自动刷新）中查看结果。
* 如果希望大部分遥测的保留期长于标准保留期，则[连续导出](app-insights-export-telemetry.md)是最佳选择。
* [指标](app-insights-metrics-explorer.md#export-to-excel)表、搜索结果和[分析](app-insights-analytics.md)结果均可导出到 Excel 电子表格。

![在 Power BI 中查看数据](./media/app-insights-overview/210.png)

## <a name="data-management"></a>数据管理
使用 Application Insights 时会存在一些限制，这在某种程度上取决于所选择的定价方案。 主要有以下几个方面的限制：

* 每分钟的遥测率
* 每月的数据点计数
* 数据保留期

[采样](app-insights-sampling.md)是一种用于降低成本和避免限制的机制。 它会丢弃一部分遥测，保留一份代表性采样。 关联的项（如异常和导致其发生的请求）将一并保留或丢弃。 对于 ASP.NET 应用程序，会自动执行采样并在应用中应用；否则，可将其设置为在引入门户时应用。

## <a name="next-steps"></a>后续步骤
在运行时开始使用：

* [IIS 服务器](app-insights-monitor-performance-live-website-now.md)
* [J2EE 服务器](app-insights-java-live.md)

在开发时开始使用：

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>支持和反馈
* 问题：
  * [故障排除][qna]
  * [MSDN 论坛](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
  * [获取开发人员支持](app-insights-get-dev-support.md)
* 用户建议：
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* 博客：
  * [Application Insights blog](https://azure.microsoft.com/blog/tag/application-insights)（Application Insights 博客）

## <a name="videos"></a>视频
[![动画简介](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/218/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/231/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]
>
> [动画简介](https://www.youtube.com/watch?v=fX2NtGrh-Y0)
>
>

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md



<!--HONumber=Jan17_HO4-->


