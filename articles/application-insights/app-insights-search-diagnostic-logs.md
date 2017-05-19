---
title: "Azure Application Insights 中的 ASP.NET 日志和诊断 | Microsoft Docs"
description: "通过搜索使用 Trace、NLog 或 Log4Net 生成的请求、异常和日志诊断 ASP.NET Web 应用中的问题。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 99860c53-0324-4a3a-9aa9-83f5dffba835
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/08/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 2f84cc336a27697819fe7bd6720e4ca5765f9283
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="logs-exceptions-and-custom-diagnostics-for-aspnet-in-application-insights"></a>Application Insights 中 ASP.NET 的日志、异常和自定义诊断
[Application Insights][start] 包含功能强大的[诊断搜索][diagnostic]工具，可使用户从应用程序探索并钻取到 Application Insights SDK 发送的遥测。 许多事件（如用户页面视图）由 SDK 自动发送。

还可编写代码以发送自定义事件、异常、报表和跟踪。 如果已经使用 log4J、log4net、NLog 或 System.Diagnostics.Trace 之类的记录框架，则可以捕获这些日志并将它们包含在搜索中。 这使将日志跟踪与用户操作、异常和其他事件关联起来更容易。

## <a name="send"></a>编写自定义遥测前
如果尚未[为项目设置 Application Insights][start]，请立即执行该操作。

运行应用程序时，它将发送某些将在“诊断搜索”中显示的遥测，包括服务器接收的请求、在客户端记录的页面视图和未捕获的异常。

打开“诊断搜索”查看 SDK 自动发送的遥测。

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

详细信息因应用程序类型而异。 可单击任何单个事件获取更多详细信息。

## <a name="sampling"></a>采样
如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则自适应采样功能可以正常运行，只发送一部分遥测数据。 [了解有关采样的详细信息。](app-insights-sampling.md)

## <a name="events"></a>自定义事件
自定义事件显示在[诊断搜索][diagnostic]和[指标资源管理器][metrics]中。 可从设备、网页和服务器应用程序发送它们。 它们可用于诊断目的和[了解使用模式][track]。

自定义事件具有名称，还可携带可与数字度量值一起作为过滤条件的属性。

客户端上使用 JavaScript

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

在服务器上使用 C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


在服务器上使用 VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### <a name="run-your-app-and-view-the-results"></a>运行应用并查看结果。
打开“诊断搜索”。

选择“自定义”事件并选择特定事件名称。

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)

通过在属性值上输入搜索词进一步筛选数据。  

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

钻取到单个事件查看其详细属性。

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

## <a name="pages"></a> 页面视图
页面视图遥测由[在网页中插入的 JavaScript 代码片段][usage]中的 trackPageView() 调用进行发送。 它的主要目标是构成页面上显示的页面视图计数。

通常在每个 HTML 页面中调用它一次，但可以插入更多调用，例如，如果你有一个单页应用，并且希望每当用户获取更多数据时都记录新页面。

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

附加可在诊断搜索中用作筛选器的属性有时很有用：

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});


## <a name="trace"></a> 跟踪遥测
跟踪遥测是专门插入用于创建诊断日志的代码。 

例如，可以插入如下调用：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


#### <a name="install-an-adapter-for-your-logging-framework"></a>为记录框架安装适配器
还可以搜索使用记录框架（log4Net、NLog 或 System.Diagnostics.Trace）生成的搜索日志。 

1. 如果计划使用 log4Net 或 NLog，请在项目中安装它。 
2. 在解决方案资源管理器中，右键单击项目并选择“管理 NuGet 包”。
3. 依次选择“联机”>“全部”、选择“包括预发行”并搜索“Microsoft.ApplicationInsights”
   
    ![获取相应适配器的预发行版本](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)
4. 选择相应的程序包 - 以下各项之一：
   
   * Microsoft.ApplicationInsights.TraceListener（用于捕获 System.Diagnostics.Trace 调用）
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

NuGet 包安装必要的程序集，并且还修改 web.config 或 app.config。

#### <a name="pepper"></a>插入诊断日志调用
如果使用 System.Diagnostics.Trace，则典型的调用将是：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果首选 log4net 或 NLog：

    logger.Warn("Slow response - database01");

在调试模式下运行应用，或者部署它。

选择跟踪筛选器时，将在“诊断搜索”中看到消息。

### <a name="exceptions"></a>异常
在 Application Insights 中获取异常报表可提供功能强大的体验，尤其是因为用户可以在失败的请求和异常之间导航，并读取异常堆栈。

在某些情况下，需要[插入一些代码行][exceptions]以确保自动捕获异常。

还可以编写显式代码以发送异常遥测：

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

属性和测量参数是可选的，但对于筛选和添加额外信息很有用。 例如，如果有一个可运行多个游戏的应用，可查找与特定游戏相关的所有异常报表。 可向每个字典添加任意数量的项目。

#### <a name="viewing-exceptions"></a>查看异常
你将看到在“概述”边栏选项卡上报告的异常摘要，并且可通过单击查看更多详细信息。 例如：

![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)[]

单击任意异常类型查看特定发生情况：

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)[]

还可以直接打开“诊断搜索”、筛选异常并选择要查看的异常类型。

### <a name="reporting-unhandled-exceptions"></a>报告未经处理的异常
在可能的情况下，Application Insights 会从设备、[web 浏览器][usage]或 Web 服务器报告未经处理的异常（无论是否由[状态监视器][redfield]或 [Application Insights SDK][greenbrown] 检测）。 

但是，在某些情况下它并非始终能够执行此操作，因为 .NET framework 会捕获异常。  因此，若要确保看到所有异常，必须编写一个小型异常处理程序。 最佳过程因技术而异。 有关详细信息，请参阅 [ASP.NET 的异常遥测][exceptions]。 

### <a name="correlating-with-a-build"></a>与版本关联
阅读诊断日志时，由于曾部署实时代码，源代码可能已发生更改。

因此，将版本信息（如当前版本的 URL）与每个异常或跟踪一起放到某个属性中很有用。 

可在默认上下文中设置信息，而不是将属性分别添加到每个异常调用。 

    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }

在应用初始值设定项（如 Global.asax.cs）中：

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }

### <a name="requests"></a> 服务器 Web 请求
[在 Web 服务器上安装状态监视器][redfield]或[将 Application Insights 添加到 Web 项目][greenbrown]时，将自动发送请求遥测。 它还发送到指标资源管理器和“概述”页面中的请求和响应时间图中。

如果要发送其他事件，可以使用 TrackRequest() API。

## <a name="questions"></a>问题解答
### <a name="emptykey"></a>我得到错误“检测密钥不能为空”
看起来你在未安装 Application Insights 的情况下安装了日志记录适配器 Nuget 包。

在解决方案资源管理器中，右键单击 `ApplicationInsights.config` 并选择“更新 Application Insights”。 你将得到一个对话框，邀请你登录 Azure 并创建 Application Insights 资源或重复使用现有资源。 这应当解决此问题。

### <a name="limits"></a>保留多少数据？
每个应用程序每秒最多保留 500 个事件。 事件保留七天。

### <a name="some-of-my-events-or-traces-dont-appear"></a>某些事件或跟踪未显示
如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则自适应采样功能可以正常运行，只发送一部分遥测数据。 [了解有关采样的详细信息。](app-insights-sampling.md)

## <a name="add"></a>后续步骤
* [设置可用性和响应能力测试][availability]
* [故障排除][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md



