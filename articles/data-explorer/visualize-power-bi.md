---
title: 教程：在 Power BI 中可视化 Azure 数据资源管理器中的数据
description: 在本教程中，你将学习如何使用 Power BI 连接到 Azure 数据资源管理器，并可视化数据。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: ec1579792a6e247bf49946bb8609a626154fbd46
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037361"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>教程：在 Power BI 中可视化 Azure 数据资源管理器中的数据

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Power BI 是一种业务分析解决方案，可以用来可视化数据，并在组织内共享结果。 在本教程中，首先了解 Azure 数据资源管理器中视觉对象的呈现方式。 然后使用 Power BI 连接到 Azure 数据资源管理器，生成基于示例数据的报表，并将报表发布到 Power BI 服务。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。 如果没有注册 Power BI Pro，请在开始之前[注册免费试用版](https://app.powerbi.com/signupredirect?pbi_source=web)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 数据资源管理器中呈现视觉对象
> * 连接到 Power BI Desktop 中的 Azure 数据资源管理器
> * 使用 Power BI Desktop 中的数据
> * 使用视觉对象创建报表
> * 发布和共享报表

## <a name="prerequisites"></a>先决条件

除了 Azure 和 Power BI 订阅，需要以下条件才能完成本教程：

* [测试群集和数据库](create-cluster-database-portal.md)

* [StormEvents 示例数据](ingest-sample-data.md)。 [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/)（选择“免费下载”  ）

## <a name="render-visuals-in-azure-data-explorer"></a>在 Azure 数据资源管理器中呈现视觉对象

在跳转到 Power BI 之前，让我们看看如何在 Azure 数据资源管理器中呈现视觉对象。 这非常适用于一些快速分析。

1. 登录到 [https://dataexplorer.azure.com](https://dataexplorer.azure.com)。

1. 在左侧窗格中，选择包含 StormEvents 示例数据的测试数据库。

1. 将以下查询粘贴到右侧窗口，然后选择“运行”  。

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    此查询会根据状态统计天气事件。 然后，它将呈现为具有超过 1800 个天气事件的所有状态柱形图。

    ![事件柱形图](media/visualize-power-bi/events-column-chart.png)

1. 将以下查询粘贴到右侧窗口，然后选择“运行”  。

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    此查询统计了华盛顿州 7 月份的天气事件。 然后，它将呈现显示每个事件类型百分比的饼图。

    ![事件饼图](media/visualize-power-bi/events-pie-chart.png)

现在可以看下 Power BI，但在 Azure 数据资源管理器中，可以通过视觉对象执行更多操作。

## <a name="connect-to-azure-data-explorer"></a>连接到 Azure 数据资源管理器

现在，连接到 Power BI Desktop 中的 Azure 数据资源管理器。

1. 在 Power BI Desktop 的“主页”选项卡上选择“获取数据”，然后选择“更多”    。

    ![获取数据](media/visualize-power-bi/get-data-more.png)

1. 搜索“Azure 数据资源管理器”  ，选择“Azure 数据资源管理器(Beta)”，然后选择“连接”   。

    ![搜索和获取数据](media/visualize-power-bi/search-get-data.png)

1. 在“预览连接器”  屏幕中，选择“继续”  。

1. 在下一屏幕上，输入测试群集和数据库的名称。 群集应采用 `https://<ClusterName>.<Region>.kusto.windows.net` 格式。 输入“StormEvents”  作为表的名称。 将保留所有其他选项的默认值，然后选择“确定”  。

    ![群集、数据库、表选项](media/visualize-power-bi/cluster-database-table.png)

1. 在数据预览屏幕上，选择“编辑”  。

    表在 Power Query 编辑器中打开，可以在其中编辑行和列，然后导入数据。

## <a name="work-with-data-in-power-bi-desktop"></a>使用 Power BI Desktop 中的数据

现在，你已连接到 Azure 数据资源管理器，请在 Power Query 编辑器中编辑数据。 删除“BeginLat”  列中具有 NULL 值的行并完全删除“StormSummary”  JSON 列。 这些都是简单的操作，但导入数据时，还可以执行复杂转换。

1. 选择“BeginLat”  列的箭头，清除“NULL”  复选框，然后选择“确定”  。

    ![筛选列](media/visualize-power-bi/filter-column.png)

1. 右键单击“StormSummary”列标题，然后选择“删除”。  

    ![删除列](media/visualize-power-bi/remove-column.png)

1. 在“查询设置”  窗格中，将名称从“Query1”  更改为“StormEvents”  。

    ![更改查询名](media/visualize-power-bi/query-name.png)

1. 在功能区的“主页”  选项卡上，选择“关闭并应用”  。

    ![关闭并应用](media/visualize-power-bi/close-apply.png)

    Power Query 应用所做的更改，然后将示例数据导入到“数据模型”  。 接下来的几个步骤演示如何扩充该模型。 同样，这只是一个展示了可能的操作的简单示例。

1. 在主窗口左侧，选择数据视图。

    ![数据视图](media/visualize-power-bi/data-view.png)

1. 在功能区的“建模”  选项卡上，选择“新列”  。

    ![新列](media/visualize-power-bi/new-column.png)

1. 在公式栏中输入下面的数据分析表达式 (DAX) 公式，然后按 Enter。

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![编辑栏](media/visualize-power-bi/formula-bar.png)

    此公式创建列“DurationHours”  ，计算每个天气事件持续的小时数。 在下节的视觉对象中使用此列。

1. 滚动到表的右侧以查看列。

## <a name="create-a-report-with-visuals"></a>使用视觉对象创建报表

已导入数据并改进了数据模型，现在可以使用视觉对象生成报表。 添加基于事件持续时间的柱形图和显示作物损坏的地图。

1. 在窗口左侧，选择报表视图。

    ![报表视图](media/visualize-power-bi/report-view.png)

1. 在“可视化”  窗格中，选择“簇状柱形图”。

    ![添加柱形图](media/visualize-power-bi/add-column-chart.png)

    会向画布添加一个空白图。

    ![空白图](media/visualize-power-bi/blank-chart.png)

1. 在“字段”  列表中，选择“DurationHours”  和“状态”  。

    ![选择字段](media/visualize-power-bi/select-fields.png)

    现在已有一张图表显示一年时间里按状态统计的天气事件的总小时数。

    ![持续时间柱形图](media/visualize-power-bi/duration-column-chart.png)

1. 单击画布上柱形图之外任意位置。

1. 在“可视化”  窗格中，选择“地图”。

    ![添加地图](media/visualize-power-bi/add-map.png)

1. 在“字段”  列表中，选择“CropDamage”  和“状态”  。 调整地图大小以便可以清楚地看到美国各州。

    ![作物损坏地图](media/visualize-power-bi/crop-damage-map.png)

    气泡大小表示作物损坏的美元价值。 将鼠标悬停在气泡上方查看详细信息。

1. 移动和调整视觉对象的大小，使报表如下图所示。

    ![完成的报表](media/visualize-power-bi/finished-report.png)

1. 使用名称“storm-events.pbix”  保存报表。

## <a name="publish-and-share-the-report"></a>发布和共享报表

至此，在 Power BI 中完成的工作已为本地工作，使用的是 Power BI Desktop。 现在将报表发布到 Power BI 服务，可在其中与他人共享。

1. 在 Power BI Desktop 功能区的“主页”  选项卡上，选择“发布”  。

    ![发布按钮](media/visualize-power-bi/publish-button.png)

1. 如果尚未登录到 Power BI，请完成整个登录过程。

1. 依次选择“我的工作区”  、“选择”  。

    ![选择工作区](media/visualize-power-bi/select-workspace.png)

1. 完成发布后，选择“在 Power BI 中打开 storm-events.pbix”  。

    ![发布成功](media/visualize-power-bi/publishing-succeeded.png)

    报表在服务中打开，包含在 Power BI Desktop 中定义的相同视觉对象和布局。

1. 在报表右上角，选择“共享”  。

    ![共享按钮](media/visualize-power-bi/share-button.png)

1. 在“共享报表”  屏幕上，将添加组织中的某个同事，添加注释，然后选择“共享”  。

    ![共享报表](media/visualize-power-bi/share-report.png)

    如果你的同事拥有相应权限，他们可以访问你共享的报表。

## <a name="clean-up-resources"></a>清理资源

如果不想保留创建的报表，只需删除“storm-events.pbix”  文件。 如果要删除已发布的报表，请按照下列步骤操作。

1. 在“我的工作区”  下，向下滚动到“报表”  并查找“storm-events”  。

1. 选择“storm-events”  旁边的省略号 (. . .  )，然后选择“删除”  。

    ![删除报表](media/visualize-power-bi/remove-report.png)

1. 确认删除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编写查询](write-queries.md)
