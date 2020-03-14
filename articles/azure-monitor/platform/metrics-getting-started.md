---
title: Azure 指标资源管理器入门
description: 了解如何使用 Azure 指标资源管理器创建第一个指标图表。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248770"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure 指标资源管理器入门

## <a name="where-do-i-start"></a>在哪里开始
Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，通过它可以绘制图表、以直观的形式关联趋势及研究指标值的上升和下降。 使用指标资源管理器调查资源的运行状况和利用率。 按以下顺序启动：

1. [选取一个资源和一个指标](#create-your-first-metric-chart)，你会看到一个基本图表。 然后选择与调查相关的[时间范围](#select-a-time-range)。

1. 尝试[应用维度筛选器和拆分](#apply-dimension-filters-and-splitting)。 "筛选器" 和 "拆分" 允许分析指标的哪些段构成总体指标值，并识别可能的离群值。

1. 使用[高级设置](#advanced-chart-settings)自定义图表，并将其固定到仪表板。 [配置警报](alerts-metric-overview.md)，以在指标值超过或低于阈值时接收通知。

## <a name="create-your-first-metric-chart"></a>创建第一个度量值图表

若要创建指标图表，请在资源、资源组、订阅或 Azure Monitor 视图中打开 "**指标**" 选项卡，然后执行以下步骤：

1. 使用资源选取器，选择要查看其指标的资源。 （如果在特定资源的上下文中打开了**度量值**，则预先选择该资源）。

    > ![选择资源](./media/metrics-getting-started/resource-picker.png)

2. 对于某些资源，你必须选择一个命名空间。 命名空间只是一种为了轻松查找指标而组织指标的方式。 例如，存储帐户具有单独的命名空间，用于存储文件、表、Blob 和队列指标。 许多资源类型只有一个命名空间。

3. 从可用指标列表中选择一个度量值。

    > ![选择指标](./media/metrics-getting-started/metric-picker.png)

4. 还可以更改指标聚合。 例如，您可能希望图表显示指标的最小值、最大值或平均值。

> [!NOTE]
> 如果要查看在同一图表中绘制的多个指标，请使用“添加指标”按钮，并重复这些步骤。 对于一个视图中的多个图表，选择顶部的 "**添加图表**" 按钮。

## <a name="select-a-time-range"></a>选择时间范围

默认情况下，图表显示最近 24 小时内的指标数据。 使用**时间选取器**面板可更改图表的时间范围、放大或缩小。 

![更改时间范围面板](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> 使用**时间画笔**来调查图表（峰值或 dip）的关注区域。 将鼠标指针放在该区域的开头，单击并按住鼠标左键，拖动到区域的另一侧，然后松开按钮。 图表将在该时间范围内放大。 

## <a name="apply-dimension-filters-and-splitting"></a>应用维度筛选器和拆分

[筛选](metrics-charts.md#apply-filters-to-charts)和[拆分](metrics-charts.md#apply-splitting-to-a-chart)是具有维度的指标的强大的诊断工具。 这些功能显示了各种指标段（"维度值"）如何影响指标的总值，并允许你确定可能的离群值。

- 通过**筛选**可以选择要包含在图表中的维度值。 例如，你可能想要在绘制*服务器响应时间*指标图表时显示成功的请求。 需要对 "请求" 维度的 "*成功*" 应用筛选器。 

- **拆分**可以控制图表是针对维度的每个值显示单独行，还是将值聚合为一行。 例如，你可以在所有服务器实例中看到一个平均响应时间的行，或查看每个服务器的单独行。 需要对*服务器实例*维度应用拆分，以查看单独的行。

请参阅应用了筛选和拆分的[图表示例](metric-chart-samples.md)。 本文介绍了用于配置图表的步骤。

## <a name="advanced-chart-settings"></a>高级图表设置

可以自定义图表样式和标题并修改高级图表设置。 完成自定义后，将其固定到仪表板以保存工作。 你还可以配置指标警报。 按照[产品文档](metrics-charts.md)来了解 Azure Monitor 指标资源管理器的这些功能和其他高级功能。

## <a name="next-steps"></a>后续步骤

* [了解指标资源管理器的高级功能](metrics-charts.md)
* [疑难解答指标资源管理器](metrics-troubleshoot.md)
* [查看 Azure 服务的可用指标列表](metrics-supported.md)
* [查看已配置图表的示例](metric-chart-samples.md)
