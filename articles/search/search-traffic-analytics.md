---
title: 用于搜索流量分析的遥测
titleSuffix: Azure Cognitive Search
description: 为 Azure 认知搜索启用搜索流量分析，使用 Application Insights 收集遥测数据和用户启动的事件，然后在 Power BI 报告中对结果进行分析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258203"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>收集遥测数据以进行搜索流量分析

搜索流量分析是一种模式，用于收集有关用户与 Azure 认知搜索应用程序（如用户启动的单击事件和键盘输入）交互的遥测数据。 使用此信息，你可以确定搜索解决方案的有效性，包括热门搜索词、点击率以及哪些查询输入产生了零个结果。

此模式依赖于 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)（[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) 的一项功能）来收集用户数据。 它要求您向客户端代码添加检测，如本文所述。 最后，你将需要一个报告机制来分析数据。 我们建议使用 Power BI，但您可以使用应用程序仪表板或任何连接到应用程序见解的工具。

> [!NOTE]
> 本文中描述的模式适用于高级方案和由添加到客户端的代码生成的点击流数据。 相反，服务日志易于设置，提供一系列指标，可以在门户中完成，无需任何代码。 建议在所有方案中启用诊断日志记录。 有关详细信息，请参阅[收集和分析日志数据](search-monitor-logs.md)。

## <a name="identify-relevant-search-data"></a>标识相关的搜索数据

要有用于搜索流量分析的有用指标，必须记录来自搜索应用程序用户的一些信号。 这些信号表示用户感兴趣的内容，并且他们认为相关内容。 对于搜索流量分析，其中包括：

+ 用户生成的搜索事件：只有用户启动的搜索查询才有趣。 用于填充 Facet、附加内容或任何内部信息的搜索查询都不重要，它们会扭曲结果并造成结果有偏差。

+ 用户生成的单击事件：在搜索结果页上，单击事件通常表示文档是特定搜索查询的相关结果。

通过将搜索和单击事件与相关 ID 链接，您可以更深入地了解应用程序的搜索功能的性能。

## <a name="add-search-traffic-analytics"></a>添加搜索流量分析

在 Azure 认知搜索服务的[门户](https://portal.azure.com)页中，“搜索流量分析”页面包含一个用于遵循此遥测模式的速查表。 从此页面中，您可以选择或创建应用程序见解资源、获取检测密钥、复制可适应解决方案的代码段，以及下载通过模式中反映的架构构建的 Power BI 报表。

![在门户中搜索流量分析页面](media/search-traffic-analytics/azuresearch-trafficanalytics.png "在门户中搜索流量分析页面")

## <a name="1---set-up-application-insights"></a>1 - 设置应用程序见解

选择现有的应用程序见解资源，或者创建[一个](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)，如果您还没有一个。 如果使用"搜索流量分析"页，则可以复制应用程序连接到应用程序见解所需的检测密钥。

获得应用程序见解资源后，您可以按照[受支持的语言和平台的说明](https://docs.microsoft.com/azure/azure-monitor/app/platforms)注册应用。 注册只是将应用程序见解中的检测密钥添加到代码中，从而设置关联。 您可以选择现有资源时，您可以在门户或"搜索流量分析"页中找到密钥。

适用于某些 Visual Studio 项目类型的快捷方式将反映在以下步骤中。 它创建一个资源，只需单击几下即可注册应用。

1. 对于可视化工作室和ASP.NET开发，请打开解决方案并选择 **"项目** > **添加应用程序见解遥测**"。

1. 单击“开始”。****

1. 通过提供 Microsoft 帐户、Azure 订阅和应用程序见解资源（默认为新资源）注册应用。 单击“注册”****。

此时，应用程序已设置为应用程序监视，这意味着使用默认指标跟踪所有页面加载。 有关上述步骤的详细信息，请参阅[启用应用程序见解服务器端遥测](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)。

## <a name="2---add-instrumentation"></a>2 - 添加检测

在此步骤中，将使用以上步骤中创建的 Application Insights 资源检测自己的搜索应用程序。 此过程有四个步骤，从创建遥测客户端开始。

### <a name="step-1-create-a-telemetry-client"></a>第 1 步：创建遥测客户端

创建将事件发送到应用程序见解的对象。 您可以将检测添加到在浏览器中运行的服务器端应用程序代码或客户端代码，此处以 C# 和 JavaScript 变体表示（对于其他语言，请参阅[支持的平台和框架](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)的完整列表）。 选择为您提供所需信息深度的方法。

服务器端遥测捕获应用程序层的指标，例如，在云中作为 Web 服务运行的应用程序或作为公司网络上的本地应用运行的应用程序中。 服务器端遥测捕获搜索和单击事件、文档在结果中的位置和查询信息，但数据收集的范围将限定为该层上可用的任何信息。

在客户端上，您可能还有其他代码来操作查询输入、添加导航或包括上下文（例如，从主页引发的查询与产品页）。 如果这描述您的解决方案，您可以选择客户端检测，以便遥测反映其他详细信息。 如何收集此附加详细信息超出了此模式的范围，但您可以查看[网页的应用程序见解](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data)以了解更多方向。 

**使用 C#**

对于 C#，在应用程序配置中找到**检测键**，例如，如果项目ASP.NET，则为 appsettings.json。 如果您不确定关键位置，请返回注册说明。

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

### <a name="step-2-request-a-search-id-for-correlation"></a>第 2 步：请求搜索 ID 以查找相关

为了将搜索请求与单击相关联，必须具有一个将这两个不同事件关联起来的相关性 ID。 Azure 认知搜索在使用 HTTP 标头请求时为您提供搜索 ID。

具有搜索 ID 允许将 Azure 认知搜索为请求本身发出的指标与在应用程序见解中记录的自定义指标关联。  

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

**使用 JavaScript（调用 REST API）**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>第 3 步：记录搜索事件

每次由用户发出搜索请求时，都应在应用程序见解自定义事件中使用以下架构将该请求记录为搜索事件。 请记住仅记录用户生成的搜索查询。

+ **搜索服务名称**：（字符串） 搜索服务名称
+ **搜索 Id**：（guid） 搜索查询的唯一标识符（在搜索响应中提供）
+ **索引名称**：（字符串）搜索服务索引要查询
+ **查询术语**：（字符串） 搜索字词由用户输入
+ **结果计数**：（int） 返回的文档数（来自搜索响应中）
+ **评分配置文件**：（字符串）名称的评分配置文件使用，如果有的话

> [!NOTE]
> 通过将$count_true添加到搜索查询，请求用户生成的查询计数。 有关详细信息，请参阅[搜索文档 （REST）](/rest/api/searchservice/search-documents#counttrue--false)。
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

### <a name="step-4-log-click-events"></a>第 4 步：记录单击事件

每次用户单击文档，都是一个必须记录以用于搜索分析的信号。 使用 Application Insights 自定义事件可利用下面的架构来记录这些事件：

+ **服务名称**： （字符串） 搜索服务名称
+ **搜索 Id**：（guid） 相关搜索查询的唯一标识符
+ **文档 Id**：（字符串）文档标识符
+ **在**搜索结果页中放置 ：（int） 文档的排名

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

以下屏幕截图显示了如果您使用了所有架构元素，则内置报表的外观。

![用于 Azure 认知搜索的 Power BI 仪表板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "用于 Azure 认知搜索的 Power BI 仪表板")

## <a name="next-steps"></a>后续步骤

检测搜索应用程序，以获取提供深入见解的有关搜索服务的强大数据。

你可以查找有关 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 的更多信息并访问[定价页面](https://azure.microsoft.com/pricing/details/application-insights/)来详细了解其各种服务层级。

了解有关创建出色报告的详细信息。 有关详细信息[，请参阅使用 Power BI 桌面入门](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)。