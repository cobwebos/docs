---
title: Azure 指标资源管理器入门
description: 了解如何使用 Azure 指标资源管理器创建第一个指标图表。
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537331"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure 指标资源管理器入门

## <a name="where-do-i-start"></a>如何开始
Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，通过它可以绘制图表、以直观的形式关联趋势及研究指标值的上升和下降。 使用指标资源管理器来调查运行状况和资源的利用率。 按以下顺序启动：

1. [选择资源和指标](#create-your-first-metric-chart)并查看基本图表。 然后[选择时间范围](#select-a-time-range)这就是为您的调查相关。

1. 请尝试[在应用维度筛选器和拆分](#apply-dimension-filters-and-splitting)。 筛选器和拆分可以分析哪些细分市场的指标的作用于的整体指标值和确定可能离群值。

1. 使用[高级设置](#advanced-chart-settings-and-next-steps)之前固定到仪表板自定义图表。 [配置警报](alerts-metric-overview.md)度量值超过或低于阈值时接收通知。

## <a name="create-your-first-metric-chart"></a>创建第一个度量值图表

若要创建指标图表中，从你的资源、 资源组、 订阅或 Azure Monitor 视图，请打开**指标**选项卡上，执行以下步骤：

1. 使用资源选取器，选择你想要查看的指标的资源。 (如果您打开的资源是预选**指标**特定资源的上下文中)。

    > ![选择资源](./media/metrics-getting-started/resource-picker.png)

2. 对于某些资源，必须选择一个命名空间。 命名空间是只是一种方法来组织度量值，以便您可以轻松地找到它们。 例如，存储帐户具有独立的命名空间来存储文件、 表、 Blob 和队列的指标。 许多资源类型只能有一个命名空间。

3. 从可用度量值的列表中选择一个指标。

    > ![选择指标](./media/metrics-getting-started/metric-picker.png)

4. （可选） 可以更改度量值的聚合。 例如，您可能希望图表以显示指标的最小值、 最大值，或平均值。

> [!NOTE]
> 使用**添加的度量值**按钮，然后重复上述步骤，如果你想要查看同一个图表中绘制多个指标。 对于在一个视图中的多个图表，选择**添加图表**顶部的按钮。

## <a name="select-a-time-range"></a>选择时间范围

默认情况下，图表将显示在最近 24 小时内的指标数据。 使用**时间选取器**要更改的时间范围、 放大，或在图表上缩小工作区面板。 

![更改时间范围面板](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>应用维度筛选器和拆分

[筛选](metrics-charts.md#apply-filters-to-charts)并[拆分](metrics-charts.md#apply-splitting-to-a-chart)是功能强大的诊断工具，对于具有维度的指标。 这些功能显示了各种指标段 （"维度值"） 产生影响总体的度量值，并允许你识别可能离群值。

- **筛选**允许您选择的维度值包含在图表中。 例如，你可能想要绘制图表时显示成功的请求*服务器响应时间*指标。 需要在应用该筛选器*请求的成功*维度。 

- **拆分**控件是否该图表显示的单独行的某个维度，每个值或聚合为单个行的值。 例如，您可以平均响应时间在所有服务器实例，将看到一个行，或者查看单独为每个服务器的行。 需要应用上拆分*服务器实例*维度，若要查看单独的行。

请参阅[图表的示例](metric-chart-samples.md)，具有筛选和拆分应用。 本文介绍的步骤用于配置图表。

## <a name="advanced-chart-settings-and-next-steps"></a>高级的图表设置和后续步骤

可以自定义图表样式，标题，并修改高级的图表设置。 完成自定义项，则将其固定到仪表板以保存所做的工作。 此外可以配置指标警报。 请按照[产品文档](metrics-charts.md)若要了解有关这些和其他高级功能的 Azure Monitor 指标资源管理器。

## <a name="next-steps"></a>后续步骤

* [请参阅 Azure 服务的可用指标的列表](metrics-supported.md)
* [了解有关指标资源管理器的高级功能](metrics-charts.md)
* [请参阅配置图表的示例](metric-chart-samples.md)
