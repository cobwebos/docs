---
title: "在 Azure Application Insights 中浏览 HockeyApp 数据 | Microsoft Docs"
description: "使用 Application Insights 分析 Azure 应用的使用情况和性能。"
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: a925241d10b068e377fa9a11fc854db34c808343
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>在 Application Insights 中浏览 HockeyApp 数据

> [!NOTE]
> Visual Studio Mobile Center 现在是 Microsoft 建议的用于监视新移动应用的服务。 [了解如何使用 Mobile Center 和 Application Insights 设置应用](app-insights-mobile-center-quickstart.md)。
> 
> 

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) 是用于监视实时桌面和移动应用的服务。 可以从 HockeyApp 发送自定义和跟踪遥测数据，以监视使用情况和帮助进行诊断（此外，还能获取崩溃数据）。 使用 [Azure Application Insights](app-insights-overview.md) 的强大 [Analytics](app-insights-analytics.md) 功能可以查询此遥测数据流。 此外，可以[导出自定义和跟踪遥测数据](app-insights-export-telemetry.md)。 要启用这些功能，可以设置用于将 HockeyApp 自定义数据转发到 Application Insights 的网桥。

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp Bridge 应用
HockeyApp Bridge 应用是一项核心功能，可让用户通过 Analytics 和连续导出功能来访问 Application Insights 中的 HockeyApp 自定义和跟踪遥测。 借助上述这些功能，可以访问在创建 HockeyApp Bridge 应用后由 HockeyApp 所收集的自定义和跟踪事件。 让我们了解如何设置其中一个 Bridge 应用。

在 HockeyApp 中，打开“帐户设置”、[API 令牌](https://rink.hockeyapp.net/manage/auth_tokens)。 创建新令牌，或重复使用现有令牌。 所需的最低权限为“只读”。 创建 API 令牌的副本。

![获取 HockeyApp API 令牌](./media/app-insights-hockeyapp-bridge-app/01.png)

打开 Microsoft Azure 门户并[创建一个 Application Insights 资源](app-insights-create-new-resource.md)。 将应用程序类型设置为“HockeyApp Bridge 应用程序”：

![新建 Application Insights 资源](./media/app-insights-hockeyapp-bridge-app/02.png)

无需设置名称 - 会根据 HockeyApp 名称自动设置。

此时会显示 HockeyApp Bridge 字段。 

![输入网桥字段](./media/app-insights-hockeyapp-bridge-app/03.png)

输入前面记下的 HockeyApp 令牌。 执行此操作时，“HockeyApp 应用程序”下拉菜单中会填充所有 HockeyApp 应用程序。 选择要使用的应用程序，并完成余下的字段。 

打开新资源。 

请注意，数据需要经过一段时间才开始流动。

![等待数据的 Application Insights 资源](./media/app-insights-hockeyapp-bridge-app/04.png)

就这么简单！ 在 HockeyApp 检测的应用程序中收集的自定义和跟踪数据现在也可在 Application Insights 的 Analytics 和连续导出功能中使用。

让我们简单回顾一下可用的每项功能。

## <a name="analytics"></a>分析
Analytics 是功能强大的即席数据查询工具，可用于诊断和分析遥测数据，快速找出根本原因和模式。

![分析](./media/app-insights-hockeyapp-bridge-app/05.png)

* [详细了解 Analytics](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>连续导出
使用连续导出可将数据导出到 Azure Blob 存储容器。 如果数据所需的保留时间超过 Application Insights 目前提供的保留期间，此功能就非常有用。 可以将数据保留在 Blob 存储中，在 SQL 数据库或首选的数据仓库解决方案中进行处理。

[详细了解连续导出](app-insights-export-telemetry.md)

## <a name="next-steps"></a>后续步骤
* [将 Analytics 应用到数据](app-insights-analytics-tour.md)

