---
title: "使用 Application Insights 的 Web 应用程序使用情况分析 | Microsoft docs"
description: "Application Insights 使用情况分析概述"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>使用 Application Insights 的 Web 应用程序使用情况分析
通过了解用户如何使用应用程序，从而将开发工作集中在对用户最重要的方案的开发中，并深入了解他们认为更容易或更难实现的目标。

[Azure Application Insights](app-insights-overview.md) 提供两个级别的使用情况跟踪：

* **用户、会话和页面视图数据** - 开箱即提供。  
* **自定义遥测** - [编写代码](app-insights-api-custom-events-metrics.md)，通过应用的用户体验对用户进行跟踪。 


## <a name="get-started"></a>入门

通过在应用服务器代码和网页中安装 Application Insights 来获得最佳体验。 应用的客户端和服务器组件将遥测发送回 Azure 门户进行分析。

1. **服务器代码：**为 [ASP.NET](app-insights-asp-net.md)、[Azure](app-insights-azure.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md) 或[其他](app-insights-platforms.md)应用安装适当的模块。

    * 不想安装服务器代码？只需[创建 Azure Application Insights 资源](app-insights-create-new-resource.md)。

2. **网页代码：**打开 [Azure 门户](https://portal.azure.com)，然后打开应用的 Application Insights 资源，再依次打开“入门”>“监视和诊断客户端”。 

    ![将脚本复制到主网页的开头。](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **获取遥测：**在调试模式下运行项目几分钟，然后在“Application Insights”中的“概述”边栏选项卡中查找结果。

    发布应用以监视应用性能，并查看用户使用该应用在执行哪些操作。

## <a name="usage-analysis-out-of-the-box"></a>立即可用的使用情况分析
打开“使用情况”边栏选项卡。

![用户、会话和页面视图图表](./media/app-insights-web-track-usage/14-usage.png)

将空白部分的鼠标悬停在图表上，查看特定点处的计数。 否则，数字显示一段时间内聚合的值，例如一段时间内不同用户的平均数、总数或计数。

这些图表显示了哪些内容？

* **用户：**图表的时间范围内不同活动用户的计数。 在 Web 应用程序中，使用 Cookie 计算用户数。 对于使用多个浏览器、清除 Cookie 或使用隐私功能的用户，将多次计入。
* 如果在代码中插入了对 [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) 的调用，则会对**已经过身份验证的用户**进行计数。
* **会话：**活动会话的计数。 Web 会话处于非活动状态 30 分钟后将计入。 
* **页面视图** 计算对 trackPageView() 的调用数（通常在每个网页中调用一次）。

单击任意图表查看更多详细信息。 请注意，可更改图表的时间范围。

### <a name="where-do-my-users-live"></a>我的用户居住在哪里？
单击“用户”图表的空白部分，可打开显示更多详细信息的另一个边栏选项卡：

![在“使用情况”边栏选项卡中，单击“用户”图表](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>他们使用哪些浏览器或操作系统？

单击用户图表上的“编辑”，按浏览器、操作系统或城市等属性对数据进行分组（分段）： 

![选择显示单个指标的图表、在“分组”上切换，然后选择属性](./media/app-insights-web-track-usage/03-browsers.png)

若要查看完整的计数集，请将图表类型切换为“网格”。 还可以选择显示其他指标：

![多列网格图表](./media/app-insights-web-track-usage/multi-column-grid.png)

对于图形图表类型，可以按属性分组，也可以选择多个指标，但不能同时进行这两个操作。 该图表比较了两个指标，用户和[已经过身份验证的用户](app-insights-api-custom-events-metrics.md#authenticated-users)。 

![选择一个图表，搜索并选中或取消选中指标。](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>页面视图
在“使用情况”边栏选项卡中，单击页面视图获取更详实的版本以及最热门页面的细分：

![从“概述”边栏选项卡单击页面视图图表](./media/app-insights-web-track-usage/05-games.png)

上面的示例取自某个游戏网站。 从图表中可以清楚看到：

* 使用情况在上周未改善。 或许我们应该考虑搜索引擎优化？
* “Tennis”是最受欢迎的游戏页面。 我们重点看一看对此页面的进一步改进。
* 平均而言，用户访问“Tennis”页面的频率约为每周三次。 （会话次数大约是用户数的三倍。）
* 大多数用户在美国工作日和工作时间访问该网站。 或许我们应该在网页上提供一个“快速隐藏”按钮。
* 图表上的[注释](app-insights-annotations.md)显示部署网站新版本的时间。 最近的部署没有一个对使用情况有明显的影响。

## <a name="custom-tracking"></a>自定义跟踪
假设你决定将游戏全都重构到同一单页应用并在网页中将大多数功能编码为 Javascript，而不是在单个网页中实现每个游戏。 这将允许用户在游戏之间快速切换，甚至在一个页面上显示多个游戏。

但如果仍希望 Application Insights 在每个游戏打开时记录次数，并采用与它们位于单个网页上时完全相同的方式。 这很简单，只需将对遥测模块的调用插入到 JavaScript 中要记录新“页面”打开的位置即可：

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
此调用模拟记录页面视图的遥测。  但是，你不希望总是将消息与页面视图混合在一起。 而使用自定义事件。 可以从网页或 Web 服务器发送它们：


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

[插入到网页或服务器代码中的自定义遥测](app-insights-api-custom-events-metrics.md#trackevent)能以多种方式用于了解应用程序的使用方式。

查看 TrackEvent() 发送的事件：打开指标资源管理器，添加新的图表，然后进行编辑。 指标将显示在“自定义指标”下。 

![显示自定义事件的图表。](./media/app-insights-web-track-usage/06-eventsSegment.png)

如果在事件中设置了[属性值](app-insights-api-custom-events-metrics.md#properties)（也称为维度），则可以按这些值进行分组和筛选。

创建多个图表以关联其他指标和事件中的更改。 例如，如果玩了多款游戏，预计已放弃的游戏数也呈上升趋势。 但是已放弃游戏数的上升不成比例，你希望找到是否是高负载导致用户觉得不可接受的问题。

## <a name="drill-into-specific-events"></a>钻取到特定事件
若要更好地了解典型会话的进展，可能需要关注包含特定事件类型的特定用户会话。

在事件网格中，单击感兴趣的事件并选择最近的特定匹配项：

![在摘要图表下的列表中，单击某个事件](./media/app-insights-web-track-usage/08-searchEvents.png)

看一下发生这一特定 NoGame 事件的会话中的所有遥测。

![单击“会话的所有遥测”](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

没有任何异常，因此没有以某些故障形式阻止用户玩游戏。

我们可以筛选出此会话所有类型的遥测，页面视图除外：

![](./media/app-insights-web-track-usage/10-filter.png)

现在，我们可以看到此用户登录只是为了查看最新分数。 也许我们应考虑开发用户情景，以便于执行该操作。 （我们应实现自定义事件以在出现此特定情景时报告。）

## <a name="filter-search-and-segment-your-data-with-properties"></a>使用属性对数据进行筛选、搜索和分段
可以向事件附加任意标记和数值。

*网页中的 JavaScript*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*在服务器上使用 C#*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*在服务器上使用 VB*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

以相同方式将属性附加到页面视图：

*网页中的 JavaScript*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

在诊断搜索中，通过单击单个事件匹配项查看属性。

![在事件列表中，打开某个事件，然后单击“...” 查看更多属性](./media/app-insights-web-track-usage/11-details.png)

使用“搜索”字段查看具有特定属性值的事件匹配项。

![在“搜索”字段中键入一个值](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>通过遥测编辑和创建查询

单击任意图表上的 Azure 分析图标，打开可编辑的等效查询。
向下滚动，查看是否有多个生成的查询。 将光标放在任意查询中，单击“转到”。 

或者，从“概览”边栏选项卡中的图标打开“分析”，并编写自己的查询，或在“分析主页”选项卡上尝试一些示例查询。


![具有生成查询的分析窗口](./media/app-insights-web-track-usage/open-analytics.png)

[深入了解 Azure 分析查询语言](app-insights-analytics.md)。

对于使用情况分析，能对这些表特别感兴趣：

* `customEvents` - [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) 调用的结果。
* `pageViews` - 客户端浏览器中打开的页面数，或对 [trackPageView()](app-insights-api-custom-events-metrics.md#page-views) 的调用数。


## <a name="a--b-testing"></a>A | B 测试
如果不知道哪个功能变体将更有成效，可同时发布这两项，使不同用户都能访问它们。 评估每个项的成效，然后移至统一的版本。

对于此技术，可将不同标记附加到应用的每个版本所发送的所有遥测。 为此，可以在活动的 TelemetryContext 中定义属性。 这些默认属性将添加到应用程序发送的每个遥测消息（并非仅自定义消息），不过标准遥测也是这样。

在 Application Insights 门户中，将能够基于标记筛选和分组（分段）数据，以便比较不同的版本。


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

单个遥测可能会覆盖默认值。

可以设置通用初始值设定项，以便所有新的 TelemetryClient 自动使用上下文。

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

在应用初始值设定项（如 Global.asax.cs）中：

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>生成 - 衡量 - 学习
当使用分析时，它将成为开发周期的集成部分，而不仅是考虑帮助解决问题的某些内容。 下面是一些提示：

* 确定应用程序的关键指标。 是需要尽可能多的用户，还是更倾向于一小部分满意度很好的用户？ 是否希望最大化访问量或销量？
* 计划衡量每个情景。 当草拟新的用户情景或功能，或者计划更新现有内容时，应始终考虑将如何衡量更改的成效。 在编码开始前，系统会询问“如果可行的话，这将对我们的指标产生什么影响？ 我们是否应跟踪任何新的事件？”
  当然，如果功能是实时功能，请确保查看分析并对结果进行操作。
* 将其他指标与关键指标相关。 例如，如果添加“收藏夹”功能，需要知道用户添加收藏夹的频率。 但或许了解他们返回到收藏夹的频率会更有趣。 而最重要的是，使用收藏夹的客户是否最终会购买更多产品？
* Canary 测试。 设置功能切换，使得仅向某些用户显示新功能。 使用 Application Insights 查看是否按设想的方式使用新功能。 进行一些调整，然后面向更大范围的受众发布它。
* 与用户沟通！ 仅依靠分析还不够，还需要补充才能维护良好的客户关系。

## <a name="learn-more"></a>了解详细信息
* [检测、会审和诊断应用中的崩溃和性能问题](app-insights-detect-triage-diagnose.md)
* [多个平台上的 Application Insights 入门](app-insights-detect-triage-diagnose.md)
* [使用 API - 概述](app-insights-api-custom-events-metrics.md)
* [JavaScript API 参考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



