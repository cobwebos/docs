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
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: f09de6fee4c9de39d98ec66714686ab1e805c4cd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014881"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在资源管理器预览版中实现数据可视化

本文档介绍 Azure 时序见解预览版[演示 Web 应用](https://insights.timeseries.azure.com/preview/demo)的用户界面以及用户体验功能和界面。 具体而言，其中介绍了托管示例的布局、界面自定义选项，以及所提供的演示中的导航。

## <a name="prerequisites"></a>先决条件

若要开始使用 Azure 时序见解预览版资源管理器，必须：

* 设置时序见解环境。 若要详细了解如何预配实例，请尝试学习 [Azure 时序见解预览版](./time-series-insights-update-create-environment.md)教程。
* 提供对你为帐户创建的时序见解环境的[数据访问](./time-series-insights-data-access.md)。 可以向其他人以及自己提供访问。
* 向时序见解环境添加事件源以将数据推送到该环境：
  * 了解[如何连接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)。
  * 了解[如何连接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="learn-about-the-preview-explorer"></a>了解预览版资源管理器

Azure 时序见解预览版资源管理器包含以下元素：

[![资源管理器视图](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">环境面板</a>：显示 Azure 时序见解环境。
- <a href="#navigation-menu">导航菜单</a>：用于在 "**分析**" 和 "**模型**" 页之间切换。
- <a href="#hierarchy-tree">层次结构树</a>：用于选择要绘制图表的特定模型和数据元素。
- <a href="#preview-well">时序</a>：通过颜色编码以表格式显示当前选定的数据元素。
- <a href="#preview-chart">图表面板</a>：显示当前工作图表。
- <a href="#time-editor-panel">时间线</a>：用于修改你的工作时间跨度。
- <a href="#navigation-panel">应用栏</a>：包含用户管理选项，如 "当前租户"。 可用于更改主题和语言设置。

## <a name="environment-drop-down-list"></a>环境下拉列表

环境下拉列表显示你有权访问的所有时序见解环境。 该列表包含即用即付环境，例如时序见解预览版。 该列表还包含正式版 S1/S2 环境。

1. 选择显示的环境旁边的下拉箭头。

   [![控制面板](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 然后选择所需的环境。

## <a name="navigation-menu"></a>导航菜单

  [![导航菜单](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

使用导航菜单在两个视图之间进行选择：

* **分析**：使用它可以对建模或未建模的时序数据进行图表和执行丰富的分析。
* **模型**：用于将新的时序见解预览版类型、层次结构和实例推送到时序见解模型。

## <a name="hierarchy-tree"></a>层次结构树

层次结构树显示所选的数据元素，包括模型、特定设备和设备上的传感器。

### <a name="model-search-panel"></a>模型搜索面板

使用模型搜索面板可以轻松搜索和导航时序模型层次结构，以找到要在图表上显示的特定时序实例。 选择实例后，它们会同时添加到当前图表以及数据中。

  [![模型搜索面板](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>模型创作

Azure 时序见解预览版支持对时序模型执行所有的创建、读取、更新和删除 (CRUD) 操作。

* **时序模型类型**：可以使用时序见解类型定义用于计算计算的变量或公式。 它们与给定的时序见解实例相关联。 一种类型可以具有一个或多个变量。
* **时序模型层次结构**：层次结构是数据的系统组织。 层次结构描述时序见解数据中不同实体之间的关系。
* **时序模型实例**：实例是时间序列本身。 在大多数情况下，它们是 **DeviceID** 或 **AssetID**（环境中的资产的唯一标识符）。

若要详细了解时序模型，请参阅[时序模型](./time-series-insights-update-tsm.md)。

## <a name="preview-well"></a>预览井

井显示与所选时序见解实例关联的实例字段和其他元数据。 选中右侧的复选框可以在当前图表中隐藏或显示特定的实例。 还可以通过选择元素左侧的红色“删除”（垃圾桶）控件，从当前数据井中删除特定的数据元素。

  [![预览井](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

若要重新配置“分析”图表页的布局，请选择右上角的省略号图标：

  [![遥测布局选项](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 如果看到以下消息，表示实例在所选时间跨度内没有任何数据。 若要解决该问题，请增大时间跨度或确认实例在推送数据。
>
> ![无数据通知](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>预览图表

使用图表可以将时序见解实例显示为行。 可以通过选择 Web 控件来折叠环境面板、数据模型和时间跨度控件面板，以放大图表。

  [![预览图表概述](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **选定的日期范围**：控制哪些数据元素可用于可视化。

- **内部日期范围滑块工具**：通过将两个端点控件拖动到所需的时间范围内来使用。

- **时间跨度折叠控件**：折叠和展开时间范围面板编辑器。

- **Y 轴格式控制**：循环访问可用的 Y 轴视图选项：

    * `Default`：每行都有一个 y 轴。
    * `Stacked`：使用它在同一 y 轴上堆叠多行，并根据所选的行更改 y 轴数据。
    * `Shared`：所有 y 轴数据一起显示。

- **Current data 元素**：当前选定的数据元素及其关联的详细信息。

若要进一步深入到特定的数据切片，请单击当前图形上的数据点，然后将所选区域拖到所选的终结点。 右键单击灰色的所选区域并选择“缩放”，如下图所示：

  [![预览图表 - 缩放](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

执行“缩放”操作后，会看到所选的数据集。 选择 Y 轴格式控件可循环访问时序见解数据的三种 Y 轴表示形式。

  [![预览图表 - Y 轴](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

在此处可以查看共享 Y 轴的示例：

  [![预览共享 Y 轴](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>时间编辑器面板

使用时序见解预览版时，首先选择时间跨度。 所选时间跨度控制可用于通过时序见解预览版小组件进行操作的数据集。 以下 Web 控件在时序见解预览版中可用于选择工作时间跨度：

  [![时间选项面板](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **内部日期范围滑块工具**：通过将两个端点控件拖动到所需的时间范围内来使用。 此内部日期范围内受外部日期范围滑块控件约束。

1. **增加和减少日期范围按钮**：通过选择所需间隔的任一按钮，增加或减少时间跨度。

1. **时间跨度折叠控件**：此 web 控件允许您隐藏除内部 "日期范围" 滑块工具之外的所有控件。

1. **外部日期范围滑块控件**：使用端点控件选择外部日期范围，该范围将可用于内部日期范围控件。

1. "**快速日期范围" 下拉列表**：使用它可以在预设的时间范围选择（如过去**30 分钟**、**最近12个小时**或**自定义范围**）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

1. **间隔大小滑块工具**：用于放大和缩小相同时间范围内的间隔。 通过此操作可更精确地控制较大时间片段之间的移动。 它会显示直至毫秒切片的平滑趋势。 使用该控件可以查看精细的高分辨率数据切片。 滑块的默认起点设置为所选数据的最佳视图，可平衡分辨率、查询速度和粒度。

1. **从 web 控件到-和的日期范围**：通过此 web 控件，可以轻松地选择所需的日期和时间范围。 还可以使用该控件在不同时区之间切换。 对当前工作区进行更改之后，选择“保存”。

   [![目标与来源选择面板](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>导航面板

时序见解预览版导航面板显示在时序见解应用的顶部。 它提供以下功能：

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![共享图标](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

选择新的“共享”图标可与团队共享 URL 链接。

  [![共享实例 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![租户选择](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

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

  [![所选深色主题](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 环境控件

### <a name="preview-terms-panel"></a>预览版搜索词面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 S1/S2 环境。 你可能想要结合使用正式版和预览版产品。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但是你可以在现有时序见解资源管理器中获得 S1/S2 环境的完整 UI 体验。 

你会看到时序见解搜索词面板（可在其中定义环境中的查询），而不是层次结构。 使用该控件可以基于谓词筛选数据。

  [![Where 查询面板](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

时序见解预览版术语编辑器面板采用以下参数：

**其中**：使用 where 子句可以通过使用下表中列出的一组操作数来快速筛选您的事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括：

| Operation | 支持的类型   | 说明 |
| --- | --- | --- |
| `<`、`>`、`<=`、`>=` | 双精度、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | String | 右侧只允许使用常量字符串文本。 不允许空字符串和 NULL。 |

若要详细了解支持的查询操作和数据类型，请参阅[时序表达式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)。

### <a name="examples-of-where-clauses"></a>where 子句示例

  [![Where 子句示例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**度量值**：一个下拉列表，其中显示了可用作当前图表元素的所有数值列（**双精度**型）。

**拆分方式**：此下拉列表显示您的模型中的所有可用分类列（字符串），您可以通过对数据进行分组。 最多可以添加要在同一个 X 轴上查看的五个搜索词。 输入所需的参数，然后选择“添加”以添加新的搜索词。

  [![已查询和筛选视图 1](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

可以通过选择可见图标，在图表面板中显示或隐藏元素，如下图所示。 若要彻底删除查询，请选择红色的“X”。

  [![已查询和筛选视图 2](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解 Azure 时序见解预览版中的[存储和流入量](./time-series-insights-update-storage-ingress.md)。
- 阅读有关[数据建模](./time-series-insights-update-tsm.md)的时序见解预览版文档。
- 了解[如何诊断和排查](./time-series-insights-update-how-to-troubleshoot.md)时序见解实例的问题。
