---
title: "在 Visual Studio 中使用 Azure Application Insights 调试应用程序 | Microsoft Docs"
description: "调试期间和生产环境中的 Web 应用性能分析和诊断。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: mbullwin
ms.openlocfilehash: 656c62e7227eef967696715f0882114631249c6c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>在 Visual Studio 中使用 Azure Application Insights 调试应用程序
在 Visual Studio（2015 和更高版本）中，可以使用来自 [Azure Application Insights](app-insights-overview.md) 的遥测，在调试和生产环境中分析 ASP.NET Web 应用中的性能和诊断问题。

如果使用 Visual Studio 2017 或更高版本创建了 ASP.NET Web 应用，则其中已包含 Application Insights SDK。 否则，如果尚未执行此操作，请[向应用添加 Application Insights](app-insights-asp-net.md)。

若要在应用处于实时生产环境中时对其进行监视，通常可以在 [Azure 门户](https://portal.azure.com)中查看 Application Insights 遥测，可以在该门户中设置警报并应用强大的监视工具。 但是对于调试，也可在 Visual Studio 中搜索和分析遥测。 可使用 Visual Studio 来分析遥测，不管是从生产站点还是从开发计算机上的调试运行均可执行该操作。 如果是后一种情况，则可分析调试运行，即使尚未将 SDK 配置为将遥测发送到 Azure 门户。 

## <a name="run"></a>调试项目
使用 F5 在本地调试模式下运行 Web 应用。 打开不同的页以生成一些遥测数据。

在 Visual Studio 中会看到事件计数，这些事件是由项目中的 Application Insights 模块记录的。

![在 Visual Studio 中，调试期间会显示“Application Insights”按钮。](./media/app-insights-visual-studio/appinsights-09eventcount.png)

单击此按钮搜索遥测。 

## <a name="application-insights-search"></a>Application Insights 搜索
Application Insights 的“搜索”窗口显示已记录的事件。 （如果在设置 Application Insights 时登录 Azure，则可在 Azure 门户中搜索相同的事件。）

![右键单击项目，并依次选择“Application Insights”、“搜索”](./media/app-insights-visual-studio/34.png)

> [!NOTE] 
> 选择或取消选择筛选器后，单击文本搜索字段末尾的“搜索”按钮。
>

自定义文本搜索适用于事件中的任何字段。 例如，搜索页面的 URL 的一部分；或客户城市的属性值；或跟踪日志中的特定字词。

单击任何事件即可查看其详细属性。

对于向 Web 应用程序提出的请求，可以通过单击来浏览代码。

![在“请求详细信息”下，通过单击来浏览代码](./media/app-insights-visual-studio/31.png)

还可以打开相关项目来帮助诊断失败的请求或异常。

![在“请求详细信息”下，向下滚动到相关项目](./media/app-insights-visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>查看异常和失败的请求
异常报告显示在“搜索”窗口中。 （在某些旧式 ASP.NET 应用程序中，必须[设置异常监视](app-insights-asp-net-exceptions.md)才能查看由框架处理的异常。）

单击异常可获得堆栈跟踪。 如果应用的代码在 Visual Studio 中打开，可从堆栈跟踪单击浏览到相关代码行。

![异常堆栈跟踪](./media/app-insights-visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>查看代码中的请求和异常摘要
在每个处理程序方法上方的 Code Lens 行中，会看到过去 24 小时内由 Application Insights 记录的请求和异常的计数。

![异常堆栈跟踪](./media/app-insights-visual-studio/21.png)

> [!NOTE] 
> Code Lens 显示 Application Insights 数据的前提是，已[将应用配置为将遥测发送到 Application Insights 门户](app-insights-asp-net.md)。
>

[Code Lens 中的 Application Insights 的详细信息](app-insights-visual-studio-codelens.md)

## <a name="trends"></a>趋势
趋势是用于可视化一段时间内的应用行为的工具。 

从 Application Insights 工具栏按钮或 Application Insights“搜索”窗口选择“浏览遥测趋势”  。 选择五种常见查询中的一个，以便开始使用。 可以根据遥测类型、时间范围和其他属性分析不同的数据集。 

若要查找数据中的异常情况，请选择“视图类型”下拉列表下的其中一个异常情况选项。 使用窗口底部的筛选选项，可轻松地全力探索遥测的特定子集。

![趋势](./media/app-insights-visual-studio/51.png)

[详细了解趋势](app-insights-visual-studio-trends.md)。

## <a name="local-monitoring"></a>本地监视
（从 Visual Studio 2015 Update 2 开始）如果未将 SDK 配置为将遥测发送到 Application Insights 门户（从而 ApplicationInsights.config 中没有检测密钥），“诊断”窗口会显示来自最新调试会话的遥测。 

如果已发布过应用的以前版本，这会是比较好的做法。 不会希望来自调试会话的遥测与 Application Insights 门户中来自已发布应用的遥测混在一起。

如果在将遥测发送到门户之前有一些想要调试的 [自定义遥测](app-insights-api-custom-events-metrics.md)，它会很有用。

* *最初，将 Application Insights 完全配置为将遥测发送到门户。但现在只想查看 Visual Studio 中的遥测。*
  
  * 在“搜索”窗口的“设置”中，即使应用将遥测发送到门户，也有选项可供搜索本地诊断。
  * 要停止将遥测发送到门户，请注释禁止 ApplicationInsights.config 的 `<instrumentationkey>...` 行。准备好再次将遥测发送到门户时，请取消注释。


## <a name="next-steps"></a>后续步骤
|  |  |
| --- | --- |
| **[添加更多数据](app-insights-asp-net-more.md)**<br/>监视使用情况、可用性、依赖项、异常。 集成来自记录框架的跟踪。 编写自定义遥测。 |![Visual Studio](./media/app-insights-visual-studio/64.png) |
| **[使用 Application Insights 门户](app-insights-dashboards.md)**<br/>查看仪表板、功能强大的诊断和分析工具、警报、应用程序的实时依赖项映射和导出的遥测数据。 |![Visual Studio](./media/app-insights-visual-studio/62.png) |

