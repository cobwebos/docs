---
title: "Analytics - Azure Application Insights 的强大搜索和查询工具 | Microsoft Docs"
description: "Analytics - Application Insights 的强大诊断搜索工具的概述。 "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 5f324051a2eeedd35a22f77c771793af9c90c434
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="analytics-in-application-insights"></a>Application Insights 中的 Analytics
Analytics 是 [Application Insights](app-insights-overview.md) 的强大搜索和查询工具。 Analytics 是一个 Web 工具，因此不需要安装。 如果已经为某个应用配置了 Application Insights，则可以通过从该应用的[概述边栏选项卡](app-insights-dashboards.md)打开 Analytics 来对应用数据进行分析。

![依次打开 portal.azure.com 和 Application Insights 资源，并单击“Analytics”。](./media/app-insights-analytics/001.png)

还可以使用 [Analytics 练习场](https://go.microsoft.com/fwlink/?linkid=859557)，这是一个免费的演示环境，其中包含大量示例数据。
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>在 Analytics 中查询数据
典型查询以表名开头，后跟一系列由 `|` 分隔的*运算符*。
例如，接下来让我们查明我们的应用在过去 3 个小时内从不同的国家/地区收到了多少请求：
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

使查询以表名 *requests* 开头，并根据需要添加以管道字符分隔的元素。  首先，我们定义一个时间筛选器，以便仅查看过去 3 个小时的记录。
然后，我们统计每个国家/地区的记录数（该数据位于 *client_CountryOrRegion* 列中）。 最后，我们将结果呈现在一个饼图中。
<br>

![查询结果](./media/app-insights-analytics/030.png)

该语言具有许多相当不错的功能：

* 按任何字段（包括自定义属性和指标）[筛选](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)原始应用遥测。
* [加入](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/join-operator)多个表 – 将请求与页面视图、依赖项调用、 异常和日志跟踪关联起来。
* 功能强大的统计[聚合](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions)。
* 功能强大的即时可视化效果。
* 可以用来以编程方式运行查询的 [REST API](https://dev.applicationinsights.io/)，例如通过 PowerShell。

[完整语言参考](https://go.microsoft.com/fwlink/?linkid=856079)详细介绍了支持的每个命令并且会定期更新。

## <a name="next-steps"></a>后续步骤
* 开始使用 [Analytics 门户](https://go.microsoft.com/fwlink/?linkid=856587)
* 开始[编写查询](https://go.microsoft.com/fwlink/?linkid=856078)
* 查看 [SQL-用户备忘单](https://aka.ms/sql-analytics)来了解最常用习惯用语的翻译。
* 如果应用尚未向 Application Insights 发送数据，请在我们的[练习场](https://analytics.applicationinsights.io/demo)上体验 Analytics。
* 观看[介绍视频](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)。