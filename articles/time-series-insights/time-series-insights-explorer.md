---
title: 使用 Azure 时序见解资源管理器浏览数据 |Microsoft Docs
description: 本文介绍如何使用 Web 浏览器中的 Azure 时序见解资源管理器快速查看大数据的全局视图并验证 IoT 环境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 25f2c07678217b69699b881d53ee9d5f92f84be1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990107"
---
# <a name="azure-time-series-insights-explorer"></a>Azure 时序见解资源管理器

本文介绍 Azure 时序见解[资源管理器 web 应用](https://insights.timeseries.azure.com/)公开上市的功能和选项。 时序见解资源管理器演示服务提供的功能强大的数据可视化功能，可以在自己的环境中对其进行访问。

Azure 时序见解是一种完全托管的分析、存储和可视化服务，让同时探索和分析数十亿 IoT 事件变得简单轻松。 它提供数据的全局视图，从而可以快速验证 IoT 解决方案并避免任务关键型设备出现代价高昂的故障时间。 可以近乎实时地发现隐藏的趋势、找出异常以及进行根本原因分析。 时序见解资源管理器当前是公共预览版。

> [!TIP]
> 有关演示环境的指导教程，请参阅[Azure 时序见解快速入门](time-series-quickstart.md)。

## <a name="video"></a>视频

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>了解如何使用时序见解资源管理器查询数据。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>请参阅前面的视频<a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"使用 Azure IoT 解决方案加速器开始使用时序见解"。</a>

## <a name="prerequisites"></a>必备组件

在可以使用时序见解资源管理器之前，必须：

- 创建时序见解环境。 有关详细信息，请参阅[如何开始时序见解](./time-series-insights-get-started.md)。
- 在环境中提供对帐户的[访问权限](time-series-insights-data-access.md)。
- 向其添加[IoT 中心](time-series-insights-how-to-add-an-event-source-iothub.md)或[事件中心](time-series-insights-how-to-add-an-event-source-eventhub.md)事件源。

## <a name="explore-and-query-data"></a>浏览和查询数据

将事件源连接到时序见解环境的几分钟内，可以浏览和查询时序数据。

1. 首先，在 web 浏览器中打开[时序见解资源管理器](https://insights.timeseries.azure.com/)。 在窗口左侧，选择 "环境"。 有权访问的所有环境均按字母顺序列出。

1. 选择环境后，可以使用顶部的 "**从**" 和 "**到**" 配置，或者单击并拖动所需的 timespan。 选择右上角的放大镜，或右键单击所选 timespan，然后选择 "**搜索**"。

1. 还可以通过选择 "**自动打开**" 按钮，每分钟自动刷新可用性。 "**自动打开**" 按钮仅适用于可用性图表，而不适用于主要可视化对象的内容。

1. Azure 云图标会将你带到 Azure 门户的环境。

   [![时序见解环境](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. 接下来，将看到一个图表，该图表显示的是所选时间范围内所有事件的计数。 在此有许多控件：

    - **术语编辑器面板**：术语 "空间" 是指查询环境的位置。 它位于屏幕左侧：
      - **度量值**：此下拉列表显示所有数值列（**双精度**）。
      - **拆分依据**：此下拉列表显示分类列（**字符串**）。
      - 可以在 "**度量值**" 旁边的 "控制面板" 中启用步骤内插、显示最小值和最大值，并调整 y 轴。 还可以调整所显示的数据是数据的计数、平均值还是总计。
      - 你最多可以添加五个字词以便在同一 x 轴上查看。 使用 "**复制**" 按钮添加其他字词，或选择 "**添加**" 以添加一个新术语。

        [![术语编辑器面板](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **谓词**：使用谓词可以通过使用下表中列出的一组操作数来快速筛选事件。 如果通过选择或单击进行搜索，谓词会根据该搜索自动更新。 支持的操作数类型包括：

         |Operation  |支持的类型  |说明  |
         |---------|---------|---------|
         |`<`、`>`、`<=`、`>=`     |  双精度、DateTime、TimeSpan       |         |
         |`=`、`!=`、`<>`     | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL        |         |
         |IN     | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL        |  所有操作数应为同一类型或者是 NULL 常数。        |
         |HAS     | 字符串        |  右侧仅允许常量字符串。 不允许空字符串和 NULL。       |

      - 查询示例

         [![示例查询](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. 您可以使用 "**间隔大小**" 滑块工具来放大和缩小相同时间跨度内的间隔。 滑块可以更精确地控制在较小的时间段之间移动，这种方式可将平滑趋势缩小到最小为毫秒的切片，这允许你查看数据的精细、高分辨率切削量。 滑块的默认起始点设置为所选数据的最佳视图，以平衡分辨率、查询速度和粒度。

1. 利用**时间画笔**工具，可以轻松地从一个 timespan 导航到另一个 timespan。

1. 使用 "**保存**" 命令保存当前查询，并将其与环境中的其他用户共享。 使用 "**打开**" 时，可以看到所有已保存的查询，以及你有权访问的环境中其他用户的任何共享查询。

   [![查询](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>可视化数据

1. 使用 "**透视视图**" 工具可同时查看最多四个唯一查询。 "**透视视图**" 按钮位于图表的右上角。

   [![透视视图](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. 查看图表以直观地浏览你的数据，并使用**图表**工具：

    - **选择**或**单击**特定 timespan 或单个数据序列。
    - 在 timespan 选择范围内，可以缩放或浏览事件。
    - 在数据系列内，可以按另一列拆分系列、将系列作为新术语添加、只显示已选系列、排除已选系列、固定该系列或从已选系列中浏览事件。
    - 在图表左侧的 "筛选器" 区域中，可以看到所有显示的数据序列并按值或名称重新排序。 您还可以查看所有数据序列或任何固定或取消固定的序列。 您可以选择单个数据序列并按另一列拆分序列，将序列添加为新术语，只显示选定的序列，排除选定的序列，固定该系列，或从所选序列中浏览事件。
    - 同时查看多个字词时，可以堆积、取消堆叠、查看有关数据序列的其他数据，并在所有词中使用相同的 y 轴。 使用图表右上角的按钮。

    [![图表工具](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. 使用**热度地图**快速发现给定查询中的唯一或异常数据系列。 仅有一个搜索术语能可视化为热度地图。

    [![热度地图](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. 通过选择或右键单击浏览事件时，"**事件**" 面板将变为可用。 在这里，可以看到所有原始事件并可将事件导出为 JSON 或 CSV 文件。 时序见解存储所有原始数据。

    [![事件](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. 浏览事件后，选择 "**统计**信息" 选项卡以公开模式和列统计信息。

    - **模式**：此功能主动地显示所选数据区域中最具统计的数据模式。 您不必查看上千个事件，了解哪些模式需要最多的时间和精力。 使用时序见解，你可以直接跳转到这些统计的重要模式，以继续进行分析。 此功能也有助于对历史数据进行事后调查。
    - **列统计**信息：列统计信息提供图表和表，这些图表和表通过所选 timespan 从所选数据系列的每个列向下细分。

      [![统计信息](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

现在，你已了解了时序见解资源管理器 web 应用中可用的各种功能和选项。

## <a name="next-steps"></a>后续步骤

- 了解如何[诊断并解决](time-series-insights-diagnose-and-solve-problems.md)时序见解环境中的问题。

- 采用指导式[Azure 时序见解快速入门](time-series-quickstart.md)教程。
