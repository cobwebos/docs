---
title: 在 Azure 时序见解预览版资源管理器中直观显示数据 | Microsoft Docs
description: 本文介绍 Azure 时序见解预览版资源管理器 Web 应用中可用的功能和选项。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: a1707740d673ea49a4b4494f5d2e6a5753982090
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553409"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在资源管理器预览版中实现数据可视化

本文档介绍 Azure 时序见解预览[演示 web 应用](https://insights.timeseries.azure.com/preview/demo)的用户界面和用户体验功能。 具体而言，它讨论了寄宿示例的布局、接口自定义选项以及通过提供的演示导航。

## <a name="prerequisites"></a>必备组件

若要开始了解 Azure 时序见解预览资源管理器，必须执行以下操作：

* 设置时序见解环境。 若要详细了解如何设置实例，请参阅[Azure 时序见解预览](./time-series-insights-update-create-environment.md)教程。
* 提供对为该帐户创建的时序见解环境的[数据访问权限](./time-series-insights-data-access.md)。 可以向其他人以及自己提供访问。
* 将事件源添加到时序见解环境，以将数据推送到环境：
  * 了解[如何连接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)。
  * 了解[如何连接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="learn-about-the-preview-explorer"></a>了解预览资源管理器

Azure 时序见解预览版资源管理器包含以下元素：

[![The 资源管理器视图](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">环境面板</a>：显示 Azure 时序见解环境。
- <a href="#navigation-menu">导航菜单</a>：用于在 "**分析**" 和 "**模型**" 页之间切换。
- <a href="#hierarchy-tree">层次结构树</a>：用于选择要绘制图表的特定模型和数据元素。
- <a href="#preview-well">时序</a>：通过颜色编码以表格式显示当前选定的数据元素。
- <a href="#preview-chart">图表面板</a>：显示当前工作图表。
- <a href="#time-editor-panel">时间线</a>：用于修改你的工作时间跨度。
- <a href="#navigation-panel">应用栏</a>：包含用户管理选项，如 "当前租户"。 你可以使用它来更改主题和语言设置。

## <a name="environment-drop-down-list"></a>环境下拉列表

"环境" 下拉列表显示有权访问的所有时序见解环境。 此列表包括即用即付环境，如时序见解预览。 此列表还包括 S1/S2 环境，这些环境已正式发布。

1. 选择所显示环境旁边的下拉箭头。

   [![The 控制面板](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 然后选择所需的环境。

## <a name="navigation-menu"></a>导航菜单

  [![The 导航菜单](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

使用导航菜单在两个视图之间进行选择：

* **分析**：使用它可以对建模或未建模的时序数据进行图表和执行丰富的分析。
* **模型**：用于将新的时序见解预览版类型、层次结构和实例推送到时序见解模型。

## <a name="hierarchy-tree"></a>层次结构树

层次结构树显示选定的数据元素，这些元素包括设备上的模型、特定设备和传感器。

### <a name="model-search-panel"></a>模型搜索面板

您可以使用 "模型搜索" 面板轻松搜索和导航时序模型层次结构，以便查找您要在图表中显示的特定时序实例。 选择实例后，它们将同时添加到当前图表和数据中。

  [![The 模型搜索面板](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>模型创作

Azure 时序见解预览版支持时序模型上的完全创建、读取、更新和删除（CRUD）操作。

* **时序模型类型**：可以使用时序见解类型定义用于计算计算的变量或公式。 它们与给定的时序见解实例相关联。 一个类型可以包含一个或多个变量。
* **时序模型层次结构**：层次结构是数据的系统组织。 层次结构描述时序见解数据中不同实体之间的关系。
* **时序模型实例**：实例是时间序列本身。 在大多数情况下，它们是**DeviceID**或**AssetID**，这是环境中资产的唯一标识符。

若要详细了解时序模型，请参阅[时序模型](./time-series-insights-update-tsm.md)。

## <a name="preview-well"></a>预览良好

这会显示实例字段和与所选时序见解实例关联的其他元数据。 通过选中右侧的复选框，您可以隐藏或显示当前图表中的特定实例。 还可以通过选择元素左侧的 "红色**删除**" （垃圾桶）控件，从当前数据中删除特定数据元素。

  [![The 预览良好](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

若要重新配置 "**分析**图表" 页的布局，请选择右上角的省略号图标：

  [![Telemetry 布局选项](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 如果看到以下消息，则在所选时间范围内实例没有任何数据。 若要解决此问题，请增加时间跨度或确认实例正在推送数据。
>
> ![无数据通知](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>预览图表

在图表中，可以显示时序见解实例作为行。 通过选择 web 控件来放大图表，可以折叠 "环境" 面板、"数据模型" 和 "时间范围" 控制面板。

  [![Preview 图表概述](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **选定的日期范围**：控制哪些数据元素可用于可视化。

- **内部日期范围滑块工具**：通过将两个端点控件拖动到所需的时间范围内来使用。

- **时间跨度折叠控件**：折叠和展开时间范围面板编辑器。

- **Y 轴格式控制**：循环访问可用的 Y 轴视图选项：

    * `Default`：每行都有一个 y 轴。
    * `Stacked`：使用它在同一 y 轴上堆叠多行，并根据所选的行更改 y 轴数据。
    * `Shared`：所有 y 轴数据一起显示。

- **Current data 元素**：当前选定的数据元素及其关联的详细信息。

若要深入了解特定数据切片，请在当前关系图上左键单击数据点，然后将所选区域拖到所选终结点。 右键单击选定的灰色区域，然后选择 "**缩放**"，如下图所示：

  [![Preview 图表缩放](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

执行**缩放**操作后，会看到所选的数据集。 选择 "y 轴格式" 控件，以便循环浏览时序见解数据的三个 y 轴表示形式。

  [![Preview 图表 y 轴](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

在这里，你可以看到一个共享 Y 轴的示例：

  [![Preview 共享 Y 轴](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>时间编辑器面板

使用时序见解预览版时，首先选择时间跨度。 所选时间范围控制可使用时序见解预览小组件操作的数据集。 以下 web 控件在时序见解预览中提供，用于选择工作时间范围：

  [![时间选项面板](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **内部日期范围滑块工具**：通过将两个端点控件拖动到所需的时间范围内来使用。 此内部日期范围受外部日期范围滑块控件约束。

1. **增加和减少日期范围按钮**：通过选择所需间隔的任一按钮，增加或减少时间跨度。

1. **时间跨度折叠控件**：此 web 控件允许您隐藏除内部 "日期范围" 滑块工具之外的所有控件。

1. **外部日期范围滑块控件**：使用端点控件选择外部日期范围，该范围将可用于内部日期范围控件。

1. "**快速日期范围" 下拉列表**：使用它可以在预设的时间范围选择（如过去**30 分钟**、**最近12个小时**或**自定义范围**）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

1. **间隔大小滑块工具**：用于放大和缩小相同时间范围内的间隔。 通过此操作可更精确地控制较大时间片段之间的移动。 它将平滑的趋势显示为小到一毫秒的片。 您可以使用它来查看数据的精细、高分辨率剪切。 滑块的默认起点设置为所选数据的最佳视图，可平衡分辨率、查询速度和粒度。

1. **从 web 控件到-和的日期范围**：通过此 web 控件，可以轻松地选择所需的日期和时间范围。 还可以使用该控件在不同时区之间切换。 将更改应用到当前工作区后，请选择 "**保存**"。

   [![To 和从选择面板](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>导航面板

时序见解预览导航面板显示在时序见解应用的顶部。 它提供以下功能：

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![Share 图标](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

选择 "新建**共享**" 图标以与团队共享 URL 链接。

  [![Share 实例 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![Tenant 选择](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 显示当前时序见解登录帐户信息。
* 使用它可以在可用时序见解主题之间切换。
* 使用它查看预览版[web 应用](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>主题选择

若要选择新的主题，请选择右上角的配置文件图标。 然后选择 "**更改主题**"。

  [![Theme 选择](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 还可以通过选择配置文件图标来选择语言。

Azure 时序见解预览版支持两个主题：

* **浅色主题**：本文档中所示的默认主题。
* **深色主题**：呈现资源管理器，如下所示：

  [![Selected 深色主题](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 环境控件

### <a name="preview-terms-panel"></a>预览条款面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 S1/S2 环境。 你可能想要结合使用正式发布的产品和预览。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但是你可以在现有时序见解资源管理器中获得 S1/S2 环境的完整 UI 体验。 

你会看到时序见解术语面板，其中定义了环境中的查询，而不是层次结构。 使用它可以根据谓词筛选数据。

  [![Where 查询面板](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

时序见解预览版术语编辑器面板采用以下参数：

**其中**：使用 where 子句可以通过使用下表中列出的一组操作数来快速筛选您的事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括：

| Operation | 支持的类型   | 说明 |
| --- | --- | --- |
| `<`、`>`、`<=`、`>=` | 双精度、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | 字符串 | 右侧仅允许常量字符串。 不允许空字符串和 NULL。 |

若要了解有关支持的查询操作和数据类型的详细信息，请参阅[时序表达式（TSX）](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)。

### <a name="examples-of-where-clauses"></a>Where 子句的示例

  [![Where 子句示例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**度量值**：一个下拉列表，其中显示了可用作当前图表元素的所有数值列（**双精度**型）。

**拆分方式**：此下拉列表显示您的模型中的所有可用分类列（字符串），您可以通过对数据进行分组。 你最多可以添加五个字词以便在同一 x 轴上查看。 输入所需的参数，然后选择 "**添加**" 以添加新术语。

  [![Queried 和筛选视图一](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

您可以通过选择可见图标在 "图表" 面板中显示和隐藏元素，如下图所示。 若要完全删除查询，请选择红色**X**。

  [![Queried 和筛选视图两个](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解 Azure 时序见解预览版中的[存储和入口](./time-series-insights-update-storage-ingress.md)。
- 阅读有关[数据建模](./time-series-insights-update-tsm.md)的时序见解预览文档。
- 了解[如何诊断和排查](./time-series-insights-update-how-to-troubleshoot.md)时序见解实例问题。
