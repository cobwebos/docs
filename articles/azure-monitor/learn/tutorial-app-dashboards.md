---
title: 在 Azure Application Insights 中创建自定义仪表板 | Microsoft Docs
description: 使用 Azure Application Insights 创建自定义 KPI 仪表板的教程。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperfq1
ms.openlocfilehash: 1a83385c7f384f7727a0fd10e238c6511950abfe
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612630"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>使用 Azure Application Insights 创建自定义 KPI 仪表板

你可以在 Azure 门户中创建多个仪表板，每个仪表板都包含跨不同资源组和订阅可视化多个 Azure 资源数据的磁贴。  你可以从 Azure Application Insights 中固定不同的图表和视图，以创建自定义仪表板，为你提供应用程序运行状况和性能的完整画面。 本教程将引导你完成自定义仪表板的创建，其中包括来自 Azure Application Insights 的多种类型的数据和可视化内容。

 学习如何：

> [!div class="checklist"]
> * 在 Azure 中创建自定义仪表板
> * 从磁贴库添加磁贴
> * 将 Application Insights 中的标准指标添加到仪表板
> * 将 Application Insights 中的自定义指标图标添加到仪表板
> * 将日志（分析）查询的结果添加到仪表板

## <a name="prerequisites"></a>必备条件

完成本教程：

- 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](../app/asp-net.md)。

> [!NOTE]
> [了解仪表板的访问控制](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards)一文中讨论了使用仪表板所需的权限。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-new-dashboard"></a>创建新的仪表板

> [!WARNING]
> 如果将 Application Insights 资源移至其他资源组或订阅，则需要手动更新仪表板，方法是删除旧磁贴并将同一 Application Insights 资源中的新磁贴固定在新位置。

单个仪表板可以包含来自多个应用程序、资源组和订阅的资源。  通过为应用程序创建新的仪表板开始本教程。  

1. 在 Azure 门户左侧的菜单下拉列表中，选择“仪表板”。

    ![Azure 门户菜单下拉列表](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. 在仪表板窗格中，选择“新建仪表板”，然后选择“空白仪表板” 。

   ![新建仪表板](media/tutorial-app-dashboards/new-dashboard.png)

3. 键入仪表板的名称。
4. 查看“磁贴库”，获取可以添加到仪表板中的各种磁贴。  除了从库添加磁贴之外，还可以将 Application Insights 中的图表和其他视图直接固定到仪表板。
5. 找到“Markdown”磁贴，并将其拖动到仪表板中。  该磁贴允许添加 markdown 格式的文本，这对于向仪表板添加描述性文本而言是理想选择。 若要了解详细信息，请参阅[在 Azure 仪表板上使用 Markdown 磁贴显示自定义内容](../../azure-portal/azure-portal-markdown-tile.md)。
6. 将文本添加到磁贴属性，然后在仪表板画布上调整其大小。

    [![编辑 markdown 磁贴](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. 选择屏幕顶部的“完成自定义”以退出自定义模式。

## <a name="add-health-overview"></a>添加运行状况概述

包含静态文本的仪表板并不是很有趣，所以现在从 Application Insights 添加磁贴，以显示有关应用程序的信息。 你可以从“磁贴库”添加 Application Insights 磁贴，也可以直接从“Application Insights”屏幕固定它们。 该操作允许将已经熟悉的图表和视图固定到仪表板之前对其进行配置。  首先添加应用程序的标准运行状况概述。  这不需要进行配置，并允许在仪表板中进行最起码的自定义操作。


1. 在主屏幕上选择你的 **Application Insights** 资源。
2. 在“概述”窗格中，选择图钉图标![图钉图标](media/tutorial-app-dashboards/pushpin.png)，将磁贴添加到仪表板。
3. 在“固定到仪表板”选项卡中，选择要向其中添加磁贴的仪表板，或创建新的仪表板。
 
3. 右上角将显示一条通知，指出你的磁贴已固定到仪表板上。  选择通知中的“已固定到仪表板”以返回到仪表板或使用仪表板窗格。
4. 该磁贴现在已添加到仪表板。 选择“编辑”以更改磁贴的位置。 选择并将其拖动至相应位置，然后选择“完成自定义”。 仪表板现在具有包含一些有用信息的磁贴。

    [![编辑模式下的仪表板](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>添加自定义指标图表

“指标”面板允许你逐渐将 Application Insights 收集的指标绘入图表，筛选器和分组为可选内容。  像 Application Insights 中的其他内容一样，你可以将此图表添加到仪表板。  这需要你先执行少量自定义操作。

1. 在主屏幕中选择你的 **Application Insights** 资源。
1. 选择“指标”。  
2. 已创建空图表，系统会提示你添加指标。  向图表添加指标，并选择性地添加筛选器和分组。  下面的示例显示了按成功分组的服务器请求数。  这给出了成功和不成功请求的运行视图。

    [![添加指标](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. 在右侧选择“固定到仪表板”。

3.  右上角将显示一条通知，指出你的磁贴已固定到仪表板上。 选择通知中的“已固定到仪表板”以返回到仪表板或使用仪表板选项卡。

4. 该磁贴现在已添加到仪表板。 选择“编辑”以更改磁贴的位置。 选择磁贴并将其拖动至相应位置，然后选择“完成自定义”。

## <a name="add-logs-query"></a>添加日志查询

Azure Application Insights 日志提供了丰富的查询语言，使你能够分析 Application Insights 收集的所有数据。 与图表和其他视图一样，可以将日志查询的输出添加到仪表板。

1. 在主屏幕中选择你的 **Application Insights** 资源。
2. 选择左侧“监视”下的“日志”，打开“日志”选项卡。
3. 键入以下查询，将返回前 10 个请求最多的页面及其请求次数：

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. 选择“运行”来验证查询的结果。
5. 选择“固定”图标 ![图钉图标](media/tutorial-app-dashboards/pushpin.png) 然后选择仪表板的名称。

5. 在返回仪表板之前，请添加其他查询，但将其呈现为图表，以便你可以看到在仪表板中直观显示日志查询的不同方法。 从以下查询开始，该查询总结了前 10 个最常出现异常的操作。

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. 选择“图表”，然后更改为“圆环图”以可视化输出。

    [![具有以上查询的圆环图](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. 选择“固定”图标 ![图钉图标](media/tutorial-app-dashboards/pushpin.png) 将图表固定到仪表板，然后返回到仪表板。
7. 查询的结果现在以你选择的格式添加到仪表板。 选择每一个并将其拖动至相应位置，然后选择“完成自定义”。
8. 选择每个标题上的铅笔图标 ![铅笔图标](media/tutorial-app-dashboards/pencil.png) 来为其提供一个描述性标题。

## <a name="share-dashboard"></a>共享仪表板

1. 在仪表板的顶部，选择“共享”以发布更改。
2. 你可以定义具有仪表板访问权限的特定用户（可选）。 有关详细信息，请参阅[通过使用基于角色的访问控制来共享 Azure 仪表板](../../azure-portal/azure-portal-dashboard-share-access.md)。
3. 选择“发布”。

## <a name="next-steps"></a>后续步骤

现在你已经学会了如何创建自定义仪表板，了解一下 Application Insights 文档的其余部分吧，包括案例研究。

> [!div class="nextstepaction"]
> [深度诊断](../app/devops.md)
