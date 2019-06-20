---
title: 使用 Azure 时序见解资源管理器浏览数据 |Microsoft Docs
description: 本文介绍如何使用 Web 浏览器中的 Azure 时序见解资源管理器快速查看大数据的全局视图并验证 IoT 环境。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: a7905213d67fd1cb97a8e50c938427b7ad775924
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165775"
---
# <a name="azure-time-series-insights-explorer"></a>Azure 时序见解资源管理器

本文介绍的功能和选项在一般情况下可用性 Azure 时序见解[资源管理器 web 应用](https://insights.timeseries.azure.com/)。 在时序见解资源管理器演示服务所提供的功能强大的数据可视化功能，可在自己的环境中访问。

Azure 时序见解是一种完全托管的分析、存储和可视化服务，让同时探索和分析数十亿 IoT 事件变得简单轻松。 它提供数据的全局视图，从而可以快速验证 IoT 解决方案并避免任务关键型设备出现代价高昂的故障时间。 可以近乎实时地发现隐藏的趋势、找出异常以及进行根本原因分析。 时序见解资源管理器当前是公共预览版。

> [!TIP]
> 有关演示环境指导教程，请阅读[Azure 时序见解快速入门](time-series-quickstart.md)。

## <a name="video"></a>视频

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>了解如何通过使用时序见解资源管理器中查询数据。 </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>请参阅前面的视频<a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"入门使用时序见解通过使用 Azure IoT 解决方案加速器。"</a>

## <a name="prerequisites"></a>必备组件

在可以使用时序见解资源管理器之前，必须：

- 创建时序见解环境。 有关详细信息，请参阅[如何开始使用时序见解](./time-series-insights-get-started.md)。
- [提供访问](time-series-insights-data-access.md)到在环境中的帐户。
- 添加[IoT 中心](time-series-insights-how-to-add-an-event-source-iothub.md)或[事件中心](time-series-insights-how-to-add-an-event-source-eventhub.md)到它的事件源。

## <a name="explore-and-query-data"></a>浏览和查询数据

将事件源连接到时序见解环境的几分钟内，可以浏览和查询时序数据。

1. 若要开始，打开[时序见解资源管理器](https://insights.timeseries.azure.com/)web 浏览器中。 在窗口的左侧，选择一个环境。 有权访问的所有环境均按字母顺序列出。

1. 选择一个环境后，使用**从**并**到**配置在顶部，或单击，然后将拖到所需的时间跨度。 在右上角中，选择放大镜或右键单击所选时间范围并选择**搜索**。

1. 您还可以自动刷新可用性每隔一分钟通过选择**自动在**按钮。 **自动在**按钮仅适用于可用性图表，而不是主要的可视化效果的内容。

1. Azure 云图标转到你在 Azure 门户中的环境。

   [![时序见解环境](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. 接下来，将看到一个图表，该图表显示的是所选时间范围内所有事件的计数。 在此有许多控件：

    - **术语编辑器面板**:术语空间是查询环境的位置。 在屏幕的左侧找到它：
      - **度量值**：此下拉列表显示所有数值列 (**双精度型值**)。
      - **拆分依据**：此下拉列表显示分类列 (**字符串**)。
      - 可以启用梯级内插、 显示最小值和最大值，并调整控件面板的 y 轴旁边**度量值**。 您还可以调整数据是否显示为计数、 平均值或数据之和。
      - 您可以添加最多五个术语，若要查看同一 x 轴上。 使用**副本列表**按钮以添加更多条件，或选择**添加**以添加新术语。

        [![术语编辑器面板](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **谓词**：使用谓词可以通过使用下表中列出的操作数集快速筛选事件。 如果通过选择或单击进行搜索，谓词自动更新基于该搜索。 支持的操作数类型包括：

         |Operation  |支持的类型  |说明  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  双精度、DateTime、TimeSpan       |         |
         |`=`、`!=`、`<>`     | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL        |         |
         |IN     | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL        |  所有操作数应为同一类型或者是 NULL 常数。        |
         |HAS     | String        |  在右侧允许仅常量字符串文本。 不允许空字符串和 NULL。       |

      - 查询示例 

         [![示例查询](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. 可以使用**间隔大小**滑块工具以在相同时间范围内放大和缩小间隔。 滑块可更精确地控制移动大型扇区之间的时间，显示平滑的趋势直至切片小到毫秒，这允许你查看细化的高分辨率切割的数据。 滑块的起始点的默认设置为数据的最大程度优化视图从你的选择来平衡分辨率、 查询速度和粒度。

1. **时间画笔**工具轻松地从一个时间跨度导航到另一个。

1. 使用**保存**命令来保存当前查询，并且与环境的其他用户共享。 当你使用**打开**，可以看到所有已保存的查询，并有权访问的环境中的其他用户的任何共享的查询。

   [![查询](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>可视化数据

1. 使用**全景视图**工具，用于同步的最多四个唯一查询视图。 **全景视图**按钮是在图表的右上角。

   [![透视视图](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. 查看图表以可视化方式浏览你的数据，并使用**图表**工具：

    - **选择**或**单击**特定的时间范围或一个数据系列。
    - 在时间范围选择，可以放大或浏览事件。
    - 在数据系列内，可以按另一列拆分系列、将系列作为新术语添加、只显示已选系列、排除已选系列、固定该系列或从已选系列中浏览事件。
    - 在图表的左侧的筛选器区域中，可以看到所有显示的数据系列并按值或名称重新排序。 你还可以查看所有数据序列或任何固定或解锁的系列。 您可以选择一个数据系列和按另一列拆分系列、 将系列作为新术语添加、 只显示已选的系列、 排除已选的系列、 固定该系列或从已选系列中浏览事件。
    - 时同时查看多个词，可以堆叠、 取消堆叠、 查看有关数据序列的其他数据和所有术语使用同一个 y 轴。 使用图表右上角的按钮。

    [![图表工具](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. 使用**热度地图**来快速发现给定查询中唯一或异常数据系列。 仅有一个搜索术语能可视化为热度地图。

    [![热度地图](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. 通过选择或右键单击浏览事件时，则**事件**面板将可用。 在这里，可以看到所有原始事件并可将事件导出为 JSON 或 CSV 文件。 时序见解存储所有原始数据。

    [![事件](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. 选择**统计信息**后浏览事件以公开模式和列统计信息选项卡上。

    - **模式**:此功能主动显示所选的数据区域中的最具统计意义的模式。 您无需查看数千个事件，以了解哪些模式需要最多时间和精力。 带时序见解，你可以直接跳到这些具有重大统计意义的模式继续进行分析。 此功能也有助于对历史数据进行事后调查。
    - **列统计信息**：列统计信息提供图表和表格，细分的所选的数据系列每列中的数据通过所选时间范围。

      [![STATS](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

现在您已经看到的各种功能和时序见解资源管理器 web 应用中可用的选项。

## <a name="next-steps"></a>后续步骤

- 了解如何[诊断并解决问题](time-series-insights-diagnose-and-solve-problems.md)时序见解环境中。
- 采用引导式[Azure 时序见解快速入门](time-series-quickstart.md)教程。
