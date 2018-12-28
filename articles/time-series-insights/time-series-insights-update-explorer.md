---
title: Azure 时序见解预览版资源管理器 - 在 Azure 时序见解预览版资源管理器中实现数据可视化 | Microsoft Docs
description: 本文介绍 Azure 时序见解预览版资源管理器 Web 应用中可用的功能和选项。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 5372a36291ee13966d497bdae83a6e214dce99b9
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272331"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在资源管理器预览版中实现数据可视化

本文介绍 Azure 时序见解预览版[资源管理器 Web 应用](https://insights.timeseries.azure.com/preview/samples)中可用的功能和选项。

## <a name="prerequisites"></a>先决条件

使用 Azure 时序见解预览版资源管理器之前，必须：

* 设置时序见解环境。 有关详细信息，请参阅[教程：Azure 时序见解预览版](./time-series-insights-update-create-environment.md)。
* 提供对为帐户创建的时序见解环境的数据访问。 可以向其他人以及自己提供访问。
* 向时序见解环境添加事件源以将数据推送到该环境。

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>了解 Azure 时序见解预览版资源管理器

  ![explorer-one][1]

Azure 时序见解预览版资源管理器包含以下元素：

* **导航栏**：使你可以在分析与模型页之间切换。
* **层次结构树**：使你可以选择要在图表中显示的特定数据元素。
* **时序井**：显示当前选择的数据元素。
* **图表面板**：显示当前工作的图表。
* **时间线**：使你可以修改工作时间跨度。
* **应用栏**：包含用户管理选项（如当前租户），并使你可以更改主题和语言设置。

## <a name="time-series-insights-preview-environment-panel"></a>时序见解预览版环境面板

环境面板中显示你有权访问的所有时序见解环境。 列表包括即用即付环境预览版和 S1/S2 环境 (GA)。 只需单击要使用的时序见解环境。

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>时序见解预览版导航菜单

  ![explorer-three][3]

借助导航菜单可以在时序见解应用之间切换：

* **分析**：使你可以对已建模或未建模时列数据创建图表和执行丰富分析。

* **模型**：使你可以将新时序见解预览版类型、层次结构和实例推送到时序见解模型。

## <a name="time-series-insights-preview-model-authoring"></a>时序见解预览版模型创作

借助此应用，可以对时序模型执行创建、读取、更新和删除 (CRUD) 操作。  

* **时序模型类型**：时序见解类型可用于定义变量或公式以便进行计算。 它们与给定时序见解实例关联。 一种类型可以具有一个或多个变量。
* **时序模型层次结构**：层次结构是数据的系统组织。 层次结构描述时序见解数据中不同实体之间的关系。
* **时序模型实例**：实例是时序本身。 在大多数情况下，它们是 DeviceID 或 AssetID（环境中的资产的唯一标识符）。

若要详细了解时序模型，请参阅[时序模型](./time-series-insights-update-tsm.md)。

## <a name="time-series-insights-preview-model-search-panel"></a>时序见解预览版模型搜索面板

模型搜索面板使你可以轻松搜索和导航时序模型层次结构，以找到要在图表上显示的特定时序实例。 选择实例时，它们会同时添加到当前图表以及数据中。

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>时序见解预览版井

井显示与所选时序实例关联的实例字段和其他元数据。 右侧复选框使你可以在当前图表中隐藏或显示特定实例。 还可以通过单击元素右侧的红色 x 控件，从当前数据井中删除特定数据元素。

  ![explorer-five][5]

还可以弹出遥测面板，以获取数据井中元素的更好的垂直视图。

  ![explorer-six][6]

> [!NOTE]
> 如果看到以下消息，则实例在所选时间跨度内没有任何数据。 若要解决该问题，可以增加时间跨度或确认实例在推送数据。
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>时序见解预览版图表

借助图表可以将时序实例显示为行。 可以通过单击 Web 控件来折叠环境面板、数据模型和时间跨度控件面板，以使图表更大。

  ![explorer-eight][8]

1. **所选日期范围**：控制可用于实现可视化效果的数据元素。

1. **内部日期范围滑块工具**：通过将两个终结点控件拖动到所需时间跨度上来使用它们。

1. **时间跨度折叠控件**：折叠和展开时间跨度面板编辑器。

1. **Y 轴格式控件**：循环显示可用 Y 轴视图选项：

    * `Default`：每行都具有单独的 Y 轴。
    * `Stacked`：使你可以在同一 Y 轴上堆叠多个行，Y 轴数据基于所选行而更改。
    * `Shared`：所有 Y 轴数据一起显示。

1. **当前数据元素**：当前所选数据元素及其关联的详细信息。

可以通过单击左键单击当前图上的数据点，然后将所选区域拖动到所选终结点，进一步深化到特定数据切片。 右键单击灰色的所选区域并单击缩放，如下图所示：

  ![explorer-nine][9]

执行缩放操作之后，你会看到所选数据集。 单击 Y 轴格式控件以循环访问时序见解数据的三个 Y 轴表示。

  ![explorer-ten][10]

在此处可以查看共享 Y 轴的示例：

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>时序见解预览版时间编辑器面板

使用时序见解预览版时，首先选择时间跨度。 所选时间跨度控制可用于通过时序见解预览版小组件进行操作的数据集。 以下 Web 控件在时序见解预览版中可用于选择工作时间跨度。

  ![explorer-twelve][12]

1. **内部日期范围滑块工具**：通过将两个终结点控件拖动到所需时间跨度上来使用它们。 此内部日期范围内受外部日期范围滑块控件约束。

1. **增加和减少日期范围按钮**：通过选择任一按钮来增加或减少时间跨度，从而获得所需间隔。

1. **时间跨度折叠控件**：此 Web 控件使你可以隐藏所有控件（内部日期范围滑块工具除外）。

1. **外部日期范围滑块控件**：使用终结点控件选择可用于内部日期范围控件的外部日期范围。

1. **快速时间日期范围下拉列表**：使你可以在预设时间跨度选择（如过去 30 分钟、过去 12 小时或自定义范围）之间快速切换。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

1. **间隔大小滑块工具**：使你可以在相同时间跨度内放大和缩小间隔。 通过此操作可更精确地控制较大时间片段之间的移动。 它显示直至小到毫秒片段的平滑趋势，从而可以查看精细的高分辨率数据切片。 滑块的默认起点设置为所选数据的最佳视图，可平衡分辨率、查询速度和粒度。

1. **日期范围起始和结束 Web 控件**：借助此 Web 控件可以轻松地单击并选择所需日期和时间范围。 还可以使用该控件在不同时区之间切换。 进行更改之后，若要应用于当前工作区，请选择“保存”。

  ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>时序见解预览版导航面板

时序见解预览版导航面板提供以下功能：

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  ![explorer-fifteen][15]

选择链接 Web 控件（突出显示）可生成 URL 来保存或共享当前 Azure 时序见解工作会话，其中包括：

* 当前选择的时间范围
* 当前选择的间隔大小
* 当前选择的数据井

### <a name="tenant-section"></a>租户分区

  ![explorer-sixteen][16]

* 显示当前时序见解登录帐户信息。
* 使你可以在可用时序见解主题之间切换。

### <a name="theme-selection"></a>主题选择

Azure 时序见解预览版支持两个主题：

* **浅色主题**：本文档中显示的默认主题。
* **深色主题**：如下所示呈现资源管理器：

  ![explorer-seventeen][17]

在此处还可以更改支持的语言。

## <a name="s1s2-environment-controls"></a>S1/S2 环境控件

### <a name="time-series-insights-preview-terms-panel"></a>时序见解预览版术语面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 S1/S2 环境。 你可能要组合使用 GA 产品和预览版。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但是你可以在现有时序见解资源管理器中获得 S1/S2 环境的完整 UI 体验。  

你会看到时序见解术语面板（在其中定义环境中的查询），而不是层次结构。 它使你可以基于谓词来筛选数据。

  ![explorer-eighteen][18]

时序见解预览版术语编辑器面板采用以下参数：

**Where**：Where 子句使你可以使用下表中列出的操作数集来快速筛选事件。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括：

| Operation | 支持的类型   | 说明 |
| --- | --- | --- |
| `<`、`>`、`<=`、`>=` | 双精度、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | String | 右侧只允许出现常数字符串文本。 不允许出现空字符串和 NULL。 |

通过阅读[表达式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) 来了解有关支持的查询操作和数据类型的详细信息。

### <a name="examples-of-where-clauses"></a>Where 子句示例

  ![explorer-nineteen][19]

**度量值**：此下拉列表显示可以用作当前图表元素的所有数值列（**双精度型**）。

**拆分依据**：此下拉列表显示模型中可以用作数据分组依据的所有可用分类列（字符串）。 最多可以添加五个术语，在同一个 X 轴上查看。 输入所需参数，然后选择“添加”以添加新术语。

  ![explorer-twenty][20]

可以通过选择可见图标，在图表面板中显示或隐藏元素，如下图所示。 可以通过单击红色“x”来完全删除查询。

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>后续步骤

请参阅以下文章：
* [Azure 时序见解预览版存储和入口](./time-series-insights-update-storage-ingress.md)
* [数据建模](./time-series-insights-update-tsm.md)
* [和故障排除](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
