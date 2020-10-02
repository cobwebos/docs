---
title: 显示时序见解资源管理器中的数据-Azure 时序见解 Gen2 |Microsoft Docs
description: 了解 Azure 时序见解资源管理器中可用的功能和选项。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 5810bea7920dbfe91a836814124cbe4c7f6f3bee
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653785"
---
# <a name="azure-time-series-insights-explorer"></a>Azure 时序见解资源管理器

本文介绍 Azure 时序见解 Gen2 [演示环境](https://insights.timeseries.azure.com/preview/demo)中可用的各种功能和选项。

## <a name="prerequisites"></a>必备条件

若要开始了解 Azure 时序见解资源管理器，必须执行以下操作：

* 已预配 Azure 时序见解 Gen2 环境。 阅读 [Azure 时序见解 Gen2](./time-series-insights-update-create-environment.md) 教程，详细了解如何设置实例。
* 提供对为帐户创建的 Azure 时序见解 Gen2 环境的[数据访问权限](./time-series-insights-data-access.md)。 可以向其他人以及自己提供访问。
* 将事件源添加到 Azure 时序见解 Gen2 环境，以将数据推送到环境：
  * 了解 [如何连接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * 了解 [如何连接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>了解 Azure 时序见解资源管理器

Azure 时序见解资源管理器由以下七个元素组成：

[![Azure 时序见解资源管理器概述](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [环境面板](#1-environment-panel)：显示所有 Azure 时序见解 Gen2 环境。
1. [导航栏](#2-navigation-bar)：用于在 " **分析** " 和 " **模型** " 页之间切换。
1. [层次结构树和搜索面板](#3-hierarchy-tree-and-search-panel)：用于选择和搜索要绘制到图表中的特定数据元素。
1. [时间序列良好](#4-time-series-well)：显示所有当前选定的数据元素。
1. [图表面板](#5-chart-panel)：显示当前工作图表。
1. [时间线](#6-time-editor-panel)：允许修改工作时间跨度。
1. [应用栏](#7-app-bar)：包含用户管理选项 (如当前租户) ，并允许你更改它们和语言设置。

## <a name="1-environment-panel"></a>1. 环境面板

"环境" 面板显示有权访问的所有 Azure 时序见解 Gen2 环境。 此列表包括 Gen2 环境以及 Gen1 环境。 只需选择要在其中立即使用的环境。

1. 选择显示的环境旁边的下拉箭头。

   [![环境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然后选择所需的环境。

## <a name="2-navigation-bar"></a>2. 导航栏

  [![导航栏](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

使用导航栏在两个视图之间进行选择：

* **分析**：使用它可以对建模或未建模的时序数据进行图表和执行丰富的分析。
* **模型**：用于将新的 Azure 时序见解 Gen2 类型、层次结构和实例推送到时序模型。

### <a name="model-authoring"></a>模型创作

Azure 时序见解 Gen2 支持对时序模型 (CRUD) 操作的完全创建、读取、更新和删除操作。

[![模型搜索面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **时序模型类型**：可以使用时序模型类型定义用于计算计算的变量或公式。 它们与给定的时序模型实例相关联。 一个类型可以包含一个或多个变量。
* **时序模型层次结构**：层次结构是数据的系统组织。 层次结构，描述时序模型中不同实例之间的关系。
* **时序模型实例**：实例是时间序列本身。 在大多数情况下，它们是 **DeviceID** 或 **AssetID**（环境中的资产的唯一标识符）。

若要了解有关时序模型的详细信息，请参阅 [时序模型](./concepts-model-overview.md)。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 层次结构树和搜索面板

利用层次结构树和搜索面板，可以轻松搜索和导航 [时序模型](./concepts-model-overview.md) 层次结构，以查找要在图表上显示的特定时序实例。 选择实例时，不仅会将它们添加到当前图表，还会将其添加到数据。

[![层次结构树和搜索面板](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

使用 "搜索结果" 窗格，还可以在层次结构视图或列表视图中查看结果，从而方便地查找要显示的实例。

## <a name="4-time-series-well"></a>4. 时序良好

这会显示实例字段和与所选时序模型实例相关联的其他元数据。 选中右侧的复选框可以在当前图表中隐藏或显示特定的实例。

  [![Gen2](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

您可以通过选择红色 **删除** (垃圾桶在元素左侧) 控制，从当前数据中删除特定数据元素。 同样，您还可以控制每个元素在图表中的显示方式。 您可以选择添加最小/最大阴影、数据点、按时间移动元素并按逐步方式可视化实例。

此外，探索控件使您可以轻松地创建时间移位和散点图。  

  [![好的布局选项](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果出现以下消息，则实例在选定时间范围内没有任何数据。 若要解决该问题，请增大时间跨度或确认实例在推送数据。
>
> ![无数据通知](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 图表面板

您可以通过图表将时序实例显示为线条。 可以通过单击 Web 控件来折叠环境面板、数据模型和时间跨度控件面板，以使图表更大。

  [![Gen2 图表概述](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **图表类型**：控制哪些数据元素可供可视化。

1. **间隔大小**： "间隔大小" 滑块工具使你能够在同一时间范围内放大和缩小时间间隔。 这样，就可以更精确地控制在较小的时间段之间移动，将平滑趋势显示为小到最小的切片，使你可以查看数据的精细、高分辨率剪切。 滑块的默认起始点设置为所选数据的最最优视图;平衡解析、查询速度和粒度。

1. **缩放和平移**：选择此控件可缩放和平移图表。

1. **Y 轴控件**：循环访问可用的 Y 轴视图选项：

    * `Stacked`：每行都有一个 Y 轴。
    * `Overlap`：用于在同一 Y 轴上堆叠多行，并根据所选的行更改 Y 轴数据。
    * `Shared`：所有 Y 轴数据一起显示。

1. **标记元素**：当前选定的数据元素及其关联的详细信息。

可以通过在按住鼠标的同时 **单击** 当前关系图上的数据点，然后将所选区域拖到所选终结点，进一步深化到特定数据切片。 **右键单击** 蓝色的选定区域，然后选择 " **缩放** "，如下所示。 您还可以在所选 timespan 中查看和下载遥测事件。

  [![Gen2 图缩放](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

执行 **缩放** 操作后，将显示所选的数据集。 选择 "格式" 控件以循环浏览数据的三个 y 轴表示形式。

  [![Gen2 图 y 轴](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

下面提供了 **重叠图表** 的示例：

  [![重叠图表选项](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

" **更多操作** " 按钮将展开以显示 " **下载为 CSV**"， **连接到 Power BI**，将 **图表数据显示为表**，并 **浏览原始事件** 选项。

  [![更多操作选项](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

在[Power BI 连接器](concepts-power-bi.md)中了解有关**连接到 Power BI**选项的详细信息。

## <a name="6-time-editor-panel"></a>6. 时间编辑器面板

使用 Azure 时序见解 Gen2 时，你将首先选择一个时间跨度。 所选时间跨度将控制可使用 Azure 时序见解 Gen2 更新小组件操作的数据集。

  [![时间选项面板](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 时间线的一部分以琥珀色或橙色突出显示，以指示热商店中可用的数据跨度。

以下 web 控件适用于 Azure 时序见解 Gen2，用于选择工作时间范围。

  [![探索良好控制](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **内部日期范围滑块控件**：通过将两个端点控件拖动到所需的时间范围内，可以使用这两个端点控件。 此内部日期范围内受外部日期范围滑块控件约束。

1. **增加和减少日期范围按钮**：通过选择所需间隔的任一按钮，增加或减少时间跨度。

1. **时间跨度折叠控件**：此 web 控件允许您隐藏除内部 "日期范围" 滑块工具之外的所有控件。

1. **外部日期范围滑块控件**：使用端点控件选择外部日期范围，该范围将可用于内部日期范围控件。

1. "**时间范围" 滑块控件**：使用它可以在预设的时间范围选择（如过去**30 分钟**、**最近12个小时**或**自定义范围**）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

   [![与选择面板之间的](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 应用栏

Azure 时序见解 Gen2 导航面板显示在应用的顶部。 它提供以下功能：

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![“共享”图标](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

选择新的“共享”图标可与团队共享 URL 链接。****

  [![共享实例 URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![租户选择](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 显示当前的 Azure 时序见解 Gen2 登录帐户信息。
* 使用它在可用的主题之间切换。
* 使用它查看 Gen2 [演示环境](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>主题选择

若要选择新主题，请选择右上角的个人资料图标。 然后选择“更改主题”。****

  [![主题选择](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 选择个人资料图标还可以设置语言。

Azure 时序见解资源管理器支持两个主题：

* **浅色主题**：本文档中所示的默认主题。
* **深色主题**：呈现资源管理器，如下所示：

  [![所选深色主题](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1 环境控件

### <a name="gen2-terms-panel"></a>Gen2 术语面板

本部分仅适用于尝试在更新的 UI 中使用资源管理器的现有 Gen1 环境。 你可能想要结合使用 Gen1 产品和 Gen2 产品。 我们已将现有 UI 中的一些功能添加到更新的资源管理器，但可在新的 Azure 时序见解资源管理器中获取 Gen1 环境的完整 UI 体验。

将显示 Azure 时序见解 Gen2 条款面板，而不是层次结构。 术语面板允许您在您的环境中定义查询。 使用它还可以根据谓词筛选数据。

  [![其中的查询面板](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Azure 时序见解 Gen2 术语编辑器面板采用以下参数：

**其中**：使用 where 子句可以通过使用下表中列出的一组操作数来快速筛选您的事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括：

| 操作    | 支持的类型    | 注释 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | 双精度、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | String | 右侧只允许使用常量字符串文本。 不允许空字符串和 NULL。 |

若要了解有关受支持的查询操作和数据类型的详细信息，请参阅 [ (TSX) 的时序表达式 ](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。

### <a name="examples-of-where-clauses"></a>where 子句示例

  [![Where 子句示例](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**度量值**：一种下拉列表，其中显示所有数值列 (**双精度** 值，) 可以将其用作当前图表的元素。

**拆分依据**：此下拉列表显示模型中的所有可用分类列 (字符串) ，你可以通过对数据进行分组。 最多可以添加要在同一个 X 轴上查看的五个搜索词。 输入所需的参数，然后选择“添加”**** 以添加新的搜索词。

  [![查询和筛选视图一](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

可以通过选择可见图标，在图表面板中显示或隐藏元素，如下图所示。 若要彻底删除查询，请选择红色的“X”。****

  [![取消查询和筛选的选项](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解将 [数据引入](./concepts-ingestion-overview.md) 到你的环境中。

* 查看有关 [存储](concepts-storage.md)的文章。

* 了解 Azure 时序见解 Gen2 中的 [数据建模](./concepts-model-overview.md) 。

* 了解 [如何诊断和排查环境问题](./time-series-insights-update-how-to-troubleshoot.md) 。
