---
title: Azure Monitor 工作簿图表可视化效果
description: 了解所有 Azure Monitor 工作簿图表可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006374"
---
# <a name="chart-visualizations"></a>图表可视化效果

工作簿允许以图表形式提供监视数据。 支持的图表类型包括折线图、条形图、条形分类、面积图、散点图、饼图和时间。 作者可以选择自定义图表的高度、宽度、调色板、图例、标题、非数据消息等，并使用图表设置自定义轴类型和序列颜色。

工作簿支持日志和指标数据源的图表。

## <a name="log-charts"></a>日志图表

Azure Monitor 日志为资源所有者提供了有关应用程序和基础结构的工作原理的详细信息。 与度量值不同的是，默认情况下不收集日志信息，需要进行某种类型的收集。 但是，当提供的日志提供了大量有关资源状态的信息和对诊断有用的数据时。 工作簿允许将日志数据呈现为直观的用户分析图表。

### <a name="adding-a-log-chart"></a>添加日志图表

下面的示例显示了过去几天内对应用程序的请求趋势。

1. 通过选择 " **编辑** " 工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析（例如，请求的趋势）的 [KQL](/azure/kusto/query/)。
5. 将可视化效果设置为下列其中一项：“面积图”、“条形图”、“条形图(分类)”、“折线图”、“饼图”、“散点图”或“时间图”。      
6. 如果需要，请设置其他参数-例如，时间范围、可视化效果、大小、调色板和图例。

[![处于编辑模式的日志图表的屏幕截图](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询的类型。 | 日志、Azure Resource Graph 等 |
| `Resource Type` | 要作为目标的资源类型。 | Application Insights、Log Analytics 或 Azure-first |
| `Resources` | 要从中获取指标值的一组资源。 | MyApp1 |
| `Time Range` | 用于查看日志图表的时间窗口。 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果。 | 面积图、条形图、折线图、饼图、散点图、时间图、分类条形图 |
| `Size` | 控件的垂直大小。 | 小、中、大或满 |
| `Color palette` | 要在图表中使用的调色板。 在多指标或分段模式下将被忽略。 | 蓝色、绿色、红色，等等 |
| `Legend` | 要用于图例的聚合函数。 | 值的总和或平均值，或者最大值、最小值、第一个值或最后一个值 |
| `Query` | 任何 KQL 查询，以图表可视化效果所需的格式返回数据。 | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="time-series-charts"></a>时序图表

使用工作簿中的查询控件可以轻松地创建时序图，如面积图、条形图、折线图、散点图和时间段。 此密钥在结果集中具有时间和指标信息。

#### <a name="simple-time-series"></a>简单时序

下面的查询返回一个表，其中包含两列： *时间戳* 和 *请求*。 查询控件对 X 轴使用 *时间戳* ，并 *请求* Y 轴。

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![简单时序日志行图的屏幕截图。](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>具有多个指标的时间序列

下面的查询返回一个表，其中包含三列： *时间戳*、 *请求*和 *用户*。 查询控件对 X 轴使用*时间戳*，并*Requests*  &  在 Y 轴上请求*用户*作为单独的序列。

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![具有多个度量值日志行的时间序列的屏幕截图。](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>分段时序

下面的查询返回一个表，其中包含三列： *timestamp*、 *Requests*和 *RequestName* ，其中 *RequestName* 是带有请求名称的分类列。 此处的查询控件对 X 轴使用 *时间戳* ，并为每个 *RequestName*值添加一个序列。

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![分段时序日志行图的屏幕截图。](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>汇总与建立序列

上一节中的示例使用 `summarize` 运算符，因为它更易于理解。 不过，汇总确实有一个主要限制，因为如果存储桶中没有任何项，则会忽略结果行。 它可以改变图表时间窗口的效果，具体取决于空 bucket 是在时间范围的正面还是背面。

通常，最好使用 `make-series` 运算符创建时序数据，该数据可用于为空 bucket 提供默认值。

下面的查询使用 `make-series` 运算符。

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

下面的查询显示带有运算符的类似图表 `summarize`

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

即使基础结果集不同也是如此。 用户需要做的就是将可视化效果设置为区域、线条、条形或时间，工作簿将负责处理其余任务。

[![从生成系列查询中创建的日志行图的屏幕截图](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>分类条形图或直方图

分类图表允许用户在图表的 X 轴上表示维度或列，这在直方图中尤其有用。 下面的示例显示了按其结果代码分配请求的方式。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

查询返回两列： *请求* 度量值和 *结果* 类别。 " *结果* " 列的每个值都将在图表中获取其自己的条形图，其高度与 *请求度量*值成正比。

[![按结果代码分类的请求的分类条形图的屏幕截图](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>饼图

饼图允许直观呈现数字比例。 下面的示例按结果代码显示请求的比例。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

查询返回两列： *请求* 度量值和 *结果* 类别。 *结果*列的每个值都将在饼图中获取其自己的切片，大小与*请求*度量值成正比。

[![带有表示结果代码的切片的饼图屏幕截图](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>指标图表

大多数 Azure 资源会发出有关状态和运行状况的指标数据 (例如，CPU 使用率、存储可用性、数据库事务计数、失败的应用请求等 ) 。 工作簿允许将此数据可视化为时序图表。 ) 

### <a name="adding-a-metric-chart"></a>添加指标图表

下面的示例将显示先前一小时内存储帐户中的事务数。 这允许存储所有者查看事务趋势，并查找行为中的异常。

1. 通过选择 " **编辑** " 工具栏项，将工作簿切换到编辑模式。
2. 使用“添加指标”链接将指标控件添加到工作簿。
3. 选择资源类型（例如“存储帐户”）、目标资源、指标命名空间和名称，以及要使用的聚合。
4. 根据需要设置其他参数 - 例如时间范围、拆分依据、可视化效果、大小和调色板。

[![处于编辑模式的指标图表的屏幕截图](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>指标图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 要作为目标的资源类型。 | “存储”或“虚拟机”。 |
| `Resources` | 要从中获取指标值的一组资源。 | MyStorage1 |
| `Namespace` | 具有指标的命名空间。 | 存储 > Blob |
| `Metric` | 要可视化的度量值。 | 存储 > Blob > 事务 |
| `Aggregation` | 要应用于度量值的聚合函数。 | Sum、Count、Average 等 |
| `Time Range` | 要在其中查看度量值的时间范围。 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果。 | 面积图、条形图、折线图、散点图、网格 |
| `Split By` | 根据需要拆分维度上的度量值。 | 按地理类型列出事务 |
| `Size` | 控件的垂直大小。 | 小、中或大 |
| `Color palette` | 要在图表中使用的调色板。 如果使用参数，则忽略 `Split by` 。 | 蓝色、绿色、红色，等等 |

### <a name="examples"></a>示例

按 API 名称将事务拆分为折线图：

[![按 API 名称拆分的存储事务的指标折线图屏幕截图](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

按响应类型将事务拆分为大条形图：

[![按响应类型拆分的存储事务的大型指标条形图的屏幕截图](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

散点图的平均延迟：

[![用于存储延迟的度量散点图的屏幕截图](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>图表设置

作者可以使用图表设置自定义图表轴中使用的字段、轴单位、自定义格式设置、范围、分组行为、图例和序列颜色。

### <a name="the-settings-tab"></a>“设置”选项卡

"设置" 选项卡控件：

- 轴设置，其中包括允许用户将数字格式设置为轴值和自定义范围的自定义格式设置。
- 分组设置，包括创建 "其他" 组之前的限制。
- 图例设置，包括显示度量值 (系列名称、颜色和数字) 底部和/或图例 (序列名称和颜色) 。

![图表设置的屏幕截图。](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>自定义格式

数字格式设置选项包括：

| 格式选项             | 说明                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | 针对百分比、计数、时间、字节、计数/时间、字节/时间等的列各种选项的单位。例如，1234的值单位可以设置为毫秒，并呈现为 1.234 s。                                  |
| `Style`                      | 将其呈现为十进制、货币和百分比的格式。                                               |
| `Show group separator`       | 用于显示组分隔符的复选框。 在 US 中将1234呈现为1234。                                    |
| `Minimum integer digits`     | 要使用 (默认 1) 的最小整数位数。                                                   |
| `Minimum fractional digits`  |  (默认值 0) 使用的最小小数位数。                                                |
| `Maximum fractional digits`  | 要使用的最大小数位数。                                                            |
| `Minimum significant digits` | 要使用 (默认 1) 的有效位数的最小值。                                               |
| `Maximum significant digits` | 要使用的最大有效位数。                                                           |

![X 轴设置的屏幕截图。](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>"序列" 选项卡

利用 "序列设置" 选项卡，您可以调整为图表中的序列显示的标签和颜色。

- 此 `Series name` 字段用于匹配数据中的序列，如果匹配，将显示显示标签和颜色。
- `Comment`此字段对于模板作者很有用，因为翻译人员可能会使用此注释来本地化显示标签。

![序列设置的屏幕截图。](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>后续步骤

- 了解如何 [在工作簿中创建磁贴](workbooks-tile-visualizations.md)。
- 了解如何创建 [交互式工作簿](workbooks-interactive.md)。