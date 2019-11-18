---
title: 可视化预览资源管理器中的数据-Azure 时序见解 |Microsoft Docs
description: 了解 Azure 时序见解预览资源管理器中可用的功能和选项。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 17ba808ebfabb68765cf35bbf0799d117bc6383b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133432"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure 时序见解预览资源管理器

本文介绍 Azure 时序见解预览[演示 web 应用程序](https://insights.timeseries.azure.com/preview/demo)中可用的各种功能和选项。

## <a name="prerequisites"></a>先决条件

若要开始使用 Azure 时序见解预览版资源管理器，必须：

* 预配了时序见解环境。 阅读[Azure 时序见解预览](./time-series-insights-update-create-environment.md)教程，详细了解如何设置实例。
* 提供对你为帐户创建的时序见解环境的[数据访问](./time-series-insights-data-access.md)。 可以向其他人以及自己提供访问。
* 向时序见解环境添加事件源以将数据推送到该环境：
  * 了解[如何连接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * 了解[如何连接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>浏览时序见解预览资源管理器

Azure 时序见解预览版浏览器由以下七个元素组成：

[![时序见解预览浏览器概述](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [环境面板](#1-environment-panel)：显示所有 Azure 时序见解环境。
1. [导航栏](#2-navigation-bar)：用于在 "**分析**" 和 "**模型**" 页之间切换。
1. [层次结构树和搜索面板](#3-hierarchy-tree-and-search-panel)：用于选择和搜索要绘制到图表中的特定数据元素。
1. [时间序列良好](#4-time-series-well)：显示所有当前选定的数据元素。
1. [图表面板](#5-chart-panel)：显示当前工作图表。
1. [时间线](#6-time-editor-panel)：允许修改工作时间跨度。
1. [应用栏](#7-app-bar)：包含你的用户管理选项（如当前租户），并允许你更改它们和语言设置。


## <a name="1-environment-panel"></a>1. 环境面板

环境面板中显示你有权访问的所有时序见解环境。 此列表包括即用即付（预览版）环境以及 S1/S2 环境（公开上市）。 只需单击要在其中立即使用的时序见解环境。

1. 选择显示的环境旁边的下拉箭头。

   [![环境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然后选择所需的环境。

## <a name="2-navigation-bar"></a>2. 导航栏

  [![导航栏](media/v2-update-explorer/navigation-bar.png)](media/v2-update-explorer/navigation-bar.png#lightbox)

使用导航栏在两个视图之间进行选择：

* **分析**：使用它可以对建模或未建模的时序数据进行图表和执行丰富的分析。
* **模型**：用于将新的时序见解预览版类型、层次结构和实例推送到时序见解模型。

### <a name="model-authoring"></a>模型创作

Azure 时序见解预览版支持对时序模型执行所有的创建、读取、更新和删除 (CRUD) 操作。

[![模型搜索面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **时序模型类型**：可以使用时序见解类型定义用于计算计算的变量或公式。 它们与给定的时序见解实例相关联。 一种类型可以具有一个或多个变量。
* **时序模型层次结构**：层次结构是数据的系统组织。 层次结构描述时序见解数据中不同实体之间的关系。
* **时序模型实例**：实例是时间序列本身。 在大多数情况下，它们是 **DeviceID** 或 **AssetID**（环境中的资产的唯一标识符）。

若要详细了解时序模型，请参阅[时序模型](./time-series-insights-update-tsm.md)。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 层次结构树和搜索面板

利用层次结构树和搜索面板，您可以轻松搜索和导航[时序模型](./time-series-insights-update-tsm.md)层次结构，以便查找您要在图表中显示的特定时序实例。 选择实例时，不仅会将它们添加到当前图表，还会将其添加到数据。 

[![层次结构树和搜索面板](media/v2-update-explorer/hierarchy-search.png)](media/v2-update-explorer/hierarchy-search.png#lightbox)

使用 "搜索结果" 窗格，还可以在层次结构视图或列表视图中查看结果，从而方便地查找要显示的实例。
 
## <a name="4-time-series-well"></a>4. 时序良好

井显示与所选时序见解实例关联的实例字段和其他元数据。 选中右侧的复选框可以在当前图表中隐藏或显示特定的实例。 

  [![预览井](media/v2-update-explorer/preview-well.png)](media/v2-update-explorer/preview-well.png#lightbox)

您可以通过选择元素左侧的 "红色**删除**" （垃圾桶）控件，从当前数据中删除特定数据元素。 同样，您还可以控制每个元素在图表中的显示方式。 您可以选择添加最小/最大阴影、数据点、按时间移动元素并按逐步方式可视化实例。 

此外，探索控件使您可以轻松地创建时间移位和散点图。  

  [![良好的布局选项](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果看到以下消息，表示实例在所选时间跨度内没有任何数据。 若要解决该问题，请增大时间跨度或确认实例在推送数据。
>
> ![无数据通知](media/v2-update-explorer/no-data-warning.png)

## <a name="5-chart-panel"></a>5. 图表面板

您可以通过图表将时序实例显示为线条。 可以通过单击 Web 控件来折叠环境面板、数据模型和时间跨度控件面板，以使图表更大。 

  [![预览图表概述](media/v2-update-explorer/chart-overview.png)](media/v2-update-explorer/chart-overview.png#lightbox)

1. **图表类型**：控制哪些数据元素可供可视化。

1. **间隔大小**： "间隔大小" 滑块工具使你能够在同一时间范围内放大和缩小时间间隔。 这能够更精确地控制在大量时间切片之间的移动，显示平滑的趋势直至小到毫秒的切片，从而允许查看数据更精细、分辨率更高的剪切片段。 滑块的默认起点设置为所选的数据的最佳视图；平衡分辨率、查询速度和粒度。

1. **缩放和平移**：单击此控件可缩放和平移图表。

1. **Y 轴控件**：循环访问可用的 Y 轴视图选项：

    * `Stacked`：每行都有一个 Y 轴。
    * `Overlap`：使用它在同一 Y 轴上堆叠多行，并根据所选的行更改 Y 轴数据。
    * `Shared`：所有 Y 轴数据一起显示。

1. **标记元素**：当前选定的数据元素及其关联的详细信息。

可以通过在按住鼠标的同时**单击**当前关系图上的数据点，然后将所选区域拖到所选终结点，进一步深化到特定数据切片。 **右键单击**灰显的选定区域，然后单击 "**缩放**"，如下所示。 您还可以在所选 timespan 中查看和下载遥测事件。

  [![预览图表 - 缩放](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

执行“缩放”操作后，会看到所选的数据集。 选择 "格式" 控件以循环浏览时序见解数据的三个 y 轴表示形式。

  [![预览图表 - Y 轴](media/v2-update-explorer/standard-chart.png)](media/v2-update-explorer/standard-chart.png#lightbox)

在这里，可以看到**重叠图表**的示例：

  [![重叠图表选项](media/v2-update-explorer/overlapping-chart.png)](media/v2-update-explorer/overlapping-chart.png#lightbox)

"**更多操作**" 按钮将展开以显示 "**以 CSV 格式下载**" 和 "**导出到 Power BI**选项"。

  [![更多操作选项](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

了解[时序见解本机 Power BI 连接器](concepts-power-bi.md)。

## <a name="6-time-editor-panel"></a>6. 时间编辑器面板

使用时序见解时，你将首先选择一个时间跨度。 所选时间跨度将控制可使用时序见解更新小组件操作的数据集。

  [![时间选项面板](media/v2-update-explorer/timeline-element.png)](media/v2-update-explorer/timeline-element.png#lightbox)

> [!TIP]
> 时间线的一部分以琥珀色或橙色突出显示，以指示热商店中可用的数据跨度。

用于选择工作时间范围的时序见解更新中提供了以下 web 控件。 

  [![探索良好控制](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **内部日期范围滑块控件**：通过将两个端点控件拖动到所需的时间范围内，可以使用这两个端点控件。 此内部日期范围内受外部日期范围滑块控件约束。

1. **增加和减少日期范围按钮**：通过选择所需间隔的任一按钮，增加或减少时间跨度。

1. **时间跨度折叠控件**：此 web 控件允许您隐藏除内部 "日期范围" 滑块工具之外的所有控件。

1. **外部日期范围滑块控件**：使用端点控件选择外部日期范围，该范围将可用于内部日期范围控件。

1. "**时间范围" 滑块控件**：使用它可以在预设的时间范围选择（如过去**30 分钟**、**最近12个小时**或**自定义范围**）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

   [![目标与来源选择面板](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 应用栏

时序见解预览版导航面板显示在时序见解应用的顶部。 它提供以下功能：

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![共享图标](media/v2-update-explorer/share-icon.png)](media/v2-update-explorer/share-icon.png#lightbox)

选择新的“共享”图标可与团队共享 URL 链接。

  [![共享实例 URL](media/v2-update-explorer/share-your-view.png)](media/v2-update-explorer/share-your-view.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![租户选择](media/v2-update-explorer/tenant-selection.png)](media/v2-update-explorer/tenant-selection.png#lightbox)

* 显示当前时序见解登录帐户信息。
* 使用该控件可在可用时序见解主题之间切换。
* 使用该控件可以查看预览版[演示 Web 应用](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>主题选择

若要选择新主题，请选择右上角的个人资料图标。 然后选择“更改主题”。

  [![主题选择](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 选择个人资料图标还可以设置语言。

Azure 时序见解预览版支持两个主题：

* **浅色主题**：本文档中所示的默认主题。
* **深色主题**：呈现资源管理器，如下所示：

  [![所选深色主题](media/v2-update-explorer/dark-theme-selected.png)](media/v2-update-explorer/dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 环境控件

### <a name="preview-terms-panel"></a>预览版搜索词面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 S1/S2 环境。 你可能想要结合使用正式版和预览版产品。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但是你可以在现有时序见解资源管理器中获得 S1/S2 环境的完整 UI 体验。 

你会看到时序见解搜索词面板（可在其中定义环境中的查询），而不是层次结构。 使用该控件可以基于谓词筛选数据。

  [![Where 查询面板](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

时序见解预览版术语编辑器面板采用以下参数：

**其中**：使用 where 子句可以通过使用下表中列出的一组操作数来快速筛选您的事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括：

| Operation | 支持的类型   | 说明 |
| --- | --- | --- |
| `<`、`>`、`<=`、`>=` | 双精度、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | String | 右侧只允许使用常量字符串文本。 不允许空字符串和 NULL。 |

若要详细了解支持的查询操作和数据类型，请参阅[时序表达式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

### <a name="examples-of-where-clauses"></a>where 子句示例

  [![Where 子句示例](media/v2-update-explorer/example-queries.png)](media/v2-update-explorer/example-queries.png#lightbox)

**度量值**：一个下拉列表，其中显示了可用作当前图表元素的所有数值列（**双精度**型）。

**拆分方式**：此下拉列表显示您的模型中的所有可用分类列（字符串），您可以通过对数据进行分组。 最多可以添加要在同一个 X 轴上查看的五个搜索词。 输入所需的参数，然后选择“添加”以添加新的搜索词。

  [![已查询和筛选视图 1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

可以通过选择可见图标，在图表面板中显示或隐藏元素，如下图所示。 若要彻底删除查询，请选择红色的“X”。

  [![取消查询和筛选的选项](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解 Azure 时序见解预览版中的[存储和流入量](./time-series-insights-update-storage-ingress.md)。

- 阅读有关[数据建模](./time-series-insights-update-tsm.md)的时序见解预览版文档。

- 了解[如何诊断和排查](./time-series-insights-update-how-to-troubleshoot.md)时序见解实例的问题。
