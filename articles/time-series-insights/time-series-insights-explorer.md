---
title: 使用资源管理器浏览数据 - Azure 时序见解 | Microsoft Docs
description: 了解如何使用 Azure 时序见解资源管理器查看 IoT 数据。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 49a763af8d936ec9fd15c59c908ba9015f803930
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568647"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure 时序见解 Gen1 资源管理器

> [!CAUTION]
> 这是一个 Gen1 的文章。

本文介绍 Azure 时序见解 Gen1 [资源管理器 Web 应用](https://insights.timeseries.azure.com/)的功能和选项。 Azure 时序见解资源管理器演示了该服务提供的，可在你自己的环境中访问的强大数据可视化功能。

Azure 时序见解是一种完全托管的分析、存储和可视化服务，让同时探索和分析数十亿 IoT 事件变得简单轻松。 它提供数据的全局视图，从而可以快速验证 IoT 解决方案并避免任务关键型设备出现代价高昂的故障时间。 可以近乎实时地发现隐藏的趋势、找出异常以及进行根本原因分析。

> [!TIP]
> 有关演示环境的指导教程，请阅读 [Azure 时序见解快速入门](time-series-quickstart.md)。

## <a name="video"></a>视频

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>了解如何使用 Azure 时序见解资源管理器查询数据。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>播放前面的视频 <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"使用 Azure IoT 解决方案加速器开始使用 Azure 时序见解"。</a>

## <a name="prerequisites"></a>先决条件

在可以使用 Azure 时序见解资源管理器之前，必须：

- 创建 Azure 时序见解环境。 有关详细信息，请阅读[如何开始使用 Azure 时序见解环境](./time-series-insights-get-started.md)。
- 提供对环境中帐户的[访问](time-series-insights-data-access.md)。
- 在环境中添加 [IoT 中心](time-series-insights-how-to-add-an-event-source-iothub.md)或[事件中心](time-series-insights-how-to-add-an-event-source-eventhub.md)事件源。

## <a name="explore-and-query-data"></a>浏览和查询数据

将事件源连接到 Azure 时序见解环境的几分钟内，你可以浏览和查询时序数据。

1. 若要开始，请在 Web 浏览器中打开 [Azure 时序见解资源管理器](https://insights.timeseries.azure.com/)。 在窗口左侧选择一个环境。 有权访问的所有环境均按字母顺序列出。

1. 选择环境后，使用顶部的“从”和“到”配置，或者将其选中并拖到所需的时间跨度   。 单击右上角的放大镜，或者右键单击所选时间跨度并选择“搜索”  。

1. 还可以通过选择“自动打开”  按钮，每分钟自动刷新可用性。 “自动打开”按钮仅适用于可用性图表，而不适用于主要可视化效果的内容  。

1. 单击 Azure 云图标会转到 Azure 门户中的环境。

   [![Azure 时序见解环境选择](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. 接下来，会显示一个图表，该图表显示的是所选时间范围内所有事件的计数。 在此有许多控件：

    - **搜索词编辑器面板**：术语空间是查询环境的位置。 它位于屏幕的左侧：
      - **度量值**：此下拉列表显示所有数值列（**双精度**）。
      - **拆分依据**：此下拉列表显示类别列（**字符串**）。
      - 可以启用梯级内插、显示最小值和最大值，以及在“度量值”旁边的控制面板中调整 Y 轴  。 还可以调整显示的是数据的计数、平均值还是总和。
      - 最多可以添加要在同一个 X 轴上查看的五个搜索词。 选择“添加”  添加新的搜索词，或者使用“克隆此搜索词”  按钮添加现有搜索词的副本。

        [![搜索词选择、筛选和查询面板](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **谓词**：使用谓词可以使用下表中列出的操作数集快速筛选事件。 如果通过选择或单击执行搜索，谓词将基于该搜索自动更新。 支持的操作数类型包括：

         |操作  |支持的类型  |注释  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **双精度**、**DateTime**、**TimeSpan**       |         |
         |**=** 、 **!=** 、 **<>**     | **字符串**、**布尔**、**双精度**、**DateTime**、**TimeSpan**、**NULL**        |         |
         |**IN**     | **字符串**、**布尔**、**双精度**、**DateTime**、**TimeSpan**、**NULL**        |  所有操作数应为同一类型或者是 **NULL** 常数。        |
         |**HAS**     | **字符串**        |  右侧只允许使用常量字符串文本。 不允许空字符串和 **NULL**。       |

      - **示例查询**

         [![Gen1 查询示例](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. 可以使用“间隔大小”  滑块工具在相同时间跨度内放大和缩小间隔。 使用该滑块可以更精确地控制在大量时间切片之间的移动，显示平滑的趋势直至小到毫秒的切片，从而可以显示并分析数据更精细、分辨率更高的剪切片段。 滑块的默认起点设置为从你的选择中获得的最佳数据视图，以均衡分辨率、查询速度和粒度。

1. 使用“时间画笔”工具可以轻松从一个时间跨度导航到另一个时间跨度。 

1. 选择“保存”  图标可以保存当前查询，并与环境的其他用户共享该查询。 选择“打开”  图标时，可以查看所有已保存的查询和有权访问的环境中的其他用户的任何共享查询。

   [![查询](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>可视化数据

1. 使用“透视视图”  工具可以查看最多四个唯一查询的同步视图。 “透视视图”按钮位于图表右上角  。

   [![选择要添加到透视窗格中的查询](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. 查看图表可以直观浏览数据；使用“图表”工具  ：

    - **选择**或**单击**特定的时间跨度或单个数据系列。
    - 在选择的时间跨度内可以缩放或浏览事件。
    - 在数据系列内，可以按另一列拆分系列、将系列作为新术语添加、只显示已选系列、排除已选系列、固定该系列或从已选系列中浏览事件。
    - 在图表左侧的筛选器区域中，可以查看显示的所有数据系列并按值或名称重新排序。 还可以查看所有数据系列或者任意已固定或取消固定的系列。 可以选择一个数据系列并按另一列拆分该系列、将系列作为新搜索词添加、只显示已选系列、排除已选系列、固定该系列或从已选系列中浏览事件。
    - 同时查看多个术语时，可以堆叠、取消堆叠、查看有关数据系列的其他数据，以及对所有搜索词使用同一个 Y 轴。 请使用图表右上角的按钮。

    [![图表工具右上角选项设置](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. 使用**热度地图**可以快速发现给定查询中的唯一或异常数据系列。 仅有一个搜索术语能可视化为热度地图。

    [时序见解资源管理器热度地图图表](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)] (媒体/时间系列-insights-资源管理器/tsi-ga-example-heatmap-charting.png # lightbox) 

1. 通过选择或右键单击浏览事件时，会显示“事件”面板  。 在这里，可以查看所有原始事件并可将事件导出为 JSON 或 CSV 文件。 Azure 时序见解存储所有原始数据。

    [![事件](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. 浏览事件以公开模式和列统计信息之后，选择“统计信息”  选项卡。

    - **模式**：此功能主动显示所选数据区域中最具统计意义的模式。 无需查看数千个事件来了解最需要花费时间和精力的模式。 你可以使用 Azure 时序见解直接跳转到这些具有重大统计意义的模式继续进行分析。 此功能也有助于对历史数据进行事后调查。
    - **列统计信息**：列统计信息提供图表和表格，细分选定时间范围内所选数据系列的每列中的数据。

      [![“统计信息”列图表和选项](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

现在，你已了解 Azure 时序见解资源管理器 Web 应用中可用的关键功能、配置设置和显示选项。

## <a name="next-steps"></a>后续步骤

- 了解如何在 Azure 时序见解环境中[诊断并解决问题](time-series-insights-diagnose-and-solve-problems.md)。

- 学习 [Azure 时序见解快速入门](time-series-quickstart.md)指导教程。
