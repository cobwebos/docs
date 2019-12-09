---
title: 报告搜索流量分析数据
titleSuffix: Azure Cognitive Search
description: 使用 Application Insights 为 Azure 认知搜索启用搜索流量分析，使用收集遥测和用户启动的事件，然后在 Power BI 报表中分析结果。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: b9b0ba85aed4d63fe6bb939c9ed3b99d3e789397
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932562"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>在 Azure 认知搜索中实现搜索流量分析

搜索流量分析是用于为搜索服务实现反馈循环的模式。 目标是收集用户启动的单击事件和键盘输入的遥测数据。 使用此信息，您可以确定搜索解决方案的有效性，包括热门搜索词、点击链接率和哪些查询输入产生零结果。

此模式依赖于[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) （ [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)的一项功能）来收集用户数据。 你还需要向客户端代码添加检测，如本文所述。 最后，您将需要一种报表机制来分析数据。 建议 Power BI，但你可以使用任何连接到 Application Insights 的工具。

> [!NOTE]
> 本文中所述的模式适用于客户端生成的高级方案和点击流数据。 或者，您可以报告搜索服务生成的日志信息。 有关服务日志报告的详细信息，请参阅[监视资源消耗和查询活动](search-monitor-usage.md)。

## <a name="identify-relevant-search-data"></a>标识相关的搜索数据

若要拥有有用的搜索指标，请务必记录来自搜索应用程序用户的某些信号。 这些信号意味着用户感兴趣的内容以及他们认为与其需求相关的内容。

搜索流量分析需要两个信号：

+ 用户生成的搜索事件：仅用户启动的搜索查询是很有趣的。 用于填充 Facet、附加内容或任何内部信息的搜索查询都不重要，它们会扭曲结果并造成结果有偏差。

+ 用户生成的单击事件：通过单击本文档，我们将引用用户选择从搜索查询返回的特定搜索结果。 一次单击通常意味着文档是特定搜索查询的相关结果。

通过链接搜索并单击具有相关 ID 的事件，可以分析用户对应用程序的行为。 如果仅使用搜索流量日志，则无法获取这些搜索见解。

## <a name="add-search-traffic-analytics"></a>添加搜索流量分析

前一部分中提到的信号必须在用户与搜索应用程序交互时从该搜索应用程序中收集。 Application Insights 是一个可扩展的监视解决方案，可用于多个平台，具有灵活的检测选项。 使用 Application Insights 可让你充分利用 Azure 认知搜索创建的 Power BI 搜索报告，以便更轻松地分析数据。

在 Azure 认知搜索服务的[门户](https://portal.azure.com)页中，"搜索流量分析" 页包含遵循此遥测模式的工作表。 还可以选择或创建 Application Insights 资源，并查看必需的数据，所有这些全都在一个位置完成。

![“搜索流量分析”说明][1]

## <a name="1---select-a-resource"></a>1 - 选择资源

需要选择要使用的 Application Insights 资源，如果还没有 Application Insights 资源，则需要创建一个。 可以使用已在使用的资源记录所需的自定义事件。

创建新的 Application Insights 资源时，此方案对所有应用程序类型都有效。 选择一个最适合所用平台的资源。

为应用程序创建遥测客户端时需要使用检测密钥。 可以从 Application Insights 门户仪表板获取该密钥，也可以从“搜索流量分析”页获取它，只需选择要使用的实例即可。

## <a name="2---add-instrumentation"></a>2 - 添加检测

在此步骤中，你将使用在上述步骤中创建的 Application Insights 资源来检测自己的搜索应用程序。 此过程有四个步骤：

**步骤1：创建遥测客户端**

这是将事件发送到 Application Insights 资源的对象。

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

对于其他语言和平台，请参阅完整的[列表](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

**步骤2：请求相关的搜索 ID**

若要通过单击来关联搜索请求，需要具有关联两个不同事件的关联 ID。 当你使用标题请求时，Azure 认知搜索会为你提供搜索 ID：

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**步骤3：日志搜索事件**

每当用户发出搜索请求时，应使用 Application Insights 自定义事件上的以下架构，将该请求作为搜索事件进行记录：

**SearchServiceName**：（string）搜索服务名称**SearchId**：（guid）搜索查询的唯一标识符（传入搜索响应） **IndexName**：（string）搜索要查询的搜索服务索引**QueryTerms** **：（** string）搜索的搜索词。返回的文档数（在搜索响应中） **ScoringProfile**：（string）使用的计分配置文件的名称（如果有）

> [!NOTE]
> 请通过向搜索查询添加 $count=true 来请求用户生成查询的计数。 请在[此处](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)查看详细信息
>

> [!NOTE]
> 请记住仅记录由用户生成的搜索查询。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**步骤4：记录单击事件**

每次用户单击文档，都是一个必须记录以用于搜索分析的信号。 使用 Application Insights 自定义事件可利用下面的架构来记录这些事件：

**ServiceName**：(string) 搜索服务名称 **SearchId**：(guid) 相关搜索查询的唯一标识符 **DocId**：(string) 文档标识符 **Position**：(int) 搜索结果页中文档的排名

> [!NOTE]
> Position 指的是应用程序中的基数顺序。 可以随意设置此数字以用于比较，只要它始终相同。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - 在 Power BI 中进行分析

检测应用并验证应用程序是否已正确连接到 Application Insights 后，可以使用 Azure 认知搜索为 Power BI 桌面创建的预定义模板。 

Azure 认知搜索提供监视[Power BI 内容包](https://app.powerbi.com/getdata/services/azure-search)，以便分析日志数据。 内容包添加预先定义的图表和表，它们可用于分析为搜索流量分析捕获的其他数据。 有关详细信息，请参阅[内容包帮助页](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/)。 

1. 在 Azure 认知搜索仪表板的左侧导航窗格中的 "**设置**" 下，单击 "**搜索流量分析**"。

2. 在“搜索流量分析”页面上，在步骤 3 中，单击“获取 Power BI Desktop”以安装 Power BI。

   ![获取 Power BI 报表](./media/search-traffic-analytics/get-use-power-bi.png "获取 Power BI 报表")

2. 在同一页上，单击 "**下载 Power BI 报表**"。

3. 该报表将在 Power BI Desktop 中打开，并且会提示你连接到 Application Insights。 可以在 Application Insights 资源的 Azure 门户页中找到此信息。

   ![连接到 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "连接到 Application Insights")

4. 单击“加载”。

该报表包含图表和表，可帮助你做出更明智的决策来提高搜索性能和相关性。

指标包括以下各项：

* 单击率 (CTR)：单击特定文档的用户占总搜索次数的比率。
* 无单击的搜索：查询次数最多但未记录任何单击的词
* 单击次数最多的文档：过去24 小时、7 天和 30 天内单击次数最多的文档，按 ID 显示。
* 常用术语-文档对：导致同一文档被单击的词，按单击次数排序。
* 时间 - 单击：自搜索查询以来按时间存储的单击次数

以下屏幕截图显示了用于分析搜索流量的内置报表和图表。

![Azure 认知搜索的 Power BI 面板](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure 认知搜索的 Power BI 面板")

## <a name="next-steps"></a>后续步骤
检测搜索应用程序，以获取提供深入见解的有关搜索服务的强大数据。

你可以查找有关 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 的更多信息并访问[定价页面](https://azure.microsoft.com/pricing/details/application-insights/)来详细了解其各种服务层级。

了解有关创建出色报告的详细信息。 有关详细信息，请参阅[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)入门。

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
