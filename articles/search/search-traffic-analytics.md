---
title: Azure 搜索的搜索流量分析 | Microsoft Docs
description: 为 Azure 搜索（Microsoft Azure 上云托管的搜索服务）启用搜索流量分析，以解锁有关用户和数据的洞察力。
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.openlocfilehash: 10963d1e022fc2aa574c88e994c4d8593b4d4de1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-search-traffic-analytics"></a>搜索流量分析是什么
搜索流量分析是用于为搜索服务实现反馈循环的模式。 此模式描述必需的数据以及如何使用 Application Insights（用于监视多个平台中的服务的行业领导者）收集这些数据。

使用搜索流量分析可以了解搜索服务并获得有关用户及其行为的见解。 拥有有关用户所选内容的数据，即可做出进一步改善搜索体验的决策，并可在结果不及预期时后退。

Azure 搜索提供集成 Azure Application Insights 和 Power BI 的遥测解决方案，以便提供深入的监视和跟踪。 由于与 Azure 搜索的交互只能通过 API 进行，因此遥测必须由开发人员按此页中的说明使用搜索来实现。

## <a name="identify-the-relevant-search-data"></a>标识相关搜索数据

若要拥有有用的搜索指标，请务必记录来自搜索应用程序用户的某些信号。 这些信号意味着用户感兴趣的内容以及他们认为与其需求相关的内容。

搜索流量分析需要两个信号：

1. 用户生成的搜索事件：只有用户启动的搜索查询才需要关注。 用于填充 Facet、附加内容或任何内部信息的搜索查询都不重要，它们会扭曲结果并造成结果有偏差。

2. 用户生成的单击事件：本文档中的单击是指用户选择从搜索查询返回的特定搜索结果。 一次单击通常意味着文档是特定搜索查询的相关结果。

通过使用相关性 ID 链接搜索和单击事件，可以分析应用程序上的用户的行为。 如果仅使用搜索流量日志，则无法获取这些搜索见解。

## <a name="how-to-implement-search-traffic-analytics"></a>如何实现搜索流量分析

前一部分中提到的信号必须在用户与搜索应用程序交互时从该搜索应用程序中收集。 Application Insights 是一个可扩展的监视解决方案，可用于多个平台，具有灵活的检测选项。 使用 Application insights 可以充分利用由 Azure 搜索创建的 Power BI 搜索报表，从而使数据分析更加容易。

在 Azure 搜索服务的[门户](https://portal.azure.com)页中，“搜索流量分析”边栏选项卡包含一个用于遵循此遥测模式的速查表。 还可以选择或创建 Application Insights 资源，并查看必需的数据，所有这些全都在一个位置完成。

![“搜索流量分析”说明][1]

### <a name="1-select-an-application-insights-resource"></a>1.选择 Application Insights 资源

需要选择要使用的 Application Insights 资源，如果还没有 Application Insights 资源，则需要创建一个。 可以使用已在使用的资源记录所需的自定义事件。

创建新的 Application Insights 资源时，此方案对所有应用程序类型都有效。 选择一个最适合所用平台的资源。

为应用程序创建遥测客户端时需要使用检测密钥。 可以从 Application Insights 门户仪表板获取该密钥，也可以从“搜索流量分析”页获取它，只需选择要使用的实例即可。

### <a name="2-instrument-your-application"></a>2.检测应用程序

在此阶段中，将使用以上步骤中创建的 Application Insights 资源检测自己的搜索应用程序。 此过程有四个步骤：

**I.创建一个遥测客户端**。这是将事件发送到 Application Insights 资源的对象。

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

**II.请求用于关联的搜索 ID**。为了将搜索请求与单击相关联，必须具有一个将这两个不同事件关联起来的相关性 ID。 使用标头请求搜索 ID 时，Azure 搜索将提供该 ID：

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**III.记录搜索事件**

每当用户发出搜索请求时，应使用 Application Insights 自定义事件上的以下架构，将该请求作为搜索事件进行记录：

**ServiceName**：(string) 搜索服务名称 **SearchId**：(guid) 搜索查询的唯一标识符（进入搜索响应） **IndexName**：(string) 要查询的搜索服务索引 **QueryTerms**：(string) 用户输入的搜索词 **ResultCount**：(int) 返回的文档数（进入搜索响应）**ScoringProfile**：(string) 所用计分概要文件的名称（如果有计分概要文件）

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

**IV.记录单击事件**

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

    appInsights.TrackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3.使用 Power BI Desktop 进行分析

检测到应用并确认应用程序已正确连接到 Application Insights 后，可以对 Power BI Desktop 使用由 Azure 搜索创建的预定义模板。
此模板包含图表和表格，可帮助你做出更明智的决策来提高搜索性能和相关性。

若要实例化 Power BI Desktop 模板，需要三部分有关 Application Insights 的信息。 此数据可以在“搜索流量分析”页中找到（选择要使用的资源时）

![“搜索流量分析”边栏选项卡中的 Application Insights 数据][2]

Power BI Desktop 模板中包括的指标：

*   单击率 (CTR)：单击特定文档的用户占总搜索次数的比率。
*   无单击的搜索：查询次数最多但未记录任何单击的词
*   单击次数最多的文档：过去24 小时、7 天和 30 天内单击次数最多的文档，按 ID 显示。
*   常用术语-文档对：导致同一文档被单击的词，按单击次数排序。
*   时间 - 单击：自搜索查询以来按时间存储的单击次数

![用于从 Application Insights 进行读取的 Power BI 模板][3]


## <a name="next-steps"></a>后续步骤
检测搜索应用程序，以获取提供深入见解的有关搜索服务的强大数据。

可以在[此处](https://go.microsoft.com/fwlink/?linkid=842905)找到有关 Application Insights 的详细信息。 访问 Application Insights [定价页](https://azure.microsoft.com/pricing/details/application-insights/)可了解有关其不同服务层的详细信息。

了解有关创建出色报告的详细信息。 有关详细信息，请参阅 [Power BI Desktop 入门](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/)

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
