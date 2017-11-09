---
title: "Azure Application Insights 中的仪表板和导航 | Microsoft Docs"
description: "创建关键 APM 图表和查询的视图。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 9a4a839e7b5cc772fb9d4c57ed70484d90a87fdd
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Application Insights 门户中的导航与仪表板
[在项目中设置 Application Insights](app-insights-overview.md) 之后，有关应用性能和使用情况的遥测数据将出现在 [Azure 门户](https://portal.azure.com)中项目的 Application Insights 资源内。

## <a name="find-your-telemetry"></a>查找遥测数据
登录到 [Azure 门户](https://portal.azure.com)，并导航到为应用创建的 Application Insights 资源。

![单击“浏览”，选择“Application Insights”，然后选择应用。](./media/app-insights-dashboards/00-start.png)

应用的概述边栏选项卡（页面）显示应用的关键诊断指标，它是访问其他门户功能所要通过的网关。

![查看遥测数据的主要途径](./media/app-insights-dashboards/010-oview.png)

可以自定义任何图表和网格，并将其固定到仪表板。 这样即可将不同应用的关键遥测数据汇集到一个中央仪表板上。

## <a name="dashboards"></a>仪表板
登录到 [Microsoft Azure 门户](https://portal.azure.com)后，最先看到的是仪表板。 可以将所有最重要的 Azure 资源的图表整合到此处，包括来自 [Azure Application Insights](app-insights-overview.md) 的遥测数据。

![自定义的仪表板。](./media/app-insights-dashboards/31.png)

1. **导航到特定的资源**，例如 Application Insights 中的应用：使用左侧栏。
2. **返回当前仪表板**，或切换到最近查看的其他视图：使用左上角的下拉菜单。
3. **切换仪表板**：使用仪表板标题中的下拉菜单
4. 在仪表板工具栏中**创建、编辑和共享仪表板**。
5. **编辑仪表板**：将鼠标悬停在某个磁贴上，然后使用其顶部条形来移动、自定义或删除该磁贴。

## <a name="add-to-a-dashboard"></a>添加到仪表板
查看特别感兴趣的边栏选项卡或图表集时，可将其副本固定到仪表板。 下一次返回到该位置时，就能看到该副本。

![要固定某个图表，可将鼠标悬停在该图表上，并单击标题中的“...”。](./media/app-insights-dashboards/33.png)

1. 将图表固定到仪表板。 图表的副本会显示在仪表板上。
2. 将整个边栏选项卡固定到仪表板 - 该边栏选项卡会在仪表板上显示为可点击的磁贴。
3. 单击左上角可返回到当前仪表板。 然后，可以使用下拉菜单返回到当前视图。

请注意，图表分组成磁贴：一个磁贴可以包含多个图表。 可将整个磁贴固定到仪表板。

图表会自动刷新，刷新频率取决于图表的时间范围：

* 时间范围为低于 1 小时：每 5 分钟刷新一次
* 时间范围为 1 - 24 小时：每 15 分钟刷新一次
* 时间范围为 24 小时以上：（时间范围）/60。

### <a name="pin-any-query-in-analytics"></a>固定 Analytics 中的任何查询
还可以[将 Analytics 图表固定](app-insights-analytics-using.md#pin-to-dashboard)到[共享的](#share-dashboards-with-your-team)仪表板。 这样，便可以添加任意查询以及标准指标的图表。 

每小时自动重新计算结果。 单击图表上的“刷新”图标可立即重新计算。 （刷新浏览器不会重新计算。）

## <a name="adjust-a-tile-on-the-dashboard"></a>调整仪表板上的磁贴
将某个磁贴放到仪表板上后，可以调整该磁贴。

![将鼠标悬停在图表即可进行编辑。](./media/app-insights-dashboards/36.png)

1. 将图表添加到磁贴中。
2. 设置指标、分组依据维度和图表的样式（表、图形）。
3. 在图表中拖动鼠标可以放大；单击撤消按钮可以重置时间跨度；可针对磁贴中的图表设置筛选属性。
4. 设置磁贴标题。

从指标资源管理器边栏选项卡固定的磁贴提供的编辑选项比从“概述”边栏选项卡固定的磁贴要多。

固定的原始磁贴不受到编辑影响。

## <a name="switch-between-dashboards"></a>切换仪表板
可以保存多个仪表板并在它们之间切换。 固定图表或边栏选项卡时，它们将添加到当前仪表板。

![要在仪表板之间切换，请单击“仪表板”，并选择某个已保存的仪表板。 若要创建并保存新仪表板，请单击“新建”。 若要重新排列，请单击“编辑”。](./media/app-insights-dashboards/32.png)

例如，可以使用一个仪表板在团队工作室中全屏显示信息，使用另一个仪表板用于普通开发工作。

在仪表板上，边栏选项卡显示为磁贴：单击该磁贴可转到该边栏选项卡。 复制的图表将保留其原始位置。

![单击某个磁贴可打开它所代表的边栏选项卡](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>共享仪表板
创建仪表板之后，可将它与其他用户共享。

![在仪表板标题中单击“共享”](./media/app-insights-dashboards/41.png)

了解[角色和访问控制](app-insights-resources-roles-access-control.md)。

## <a name="app-navigation"></a>应用导航
概述边栏选项卡是访问应用相关信息所要通过的网关。

* **任何图表或磁贴** - 单击任一图表或磁贴可查看其中显示的内容的更详细信息。

### <a name="overview-blade-buttons"></a>“概述”边栏选项卡按钮
![“概述”边栏选项卡顶部导航栏](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**指标资源管理器**](app-insights-metrics-explorer.md) - 创建自己的性能和使用情况图表。
* [**搜索**](app-insights-diagnostic-search.md) - 调查特定事件的实例，例如请求、异常或日志跟踪。
* [**分析**](app-insights-analytics.md) - 功能强大的遥测数据查询工具。
* **时间范围** - 调整边栏选项卡上所有图表显示的范围。
* **删除** - 删除此应用的 Application Insights 资源。 还可以从应用代码中删除 Application Insights 包，或编辑应用中的[检测密钥](app-insights-create-new-resource.md#copy-the-instrumentation-key)，以便将遥测数据定向到不同的 Application Insights 资源。

### <a name="essentials-tab"></a>“概要”选项卡
* [检测密钥](app-insights-create-new-resource.md#copy-the-instrumentation-key) - 标识此应用资源。
* 定价 - 获取功能，设置用量限制。

### <a name="app-navigation-bar"></a>应用导航栏
![左侧导航栏](./media/app-insights-dashboards/app-left-nav-bar.png)

* **概述** - 返回到应用概述边栏选项卡。
* **活动日志** - 警报和 Azure 管理事件。
* [**访问控制**](app-insights-resources-roles-access-control.md) - 向团队成员和其他人提供访问权限。
* [**标记**](../azure-resource-manager/resource-group-using-tags.md) - 使用标记将你的应用与其他应用分组。

调查

* [**应用程序地图**](app-insights-app-map.md) - 显示应用程序组件的活动地图，其内容派生自依赖关系信息。
* [**智能检测**](app-insights-proactive-diagnostics.md) - 查看最近的性能警报。
* [**实时流**](app-insights-live-stream.md) - 一组固定的近乎实时的指标，在部署新的构建或调试时非常有用。
* [**可用性/Web 测试**](app-insights-monitor-web-app-availability.md) - 从世界各地将常规请求发送到 Web 应用。*
* [**故障、性能**](app-insights-web-monitor-performance.md) - 发往应用的请求以及从应用发往[依赖项](app-insights-asp-net-dependencies.md)的请求的异常、故障率和响应时间。
* [**性能**](app-insights-web-monitor-performance.md) - 响应时间和依赖项响应时间。
* [服务器](app-insights-web-monitor-performance.md) - 性能计数器。 [安装状态监视器](app-insights-monitor-performance-live-website-now.md)时可用。
* **浏览器** - 页面视图和 AJAX 性能。 [检测网页](app-insights-javascript.md)时可用。
* **使用情况** - 页面视图、用户和会话计数。 [检测网页](app-insights-javascript.md)时可用。

配置

* **入门** - 内联教程。
* **属性** - 检测密钥、订阅和资源 ID。
* [警报](app-insights-alerts.md) - 指标警报配置。
* [连续导出](app-insights-export-telemetry.md) - 配置如何将遥测数据导出到 Azure 存储。
* [性能测试](app-insights-monitor-web-app-availability.md#performance-tests) - 在网站上设置合成负载。
* [配额与定价](app-insights-pricing.md)以及[引入采样](app-insights-sampling.md)。
* **API 访问** - 创建[版本批注](app-insights-annotations.md)，适用于数据访问 API。
* [**工作项**](app-insights-diagnostic-search.md#create-work-item) - 连接到工作跟踪系统，以便可以在检查遥测数据时创建 Bug。

设置

* [**锁定**](../azure-resource-manager/resource-group-lock-resources.md) - 锁定 Azure 资源
* [**自动化脚本**](app-insights-powershell.md) - 导出 Azure 资源的定义，以便可以将它用作模板来创建新资源。


## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>后续步骤

|  |  |
| --- | --- |
| [指标资源管理器](app-insights-metrics-explorer.md)<br/>筛选器和分段指标 |![搜索示例](./media/app-insights-dashboards/64.png) |
| [诊断搜索](app-insights-diagnostic-search.md)<br/>查找和检查事件及其相关事件，以及创建 Bug |![搜索示例](./media/app-insights-dashboards/61.png) |
| [分析](app-insights-analytics.md)<br/>功能强大的查询语言 |![搜索示例](./media/app-insights-dashboards/63.png) |
