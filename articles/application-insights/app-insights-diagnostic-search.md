---
title: "使用诊断搜索 | Microsoft 文档"
description: "搜索和筛选单个事件、请求与日志跟踪。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/09/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: 50d8c22dd0bdd3618ca0cbcd29ffa290c0c5f35b


---
# <a name="using-diagnostic-search-in-application-insights"></a>使用 Application Insights 中的诊断搜索
诊断搜索是 [Application Insights][启动] 中的一项功能，可用于查找和浏览单个遥测项，例如页面视图、异常或 Web 请求。 此外，可以查看编写的日志跟踪和事件。

## <a name="where-do-you-see-diagnostic-search"></a>可以在何处找到诊断搜索？
### <a name="in-the-azure-portal"></a>在 Azure 门户中
可以直接打开诊断搜索：

![打开诊断搜索](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

单击某些图表和网格项时，也会打开诊断搜索。 在这种情况下，诊断搜索的筛选器预先设置为将焦点放在选定的项类型上。 

例如，如果应用程序是 Web 服务，概述边栏选项卡将显示请求数量图表。 单击该图表会打开一个更详细的图表，其中包含一个列表，显示针对每个 URL 发出的请求数量。 单击任何一行可以看到针对该 URL 发出的各个请求的列表：

![打开诊断搜索](./media/app-insights-diagnostic-search/07-open-from-filters.png)

诊断搜索的主体是遥测项列表 - 服务器请求、页面视图、编写的自定义事件，等等。 该列表的上面是摘要图表，显示一段时间内的事件计数。

事件通常先显示在诊断搜索中，然后再显示在指标资源管理器中。 尽管边栏选项卡会定期自动刷新，但在等待查看特定的事件时，你可以单击“刷新”。

### <a name="in-visual-studio"></a>在 Visual Studio 中
在 Visual Studio 中打开“搜索”窗口：

![](./media/app-insights-diagnostic-search/32.png)

“搜索”窗口中的功能与 Web 门户相同：

![](./media/app-insights-diagnostic-search/34.png)

## <a name="sampling"></a>采样
如果应用生成大量遥测（且使用的是 ASP.NET SDK 版本 2.0.0-beta3 或更高版本），自适应采样模块将通过仅发送具有代表性的事件部分来自动减少发送到门户的量。 但是，将以组为单位选择或取消选择与同一请求相关的事件，以便可以在相关事件之间浏览。 

[了解采样](app-insights-sampling.md)。

## <a name="inspect-individual-items"></a>检查单个项
选择任一遥测项可查看关键字段和相关的项。 如果想要查看完整的字段集，请单击“...”。 

![单击“新建工作项”、编辑字段，然后单击“确定”。](./media/app-insights-diagnostic-search/10-detail.png)

若要查找完整的字段集，请使用纯字符串（不包含通配符）。 可用的字段取决于遥测类型。

## <a name="create-work-item"></a>创建工作项
可以使用任何遥测项中的详细信息，在 Visual Studio Team Services 中创建 Bug。 

![单击“新建工作项”、编辑字段，然后单击“确定”。](./media/app-insights-diagnostic-search/42.png)

首次执行此操作时，系统将要求配置 Team Services 帐户和项目的链接。

![请填写 Team Services 服务器的 URL 和项目名称，然后单击“授权”](./media/app-insights-diagnostic-search/41.png)

（也可以通过“设置”>“工作项”转到配置边栏选项卡。）

## <a name="filter-event-types"></a>筛选事件类型
打开“筛选”边栏选项卡，然后选择要查看的事件类型。 （如果以后要还原打开边栏选项卡时所用的筛选器，请单击“重置”。）

![选择筛选器并选择遥测类型](./media/app-insights-diagnostic-search/02-filter-req.png)

事件类型包括：

* **跟踪** - 诊断日志，包括 TrackTrace、log4Net、NLog 和 System.Diagnostic.Trace 调用。
* **请求** - 服务器应用程序收到的 HTTP 请求，包括页面、脚本、图像、样式文件和数据。 这些事件用于创建请求和响应概述图表。
* **页面视图** - Web 客户端发送的，用于创建页面视图报告的遥测数据。 
* **自定义事件** - 如果插入了对 TrackEvent() 的调用以便[监视使用情况][跟踪]，可以在此处搜索这些调用。
* **异常** - 服务器中未捕获到的异常，以及使用 TrackException() 记录的异常。

## <a name="filter-on-property-values"></a>按属性值筛选
可以按事件的属性值筛选事件。 可用的属性取决于所选的事件类型。 

例如，找出具有特定响应代码的请求。

![展开属性并选择值](./media/app-insights-diagnostic-search/03-response500.png)

不选择特定属性的任何值与选择所有值的效果相同；这会关闭根据该属性进行筛选。

### <a name="narrow-your-search"></a>缩小搜索范围
请注意，筛选器值右侧的计数显示当前筛选的集内有多少个出现的项。 

本示例清楚地显示 `Reports/Employees` 请求导致 500 个错误中的大多数错误：

![展开属性并选择值](./media/app-insights-diagnostic-search/04-failingReq.png)

此外，如果还想要查看在这段时间内其他事件发生的情况，可以选中“包含未定义属性的事件”。

## <a name="remove-bot-and-web-test-traffic"></a>删除 Bot 和 Web 测试流量
使用筛选器“实际或综合流量”并选中“实际”。

也可以按“综合流量的源”筛选。

## <a name="inspect-individual-occurrences"></a>检查单个出现的项
将该请求名称添加到筛选器集，然后可以检查该事件的单个出现项。

![选择值](./media/app-insights-diagnostic-search/05-reqDetails.png)

对于“请求”事件，详细信息将显示处理该请求时发生的异常。

单击某个异常可查看其详细信息，包括堆栈跟踪。

![单击异常](./media/app-insights-diagnostic-search/06-callStack.png)

## <a name="find-events-with-the-same-property"></a>查找具有相同属性的事件
查找具有相同属性值的所有项：

![右键单击属性](./media/app-insights-diagnostic-search/12-samevalue.png)

## <a name="search-by-metric-value"></a>按指标值搜索
获取响应时间大于 5 秒的所有请求。  时间以刻度为单位：10000 刻度 = 1 毫秒。

!["Response time":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)

## <a name="search-the-data"></a>搜索数据

> [!NOTE]
> 若要编写更复杂的查询，请在“搜索”边栏选项卡的顶部打开 [**Analytics**](app-insights-analytics-tour.md)。
> 

可以搜索任何属性值中的关键词。 如果已编写包含属性值的[自定义事件][跟踪]，此功能特别有用。 

可以设置时间范围，因为搜索一小段时间内的值可以更快地返回结果。 

![打开诊断搜索](./media/app-insights-diagnostic-search/appinsights-311search.png)

搜索关键词而不是子字符串。 关键词是字母数字字符串，可包含“.”和“_”等标点符号。 例如：

| 关键词 | *不*匹配 | 但匹配 |
| --- | --- | --- |
| HomeController.About |about<br/>主页 |h\*about<br/>home\* |
| IsLocal |local<br/>is<br/>\*local |isl\*<br/>islocal<br/>i\*l\* |
| New Delay |w d |新的<br/>delay<br/>n\* AND d\* |

下面是可以使用的搜索表达式：

| 示例查询 | 效果 |
| --- | --- |
| slow |在日期范围中查找字段包含关键词“slow”的所有事件 |
| database?? |匹配 database01、databaseAB...<br/>? 不允许出现在搜索关键词的开头。 |
| database* |匹配 database、database01、databaseNNNN<br/> * 不允许出现在搜索关键词的开头 |
| apple AND banana |查找同时包含这两个关键词的事件。 应使用大写“AND”，而不是“and”。 |
| apple OR banana<br/>apple banana |查找包含任一关键词的事件。 请使用“OR”，而不是“or”。</br/>短格式 |
| apple NOT banana<br/>apple -banana |查找包含一个关键词但不包含另一个关键词的事件。<br/>简格式。 |
| app* AND banana -(grape pear) |逻辑运算符和括号。 |
| "Metric": 0 TO 500<br/>"Metric" : 500 TO * |在值范围内查找包含指定度量值的事件。 |



## <a name="save-your-search"></a>保存搜索
设置所需的所有筛选器后，可以将搜索保存到收藏夹。 如果使用组织帐户，可以选择是否要将此搜索与其他团队成员共享。

![单击“收藏夹”，设置名称，然后单击“保存”](./media/app-insights-diagnostic-search/08-favorite-save.png)

若要再次查看该搜索，**请转到概览边栏选项卡**并打开“收藏夹”：

![“收藏夹”磁贴](./media/app-insights-diagnostic-search/09-favorite-get.png)

如果保存时使用了“相对”时间范围，重新打开的边栏选项卡包含最新数据。 如果保存时使用了“绝对”时间范围，则每次看到的数据都是相同的。

## <a name="send-more-telemetry-to-application-insights"></a>将更多遥测数据发送到 Application Insights
除了 Application Insights SDK 原本发送的遥测数据以外，可以：

* 从 [.NET][netlogs] 或 [Java][javalogs] 中偏好的日志记录框架捕获日志跟踪。 也就是说，可以搜索日志跟踪并将其与页面视图、异常和其他事件相关联。 
* [编写代码][跟踪]用于发送自定义事件、页面视图和异常。 

[了解如何向 Application Insights 发送日志和自定义的遥测数据][跟踪]。

## <a name="a-namequestionsaq-a"></a><a name="questions"></a>问题解答
### <a name="a-namelimitsahow-much-data-is-retained"></a><a name="limits"></a>保留多少数据？
每个应用程序每秒最多保留 500 个事件。 事件保留七天。

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>如何查看服务器请求中的 POST 数据？
我们不自动记录 POST 数据，但你可以使用 [TrackTrace 或日志调用][跟踪]。 POST 数据放在消息参数中。 无法像筛选属性一样筛选消息，但消息的大小限制更大。

## <a name="a-nameaddanext-steps"></a><a name="add"></a>后续步骤
* [在 Analytics 中编写复杂查询](app-insights-analytics-tour.md)
* [向 Application Insights 发送日志和自定义的遥测数据][跟踪]
* [设置可用性和响应能力测试][可用性]
* [故障排除][qna]

<!--Link references-->

[可用性]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[启动]: app-insights-overview.md
[跟踪]: app-insights-search-diagnostic-logs.md
[跟踪]: app-insights-api-custom-events-metrics.md




<!--HONumber=Nov16_HO3-->


