---
title: "适用于 ASP.NET Core 的 Azure Application Insights | Microsoft Docs"
description: "监视 Web 应用程序的可用性、性能和使用情况。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 560db0f2a3ac6beb2664725514b0cbe4ae2be0d4
ms.contentlocale: zh-cn
ms.lasthandoff: 03/21/2017


---
# <a name="application-insights-for-aspnet-core"></a>用于 ASP.NET Core 的 Application Insights
使用 [Application Insights](app-insights-overview.md) 可以监视 Web 应用程序的可用性、性能和使用情况。 通过收到的有关应用在现实中的性能和有效性的反馈，你可以针对每个开发生命周期确定合理的设计方向。

![示例](./media/app-insights-asp-net-core/sample.png)

你将需要 [Microsoft Azure](http://azure.com) 订阅。 使用 Microsoft 帐户登录，该帐户可能适用于 Windows、XBox Live 或其他 Microsoft 云服务。 你的团队可能拥有 Azure 组织订阅：要求所有者使用你的 Microsoft 帐户将你加入其中。

## <a name="getting-started"></a>入门

* 在 Visual Studio 解决方案资源管理器中，右键单击项目，然后选择“配置 Application Insights”或“添加”>“Application Insights”。 [了解详细信息](app-insights-asp-net.md)。
* 如果未看到这些菜单命令，则请遵循[手动获取入门指南](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)进行操作。 如果创建项目时所用的版本低于 Visual Studio 2017，则可能需要执行此操作。

## <a name="using-application-insights"></a>使用 Application Insights
登录到“Microsoft Azure 门户”[](https://portal.azure.com)，选择“所有资源”或“Application Insights”，然后选择为监视应用而创建的资源。

在单独的浏览器窗口中，使用你的应用一段时间。 你将看到数据显示在 Application Insights 图表中。 （可能需要单击“刷新”。）开发时可能只有少量数据，但在你发布应用并且有许多用户后，这些图表才会完整显示。 

可在概述页了解主要的性能图表：服务器响应时间、页面加载时间和失败请求计数。 单击任何图表，查看更多图表和数据。

门户中的视图分为 3 大主要类别：

* [指标资源管理器](app-insights-metrics-explorer.md)显示了指标和计数（例如响应时间、故障率）或你通过 [API](app-insights-api-custom-events-metrics.md) 自行创建的指标的关系图和表。 按属性值筛选和细分数据，以便更好地了解你的应用及其用户。
* [搜索资源管理器](app-insights-diagnostic-search.md)列出了单独事件（例如特定请求、异常、日志跟踪）或你通过 [API](app-insights-api-custom-events-metrics.md) 自行创建的事件。 在事件中筛选和搜索，并在相关事件中导航以调查问题。
* [分析](app-insights-analytics.md)允许你通过遥测运行类似 SQL 的查询，它是一个功能强大的分析和诊断工具。

## <a name="alerts"></a>警报
* 你将自动获取[主动诊断警报](app-insights-proactive-diagnostics.md)，告知你有关失败率和其他指标的异常更改。
* 设置[可用性测试](app-insights-monitor-web-app-availability.md)，从全球位置持续测试你的网站并在任意测试失败后立即获取电子邮件。
* 设置[指标警报](app-insights-monitor-web-app-availability.md)，了解响应时间或异常率等指标是否超出可接受的限制。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>开放源
[阅读代码或为其做出贡献](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>后续步骤
* [将遥测添加到网页](app-insights-javascript.md)，监视页面使用情况和性能。
* [监视依赖项](app-insights-asp-net-dependencies.md)，查看 REST、SQL 或其他外部资源是否会降低你的性能。
* [使用 API](app-insights-api-custom-events-metrics.md)，发送你自己的事件和指标以获取你的应用的性能和使用情况的更详细视图。
* [可用性测试](app-insights-monitor-web-app-availability.md)从世界各地不断检查你的应用。 


