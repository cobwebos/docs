---
title: 教程 - 在 Azure Monitor 中创建指标图表
description: 了解如何使用 Azure 指标资源管理器创建第一个指标图表。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.subservice: metrics
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: bwren
ms.openlocfilehash: 7d9360840912614b6ae89d958d90de962b36506d
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689955"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>教程：在 Azure Monitor 中创建指标图表
指标资源管理器是 Azure 门户中 Azure Monitor 的一项功能，通过它可以根据指标值创建图表，直观地关联趋势，以及调查指标值中的高峰和凹陷。 使用指标资源管理器调查 Azure 资源的运行状况和利用率，或根据自定义指标绘制图表。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 选择要为其绘制图表的指标
> * 执行不同的指标值聚合
> * 修改图表的时间范围和粒度

## <a name="prerequisites"></a>必备条件

要完成本教程，需要一个要监控的 Azure 资源。 可以使用 Azure 订阅中支持指标的任何资源。 若要确定资源是否支持指标，请在 Azure 门户中前往其菜单，并验证菜单的“监视”部分中是否有“指标”选项   。


## <a name="log-in-to-azure"></a>登录 Azure
通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。

## <a name="open-metrics-explorer-and-select-a-scope"></a>打开指标资源管理器并选择范围
可以从 Azure Monitor 菜单或从 Azure 门户中资源的菜单打开指标资源管理器。 无论采用哪种方式，都可使用所有资源的指标。 

1. 从 Azure Monitor 菜单或从资源的菜单的“监视”部分选择“指标”    。

1. 选择“范围”，即要查看其指标的资源  。 如果从资源的菜单中打开了指标资源管理器，则该范围已填充完毕。

    ![选择范围](media/tutorial-metrics-explorer/scope-picker.png)

2. 如果范围具有多个命名空间，请选择“命名空间”  。 命名空间只是一种为了轻松查找指标而组织指标的方式。 例如，存储帐户具有单独的命名空间，用于存储文件、表、Blob 和队列指标。 许多资源类型只有一个命名空间。

3. 从所选范围和命名空间的可用指标列表中选择指标。

    ![选择指标](media/tutorial-metrics-explorer/metric-picker.png)

4. （可选）更改指标聚合  。 这定义指标值将如何跨图的时间粒度进行聚合。 例如，如果时间粒度设置为 15 分钟，聚合设置为“求和”，则图中的每个点都将是每 15 分钟时间段内收集的所有值的总和。

    ![图表](media/tutorial-metrics-explorer/chart.png)

5. 如果要查看在同一图表中绘制的多个指标，请使用“添加指标”按钮，并重复这些步骤  。 对于一个视图中的多个图表，选择“新建图表”按钮  。

## <a name="select-a-time-range-and-granularity"></a>选择时间范围和粒度

默认情况下，图表显示最近 24 小时内的指标数据。 使用时间选取器更改图表的“时间范围”或更改“时间粒度”（定义每个数据点的时间范围）。   图表使用指定的聚合来计算指定时间粒度内的所有采样值。

![更改时间范围面板](media/tutorial-metrics-explorer/time-picker.png)


使用“时间刷”来调查图表中值得注意的区域（如高峰或凹陷）  。 将鼠标指针放在区域的开头位置，单击并按住鼠标左键，拖动到区域的另一侧，然后松开按钮。 图表将在该时间范围内放大。 

![时间刷](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>应用维度筛选器和拆分
请参阅以下有关高级功能的参考资料，使用这些功能可以对指标进行其他分析并确定数据中的可能的异常值。

- 通过[筛选](../platform/metrics-charts.md#apply-filters-to-charts)可以选择要包含在图表中的维度值。 例如，在为“服务器响应时间”指标绘制图表时，你可能希望仅显示成功的请求  。 

- [拆分](../platform/metrics-charts.md#apply-splitting-to-a-chart)可以控制图表是针对维度的每个值显示单独行，还是将值聚合为一行。 例如，你可能希望看到针对所有服务器实例中的平均响应时间的一行，或者可能希望看到针对每个服务器的单独的行。 

请参阅应用了筛选和拆分的[图表示例](../platform/metric-chart-samples.md)。

## <a name="advanced-chart-settings"></a>高级图表设置

可以自定义图表样式和标题并修改高级图表设置。 完成自定义后，将其固定到仪表板以保存工作。 你还可以配置指标警报。 请参阅 [Azure 指标资源管理器的高级功能](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis)，了解 Azure Monitor 指标资源管理器的这些功能和其他高级功能。


## <a name="next-steps"></a>后续步骤
现在，你已了解如何在 Azure Monitor 中使用指标，接下来了解如何使用指标来发送主动警报。

> [!div class="nextstepaction"]
> [使用 Azure Monitor 创建、查看和管理指标警报](../platform/alerts-metric.md)

