---
title: "Analytics - Azure Application Insights 的强大搜索工具 | Microsoft Docs"
description: "Analytics - Application Insights 的强大诊断搜索工具的概述。 "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: f760494cbe7341391f0ce51bb1161cb1395cbe5c
ms.openlocfilehash: b56ac6e0b75492e8d784c5fc67e555f8f450b65f


---
# <a name="analytics-in-application-insights"></a>Application Insights 中的 Analytics
[Analytics](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的强大搜索功能。 这些页面介绍了 Analytics 查询语言。 

* **[观看介绍视频](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[针对模拟数据测试驱动 Analytics](https://analytics.applicationinsights.io/demo)**（如果应用尚未将数据发送到 Application Insights）。
* **[SQL 用户的备忘单](https://aka.ms/sql-analytics)**转换最常见的惯用语言。

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

让我们用条形图演示法来显示下结果，选择从不同的响应代码堆栈结果：

![选择条形图，x 和 y 轴，然后是分段](./media/app-insights-analytics/020.png)

看起来在海得拉巴，我们的应用在午餐和就寝时段使用人数最多。 （并且我们应调查这 500 个代码。）

此外，还有功能强大的统计操作：

![](./media/app-insights-analytics/025.png)

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



[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

## <a name="next-steps"></a>后续步骤
* 我们建议用户从[语言教程](app-insights-analytics-tour.md)开始。





<!--HONumber=Nov16_HO4-->


