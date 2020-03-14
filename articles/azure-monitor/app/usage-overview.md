---
title: 使用 Azure Application Insights 进行使用情况分析 | Microsoft docs
description: 了解用户，以及他们将应用用于哪些目的。
ms.topic: conceptual
ms.date: 09/19/2019
ms.openlocfilehash: 9f34267a1820f8b2365a41569bd3c8eaed9f2f9c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275641"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights 使用分析

Web 或移动应用有哪些最热门的功能？ 用户是否使用应用实现了其目标？ 他们是否中途放弃应用，后来又回头使用了吗？  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 可帮助你有效地深入分析人们如何使用应用。 每次更新应用时，都可以评估它在用户那里的运行状况。 了解这些信息后，可以针对下一个开发周期做出数据驱动的决策。

## <a name="send-telemetry-from-your-app"></a>从应用发送遥测数据

通过在应用服务器代码和网页中安装 Application Insights 来获得最佳体验。 应用的客户端和服务器组件将遥测发送回 Azure 门户进行分析。

1. **服务器代码：** 为 [ASP.NET](../../azure-monitor/app/asp-net.md)、[Azure](../../azure-monitor/app/app-insights-overview.md)、[Java](../../azure-monitor/app/java-get-started.md)、[Node.js](../../azure-monitor/app/nodejs.md) 或[其他](../../azure-monitor/app/platforms.md)应用安装适当的模块。

    * *不想安装服务器代码？只需[创建一个 Azure 应用程序 Insights 资源](../../azure-monitor/app/create-new-resource.md )。*

2. 网页**代码：** 将以下脚本添加到您的网页，然后关闭 ``</head>``。 将检测密钥替换为 Application Insights 资源的相应值：
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    若要了解有关监视网站的更多高级配置，请查看[JAVASCRIPT SDK 参考文章](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

3. **移动应用代码：** 通过[按照此指南操作](../../azure-monitor/learn/mobile-center-quickstart.md)，使用 App Center SDK 收集应用中的事件，然后将这些事件的副本发送到 Application Insights 进行分析。

4. **获取遥测：** 在调试模式下运行项目几分钟，并在“Application Insights”中的“概述”边栏选项卡中查找结果。

    发布应用以监视应用性能，并查看用户使用该应用在执行哪些操作。

## <a name="include-user-and-session-id-in-your-telemetry"></a>在遥测中包括用户和会话 ID
若要持续跟踪用户，Application Insights 需要识别用户的方法。 事件工具是唯一不需要用户 ID 或会话 ID 的使用情况工具。

开始使用[此过程](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)发送用户 ID 和会话 ID。

## <a name="explore-usage-demographics-and-statistics"></a>浏览用户人口和统计信息
查明人们何时使用应用，他们对哪些页面最感兴趣，用户在哪里以及他们使用什么浏览器和操作系统。 

“用户和会话”报告按页面或自定义事件筛选数据，并按位置、环境和页面等属性将数据分段。 也可以添加自己的筛选器。

![用户](./media/usage-overview/users.png)  

右侧的见解指出了数据集中的相关模式。  

* “用户”报告统计所选时间段内访问页面的唯一用户数目。 对于 Web 应用，将使用 Cookie 统计用户。 如果某个用户使用不同的浏览器或客户端计算机访问站点或者清除了其 Cookie，该用户会被统计多次。
* “会话”报告统计访问站点的用户会话数。 会话是指某个用户的活动时段，如果有半个小时以上处于非活动状态，会话会被终止。

[有关用户、会话和事件工具的详细信息](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>保留 - 有多少个回头用户？

保留可帮助你根据特定时间桶内执行某个业务操作的用户队列，了解用户回头使用其应用的频率。 

- 了解哪些特定的功能导致某些用户比其他用户回来得更频繁 
- 基于真实的用户数据构成假设 
- 确定产品中是否存在保留问题 

![保留](./media/usage-overview/retention.png) 

使用顶部的保留控件可以定义特定的事件和时间范围来计算保留。 中间的图表根据指定的时间范围提供总体保留百分比的视觉表示形式。 底部的图表显示给定时间段内的各个保留。 这种详细程度可让你更细致地了解用户正在做什么，以及哪些因素可能会影响用户回头。  

[有关保留工具的详细信息](usage-retention.md)

## <a name="custom-business-events"></a>自定义业务事件

要明确了解用户将应用用于什么目的，可以插入代码行来记录自定义事件。 这些事件可以跟踪任何活动，包括详细的用户操作（例如单击特定的按钮），以及更重要的业务活动（例如购买活动或游戏获胜）。 

尽管在某些情况下页面视图可呈现有用的事件，但一般情况下这些事件并不真实。 用户无需购买产品即可打开产品页面。 

使用特定的业务事件可以绘制用户在站点中的进度图表。 可以了解用户对不同选项的偏好，以及他们在哪个位置放弃了应用或者遇到了问题。 了解这些信息后，可以针对开发积压工作的优先级做出明智的决策。

可以在应用的客户端记录事件：

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

也可以在服务器端进行记录：

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

可将属性值附加到这些事件，以便在门户检查事件时可以筛选或拆分事件。 此外，已将一组标准属性（例如匿名用户 ID）附加到每个事件，以便你跟踪单个用户的活动序列。

详细了解[自定义事件](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)和[属性](../../azure-monitor/app/api-custom-events-metrics.md#properties)。

### <a name="slice-and-dice-events"></a>分解事件

在“用户”、“会话”和“事件”工具中，可按用户、事件名称和属性分解自定义事件。
![用户](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>在应用中设计遥测

设计应用的每项功能时，请考虑如何衡量它在用户那里取得的成功。 决定需要记录哪些事件，并一开始就在应用中针对这些事件编写跟踪调用。

## <a name="a--b-testing"></a>A | B 测试
如果不知道哪个功能变体将更有成效，可同时发布这两项，使不同用户都能访问它们。 评估每个项的成效，并移至统一的版本。

对于此技术，可将不同的属性值附加到应用的每个版本所发送的所有遥测数据。 为此，可以在活动的 TelemetryContext 中定义属性。 这些默认属性将添加到应用程序发送的每个遥测消息（并非仅自定义消息），不过标准遥测也是这样。

在 Application Insights 门户中根据属性值筛选和拆分数据，以便比较不同的版本。

为此，请[设置遥测初始值设定项](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)：

**ASP.NET 应用**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

在 Global.asax.cs 等 Web 应用初始值设定项中：

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core 应用**

> [!NOTE]
> 使用 `ApplicationInsights.config` 或使用 `TelemetryConfiguration.Active` 添加初始值设定项对于 ASP.NET Core 应用程序无效。 

对于[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers)应用程序，通过将新 `TelemetryInitializer` 添加到依赖关系注入容器来添加新，如下所示。 这是在 `Startup.cs` 类 `ConfigureServices` 方法中完成的。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

所有新的 TelemetryClient 会自动添加指定的属性值。 单个遥测事件可以替代默认值。

## <a name="next-steps"></a>后续步骤
   - [用户、会话、事件](usage-segmentation.md)
   - [漏斗图](usage-funnels.md)
   - [保留](usage-retention.md)
   - [用户流](usage-flows.md)
   - [工作簿](../../azure-monitor/app/usage-workbooks.md)
   - [添加用户上下文](usage-send-user-context.md)
