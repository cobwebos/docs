---
title: "Analytics - Azure Application Insights 的强大搜索工具 | Microsoft Docs"
description: "Analytics - Application Insights 的强大诊断搜索工具的概述。 "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: 969d4f5c76c0f91c13622cb91d137c7be8007505
ms.contentlocale: zh-cn
ms.lasthandoff: 05/19/2017


---
# <a name="analytics-in-application-insights"></a>Application Insights 中的 Analytics
[Analytics](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的强大搜索功能。 这些页面介绍 Analytics 查询语言。 

* **[观看介绍视频](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[针对模拟数据测试驱动 Analytics](https://analytics.applicationinsights.io/demo)**（如果应用尚未将数据发送到 Application Insights）。
* **[SQL 用户的备忘单](https://aka.ms/sql-analytics)**转换最常见的惯用语言。
* **[语言参考](app-insights-analytics-reference.md)**了解如何使用 Analytics 查询语言的所有强大功能。


## <a name="queries-in-analytics"></a>Analytics 中的查询
典型的查询是一个*源*表，后跟一系列以 `|` 隔开的*运算符*。 

例如，让我们来看看海得拉巴市民在一天的何时试用了我们的 Web 应用。 与此同时，让我们来看看他们的 HTTP 请求返回了什么结果代码。 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

我们将计算不同的客户端 IP 地址，并按过去 7 天内每天的小时时间值对其进行分组。 

> [!NOTE]
> 若要获取 24 小时以前的结果，请在查询中显式包括“时间戳”，或者使用时间范围下拉菜单。
>

让我们用条形图演示法来显示下结果，选择从不同的响应代码堆栈结果：

![选择条形图，x 和 y 轴，然后是分段](./media/app-insights-analytics/020.png)

看起来在海得拉巴，我们的应用在午餐和就寝时段使用人数最多。 （并且我们应调查这 500 个代码。）

此外，还有功能强大的统计操作：

![统计查询的结果](./media/app-insights-analytics/025.png)

该语言具有许多相当不错的功能：


* 按任何字段（包括自定义属性和指标）[筛选](app-insights-analytics-reference.md#where-operator)原始应用遥测。
* [加入](app-insights-analytics-reference.md#join-operator)多个表 – 将请求与页面视图、依赖项调用、 异常和日志跟踪关联起来。
* 功能强大的统计[聚合](app-insights-analytics-reference.md#aggregations)。
* 与 SQL 一样强大，但对于复杂查询，要容易的多：不用嵌套语句，而是将数据从一个基本操作传递到下一个操作。
* 功能强大的即时可视化效果。
* [将图表固定到 Azure 仪表板](app-insights-analytics-using.md#pin-to-dashboard)。
* [将查询导出到 Power BI](app-insights-analytics-using.md#export-to-power-bi)。
* 可使用 [REST API](https://dev.applicationinsights.io/) 以编程方式运行查询，例如通过 Powershell。


## <a name="connect-to-your-application-insights-data"></a>连接到 Application Insights 数据
在 Application Insights 中从应用的[概述边栏选项卡](app-insights-dashboards.md)打开 Analytics： 

![依次打开 portal.azure.com 和 Application Insights 资源，然后单击“Analytics”。](./media/app-insights-analytics/001.png)


## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



## <a name="query-examples"></a>查询示例

尝试使用以下演练来展示使用分析的功能：

 *    [请求期间自动诊断峰值和步骤跳跃](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *    [通过时序分析来分析性能下降](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *    [分析应用程序的 autocluster 和 diffpatterns 故障](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *    [时序分析的高级形状检测](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *    [使用滑动窗口操作分析应用程序使用情况（滚动 MAU/DAU 等）](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *    [基于调试日志分析检测服务中断](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true)以及[此处](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics)的相关博客文章。
 *    [使用简单调试日志分析应用程序性能](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true)以及[此处](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/)的相关博客文章
 *    [使用简单调试日志测量代码流中每个步骤的持续时间](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true)以及[此处](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/)的相关博客文章
 *    [使用简单调试日志分析并发](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true)以及[此处](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/)的相关博客文章



## <a name="next-steps"></a>后续步骤
* 我们建议用户从[语言教程](app-insights-analytics-tour.md)开始。 
* [使用 Analytics](app-insights-analytics-using.md) 的详细信息。 
* [语言参考](app-insights-analytics-reference.md)。 
