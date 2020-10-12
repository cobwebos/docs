---
title: Azure Monitor 工作簿网格可视化效果
description: 了解所有 Azure Monitor 工作簿网格可视化对象。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663764"
---
# <a name="grid-visualizations"></a>网格可视化

网格或表是向用户呈现数据的常用方式。 工作簿允许用户为网格列单独设置样式，以便在报告中提供丰富的 UI。

以下示例显示了一个网格，其中组合了图标、热度地图和缩微条形图，以呈现复杂的信息。 该工作簿还提供了排序控件、搜索框和“转到分析”按钮。

[![基于日志的网格的屏幕截图](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>添加基于日志的网格

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析（例如，内存低于阈值的 VM）的 KQL
5. 将可视化效果设置为“网格”
6. 如果需要，请设置其他参数-例如，时间范围、大小、调色板和图例。

[![基于日志的网格查询的屏幕截图](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询的类型。 | 日志、Azure Resource Graph 等 |
| `Resource Type` | 要作为目标的资源类型。 | Application Insights、Log Analytics 或 Azure-first |
| `Resources` | 要从中获取指标值的一组资源。 | MyApp1 |
| `Time Range` | 用于查看日志图表的时间窗口。 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果。 | 网格 |
| `Size` | 控件的垂直大小。 | 小、中、大或满 |
| `Query` | 任何 KQL 查询，以图表可视化效果所需的格式返回数据。 | _请求 \| 汇总请求数 = 计数 ( 按名称的 # A1_ |

## <a name="simple-grid"></a>简单网格

工作簿可以将 KQL 结果呈现为简单表。 下图显示了应用程序中每个请求类型的请求计数和唯一用户数。

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![处于编辑模式的基于日志的网格的屏幕截图](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>网格样式

尽管纯表显示数据，但很难阅读，并且见解并不总是明显。 设置网格样式有助于更轻松地读取和解释数据。

下面是上一节中样式为热图的相同网格。

[![带有样式为热图的列的基于日志的网格屏幕截图](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

下面是与条形样式相同的网格： [ ![ 基于日志的网格屏幕截图，列样式为条形](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>设置网格列的样式

1. 选择 "查询控件" 工具栏中的 " **列设置** " 按钮。
2. 在 " *编辑列设置*" 中，选择要设置样式的列。
3. 选择列呈现器 (例如热度地图、bar、bar 等 ) 以及用于设置列样式的相关设置。

下面的示例将 *请求* 列的样式作为一个条形：

[![基于日志的网格屏幕截图，请求列样式为条形。](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>列呈现器

| 列呈现器 | 说明 | 其他选项 |
|:------------- |:-------------|:-------------|
| `Automatic` | 默认值-根据列类型使用最适合的呈现器。  |  |
| `Text` | 呈现文本形式的列值。 | |
| `Right Aligned` | 类似于文本，只不过它是右对齐的。 | |
| `Date/Time` | 呈现可读日期时间字符串。 | |
| `Heatmap` | 根据单元格的值为网格单元格着色。 | 用于缩放的调色板和最小/最大值。 |
| `Bar` | 根据单元格的值，呈现单元格旁边的条。 | 用于缩放的调色板和最小/最大值。 |
| `Bar underneath` | 根据单元格的值，在单元格的底部附近呈现一个条形。 | 用于缩放的调色板和最小/最大值。 |
| `Composite bar` | 使用该行中的指定列呈现复合条。 有关详细信息，请参阅 [复合条](workbooks-composite-bar.md) 。 | 具有相应颜色的列，用于呈现条形图和在条形顶部显示的标签。 |
| `Spark bars` | 根据单元格中动态数组的值，在单元格中呈现 spark bar。 例如，从顶部屏幕截图到趋势列。 | 用于缩放的调色板和最小/最大值。 |
| `Spark lines` | 根据单元格中动态数组的值，在单元格中呈现 spark 行。 | 用于缩放的调色板和最小/最大值。 |
| `Icon` | 基于单元格中的文本值呈现图标。 支持的值包括： `cancelled` 、 `critical` 、 `disabled` 、 `error` 、、、 `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` 、、、、、、、、、、、、、、、、、、、、、、、、、、、、和。|  |
| `Link` | 呈现单击或执行可配置操作时的链接。 如果 *只* 希望该项是一个链接，请使用此选项。  任何其他类型 *也* 可以使用设置作为链接 `Make this item a link` 。 有关详细信息，请参阅以下 [链接操作](#link-actions) 。 |  |
| `Location` | 基于区域 id 呈现友好的 Azure 区域名称。 |  |
| `Resource type` | 基于资源类型 id 呈现友好资源类型字符串  |  |
| `Resource` | 基于资源 id 呈现友好资源名称和链接  | 用于显示 "资源类型" 图标的选项  |
| `Resource group` | 基于资源组 id 呈现友好资源组名称和链接。如果该单元格的值不是资源组，则会将其转换为一个资源组。  | 用于显示资源组图标的选项  |
| `Subscription` | 基于订阅 id 呈现友好订阅名称和链接。 如果该单元格的值不是一个订阅，则将其转换为一个。  | 用于显示订阅图标的选项。  |
| `Hidden` | 在网格中隐藏列。 当默认查询返回的列多于所需的列，但不需要项目时，此方法非常有用。 |  |

### <a name="link-actions"></a>链接操作

如果选择了 `Link` 呈现器或选中了 " *将此项设置为链接* " 复选框，则作者可以配置在选择该单元格时将发生的链接操作。 这通常会使用户使用来自该单元的上下文的其他视图，或者可能会打开 url。

### <a name="custom-formatting"></a>自定义格式

工作簿还允许用户设置其单元值的数字格式。 可以通过单击 " *自定义格式* " 复选框（如果可用）来执行此操作。

| 格式选项 | 说明 |
|:------------- |:-------------|
| `Units` | 针对百分比、计数、时间、字节、计数/时间、字节/时间等的列各种选项的单位。例如，1234的值单位可以设置为毫秒，并呈现为1.234 秒。 |
| `Style` | 将其呈现为十进制、货币和百分比的格式。 |
| `Show group separator` | 用于显示组分隔符的复选框。 在 US 中将1234呈现为1234。 |
| `Minimum integer digits` | 要使用 (默认 1) 的最小整数位数。 |
| `Minimum fractional digits` |  (默认值 0) 使用的最小小数位数。  |
| `Maximum fractional digits` | 要使用的最大小数位数。 |
| `Minimum significant digits` | 要使用 (默认 1) 的有效位数的最小值。 |
| `Maximum significant digits` | 要使用的最大有效位数。 |
| `Custom text for missing values` | 如果数据点没有值，则显示此自定义文本而不是空白。 |

### <a name="custom-date-formatting"></a>自定义日期格式

当作者已指定将列设置为日期/时间呈现器时，作者可以使用 " *自定义日期格式* " 复选框指定自定义日期格式设置选项。

| 格式选项 | 说明 |
|:------------- |:-------------|
| `Style` | 将日期呈现为 short、long、full 格式或将时间呈现为短时间或长时间格式的格式。 |
| `Show time as` | 允许作者决定是在本地时间 (默认) 时间还是 UTC 时间显示时间。 根据选择的日期格式样式，可能不会显示 UTC/时区信息。 |

## <a name="custom-column-width-setting"></a>自定义列宽设置

作者可以在 "*列设置*" 中使用 "*自定义列宽度*" 字段自定义网格中任意列的宽度。

![带有红框中显示的自定义列宽字段的列设置屏幕截图](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

如果该字段为黑色，则将根据列中的字符数和可见列数自动确定宽度。 默认单位为 "ch" (个字符) 。

选择标签中的蓝色 ** (当前宽度) ** 按钮将用所选列的当前宽度填充文本字段。 如果在自定义 width 字段中存在值时没有度量单位，则将使用默认值。

可用度量单位为：

| 度量单位 | 定义           |
|:--------------------|:---------------------|
| ch                  | 字符 (默认值)  |
| 像素                  | 像素               |
| fr                  | 小数单位     |
| %                   | percentage           |

输入验证-如果验证失败，则会在字段下面弹出一条红色指导消息，但用户仍可以应用宽度。 如果输入中存在值，则将对其进行分析。如果找不到有效的度量单位，则将使用默认值。

没有最小/最大宽度，因为这是由作者自行决定的。 对于隐藏列禁用 "自定义列宽度" 字段。

## <a name="examples"></a>示例

### <a name="spark-lines-and-bar-underneath"></a>Spark 线条和下面的栏

下面的示例按请求名称显示请求计数和其趋势。

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![带有下面的栏和 spark 行的基于日志的网格屏幕截图](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>带有共享缩放和自定义格式设置的热度地图

此示例显示了不同的请求持续时间指标及其计数。 热度地图呈现器使用设置中设置的最小值或计算列的最小值和最大值，并根据单元格的值相对于列的最小值和最大值从所选调色板分配背景色。

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![基于日志的网格屏幕截图，其中热度地图具有跨列的共享缩放](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

在上面的示例中，将使用 (绿色或红色) 和缩放的共享调色板来着色 (均值、中间值、p80、p95 和 p99) 的列。 用于请求列 (蓝色) 的单独调色板。

#### <a name="shared-scale"></a>共享规模

获取共享规模：

1. 使用正则表达式来选择要应用设置的多个列。 例如，将列名称设置为， `Mean|Median|p80|p95|p99` 以将其全部选中。
2. 删除单个列的默认设置。

这将导致新的多列设置应用其设置以包含一个共享缩放。

[![基于日志的网格设置的屏幕截图，用于跨列获取共享缩放](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>用于表示状态的图标

下面的示例演示了基于 p95 持续时间的请求的自定义状态。

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![基于日志的网格的屏幕截图，其中热度地图使用上面的查询跨列共享缩放。](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

支持的图标名称包括： `cancelled` 、 `critical` 、 `disabled` 、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` 和 `Blank` 。

### <a name="using-thresholds-with-links"></a>将阈值用于链接

下面的说明将演示如何将阈值与链接结合使用来分配图标和打开不同的工作簿。 网格中的每个链接都将为该 Application Insights 资源打开不同的工作簿模板。

1. 通过选择 " *编辑* 工具栏项" 将工作簿切换到编辑模式。
2. 选择 " **添加** "，然后选择 " *添加查询*"。
3. 将 *数据源* 更改为 "JSON"，将 *可视化效果* 更改为 "网格"。
4. 输入以下查询。

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. 运行查询。
6. 选择 " **列设置** " 打开设置。
7. 从 *列*中选择 "名称"。
8. 在 *列呈现*器下，选择 "阈值"。
9.  输入并选择以下 *阈值设置*。
   
    | 操作员 | 值   | 图标   |
    |----------|---------|---------|
    | ==       | warning | 警告 |
    | ==       | error   | 已失败  |

    ![带有以上设置的 "编辑列设置" 选项卡的屏幕截图。](./media/workbooks-grid-visualizations/column-settings.png)

    保持默认行为。 可以输入所需的任何文本。 文本列采用字符串格式作为输入，并使用列值和单元填充该值（如果指定）。 例如，如果 warning 为列值，则文本可以是 " {0} {1} link！"，它将显示为 "warning link！"。
10. 选中 "将 *此项设为链接* " 框。
    1. 在 " *要打开的视图*" 下，选择 "工作簿 (模板) "。
    2. 在 " *链接值*来自" 下，选择 "链接"。
    3. 选择 " *在上下文边栏选项卡中打开链接* " 框。
    4. 在*工作簿链接设置*中选择以下设置
        1. 在 " *模板 Id 来源*" 下，选择 "列"。
        2. 在 " *列* " 下选择 "链接"。

    ![具有以上设置的链接设置的屏幕截图。](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. 从 *列*中选择 "链接"。 在 " *列呈现*器" 旁的 "设置" 下，选择 ** (隐藏列 ") **。
1. 若要更改 "名称" 列的显示名称，请选择 " **标签** " 选项卡。在 "名称" 行 *中，在 "列标签*" 下，输入要显示的名称。
2. 选择 **应用**

![网格中具有以上设置的阈值的屏幕截图](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>小数单位百分比

小数单位 (fr) 是各种网格类型中常用的动态度量单位。 当窗口大小/分辨率发生变化时，fr 宽度也随之更改。

下面的屏幕截图显示了一个表，其中包含八列，每个列1fr 宽度和所有等宽宽度。 随着窗口大小的变化，每列的宽度会按比例变化。

[![网格中列的屏幕截图，每个列的列宽值为1fr](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

下图显示了相同的表，只不过第一列的宽度设置为50%。 这会动态地将列设置为整个网格宽度的一半。 调整窗口大小将继续保留50% 的宽度，除非窗口大小太小。 这些动态列的最小宽度取决于其内容。 剩余的50% 的网格除以8个总小数部分。 下面的 "kind" 列被设置为2fr，因此它占据了剩余空间的四分之一。 由于其他列每个1fr，每个列占网格右半部分的八分之一。

[![网格中列的屏幕截图，每个列宽度值为50%，其余值为1fr](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

可以将 fr、%、px 和 ch 宽度结合使用，并且其工作方式与前面的示例类似。 静态单位 (ch 和 px) 设置的宽度是不会更改的硬常量，即使更改了窗口/分辨率也是如此。 % 由% 设置的列将根据网格总宽度的百分比来表示， (可能不是因为以前最小宽度) 。 用 fr 设置的列只会根据分配的小数单位数来拆分剩余的网格空间。

[![网格中列的屏幕截图，其中使用了不同的宽度单位](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何 [在工作簿中创建树](workbooks-tree-visualizations.md)。
* 了解如何创建 [工作簿文本参数](workbooks-text.md)。