---
title: 搜索流量分析遥测
titleSuffix: Azure Cognitive Search
description: 为 Azure 认知搜索启用搜索流量分析，使用 Application Insights 收集遥测数据和用户启动的事件，然后在 Power BI 报告中对结果进行分析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128071"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>收集搜索流量分析的遥测数据

搜索流量分析是一种用于收集与 Azure 认知搜索应用程序进行用户交互的遥测数据的模式，例如用户启动的单击事件和键盘输入。 使用此信息，你可以确定搜索解决方案的有效性，包括热门搜索词、点击率以及哪些查询输入产生了零个结果。

此模式依赖于 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)（[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) 的一项功能）来收集用户数据。 它要求您向客户端代码添加检测，如本文所述。 最后，你将需要一个报告机制来分析数据。 建议 Power BI，但你可以使用应用程序仪表板或连接到 Application Insights 的任何工具。

> [!NOTE]
> 本文中所述的模式适用于你添加到客户端的代码所生成的高级方案和点击流数据。 与此相反，服务日志易于设置，提供一系列指标，可在门户中完成，无需任何代码。 对于所有方案，建议启用日志记录。 有关详细信息，请参阅[收集和分析日志数据](search-monitor-logs.md)。

## <a name="identify-relevant-search-data"></a>标识相关的搜索数据

若要为搜索流量分析提供有用的指标，必须从搜索应用程序的用户记录一些信号。 这些信号表示用户感兴趣的内容以及他们认为相关的内容。 对于搜索流量分析，其中包括：

+ 用户生成的搜索事件：仅用户启动的搜索查询是很有趣的。 用于填充 Facet、附加内容或任何内部信息的搜索查询都不重要，它们会扭曲结果并造成结果有偏差。

+ 用户生成的单击事件：在 "搜索结果" 页上，单击事件通常表示某个文档是特定搜索查询的相关结果。

通过链接搜索并单击关联 ID 的事件，可以更深入地了解应用程序搜索功能的执行情况。

## <a name="add-search-traffic-analytics"></a>添加搜索流量分析

在 Azure 认知搜索服务的[门户](https://portal.azure.com)页中，“搜索流量分析”页面包含一个用于遵循此遥测模式的速查表。 在此页上，您可以选择或创建一个 Application Insights 资源，获取检测密钥，可以为您的解决方案改编的复制代码片段，并下载在该模式中反映的架构上构建的 Power BI 报表。

![在门户中搜索流量分析页面](media/search-traffic-analytics/azuresearch-trafficanalytics.png "在门户中搜索流量分析页面")

## <a name="1---set-up-application-insights"></a>1-设置 Application Insights

选择现有 Application Insights 资源或[创建一个](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)资源（如果尚未创建）。 如果使用 "搜索流量分析" 页，则可以复制应用程序连接到 Application Insights 所需的检测密钥。

拥有 Application Insights 资源后，可以按照[支持的语言和平台注册应用程序的说明进行](https://docs.microsoft.com/azure/azure-monitor/app/platforms)操作。 注册只是将 Application Insights 中的检测密钥添加到代码，从而设置关联。 选择现有资源后，可以在门户中找到该密钥，或从 "搜索流量分析" 页中找到该密钥。

以下步骤反映了适用于某些 Visual Studio 项目类型的快捷方式。 只需单击几下鼠标，即可创建资源并注册应用。

1. 对于 Visual Studio 和 ASP.NET 开发，请打开解决方案，然后选择 "**项目** > " "**添加 Application Insights 遥测**"。

1. 单击“开始”。****

1. 提供 Microsoft 帐户、Azure 订阅和 Application Insights 资源来注册应用（默认值为新资源）。 单击“注册”  。

此时，你的应用程序已设置为应用程序监视，这意味着所有页面加载都将用默认指标进行跟踪。 有关上述步骤的详细信息，请参阅[Enable Application Insights server 端遥测](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)。

## <a name="2---add-instrumentation"></a>2 - 添加检测

在此步骤中，将使用以上步骤中创建的 Application Insights 资源检测自己的搜索应用程序。 从创建遥测客户端开始，此过程包含四个步骤。

### <a name="step-1-create-a-telemetry-client"></a>步骤1：创建遥测客户端

创建一个对象，用于将事件发送到 Application Insights。 可以向服务器端应用程序代码或在浏览器中运行的客户端代码添加检测，此处以 c # 和 JavaScript 变量表示（对于其他语言，请参阅[支持的平台和框架](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)的完整列表。 选择提供所需信息深度的方法。

服务器端遥测在应用程序层捕获度量值，例如，在作为云中的 web 服务运行的应用程序中或作为公司网络上的本地应用程序。 服务器端遥测捕获搜索并单击事件、文档在结果中的位置和查询信息，但数据收集的作用域为该层上提供的任何信息。

在客户端上，你可能具有操作查询输入、添加导航或包括上下文（例如，从主页和产品页面启动的查询）的附加代码。 如果此描述你的解决方案，你可以选择客户端检测，以便遥测反映更多详细信息。 如何收集此附加详细信息超出了此模式的范围，但你可以查看[网页 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data)以获得更多方向。 

**使用 C#**

对于 c #，如果你的项目为 ASP.NET，则在应用程序配置中找到**InstrumentationKey** ，例如 appsettings。 如果你不确定密钥位置，请参阅回注册说明。

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**使用 JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>步骤2：请求相关的搜索 ID

为了将搜索请求与单击相关联，必须具有一个将这两个不同事件关联起来的相关性 ID。 当你使用 HTTP 标头请求时，Azure 认知搜索会为你提供搜索 ID。

如果使用搜索 ID，则可以将 Azure 认知搜索发出的指标与你要 Application Insights 在其中进行日志记录的自定义指标相关联。  

**使用 C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**使用 JavaScript （调用 REST Api）**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>步骤3：日志搜索事件

每次用户发出搜索请求时，您都应该使用以下架构作为搜索事件记录 Application Insights 自定义事件。 请记住，只记录用户生成的搜索查询。

+ **SearchServiceName**：（string）搜索服务名称
+ **SearchId**：（guid）搜索查询的唯一标识符（位于搜索响应中）
+ **IndexName**：（string）要查询的搜索服务索引
+ **QueryTerms**：（string）用户输入的搜索词
+ **Objectqueryoptions**：（int）返回的文档数（在搜索响应中）
+ **ScoringProfile**：（string）使用的计分配置文件的名称（如果有）

> [!NOTE]
> 通过向搜索查询添加 $count = true 来请求用户生成查询的计数。 有关详细信息，请参阅[搜索文档（REST）](/rest/api/searchservice/search-documents#counttrue--false)。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**使用 JavaScript**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>步骤4：记录单击事件

每次用户单击文档，都是一个必须记录以用于搜索分析的信号。 使用 Application Insights 自定义事件可利用下面的架构来记录这些事件：

+ **ServiceName**：（string）搜索服务名称
+ **SearchId**：（guid）相关搜索查询的唯一标识符
+ **DocId**：（string）文档标识符
+ **位置**：（int）文档在 "搜索结果" 页中的排名

> [!NOTE]
> Position 指的是应用程序中的基数顺序。 可以随意设置此数字以用于比较，只要它始终相同。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**使用 JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - 在 Power BI 中进行分析

检测到应用并确认应用程序已正确连接到 Application Insights 后，下载一个预定义的报表模板以在 Power BI Desktop 中分析数据。 该报告包含预定义的图表和表，它们可用于分析为搜索流量分析捕获的其他数据。

1. 在 Azure 认知搜索仪表板左侧导航窗格中，在“设置”**** 下，单击“搜索流量分析”****。

1. 在“搜索流量分析”**** 页面上，在步骤 3 中，单击“获取 Power BI Desktop”**** 以安装 Power BI。

   ![获取 Power BI 报表](./media/search-traffic-analytics/get-use-power-bi.png "获取 Power BI 报表")

1. 在同一页面上，单击“下载 Power BI 报表”****。

1. 该报表将在 Power BI Desktop 中打开，并且会提示你连接到 Application Insights 并提供凭据。 可以在你的 Application Insights 资源的 Azure 门户页面中找到连接信息。 对于凭据，请提供用于门户登录的相同用户名和密码。

   ![连接到 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "连接到 Application Insights")

1. 单击“加载”。****

该报表包含图表和表，可帮助你做出更明智的决策来提高搜索性能和相关性。

指标包括以下各项：

+ 搜索量和最常用术语-文档对：导致同一文档被单击的词，按单击次数排序。
+ 无单击的搜索：查询次数最多但未记录任何单击的词

以下屏幕截图显示了在使用了所有架构元素的情况下，内置报表的外观。

![用于 Azure 认知搜索的 Power BI 仪表板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "用于 Azure 认知搜索的 Power BI 仪表板")

## <a name="next-steps"></a>后续步骤

检测搜索应用程序，以获取提供深入见解的有关搜索服务的强大数据。

你可以查找有关 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 的更多信息并访问[定价页面](https://azure.microsoft.com/pricing/details/application-insights/)来详细了解其各种服务层级。

了解有关创建出色报告的详细信息。 有关详细信息，请参阅[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)入门。