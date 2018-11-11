---
title: 监视 Azure Web 应用的性能 | Microsoft 文档
description: Azure Web 应用的应用程序性能监视。 对负载和响应时间、依赖项信息绘制图表，并对性能设置警报。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: e88d38e6d95459cc1c41adecee6b7ece659eac99
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957222"
---
# <a name="monitor-azure-web-app-performance"></a>监视 Azure Web 应用性能
在 [Azure 门户](https://portal.azure.com)中，可为 [Azure Web 应用](../app-service/app-service-web-overview.md)设置应用程序性能监视。 [Azure Application Insights](app-insights-overview.md) 将检测应用，将其活动的相关遥测数据发送到 Application Insights 服务，以便在其中存储和分析。 此处的指标图表和搜索工具可用于帮助诊断问题、改善性能以及评估使用情况。

## <a name="run-time-or-build-time"></a>运行时或生成时
可以使用以下两种方法之一来配置通过检测应用进行监视：

* **运行时** - 可以在 Web 应用激活后，选择性能监视扩展。 无需重建或重装应用。 获取一组标准包用于监视响应时间、成功率、异常、依赖项等。 
* **生成时** - 在开发期间，可以在应用中安装一个包。 此选项更灵活。 除了安装相同的标准包以外，还可以编写代码来自定义遥测，或发送自己的遥测数据。 可以根据应用域的语义记录特定的活动或记录事件。 

## <a name="run-time-instrumentation-with-application-insights"></a>Application Insights 的运行时检测
如果已在 Azure 中运行了 Web 应用，则已获得了一些监视信息：请求和错误率。 添加 Application Insights 可获得更多功能，例如响应时间监视、监视对依赖项的调用，智能检测和功能强大的 Log Analytics 查询语言。 

1. 在 Web 应用的 Azure 控制面板中，**选择“Application Insights”**。

    ![在“设置”下选择“Application Insights”](./media/app-insights-azure-web-apps/settings-app-insights.png)

   * 除非已为此应用设置了 Application Insights 资源，否则请选择创建新资源。 

    > [!NOTE]
    > 当单击“确定”来创建新资源时，将提示你**应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的 Web 应用，这样做还会**触发 Web 应用重新启动**。 

    ![检测 Web 应用](./media/app-insights-azure-web-apps/create-resource.png)

2. 在创建资源后，选择“为 Web 应用检查并更新 Application Insights 扩展”以确认扩展是最新的。

     ![检查并更新站点扩展](./media/app-insights-azure-web-apps/check-and-update.png)

3. 安装 Application Insights 后，可以**检测 Web 应用**。

   为页面视图和用户遥测**启用客户端监视**。

   * 选择“设置”>“应用程序设置”
   * 在“应用设置”下添加新的键/值对：

    键：`APPINSIGHTS_JAVASCRIPT_ENABLED`

    值： `true`
   * **保存**设置并**重新启动**应用。
4. 通过选择“设置” > “Application Insights” > “在 Application Insights 中查看更多”来浏览你的应用的监视数据。

以后，可以根据需要使用 Application Insights 生成应用。

*如何删除 Application Insights 或改为发送到另一个资源？*

* 在 Azure 中，打开 Web 应用程序控制边栏选项卡，并在“开发工具”下面打开“扩展”。 删除 Application Insights 扩展。 在“监视”下面选择“Application Insights”，并创建或选择所需的资源。

## <a name="build-the-app-with-application-insights"></a>使用 Application Insights 生成应用
Application Insights 可以通过将 SDK 安装到应用中来提供更详细的遥测。 具体而言，可以收集跟踪日志，[编写自定义遥测](app-insights-api-custom-events-metrics.md)，以及获取更详细的异常报告。

1. **在 Visual Studio 中**（2013 Update 2 或更高版本），为项目配置 Application Insights。

    右键单击 Web 项目，并选择“添加”>“Application Insights”或“项目” > “Application Insights” > “配置 Application Insights”。

    ![右键单击 Web 项目，并选择“添加或配置 Application Insights”](./media/app-insights-azure-web-apps/03-add.png)

    如果系统提示登录，请使用 Azure 帐户的凭据。

    该操作会产生两种效果：

   1. 在 Azure 中创建 Application Insights 资源，这是存储、分析和显示遥测数据的位置。
   2. 将 Application Insights NuGet 包添加到代码（如果该包尚不存在），并将该包配置为向 Azure 资源发送遥测数据。
2. 可以通过在开发计算机上运行应用来**测试遥测** (F5)。
3. 以普通的方式**将应用发布**到 Azure。 

*如何改为向不同的 Application Insights 资源发送数据？*

* 在 Visual Studio 中右键单击项目，选择“配置 Application Insights”，并选择所需的资源。 可以使用相应的选项来创建新资源。 重新生成并重新部署。

## <a name="more-telemetry"></a>其他遥测数据

* [网页加载数据](app-insights-javascript.md)
* [自定义遥测](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>后续步骤
* [在实时应用上运行探查器](app-insights-profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 监视 Azure Functions
* [将 Azure 诊断配置为](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../monitoring/monitoring-data-collection.md)以确保服务可用且做出快速响应。
* 每当操作事件发生或指标超过阈值时[接收警报通知](../monitoring-and-diagnostics/monitoring-overview-alerts.md)。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](app-insights-javascript.md)。
* [设置可用性 Web 测试](app-insights-monitor-web-app-availability.md)，以便在站点关闭时发出警报。

