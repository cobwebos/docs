---
title: 用于分析搜索流量的遥测数据
titleSuffix: Azure Cognitive Search
description: 为 Azure 认知搜索启用搜索流量分析，使用 Application Insights 收集遥测数据和用户启动的事件，然后在 Power BI 报告中对结果进行分析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: d93ced4b45befec207494909de61d30a98d2a67e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333726"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>收集遥测数据以用于分析搜索流量

搜索流量分析是一种用于收集遥测数据的模式，它收集有关用户与 Azure 认知搜索应用程序之间的交互（例如用户发起的单击事件和键盘输入）的遥测数据。 使用此信息，你可以确定搜索解决方案的有效性，包括热门搜索词、点击率以及哪些查询输入产生了零个结果。

此模式依赖于 [Application Insights](../azure-monitor/app/app-insights-overview.md)（[Azure Monitor](../azure-monitor/index.yml) 的一项功能）来收集用户数据。 你还需要向客户端代码中添加检测机制，如本文中所述。 最后，你将需要一个报告机制来分析数据。 建议使用 Power BI，但你可以使用应用程序仪表板或可连接到 Application Insights 的任何工具。

> [!NOTE]
> 本文中所述的模式适用于你添加到客户端的代码生成的高级方案和点击流数据。 相比之下，服务日志易于设置，提供各种指标，且无需编写任何代码即可在门户中操作。 建议对所有方案启用日志记录。 有关详细信息，请参阅[收集和分析日志数据](search-monitor-logs.md)。

## <a name="identify-relevant-search-data"></a>标识相关的搜索数据

若要为搜索流量分析提供有用的指标，必须记录搜索应用程序用户发出的一些信号。 这些信号表示用户感兴趣的内容以及他们认为相关的内容。 对于搜索流量分析，这些信号包括：

+ 用户生成的搜索事件：只有用户发起的搜索查询才是需要关注的。 用于填充 Facet、附加内容或任何内部信息的搜索查询都不重要，它们会扭曲结果并造成结果有偏差。

+ 用户生成的单击事件：在搜索结果页上，单击事件通常意味着某个文档是特定搜索查询的相关结果。

通过将搜索和单击事件链接到相关 ID，可以更深入地了解应用程序搜索功能的性能表现。

## <a name="add-search-traffic-analytics"></a>添加搜索流量分析

在 Azure 认知搜索服务的[门户](https://portal.azure.com)页中，“搜索流量分析”页面包含一个用于遵循此遥测模式的速查表。 在此页中，可以选择或创建一个 Application Insights 资源，获取检测密钥，复制可以根据你的解决方案改编的代码片段，并下载基于模式中反映的架构生成的 Power BI 报表。

![门户中的“搜索流量分析”页](media/search-traffic-analytics/azuresearch-trafficanalytics.png "门户中的“搜索流量分析”页")

## <a name="1---set-up-application-insights"></a>1 - 设置 Application Insights

选择现有的 Application Insights 资源，如果没有 Application Insights 资源，则[创建一个资源](../azure-monitor/app/create-new-resource.md)。 如果使用“搜索流量分析”页，则可以复制应用程序在连接到 Application Insights 时所需的检测密钥。

有了 Application Insights 资源后，可以按照[适用于受支持语言和平台的说明](../azure-monitor/app/platforms.md)来注册应用。 注册只是将 Application Insights 中的检测密钥添加到代码，以设置关联。 选择现有的资源时，可以在门户或“搜索流量分析”页中找到该密钥。

以下步骤反映了适用于某些 Visual Studio 项目类型的快捷方式。 只需单击几下鼠标，即可创建资源并注册应用。

1. 对于 Visual Studio 和 ASP.NET 开发，请打开你的解决方案，并选择“项目” > “添加 Application Insights 遥测” 。

1. 单击“开始”。

1. 通过提供 Microsoft 帐户、Azure 订阅和 Application Insights 资源（某个新资源是默认资源）来注册你的应用。 单击“注册”。

此时，你已经为应用程序设置了应用程序监视，这意味着，将使用默认指标跟踪所有页面加载。 有关上述步骤的详细信息，请参阅[启用 Application Insights 服务器端遥测](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)。

## <a name="2---add-instrumentation"></a>2 - 添加检测

在此步骤中，将使用以上步骤中创建的 Application Insights 资源检测自己的搜索应用程序。 此过程包括四个步骤，第一个步骤是创建遥测客户端。

### <a name="step-1-create-a-telemetry-client"></a>步骤 1：创建遥测客户端

创建一个用于将事件发送到 Application Insights 的对象。 可以将检测机制添加到服务器端应用程序代码或在浏览器中运行的客户端代码中，此处的代码以 C# 和 JavaScript 变体表示（对于其他语言，请参阅[支持的平台和框架](../azure-monitor/app/platforms.md)的完整列表）。 选择可提供所需信息深度的方法。

服务器端遥测将捕获应用程序层（例如，在云中作为 Web 服务运行的应用程序，或者在企业网络中作为本地应用运行的应用程序）的指标。 服务器端遥测捕获搜索和单击事件、结果中文档的位置以及查询信息，但数据收集范围将限定为该层上可用的任何信息。

在客户端上，可以使用附加的代码来操作查询输入、添加导航或包含上下文（例如，从主页或产品页发起的查询）。 如果此描述符合你的解决方案，则你可以选择启用客户端检测机制，使遥测数据反映附加的详细信息。 如何收集这些附加详细信息超出了此模式的范围，但你可以查看[适用于网页的 Application Insights](../azure-monitor/app/javascript.md#explore-browserclient-side-data) 来获取更多指导。 

**使用 C#**

对于 C#，如果项目是 ASP.NET，则可以在应用程序配置（例如 appsettings.json）中找到 InstrumentationKey。 如果你不确定密钥的位置，请再次参考注册说明。

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

### <a name="step-2-request-a-search-id-for-correlation"></a>步骤 2：请求用于关联的搜索 ID

为了将搜索请求与单击相关联，必须具有一个将这两个不同事件关联起来的相关性 ID。 使用 HTTP 标头请求搜索 ID 时，Azure 认知搜索将提供该 ID。

使用搜索 ID 可将 Azure 认知搜索为请求本身发出的指标关联到在 Application Insights 中记录的自定义指标。  

**使用 C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**使用 JavaScript（调用 REST API）**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>步骤 3：记录搜索事件

每当用户发出搜索请求时，应在 Application Insights 自定义事件上使用以下架构，将该请求记录为一个搜索事件。 请记得仅记录用户生成的搜索查询。

+ **SearchServiceName**：（字符串）搜索服务名称
+ **SearchId**：(GUID) 搜索查询的唯一标识符（位于搜索响应中）
+ **IndexName**：（字符串）要查询的搜索服务索引
+ **QueryTerms**：（字符串）用户输入的搜索字词
+ **ResultCount**：（整数）返回的文档数（位于搜索响应中）
+ **ScoringProfile**：（字符串）使用的评分配置文件的名称（如果有）

> [!NOTE]
> 通过向搜索查询添加 $count=true 来请求用户生成的查询的计数。 有关详细信息，请参阅[搜索文档 (REST)](/rest/api/searchservice/search-documents#counttrue--false)。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

### <a name="step-4-log-click-events"></a>步骤 4：记录单击事件

每次用户单击文档，都是一个必须记录以用于搜索分析的信号。 使用 Application Insights 自定义事件可利用下面的架构来记录这些事件：

+ **ServiceName**：（字符串）搜索服务名称
+ **SearchId**：(GUID) 相关搜索查询的唯一标识符
+ **DocId**：（字符串）文档标识符
+ **Position**：（整数）文档在搜索结果页中的排名

> [!NOTE]
> Position 指的是应用程序中的基数顺序。 可以随意设置此数字以用于比较，只要它始终相同。
>

**使用 C#**

```csharp
var properties = new Dictionary <string, string> 
{
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

1. 在 Azure 认知搜索仪表板左侧导航窗格中，在“设置”下，单击“搜索流量分析”。

1. 在“搜索流量分析”页面上，在步骤 3 中，单击“获取 Power BI Desktop”以安装 Power BI。

   ![获取 Power BI 报表](./media/search-traffic-analytics/get-use-power-bi.png "获取 Power BI 报表")

1. 在同一页面上，单击“下载 Power BI 报表”。

1. 该报表将在 Power BI Desktop 中打开，并且会提示你连接到 Application Insights 并提供凭据。 可以在你的 Application Insights 资源的 Azure 门户页面中找到连接信息。 对于凭据，请提供用于门户登录的相同用户名和密码。

   ![连接到 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "连接到 Application Insights")

1. 单击“加载”。

该报表包含图表和表，可帮助你做出更明智的决策来提高搜索性能和相关性。

指标包括以下各项：

+ 搜索量和最常用术语-文档对：导致同一文档被单击的词，按单击次数排序。
+ 无单击的搜索：查询次数最多但未记录任何单击的词

以下屏幕截图显示了使用了所有架构元素的内置报表的外观。

![用于 Azure 认知搜索的 Power BI 仪表板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "用于 Azure 认知搜索的 Power BI 仪表板")

## <a name="next-steps"></a>后续步骤

检测搜索应用程序，以获取提供深入见解的有关搜索服务的强大数据。

你可以查找有关 [Application Insights](../azure-monitor/app/app-insights-overview.md) 的更多信息并访问[定价页面](https://azure.microsoft.com/pricing/details/application-insights/)来详细了解其各种服务层级。

了解有关创建出色报告的详细信息。 有关详细信息，请参阅 [Power BI Desktop 入门](/power-bi/fundamentals/desktop-getting-started)。