---
title: "在 Azure Application Insights 中创建自定义仪表板 | Microsoft Docs"
description: "使用 Azure Application Insights 创建自定义 KPI 仪表板的教程。"
keywords: 
services: application-insights
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb00711c762aa67c632d1cc582741970e3df0ef2
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>使用 Azure Application Insights 创建自定义 KPI 仪表板

你可以在 Azure 门户中创建多个仪表板，每个仪表板都包含跨不同资源组和订阅可视化多个 Azure 资源数据的磁贴。  你可以从 Azure Application Insights 中固定不同的图表和视图，以创建自定义仪表板，为你提供应用程序运行状况和性能的完整画面。  本教程将引导你完成自定义仪表板的创建，其中包括来自 Azure Application Insights 的多种类型的数据和可视化内容。  学习如何：

> [!div class="checklist"]
> * 在 Azure 中创建自定义仪表板
> * 从磁贴库添加磁贴
> * 将 Application Insights 中的标准指标添加到仪表板 
> * 将 Application Insights 中的自定义指标图标添加到仪表板
> * 将 Analytics 查询的结果添加到仪表板 



## <a name="prerequisites"></a>先决条件

完成本教程：

- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](app-insights-asp-net.md)。 

## <a name="log-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="create-a-new-dashboard"></a>创建新的仪表板
单个仪表板可以包含来自多个应用程序、资源组和订阅的资源。  通过为应用程序创建新的仪表板开始本教程。  

2.  在门户主屏幕上，选择“新建仪表板”。

    ![新建仪表板](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. 键入仪表板的名称。
4. 查看“磁贴库”，获取可以添加到仪表板中的各种磁贴。  除了从库添加磁贴之外，还可以将图表和其他视图直接从 Application Insights 固定到仪表板。
5. 找到“Markdown”磁贴，并将其拖动到仪表板中。  该磁贴允许添加 markdown 格式的文本，这对于向仪表板添加描述性文本而言是理想选择。
6. 将文本添加到磁贴属性，然后在仪表板画布上调整其大小。
    
    ![编辑 markdown 磁贴](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. 单击屏幕顶部的“完成自定义”退出磁贴自定义模式，然后“发布更改”以保存所做的更改。

    ![具有 markdown 磁贴的仪表板](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>添加运行状况概述
只有静态文本的仪表板并不是很有趣，所以现在从 Application Insights 添加磁贴，以显示有关应用程序的信息。  你可以从“磁贴库”添加 Application Insights 磁贴，也可以直接从“Application Insights”屏幕固定它们。  该操作允许将已经熟悉的图表和视图固定到仪表板之前对其进行配置。  首先添加应用程序的标准运行状况概述。  这不需要进行配置，并允许在仪表板中进行最起码的自定义操作。


1. 选择 Azure 菜单中的“Application Insights”，然后选择应用程序。
2. 在“概述时间线”中，选择上下文菜单，然后单击“固定到仪表板”。  这会向你查看的最后一个仪表板添加磁贴。  

    ![固定概述时间线](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. 在屏幕的顶部，单击“查看仪表板”以返回到仪表板。
4. “概述时间线”现已添加到仪表板。  单击并将其拖动至相应位置，然后单击“完成自定义”和“发布更改”。  仪表板现在具有包含一些有用信息的磁贴。

    ![带有概述时间线的仪表板](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>添加自定义指标图表
“指标”面板允许你逐渐将 Application Insights 收集的指标绘入图表，筛选器和分组为可选内容。  像 Application Insights 中的其他内容一样，你可以将此图表添加到仪表板。  这需要你先执行少量自定义操作。

1. 选择 Azure 菜单中的“Application Insights”，然后选择应用程序。
1. 选择“指标”。  
2. 已创建空图表，系统会提示你添加指标。  向图表添加指标，并选择性地添加筛选器和分组。  下面的示例显示了按成功分组的服务器请求数。  这给出了成功和不成功请求的运行视图。

    ![添加指标](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. 选择该图表的上下文菜单，然后选择“固定到仪表板”。  这会向你使用的最后一个仪表板添加视图。

    ![固定指标图表](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. 在屏幕的顶部，单击“查看仪表板”以返回到仪表板。

4. “概述时间线”现在已添加到仪表板。  单击并将其拖动至相应位置，然后依次单击“完成自定义”和“发布更改”。 

    ![包含指标的仪表板](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>指标资源管理器
“指标资源管理器”与“指标”类似，尽管前者在添加到仪表板时允许执行明显更多的自定义操作。  使用哪种来将指标绘入图表具体取决于你的特定偏好和要求。

1. 选择 Azure 菜单中的“Application Insights”，然后选择应用程序。
1. 选择“指标资源管理器”。 
2. 单击可编辑图表，并选择一个或多个指标和可选详细配置。  此示例显示折线图跟踪平均页面响应时间。
3. 单击右上角的图钉图标将图表添加到仪表板，然后将其拖动到相应位置。

    ![指标资源管理器](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. 将指标资源管理器磁贴添加到仪表板后，它将允许执行更多自定义操作。  右键单击该磁贴，然后选择“编辑磁贴”以添加自定义磁贴。  如有必要，请继续执行其他自定义操作。

    ![包含指标资源管理器的仪表板](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. 现在你已将指标资源管理器图表添加到仪表板。

    ![包含指标资源管理器的仪表板](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>添加 Analytics 查询
Azure Application Insights Analytics 提供了丰富的查询语言，使你能够分析 Application Insights 收集的所有数据。  与图表和其他视图一样，你可以将 Analytics 查询的输出添加到仪表板。   

由于 Azure Applications Insights Analytics 是一项独立的服务，因此需要向其共享你的仪表板以包含 Analytics 查询。  当共享 Azure 仪表板时，你可以将其作为 Azure 资源进行发布，从而使其可供其他用户和资源使用。  

1. 在仪表板屏幕顶部，单击“共享”。

    ![发布仪表板](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. 保持“仪表板名称”不变，并选择“订阅名称”以共享仪表板。  单击“发布” 。  仪表板现在可用于其他服务和订阅。  你可以定义具有仪表板访问权限的特定用户（可选）。
1. 选择 Azure 菜单中的“Application Insights”，然后选择应用程序。
2. 单击屏幕顶部的“Analytics”以打开 Analytics 门户。

    ![启动 Analytics](media/app-insights-tutorial-dashboards/start-analytics.png)

3. 键入以下查询，将返回前 10 个请求最多的页面及其请求次数：

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. 单击“转到”来验证查询的结果。
5. 单击图钉图标，然后选择仪表板的名称。  此选项选择的仪表板与先前步骤中所选的仪表板不同（后者使用上一个仪表板）的原因是：Analytics 控制台是一项独立服务，需要从所有可用的共享仪表板中进行选择。

    ![固定 Analytics 查询](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. 在返回仪表板之前，请添加其他查询，但这次将其呈现为图表，这样你可以在仪表板中查看可视化 Analytics 查询的不同方法。  从以下查询开始，该查询总结了前 10 个最常出现异常的操作。

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. 选择“图表”，然后更改为“圆环图”以可视化输出。

    ![Analytics 图表](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. 单击图钉图标将图表固定到仪表板，然后选择该链接以返回到仪表板。
4. 查询的结果现在以你选择的格式添加到仪表板。  单击每个结果并拖动到相应位置，然后单击“完成编辑”。
5. 右键单击每个磁贴，然后选择“编辑标题”以为其提供描述性标题。

    ![具有 Analytics 的仪表板](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. 单击“发布更改”将更改提交到仪表板，其中包含来自 Application Insights 的各种图表和可视化内容。


## <a name="next-steps"></a>后续步骤
现在你已经学会了如何创建自定义仪表板，了解一下 Application Insights 文档的其余部分吧，包括案例研究。

> [!div class="nextstepaction"]
> [深度诊断](app-insights-devops.md)
