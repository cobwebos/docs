---
title: "充分利用 Application Insights | Microsoft 文档"
description: "开始使用 Application Insights 后，下面概述了你可以浏览的功能。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: e1f9c62765e414e194330419f26c2b7437da21b3
ms.openlocfilehash: 79cfe90bb883b5cadf707272d31a990c8886dbe4


---
# <a name="more-telemetry-from-application-insights"></a>Application Insights 中的更多遥测
[将 Application Insights 添加到 ASP.NET 代码](app-insights-asp-net.md)后，可执行一些操作来获取更多遥测。 

## <a name="if-your-app-runs-on-your-iis-server-"></a>如果应用在 IIS 服务器上运行...
如果应用托管在你的控制中的 IIS 服务器上，在服务器上安装 Application Insights 状态监视器。 如果已安装，无需重新执行任何其他操作。

1. 在每个 IIS Web 服务器上，使用管理员凭据登录。
2. 下载并运行 [状态监视器安装程序](http://go.microsoft.com/fwlink/?LinkId=506648)。
3. 在安装向导中，登录到 Microsoft Azure。

无需执行任何其他操作，即可确认为应用启用了监视。

![在 Azure 中扩展](./media/app-insights-asp-net-more/025.png)

（还可以使用状态监视器[在运行时启用监视](app-insights-monitor-performance-live-website-now.md)，即使没有在 Visual Studio 中检测你的应用也是如此。）

### <a name="what-do-you-get"></a>你将获得哪些内容？
如果你的服务器计算机安装了状态监视器，你可获得一些其他遥测；

* 用于 .NET 4.5 应用的依赖项遥测（你的应用进行的 SQL 调用和 REST 调用）。 （对于更高版本的 .NET，依赖项遥测不需要状态监视器。） 
* 异常堆栈跟踪显示更多详细信息。
* 性能计数器。 在 Application Insights 中，这些计数器显示在“服务器”边栏选项卡中。 

![在 Azure 中扩展](./media/app-insights-asp-net-more/070.png)

若要查看更多或更少计数器，请[编辑图表](app-insights-metrics-explorer.md)。 如果所需的性能计数器未在可用集中，你可以[将其添加到性能计数器所收集的集](app-insights-performance-counters.md)。

## <a name="if-its-an-azure-web-app-"></a>如果它是 Azure Web 应用...
如果你的应用以 Azure Web 应用的形式运行，请转到应用或 VM 的 Azure 控制面板，然后打开“Application Insights”边栏选项卡。 

### <a name="what-do-you-get"></a>你将获得哪些内容？
* 异常堆栈跟踪显示更多详细信息。
* 用于 .NET 4.5 应用的依赖项遥测（你的应用进行的 SQL 调用和 REST 调用）。 （对于更高版本的 .NET，依赖项遥测不需要扩展。） 

![在 Azure 中扩展](./media/app-insights-asp-net-more/080.png)

（还可以使用状态监视器[在运行时启用监视](app-insights-monitor-performance-live-website-now.md)，即使没有在 Visual Studio 中检测你的应用也是如此。）

## <a name="client-side-monitoring"></a>客户端监视
已安装可从应用程序的服务器（后端）发送遥测数据的 SDK。 现在，可添加客户端监视。 这向你提供有关用户、会话、页面视图和在浏览器中发生的任何异常或崩溃的数据。 你还能够编写自己的代码，跟踪用户使用你的应用的方式并一直跟踪到单击和击键的详细级别。

将 Application Insights JavaScript 代码段添加到每个网页，从客户端浏览器获取遥测。

1. 在 Azure 中，打开你的应用的 Application Insights 资源。
2. 依次打开“开始使用”、“监视和诊断客户端应用程序”，然后复制该代码片段。
3. 粘贴代码段，以便它显示在每个网页的页眉，你通常可以通过将其粘贴到主布局页面中实现此目的。

![在 Azure 中扩展](./media/app-insights-asp-net-more/100.png)

请注意，代码包括用于标识应用程序资源的检测密钥。

### <a name="what-do-you-get"></a>你将获得哪些内容？
* 你可以编写 JavaScript 以[从网页发送自定义遥测](app-insights-api-custom-events-metrics.md)，例如跟踪按钮单击。
* 在“分析”[](app-insights-analytics.md)中，获取 `pageViews` 中的数据和 `dependencies` 中的 AJAX 数据。 
* 获取“浏览器”边栏选项卡中的[客户端性能和使用情况数据](app-insights-javascript.md)。

![在 Azure 中扩展](./media/app-insights-asp-net-more/090.png)

[了解有关网页跟踪的详细信息。](app-insights-web-track-usage.md)

## <a name="track-application-version"></a>跟踪应用程序版本
请确保 `buildinfo.config` 由 MSBuild 进程生成。 在 .csproj 文件中，添加：  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

当它具有内部信息时，Application Insights Web 模块自动将**应用程序版本**作为属性添加到每个遥测项。 这允许你在执行[诊断搜索](app-insights-diagnostic-search.md)时或在[浏览指标](app-insights-metrics-explorer.md)时按版本进行筛选。 

但请注意，内部版本号仅由 MS Build，而不是 Visual Studio 内部的开发人员生成。

## <a name="availability-web-tests"></a>可用性 Web 测试
定期从世界各地发送你的 Web 应用 HTTP 请求。 如果响应速度较慢或不可靠，我们会向你发送警报。

在你的应用的 Application Insights 资源中，单击“可用性”磁贴添加、编辑和查看 Web 测试。

可添加在多个位置运行的多个测试。

![在 Azure 中扩展](./media/app-insights-asp-net-more/110.png)

[了解详细信息](app-insights-monitor-web-app-availability.md)

## <a name="custom-telemetry-and-logging"></a>自定义遥测和日志记录
添加到代码中的 Application Insights 程序包提供了一个可从你的应用程序调用的 API。

* [生成你自己的事件和指标](app-insights-api-custom-events-metrics.md)，例如对业务事件或监视性能进行计数。
* 从 Log4Net、NLog 或 System.Diagnostics.Trace [捕获日志跟踪](app-insights-asp-net-trace-logs.md)。
* 通过编写遥测处理器[筛选、修改或增加](app-insights-api-filtering-sampling.md)从你的应用发送的标准遥测。 

## <a name="powerful-analysis-and-presentation"></a>功能强大的分析和演示
可通过许多方式浏览你的数据。 如果你刚刚开始使用 Application Insights，请查看以下文章：

|  |  |
| --- | --- |
| [**实例数据的诊断搜索**](app-insights-visual-studio.md)<br/>搜索和筛选事件，例如请求、异常、依赖项调用、日志跟踪和页面视图。 在 Visual Studio 中，从堆栈跟踪转到代码。 |![Visual Studio](./media/app-insights-asp-net-more/61.png) |
| [**聚合数据的指标资源管理器**](app-insights-metrics-explorer.md)<br/>浏览、筛选和细分聚合的数据，例如请求率、故障率和异常率；响应时间、页面加载时间。 |![Visual Studio](./media/app-insights-asp-net-more/060.png) |
| [**仪表板**](app-insights-dashboards.md#dashboards)<br/>混合使用来自多个资源的数据并与他人共享。 对于多组件应用程序和在团队聊天室中连续显示很有用。 |![仪表板示例](./media/app-insights-asp-net-more/62.png) |
| [**实时指标流**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>部署新的生成时，观看这些准实时性能指示器，确保一切按预期工作。 |![分析示例](./media/app-insights-asp-net-more/050.png) |
| [**分析**](app-insights-analytics.md)<br/>使用此功能强大的查询语言，回答有关应用的性能和使用情况的疑难问题。 |![分析示例](./media/app-insights-asp-net-more/010.png) |
| [**自动和手动警报**](app-insights-alerts.md)<br/>当某些内容处于异常模式时，自动警报适应你的应用的遥测和触发器正常模式。 还可以在自定义或标准指标的特定级别上设置警报。 |![警报示例](./media/app-insights-asp-net-more/020.png) |

## <a name="data-management"></a>数据管理
|  |  |
| --- | --- |
| [**连续导出**](app-insights-export-telemetry.md)<br/>将所有遥测复制到存储中，以便你可以通过自己的方式分析它。 | |
| **数据访问 API**<br/>即将推出。 | |
| [**采样**](app-insights-sampling.md)<br/>降低数据速率，并帮助你保持在定价层的限制内。 |![采样磁贴](./media/app-insights-asp-net-more/030.png) |




<!--HONumber=Dec16_HO3-->


