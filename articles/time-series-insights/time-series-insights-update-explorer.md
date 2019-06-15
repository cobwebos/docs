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
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399860"
---
# <a name="visualize-data-in-the-explorer-preview"></a>在资源管理器预览版中实现数据可视化

本文档介绍的用户界面和用户体验功能和界面 Azure 时间系列 Insights 预览版[演示 web 应用](https://insights.timeseries.azure.com/preview/demo)。 具体而言，它讨论托管的示例、 接口的自定义选项，并通过提供演示导航的布局。

## <a name="prerequisites"></a>必备组件

若要开始使用 Azure 时时序见解预览资源管理器，必须：

* 设置时序见解环境。 若要了解有关预配实例的详细信息，请尝试[Azure 时间系列 Insights 预览版](./time-series-insights-update-create-environment.md)教程。
* [提供的数据访问](./time-series-insights-data-access.md)于可为帐户创建的时序见解环境。 可以向其他人以及自己提供访问。
* 向数据推送到该环境的时序见解环境添加事件源：
  * 了解[如何连接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)。
  * 了解[如何连接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="learn-about-the-preview-explorer"></a>了解预览版资源管理器

Azure 时序见解预览版资源管理器包含以下元素：

[![资源管理器视图](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">环境面板</a>:显示 Azure 时序见解环境。
- <a href="#navigation-menu">导航菜单</a>:使用它来切换**分析**并**模型**页。
- <a href="#hierarchy-tree">层次结构树</a>：使用它来选择特定的模型和数据元素，以在图表中表示。
- <a href="#preview-well">时序井</a>：使用颜色编码的表格格式显示当前所选的数据元素。
- <a href="#preview-chart">图表面板</a>：显示当前工作的图表。
- <a href="#time-editor-panel">时间线</a>：使用它来修改您的工作时间跨度。
- <a href="#navigation-panel">应用栏</a>：包含您的用户管理选项，例如当前租户。 可用于更改主题和语言设置。

## <a name="environment-drop-down-list"></a>环境下拉列表

环境下拉列表显示有权访问的所有时序见解环境。 此列表包括即用即付环境，如时间序列 Insights 预览版。 该列表还包含 S1/S2 环境，已公开发布。

1. 选择显示环境旁边的下拉箭头。

   [![控制面板](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. 然后，选择所需的环境。

## <a name="navigation-menu"></a>导航菜单

  [![在导航菜单](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

使用导航菜单上两个视图之间进行选择：

* **分析**：使用它来对模型化或非模型化时间系列数据执行丰富的分析和图表。
* **模型**：使用它来将新的时间系列 Insights 预览类型、 层次结构和实例推送到时序见解模型。

## <a name="hierarchy-tree"></a>层次结构树

层次结构树显示包括模型、 特定设备和传感器设备上的所选的数据元素。

### <a name="model-search-panel"></a>模型搜索面板

模型搜索面板可用于轻松搜索和浏览时序模型层次结构，以找到你想要在图表上显示的特定时间系列实例。 选择你的实例后，它们是也添加到当前图表和数据。

  [![模型搜索面板](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>在创作模型

完整的 Azure 时间系列 Insights 预览版支持创建、 读取、 更新和删除 (CRUD) 操作在时序模型。

* **时序模型类型**：时序见解类型可用于定义变量或进行计算的公式。 它们与给定的时序见解实例相关联。 一个类型可以包含一个或多个变量。
* **时序模型层次结构**：层次结构是数据的系统组织。 层次结构描述时序见解数据中不同实体之间的关系。
* **时序模型实例**：实例是时序本身。 在大多数情况下，它们**DeviceID**或**AssetID**，这是在环境中的资产的唯一标识符。

若要详细了解时序模型，请参阅[时序模型](./time-series-insights-update-tsm.md)。

## <a name="preview-well"></a>好的预览

也显示实例字段和其他与所选的时序见解实例相关联的元数据。 通过选择右侧的复选框，可以隐藏或显示当前的图表中的特定实例。 您还可以删除特定的数据元素从当前数据也通过选择红色**删除**（垃圾桶） 上的元素的左侧和右侧的控件。

  [![好在预览](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

若要重新配置的布局你**分析**图表页中，在右上角选择省略号图标：

  [![遥测数据布局选项](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> 如果看到以下消息，该实例不会在选定的时间范围期间有任何数据。 若要解决此问题，增加的时间跨度或确认实例推送的数据。
>
> ![没有数据通知](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>预览图表

图表后，您可以将时序见解实例显示为行。 您可以通过选择要使图表更大的 web 控件折叠环境面板、 数据模型和时间范围控件面板。

  [![预览图表概述](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **所选日期范围**:控制可用于实现可视化效果的数据元素。

- **内部日期范围滑块工具**:通过拖动所需的时间范围内使用两个终结点的控件。

- **时间跨度折叠控件**：折叠和展开时间跨度面板编辑器。

- **Y 轴格式控件**：循环显示可用的 y 轴视图选项：

    * `Default`：每一行都具有单独的 y 轴。
    * `Stacked`：使用它来与 y 轴数据更改所选行基于堆栈同一 y 轴上的多个行。
    * `Shared`：所有的 y 轴数据一起显示。

- **当前数据元素**：当前所选数据元素及其关联的详细信息。

若要向下钻取进一步到特定的数据切片，左键单击当前关系图上的数据点，然后将所选的区域拖到所选的终结点。 右键单击灰色的所选的区域中，然后选择**缩放**，此图中所示：

  [![预览图表缩放](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

在执行后**缩放**操作，则请参阅设置所选的数据。 选择要循环访问时序见解数据的三个 y 轴表示的 y 轴格式控件。

  [![预览图表 y 轴](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

这里，您可以看到共享的 Y 轴的示例：

  [![预览共享的 Y 轴](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>时间编辑器面板

使用时序见解预览版时，首先选择时间跨度。 所选的时间范围控制数据集是可用于操作的时间系列 Insights 预览版小组件。 以下 web 控件是在时间系列 Insights 预览版中提供用于选择在工作时间跨度：

  [![时间选项面板](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **内部日期范围滑块工具**:通过拖动所需的时间范围内使用两个终结点的控件。 此内部日期范围内受到外部日期范围滑块控件。

1. **增加和减少日期范围按钮**：通过选择任一按钮来增加或减少时间跨度，从而获得所需间隔。

1. **时间跨度折叠控件**：此 web 控件允许您隐藏内部日期范围滑块工具除外的所有控件。

1. **外部日期范围滑块控件**:使用终结点控件选择外部日期范围，将可供您内部日期范围的控件。

1. **快速选择时间日期范围下拉菜单**:使用它来快速切换预定的时间跨度的选择，如最后一个**30 分钟**，则**最近 12 个小时**，或**自定义范围**。 更改此值也会更改间隔大小滑块工具中所讨论的可用间隔范围。

1. **间隔大小滑块工具**：使用它来在相同的时间范围内放大和缩小间隔。 通过此操作可更精确地控制较大时间片段之间的移动。 它将显示平滑的趋势直至小可为一毫秒以下的切片。 可用于查看细化的高分辨率切割的数据。 滑块的默认起点设置为所选数据的最佳视图，可平衡分辨率、查询速度和粒度。

1. **日期范围为-和-从 web 控件**:与此 web 控件，可以轻松选择所需的日期和时间范围。 还可以使用该控件在不同时区之间切换。 进行更改，以便将应用于当前工作区后，选择**保存**。

   [![与选择面板](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>导航面板

时序见解应用的顶部显示的时间系列 Insights 预览版导航面板中。 它提供了以下功能。

### <a name="current-session-share-link-control"></a>当前会话共享链接控件

  [![共享图标](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

选择新**共享**图标以与你的团队共享的 URL 链接。

  [![共享实例 URL](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>租户分区

  [![租户所选内容](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* 显示你当前的时序见解登录帐户信息。
* 使用它来提供的时序见解主题之间切换。
* 使用它来查看预览版[演示 web 应用](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>主题选择

若要选择新的主题，请选择配置文件图标位于右上角。 然后，选择**更改主题**。

  [![主题选择](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> 语言选择也会提供选择配置文件图标。

Azure 时序见解预览版支持两个主题：

* **浅色主题**:本文档中显示的默认主题。
* **深色主题**：如下所示呈现资源管理器：

  [![所选的深色主题](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 环境控件

### <a name="preview-terms-panel"></a>预览条款面板

此部分仅适用于尝试在更新后的 UI 中使用资源管理器的现有 S1/S2 环境。 您可能想要在组合中使用的已公开发布的产品和预览。 我们将现有 UI 中的一些功能添加到了更新的资源管理器中，但是你可以在现有时序见解资源管理器中获得 S1/S2 环境的完整 UI 体验。 

而不是层次结构中，您看到的时序见解术语面板中，您在环境中定义的查询。 使用该值来筛选你基于谓词的数据。

  [![其中查询面板](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

时序见解预览版术语编辑器面板采用以下参数：

**Where**：使用 where 子句，若要使用的操作数集快速筛选事件下表中列出。 如果通过选择操作数来执行搜索，则谓词会基于该搜索自动更新。 支持的操作数类型包括：

| Operation | 支持的类型   | 说明 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | 双精度、DateTime、TimeSpan | |
| `=`、`!=`、`<>` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL |
| `IN` | 字符串、布尔型、双精度型、DateTime、TimeSpan、NULL | 所有操作数应为同一类型或者是 NULL 常数。 |
| `HAS` | String | 在右侧允许仅常量字符串文本。 不允许空字符串和 NULL。 |

若要了解有关支持的查询操作和数据类型的详细信息，请参阅[时间序列表达式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)。

### <a name="examples-of-where-clauses"></a>示例的 where 子句

  [![其中子句示例](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**度量值**：显示所有数值列的下拉列表 (**双精度型值**) 可以用作当前图表元素。

**拆分依据**：此下拉列表中您可以对数据进行分组的模型显示所有可用分类列 （字符串）。 您可以添加最多五个术语，若要查看同一 x 轴上。 输入的参数，然后选择**添加**以添加新术语。

  [![其中一个查询和筛选视图](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

您可以按显示或隐藏图表面板中的元素通过选择可见图标，如下图中所示。 若要完全删除查询，请选择红色**X**。

  [![两个查询和筛选视图](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解如何[存储和入口](./time-series-insights-update-storage-ingress.md)Azure 时间系列 Insights 预览版中。
- 在读取时时序见解预览文档[数据建模](./time-series-insights-update-tsm.md)。
- 了解[如何诊断和故障排除](./time-series-insights-update-how-to-troubleshoot.md)时序见解实例。
