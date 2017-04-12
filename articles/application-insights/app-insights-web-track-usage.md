---
title: "使用 Application Insights 的 Web 应用程序使用情况分析"
description: "使用 Application Insights 的 Web 应用使用情况分析概述"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/12/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 8e32014147c322e09af08e5c05d83aea13041a4c
ms.lasthandoff: 04/07/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>使用 Application Insights 的 Web 应用程序使用情况分析
通过了解用户如何使用应用程序，从而专注于他们最重视的方案的开发工作，并深入了解他们发现更容易或更难实现的目标。 

Azure Application Insights 提供两个级别的使用情况跟踪：

* **用户、会话和页面视图数据** - 开箱即提供。  
* **自定义遥测** - [编写代码][api]用于跟踪用户的应用体验。 

## <a name="setting-up"></a>设置
在 [Azure 门户](https://portal.azure.com)中打开 Application Insights 资源，单击空的浏览器页面负载图，然后按照安装说明操作。

[了解详细信息](app-insights-javascript.md) 

## <a name="how-popular-is-my-web-application"></a>我的 Web 应用程序有多热门？
登录到 [Azure 门户][portal]，浏览到你的应用程序资源，然后单击“使用情况”：

![](./media/app-insights-web-track-usage/14-usage.png)

* **用户：**图表的时间范围内不同活动用户的计数。 
* **会话：**活动会话的计数
* **页面视图** 计算对 trackPageView() 的调用数（通常在每个网页中调用一次）。

单击任意图表查看更多详细信息。 请注意，可更改图表的时间范围。

### <a name="where-do-my-users-live"></a>我的用户居住在哪里？
从使用情况边栏选项卡，单击“用户”图表查看更多详细信息：

![在“使用情况”边栏选项卡中，单击“用户”图表](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>他们使用哪些浏览器或操作系统？
按浏览器、操作系统或城市等属性对数据进行分组（分段）：

![选择显示单个指标的图表、在“分组”上切换，然后选择属性](./media/app-insights-web-track-usage/03-browsers.png)

## <a name="sessions"></a>会话
会话是 Application Insights 中的基本概念，致力于将每个遥测事件（如请求、页面视图、异常或自行编码的自定义事件）与特定用户会话关联起来。 

收集关于每个会话的丰富上下文信息，如设备特征、地理位置、操作系统等。

如果同时检测客户端和服务器（[ASP.NET][greenbrown] 或 [J2EE][java]），SDK 将在客户端和服务器之间传播会话 ID，以便将两端的事件关联起来。

[诊断问题][diagnostic]时，可查找与出现问题的会话相关的所有遥测，包括所有请求以及记录的任何事件、异常或跟踪。

会话提供了上下文受欢迎程度的良好测量方式，如按设备、操作系统或位置。 例如，与计算页面视图相比，通过显示按设备分组的会话计数，可获取为应用使用该设备的频率的更准确计数。 对于任何特定于设备的问题的会审，这都将是有用的输入。

#### <a name="whats-a-session"></a>什么是会话？
会话表示用户和应用之间的一次相遇。 在最简单的形式中，会话从用户启动应用开始，在用户离开应用时结束。 对于 Web 应用，默认情况下，会话在处于非活动状态 30 分钟后或处于活动状态 24 小时后终止。 

可通过编辑代码片段修改这些默认值：

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });
    </script>

* `sessionRenewalMs`：会话由于用户的非活动状态而过期的时间，以毫秒为单位。 默认值：30 分钟。
* `sessionExpirationMs`：最大会话长度，以毫秒为单位。 如果用户在此时间后保持活动状态，将算作另一个会话。 默认值：24 小时。

**会话持续时间**是记录会话的第一个和最后一个遥测项之间的时间范围的[指标][metrics]。 （它不包括超时期限。）

采用特定间隔的**会话计数**定义为在此间隔期间具有某些活动的唯一会话数。 查看较长的时间范围（如上周的每日会话计数）时，这通常等效于会话总数。 

但是，探索较短的时间范围（如每小时粒度）时，对于跨多个小时的长时间会话，该会话处于活动状态的每个小时都将计数。 

## <a name="users-and-user-counts"></a>用户和用户计数
每个用户会话都与一个唯一用户 ID 相关联。 

默认情况下，通过放置 cookie 标识用户。 使用多个浏览器或设备的用户将多次计数。 （但是，请参阅[经过身份验证的用户](#authenticated-users)）

采用特定间隔的**用户计数**指标定义为在此间隔期间具有记录活动的唯一用户数。 因此，将时间范围设置为使粒度小于一小时左右时，具有长时间会话的用户可能多次计数。

**新用户**计算在此间隔期间首次与应用发生会话的用户。 如果使用了按 cookie 计算用户的默认方法，则这还将包括已清除其 cookie 或正在使用新设备或浏览器首次访问应用的用户。
![从使用情况边栏选项卡，单击“用户”图表检查“新用户”。](./media/app-insights-web-track-usage/031-dual.png)

### <a name="authenticated-users"></a>经过身份验证的用户
如果 Web 应用允许用户登录，则可通过向 Application Insights 提供唯一用户标识符获取更准确的计数。 它不一定是他们的名称或在应用中使用的相同 ID。 应用标识用户后，请立即使用此代码：

*在客户端使用 JavaScript*

      appInsights.setAuthenticatedUserContext(userId);

如果应用将用户分组到各个帐户，则还可以传递帐户的标识符。 

      appInsights.setAuthenticatedUserContext(userId, accountId);

用户和帐户 ID 不得包含空格或字符 `,;=|`

在[指标资源管理器](app-insights-metrics-explorer.md)中，可创建**经身份验证的用户**和**帐户**的图表。 

## <a name="synthetic-traffic"></a>综合流量
综合流量包括来自可用性和负载测试、搜索引擎爬网程序和其他代理的请求。 

Application Insights 致力于自动确定和分类综合流量，并对其进行相应的标记。 在大多数情况下，综合流量不会调用 JavaScript SDK，以便将此活动从用户和会话计数中排除。 

但是，对于 Application Insights [Web 测试][availability]，根据 POP 位置自动设置用户 ID，根据测试运行 ID 设置会话 ID。 在默认报表中，默认筛出综合流量，这将排除这些用户和会话。 但是，当包含综合流量时，它可能导致总体用户和会话计数小幅增加。

## <a name="page-usage"></a>页面使用情况
在页面视图中多次单击获取更加放大的版本以及最热门页面的细分：

![从“概述”边栏选项卡单击页面视图图表](./media/app-insights-web-track-usage/05-games.png)

上面的示例取自某个游戏网站。 通过该示例，我们可以立即看到：

* 使用情况在上周未改善。 或许我们应该考虑搜索引擎优化？
* 查看游戏页面的人数比查看主页的人数少很多。 为什么我们的主页不能吸引用户来玩游戏？
* “Crossword”是最受欢迎的游戏。 在这里，我们应优先考虑新的想法和改进。

## <a name="custom-tracking"></a>自定义跟踪
假设你决定将游戏全都重构到同一单页应用并在网页中将大多数功能编码为 Javascript，而不是在单个网页中实现每个游戏。 这将允许用户在游戏之间快速切换，甚至在一个页面上显示多个游戏。 

但如果仍希望 Application Insights 在每个游戏打开时记录次数，并采用与它们位于单个网页上时完全相同的方式。 这很简单，只需将对遥测模块的调用插入到 JavaScript 中要记录新“页面”打开的位置即可：

    appInsights.trackPageView(game.Name);

## <a name="custom-events"></a>自定义事件
编写自定义遥测以记录特定事件。 尤其在单页应用中，将需要知道用户指定特定操作或实现某些目标的频率： 

    appInsights.trackEvent("GameEnd");

例如，记录单击链接的操作：

    <a href="target.htm" onclick="appInsights.trackEvent('linkClick');return true;">my link</a>


## <a name="view-counts-of-custom-events"></a>查看自定义事件计数
打开指标资源管理器并添加图标以显示“事件”。 按名称分段：

![选择仅显示一个指标的图表。 在“分组”上切换。 选择属性。 并非所有属性都可用。](./media/app-insights-web-track-usage/06-eventsSegment.png)

## <a name="drill-into-specific-events"></a>钻取到特定事件
若要更好地了解典型会话的进展，可能需要关注包含特定事件类型的特定用户会话。 

在此示例中，我们已编码自定义事件“NoGame”，即用户注销而未实际启动游戏时调用的事件。 用户为何需要执行此操作？ 假如钻取到某些特定匹配项，将获得提示。 

从应用接收的自定义事件将按名称在“概述”边栏选项卡上列出：

![在“概述”边栏选项卡上，单击其中一个自定义事件类型。](./media/app-insights-web-track-usage/07-clickEvent.png)

单击感兴趣的事件并选择最近的特定匹配项：

![在摘要图表下的列表中，单击某个事件](./media/app-insights-web-track-usage/08-searchEvents.png)

看一下发生这一特定 NoGame 事件的会话中的所有遥测。 

![单击“会话的所有遥测”](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

没有任何异常，因此没有以某些故障形式阻止用户玩游戏。

我们可以筛选出此会话所有类型的遥测，页面视图除外：

![](./media/app-insights-web-track-usage/10-filter.png)

现在，我们可以看到此用户登录只是为了查看最新分数。 也许我们应考虑开发用户情景，以便于执行该操作。 （我们应实现自定义事件以在出现此特定情景时报告。）

## <a name="filter-search-and-segment-your-data-with-properties"></a>使用属性对数据进行筛选、搜索和分段
可以向事件附加任意标记和数值。

*在客户端使用 JavaScript*

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

*在客户端使用 JavaScript*

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

## <a name="a--b-testing"></a>A | B 测试
如果不知道哪个功能变体将更有成效，可同时发布这两项，使不同用户都能访问它们。 评估每个项的成效，然后移至统一的版本。

对于此技术，可将不同标记附加到应用的每个版本所发送的所有遥测。 为此，可以在活动的 TelemetryContext 中定义属性。 这些默认属性将添加到应用程序发送的每个遥测消息（并非仅自定义消息），不过标准遥测也是这样。 

在 Application Insights 门户中，将能够基于标记筛选和分组（分段）数据，以便比较不同的版本。

*在服务器上使用 C#*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*在服务器上使用 VB*

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

## <a name="references"></a>参考
* [使用 API - 概述][api]
* [JavaScript API 参考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md



