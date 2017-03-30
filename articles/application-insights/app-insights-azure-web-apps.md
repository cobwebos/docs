---
title: "监视 Azure Web 应用的性能 | Microsoft 文档"
description: "Azure Web 应用的应用程序性能监视。 对负载和响应时间、依赖项信息绘制图表，并对性能设置警报。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: d7a8d669e75991f6707641e4ce53c9b63d04853c
ms.lasthandoff: 03/16/2017


---
# <a name="monitor-azure-web-app-performance"></a>监视 Azure Web 应用性能
在 [Azure 门户](https://portal.azure.com)中，可为 [Azure Web 应用](../app-service-web/app-service-web-overview.md)设置应用程序性能监视。 [Azure Application Insights](app-insights-overview.md) 将检测应用，将其活动的相关遥测数据发送到 Application Insights 服务，以便在其中存储和分析。 此处的指标图表和搜索工具可用于帮助诊断问题、改善性能以及评估使用情况。

## <a name="run-time-or-build-time"></a>运行时或生成时
可以使用以下两种方法之一来配置通过检测应用进行监视：

* **运行时** - 可以在 Web 应用激活后，选择性能监视扩展。 无需重建或重装应用。 获取一组标准包用于监视响应时间、成功率、异常、依赖项等。 
* **生成时** - 在开发期间，可以在应用中安装一个包。 此选项更灵活。 除了安装相同的标准包以外，还可以编写代码来自定义遥测，或发送自己的遥测数据。 可以根据应用域的语义记录特定的活动或记录事件。 

## <a name="run-time-instrumentation-with-application-insights"></a>Application Insights 的运行时检测
如果已在 Azure 中运行了 Web 应用，则已获得了一些监视信息：请求和错误率。 添加 Application Insights 可获得更多功能，例如响应时间监视、监视对依赖项的调用，智能检测和功能强大的分析查询语言。 

1. 在 Web 应用的 Azure 控制面板中，**选择“Application Insights”**。
   
    ![在“监视”下面选择“Application Insights”](./media/app-insights-azure-web-apps/05-extend.png)
   
   * 除非已通过另一种途径为此应用设置了 Application Insights 资源，否则请选择创建新资源。
2. 安装 Application Insights 后，可以**检测 Web 应用**。 
   
    ![检测 Web 应用](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)
3. **监视应用**。  [浏览数据](#explore-the-data)。

以后，可以根据需要使用 Application Insights 来生成和重新部署应用。

*如何删除 Application Insights 或改为发送到另一个资源？*

* 在 Azure 中，打开 Web 应用程序控制边栏选项卡，然后在“开发工具”下面打开“扩展”。 删除 Application Insights 扩展。 在“监视”下面选择“Application Insights”，然后创建或选择所需的资源。

## <a name="build-the-app-with-application-insights"></a>使用 Application Insights 生成应用
Application Insights 可以通过将 SDK 安装到应用中来提供更详细的遥测。 具体而言，可以收集跟踪日志，[编写自定义遥测](app-insights-api-custom-events-metrics.md)，以及获取更详细的异常报告。

1. **在 Visual Studio 中**（2013 Update 2 或更高版本），为项目配置 Application Insights。
   
    ![右键单击 Web 项目，然后选择“添加或配置 Application Insights”](./media/app-insights-azure-web-apps/03-add.png)
   
    如果系统提示登录，请使用 Azure 帐户的凭据。
   
    该操作会产生两种效果：
   
   1. 在 Azure 中创建 Application Insights 资源，这是存储、分析和显示遥测数据的位置。
   2. 将 Application Insights NuGet 包添加到代码（如果该包尚不存在），并将该包配置为向 Azure 资源发送遥测数据。
2. 可以通过在开发计算机上运行应用来**测试遥测** (F5)。
3. 以普通的方式**将应用发布**到 Azure。 

*如何改为向不同的 Application Insights 资源发送数据？*

* 在 Visual Studio 中右键单击项目，选择“配置 Application Insights”，然后选择所需的资源。 可以使用相应的选项来创建新资源。 重新生成并重新部署。

## <a name="explore-the-data"></a>浏览数据
1. 在 Web 应用控制面板上的“Application Insights”边栏选项卡中，可以看到“实时指标”，其中显示一两秒内发生的请求和失败。 重新发布应用时，该边栏选项卡非常有用 - 可以立即发现所有问题。
2. 单击整个 Application Insights 资源。

    ![单击](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    也可以直接从 Azure 资源导航栏转到该位置。

1. 单击任一图表可获取更多详细信息：
   
    ![在 Application Insights 概述边栏选项卡中单击某个图表](./media/app-insights-azure-web-apps/07-dependency.png)
   
    可以[自定义指标边栏选项卡](app-insights-metrics-explorer.md)。
2. 进一步单击可查看单个事件及其属性：
   
    ![单击某个事件类型可以打开根据该类型筛选的搜索结果](./media/app-insights-azure-web-apps/08-requests.png)
   
    请注意，单击“...”链接可打开所有属性。
   
    可以[自定义搜索](app-insights-diagnostic-search.md)。

若要针对遥测数据执行更强大的搜索，请使用[分析查询语言](app-insights-analytics-tour.md)。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>后续步骤
* [将 Azure 诊断配置为](app-insights-azure-diagnostics.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)以确保服务可用且做出快速响应。
* 每当操作事件发生或指标超过阈值时[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](app-insights-web-track-usage.md)。
* [设置可用性 Web 测试](app-insights-monitor-web-app-availability.md)，以便在站点关闭时发出警报。


