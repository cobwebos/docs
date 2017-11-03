---
title: "在 Visual Studio 中分析趋势 | Microsoft Docs"
description: "在 Visual Studio 中分析、可视化和浏览 Application Insights 遥测的趋势。"
services: application-insights
documentationcenter: .net
author: numberbycolors
manager: carmonm
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 064b1b10f2bd776e4ae88d8460e106a0318476c7
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="analyzing-trends-in-visual-studio"></a>在 Visual Studio 中分析趋势
Application Insights 趋势工具以可视化方式呈现 Web 应用程序在各时间段的重要遥测事件变化，帮助快速识别问题和异常。 “趋势”提供更详细诊断信息的链接，可帮助改进应用程序性能、跟踪异常的原因，以及从自定义事件中找到见解。

![示例趋势窗口](./media/app-insights-visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>为 Application Insights 配置 Web 应用

如果没有完成此操作，请[为 Application Insights 配置 Web 应用](app-insights-overview.md)。 这样即可将遥测数据发送到 Application Insights 门户。 趋势工具从该处读取遥测数据。

Application Insights 趋势在 Visual Studio 2015 Update 3 及更高版本中提供。

## <a name="open-application-insights-trends"></a>打开 Application Insights 趋势
若要打开“Application Insights 趋势”窗口，请执行以下操作：

* 通过 Application Insights 工具栏按钮选择“浏览遥测趋势”，或者 
* 在项目上下文菜单中选择“Application Insights”>“浏览遥测趋势”，或者
* 在 Visual Studio 菜单栏中，选择“视图”>“其他窗口”>“Application Insights 趋势”。

此时会出现选择资源的提示。 单击“选择资源”，使用 Azure 订阅登录，并从列表中选择要分析其遥测趋势的 Application Insights 资源。

## <a name="choose-a-trend-analysis"></a>选择趋势分析
![常用趋势分析类型的菜单](./media/app-insights-visual-studio-trends/app-insights-trends-1-750.png)

首先选择五种通用趋势分析中的一种，其中的每一种都可分析过去 24 小时内的数据：

* **调查服务器请求的性能问题** - 对服务发出的请求，按响应时间分组
* **分析服务器请求中的错误** - 对服务发出的请求，按 HTTP 响应代码分组
* **检查应用程序中的异常** - 服务中的异常，按异常类型分组
* **检查应用程序依赖项的性能** - 用户服务调用的服务，按响应时间分组
* **检查自定义事件** - 为服务设置的自定义事件，按事件类型分组。

稍后可从“趋势”窗口左上角的“查看常见的遥测分析类型”按钮获取这些预先构建的分析。

## <a name="visualize-trends-in-your-application"></a>可视化应用程序中的趋势
Application Insights 趋势基于应用的遥测数据创建时序视觉效果。 每个时序视觉效果显示某个时间范围内一种类型的遥测（按该遥测的某个属性分组）。 例如，可以查看过去 24 小时内的服务器请求（按来源国家/地区分组）。 在本示例中，视觉效果上的每个气泡代表一小时内某个国家/地区的服务器请求计数。

使用窗口顶部的控件可调整要查看的遥测类型。 首先，选择感兴趣的遥测类型：

* **遥测类型** - 服务器请求、异常、依赖项或自定义事件
* **时间范围** - 从过去 30 分钟到过去 3 天的任何时间段
* **分组依据** - 异常类型、问题 ID、国家/地区，等等。

然后，单击“分析遥测”以运行查询。

若要在视觉效果中的气泡之间导航：

* 通过单击选择气泡，该气泡将更新窗口底部的筛选器，并且只汇总特定时间段内发生的事件
* 双击气泡可以导航到“搜索”工具，查看在该时间段内发生的所有遥测事件
* 在按住 Ctrl 键的同时单击气泡可在视觉效果中取消选择该气泡。

> [!TIP]
> “趋势”和“搜索”工具配合工作，帮助在数千个遥测事件中找到服务中问题的原因。 例如，如果客户在某天下午告知应用的响应速度不快，便可以从“趋势”着手。 分析过去若干小时内对服务发出的请求（按响应时间分组）。 检查是否速度缓慢的请求数量是否超乎寻常。 然后双击该气泡以转到“搜索”工具，该工具已筛选出这些请求事件。 在“搜索”中，可以浏览这些请求的内容并导航到相关代码来解决问题。
> 
> 

## <a name="filter"></a>筛选器
使用窗口底部的筛选控件发现更明确的趋势。 若要应用某个筛选器，请单击其名称。 可以快速切换不同的筛选器，发现可能隐藏在遥测特定维度中的趋势。 如果在某个维度（例如异常类型）中应用一个筛选器，其他维度中的筛选器即使灰显，也仍可以点击。若要取消应用某个筛选器，请再次单击它。 在按住 Ctrl 的同时单击可以选择同一维度中的多个筛选器。

![趋势筛选器](./media/app-insights-visual-studio-trends/TrendsFiltering-750.png)

要应用多个筛选器怎么办？ 

1. 应用第一个筛选器。 
2. 单击第一个筛选器的维度名称旁边的“应用选定的筛选器并再次查询”按钮。 这只会针对匹配第一个筛选器的事件重新查询遥测。 
3. 应用第二个筛选器。 
4. 重复上述过程，在遥测的特定子集中查找趋势。 例如，名为“GET Home/Index”*且*来自德国*且*收到了 500 响应代码的服务器请求。 

若要取消应用上述筛选器之一，请单击维度对应的“删除选定的筛选器并再次查询”按钮。

![多个筛选器](./media/app-insights-visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>查找异常
“趋势”工具突出显示异常事件的气泡，与同一时序中的其他气泡相对比。 在“视图类型”下拉列表中，选择“时间存储桶中的计数(突出显示异常)”或“时间存储桶中的百分比(突出显示异常)”。 红色气泡表示异常。 异常定义为计数/百分比超过 2.1 乘以在过去两个时间周期（如果正在查看过去 24 小时的数据，则为 48 小时）内发生的计数/百分比的标准偏差的气泡。

![彩色圆点表示异常](./media/app-insights-visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> 在小型气泡的时序中查找可能看起来大小相似的离群值时，突出显示异常特别有用。  
> 
> 

## <a name="next"></a>后续步骤
|  |  |
| --- | --- |
| **[在 Visual Studio 中使用 Application Insights](app-insights-visual-studio.md)**<br/>搜索遥测、查看 CodeLens 中的数据以及配置 Application Insights。 一切尽在 Visual Studio 中。 |![右键单击项目，并依次选择“Application Insights”、“搜索”](./media/app-insights-visual-studio-trends/34.png) |
| **[添加更多数据](app-insights-asp-net-more.md)**<br/>监视使用情况、可用性、依赖项、异常。 集成来自记录框架的跟踪。 编写自定义遥测。 |![Visual Studio](./media/app-insights-visual-studio-trends/64.png) |
| **[使用 Application Insights 门户](app-insights-dashboards.md)**<br/>仪表板、功能强大的诊断和分析工具、警报、应用程序的实时依赖项映射和遥测导出。 |![Visual Studio](./media/app-insights-visual-studio-trends/62.png) |

