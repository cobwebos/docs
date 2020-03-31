---
title: 预览资源管理器中可视化数据 - Azure 时间序列见解 |微软文档
description: 了解 Azure 时间序列预览资源管理器中可用的功能和选项。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861755"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure 时间序列见解预览资源管理器

本文介绍了 Azure 时间序列预览[版预览版 Web 应用程序中](https://insights.timeseries.azure.com/preview/demo)提供的各种功能和选项。

## <a name="prerequisites"></a>先决条件

若要开始使用 Azure 时序见解预览版资源管理器，必须：

* 预配时间序列见解环境。 通过阅读[Azure 时间序列见解预览](./time-series-insights-update-create-environment.md)教程，了解有关预配实例的更多信息。
* 提供对为帐户创建的时序见解环境[的数据访问](./time-series-insights-data-access.md)。 可以向其他人以及自己提供访问。
* 向时序见解环境添加事件源以将数据推送到该环境：
  * [了解如何连接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * [了解如何连接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>探索时序见解预览资源管理器

Azure 时间序列预览资源管理器包含以下七个元素：

[![时间序列见解 预览资源管理器概述](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [环境面板](#1-environment-panel)：显示所有 Azure 时间序列见解环境。
1. [导航栏](#2-navigation-bar)：允许您在 **"分析"** 和 **"模型"** 页之间切换。
1. [层次结构树和搜索面板](#3-hierarchy-tree-and-search-panel)：允许您选择和搜索要绘制的图表的特定数据元素。
1. [时间序列良好](#4-time-series-well)：显示当前选定的所有数据元素。
1. [图表面板](#5-chart-panel)：显示当前工作图表。
1. [时间轴](#6-time-editor-panel)：允许您修改工作时间范围。
1. [应用栏](#7-app-bar)：包含用户管理选项（如当前租户），并允许您更改它们和语言设置。


## <a name="1-environment-panel"></a>1. 环境面板

环境面板中显示你有权访问的所有时序见解环境。 该列表包括即用即付（预览）环境以及 S1/S2 环境（常规可用性）。 只需选择要立即获取的时间序列见解环境即可。

1. 选择显示的环境旁边的下拉箭头。

   [![环境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然后选择所需的环境。

## <a name="2-navigation-bar"></a>2. 导航栏

  [![导航栏](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

使用导航栏在两个视图之间进行选择：

* **分析**：使用它对建模或未建模的时间序列数据进行图表并执行丰富的分析。
* **模型**：使用它将新的时序见解预览类型、层次结构和实例推送到时序见解模型。

### <a name="model-authoring"></a>模型创作

Azure 时序见解预览版支持对时序模型执行所有的创建、读取、更新和删除 (CRUD) 操作。

[![模型搜索面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **时间序列模型类型**：您可以使用时序见解类型来定义变量或公式以执行计算。 它们与给定的时序见解实例相关联。 一个类型可以包含一个或多个变量。
* **时间序列模型层次结构**：层次结构是数据的系统组织。 层次结构描述时序见解数据中不同实体之间的关系。
* **时序模型实例**：实例是时间序列本身。 在大多数情况下，它们是 **DeviceID** 或 **AssetID**（环境中的资产的唯一标识符）。

要了解有关时间序列模型的更多内容，请阅读[时间序列模型](./time-series-insights-update-tsm.md)。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 层次结构树和搜索面板

层次结构树和搜索面板允许您轻松搜索和导航[时序模型](./time-series-insights-update-tsm.md)层次结构，以查找要在图表上显示的特定时间序列实例。 当您选择实例时，它们不仅会添加到当前图表中，还会很好地添加到数据中。 

[![层次结构树和搜索面板](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

搜索结果窗格还允许您在层次结构视图或列表视图中查看结果，以便轻松查找要显示的实例。
 
## <a name="4-time-series-well"></a>4. 时间序列良好

井显示与所选时序见解实例关联的实例字段和其他元数据。 选中右侧的复选框可以在当前图表中隐藏或显示特定的实例。 

  [![预览井](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

您可以通过选择元素左侧的红色**Delete（** 垃圾箱）控件，从当前数据中删除特定数据元素。 井还允许您控制图表中每个元素的显示方式。 您可以选择添加最小/最大阴影、数据点、及时移动元素和以阶梯式方式可视化实例。 

此外，探索控件可让您轻松创建时间偏移和散射图。  

  [![良好布局选项](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果出现以下消息，则实例在所选时间跨度内没有任何数据。 若要解决该问题，请增大时间跨度或确认实例在推送数据。
>
> ![无数据通知](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 图表面板

该图表使您能够将时间序列实例显示为线。 可以通过单击 Web 控件来折叠环境面板、数据模型和时间跨度控件面板，以使图表更大。 

  [![预览图表概述](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **图表类型**：控制哪些数据元素可用于可视化。

1. **间隔大小**：间隔大小滑块工具使您能够在同一时间跨度内放大和缩小间隔。 这提供了对大切片之间移动的更精确控制，这些时间切片显示平滑趋势，下至小至毫秒的切片，使您能够查看数据的粒度高分辨率削减。 滑块的默认起点设置为所选的数据的最佳视图；平衡分辨率、查询速度和粒度。

1. **缩放和平移**：选择此控件以缩放和平移图表。

1. **Y 轴控制**： 循环浏览可用的 y 轴视图选项：

    * `Stacked`：每行都有一个单独的 Y 轴。
    * `Overlap`：使用它在同一 Y 轴上堆叠多条线，Y 轴数据会根据所选线更改。
    * `Shared`：所有 Y 轴数据一起显示。

1. **标记元素**：当前选择的数据元素及其关联详细信息。

通过按住鼠标时**左键单击**当前图形上的数据点，然后将所选区域拖动到您选择的终结点，可以进一步钻取特定数据切片。 **右键单击**蓝色所选区域，然后选择 **"缩放**"，如下所示。 您还可以在所选时间跨度中查看和下载遥测事件。

  [![预览图表缩放](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

执行 **"缩放**"操作后，将显示所选数据集。 选择要循环浏览时序见解数据的三个 y 轴表示的格式控件。

  [![预览图表 y 轴](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

此处提供了**重叠图表**的示例：

  [![重叠图表选项](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

**"更多操作"** 按钮展开以显示 **"下载为 CSV"、****连接到电源 BI、****将图表数据显示为表**以及**浏览原始事件**选项。

  [![更多操作选项](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

详细了解["在时间序列见解本机 Power BI 连接器](concepts-power-bi.md)中**连接到电源 BI"** 选项。

## <a name="6-time-editor-panel"></a>6. 时间编辑器面板

使用时间序列见解时，您首先将选择时间跨度。 所选时间跨度将控制可用于使用时序见解更新小部件进行操作的数据集。

  [![时间选择面板](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 时间线的一部分以琥珀色或橙色突出显示，以指示暖存储中可用的数据范围。

以下 Web 控件在时间序列见解更新中提供，用于选择工作时间跨度。 

  [![勘探井控制](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **内部日期范围滑块控件**：通过拖动两个终结点控件来在所需的时间跨度内使用它们。 此内部日期范围内受外部日期范围滑块控件约束。

1. **增加和减少日期范围按钮**：通过选择任意一个按钮来选择所需的时间跨度来增加或减少您的时间跨度。

1. **时间跨度折叠控件**：此 Web 控件允许您隐藏除内部日期范围滑块工具之外的所有控件。

1. **外部日期范围滑块控件**：使用端点控件选择外部日期范围，该范围可用于内部日期范围控件。

1. **时间范围滑块控件**：使用它在预设的时间跨度选择（如最后**30 分钟**、**最后 12 小时**或**自定义范围**）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

   [![到/从选择面板](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 应用程序栏

时序见解预览版导航面板显示在时序见解应用的顶部。 它提供了以下功能：

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![共享图标](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

选择新的“共享”图标可与团队共享 URL 链接。****

  [![共享实例 URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![租户选择](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 显示当前时序见解登录帐户信息。
* 使用该控件可在可用时序见解主题之间切换。
* 使用该控件可以查看预览版[演示 Web 应用](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>主题选择

若要选择新主题，请选择右上角的个人资料图标。 然后选择“更改主题”。****

  [![主题选择](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 选择个人资料图标还可以设置语言。

Azure 时序见解预览版支持两个主题：

* **浅色主题**：本文档中显示的默认主题。
* **黑暗主题**： 渲染资源管理器，如下所示：

  [![选定的深色主题](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 环境控件

### <a name="preview-terms-panel"></a>预览版搜索词面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 S1/S2 环境。 你可能想要结合使用正式版和预览版产品。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但是你可以在现有时序见解资源管理器中获得 S1/S2 环境的完整 UI 体验。 

而不是层次结构，时间序列见解术语面板显示。 术语面板允许您在环境中定义查询。 使用它还可以根据谓词筛选数据。

  [![查询面板的位置](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

时序见解预览版术语编辑器面板采用以下参数：

**Where**： 使用 where 子句使用下表中列出的一组操作数快速筛选事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括以下内容：

| Operation | 支持的类型   | 说明 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | 双精度、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | String | 右侧只允许使用常量字符串文本。 不允许空字符串和 NULL。 |

要了解有关受支持的查询操作和数据类型的更多内容，请阅读[时间序列表达式 （TSX）。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="examples-of-where-clauses"></a>where 子句示例

  [![其中子句示例](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**度量值**：显示所有数字列（**双精度**列）的下拉列表，可用于当前图表的元素。

**拆分为**： 此下拉列表显示模型中所有可用的分类列（字符串），您可以按该列对数据进行分组。 最多可以添加要在同一个 X 轴上查看的五个搜索词。 输入所需的参数，然后选择“添加”**** 以添加新的搜索词。

  [![查询视图和筛选视图之一](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

可以通过选择可见图标，在图表面板中显示或隐藏元素，如下图所示。 若要彻底删除查询，请选择红色的“X”。****

  [![取消查询和筛选选项](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解 Azure 时序见解预览版中的[存储和流入量](./time-series-insights-update-storage-ingress.md)。

- 阅读有关[数据建模](./time-series-insights-update-tsm.md)的时序见解预览版文档。

- 了解[如何诊断和排查](./time-series-insights-update-how-to-troubleshoot.md)时序见解实例的问题。
