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
ms.openlocfilehash: 3306e888970d99132d17d4ccf967f074302412ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595451"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure 指标资源管理器入门

## <a name="where-do-i-start"></a>从哪里开始
Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，通过它可以绘制图表、以直观的形式关联趋势及研究指标值的上升和下降。 使用指标资源管理器调查资源的运行状况和利用率。 请按以下顺序开始：

1. [选取资源和指标](#create-your-first-metric-chart)并查看基本图表。 然后选择与调查相关的[时间范围](#select-a-time-range)。

1. 尝试[应用维度筛选器和拆分](#apply-dimension-filters-and-splitting)。 使用筛选器和拆分可以分析哪些指标段影响了整体指标值，以及识别可能的离群值。

1. 在将图表固定到仪表板之前使用[高级设置](#advanced-chart-settings)对其进行自定义。 [配置警报](alerts-metric-overview.md)，以便在指标值超过阈值或降到阈值以下时收到通知。

## <a name="create-your-first-metric-chart"></a>创建第一个指标图表

若要在资源、资源组、订阅或 Azure Monitor 视图中创建指标图表，请打开“指标”选项卡并执行以下步骤： 

1. 使用资源选取器选择要查看其指标的资源。 （如果已在特定资源的上下文中打开了“指标”，则系统已预先选择该资源）。

    > ![选择资源](./media/metrics-getting-started/resource-picker.png)

2. 对于某些资源，必须选取一个命名空间。 命名空间只是组织指标的一种方式，可方便你查找指标。 例如，存储帐户提供用于存储文件、表、Blob 和队列指标的独立命名空间。 许多资源类型只有一个命名空间。

3. 从可用指标列表中选择一个指标。

    > ![选择指标](./media/metrics-getting-started/metric-picker.png)

4. （可选）可以更改指标聚合。 例如，你可能希望图表显示指标的最小值、最大值或平均值。

> [!NOTE]
> 若要查看同一个图表中绘制的多个指标，请使用“添加指标”按钮并重复上述步骤。  若要在一个视图中添加多个图表，请选择顶部的“添加图表”按钮。 

## <a name="select-a-time-range"></a>选择时间范围

默认情况下，图表会显示最近 24 小时的指标数据。 使用“时间选取器”面板可更改时间范围，以及放大或缩小图表。  

![更改时间范围面板](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>应用维度筛选器和拆分

[筛选](metrics-charts.md#apply-filters-to-charts)和[拆分](metrics-charts.md#apply-splitting-to-a-chart)是用于处理包含维度的指标的强大诊断工具。 这些功能会显示各种指标段（“维度值”）如何影响指标的整体值，并让你识别可能的离群值。

- 使用**筛选**可以选择要将哪些维度值包含在图表中。 例如，在绘制“服务器响应时间”指标的图表时，你可能想要成功的请求。  需要对“请求成功”维度应用筛选器。  

- **拆分**控制图表是要对每个维度值显示单独的行，还是要将这些值聚合到一行。 例如，可以在一行中查看所有服务器实例的平均响应时间，也可以在不同的行中查看每台服务器的此项指标。 需要对“服务器实例”维度应用拆分才能查看单独的行。 

请参阅[图表的示例](metric-chart-samples.md)，具有筛选和拆分应用。 本文介绍的步骤用于配置图表。

## <a name="advanced-chart-settings"></a>高级图表设置

可以自定义图表样式、标题，以及修改高级图表设置。 完成自定义后，请将图表固定到仪表板以保存所做的工作。 还可以配置指标警报。 请参阅[产品文档](metrics-charts.md)来了解 Azure Monitor 指标资源管理器的上述功能和其他高级功能。

## <a name="next-steps"></a>后续步骤

* [了解指标资源管理器的高级功能](metrics-charts.md)
* [故障排除的指标资源管理器](metrics-troubleshoot.md)
* [查看 Azure 服务的可用指标列表](metrics-supported.md)
* [请参阅配置图表的示例](metric-chart-samples.md)
