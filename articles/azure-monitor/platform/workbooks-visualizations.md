---
title: Azure Monitor 工作簿可视化效果
description: 了解所有的 Azure Monitor 工作簿可视化组件，包括文本、图表、网格、树和图形。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 443751ee974dae3c426f36cbc262bc1a3af65bd0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504931"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor 工作簿可视化效果

Azure Monitor 工作簿支持许多不同的可视化样式以满足你的报告需求。 本文提供每种可视化效果的示例。

## <a name="text"></a>文本

工作簿允许作者在其工作簿中包含文本块。 文本可以是遥测数据的人工分析、用于帮助用户解释数据的信息、部分标题，等等。

![Apdex 文本表的屏幕截图](./media/workbooks-visualizations/apdex.png)

文本是通过 Markdown 控件添加的，在该控件中可以进行全面的格式控制。

![用于生成所要呈现的表的原始 Markdown 屏幕截图](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>添加文本控件

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加文本”链接将一个文本控件添加到工作簿。
3. 将 Markdown 添加到该控件。
4. 单击“完成编辑”按钮以查看带格式的文本。

> [!TIP]
> 使用此 [Markdown 速查表](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)了解不同的格式设置选项。

## <a name="charts"></a>图表

工作簿允许以图表形式提供监视数据。 支持的图表类型包括折线图、条形图、分类条形图、面积图、散点图、饼图和时间图。 作者可以选择自定义图表的高度、宽度、调色板、图例、标题和非数据消息等。

工作簿支持日志和指标数据源的图表。 

### <a name="adding-a-log-chart"></a>添加日志图表

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析（例如，请求的趋势）的 [KQL](/azure/kusto/query/)。
5. 将可视化效果设置为下列其中一项：“面积图”、“条形图”、“条形图(分类)”、“折线图”、“饼图”、“散点图”或“时间图”。      
6. 根据需要设置其他参数 - 例如时间范围、可视化效果、大小、调色板和图例。

![处于编辑模式的日志图表的屏幕截图](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询类型 | 日志、Azure Resource Graph 等 |
| `Resource Type` | 目标资源类型 | Application Insights、Log Analytics 或 Azure-first |
| `Resources` | 要从中获取指标值的一组资源 | MyApp1 |
| `Time Range` | 用于查看日志图表的时间范围 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果 | 面积图、条形图、折线图、饼图、散点图、时间图、分类条形图 |
| `Size` | 控件的垂直大小 | 小、中、大或完整 |
| `Color palette` | 要在图表中使用的调色板。 在多指标或分段模式下将被忽略。 | 蓝色、绿色、红色，等等 |
| `Legend` | 用于图例的聚合函数 | 值的总和或平均值，或者最大值、最小值、第一个值或最后一个值 |
| `Query` | 以图表可视化效果所需的格式返回数据的任何 KQL 查询 | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="adding-a-metric-chart"></a>添加指标图表

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加指标”链接将指标控件添加到工作簿。
3. 选择资源类型（例如“存储帐户”）、目标资源、指标命名空间和名称，以及要使用的聚合。
4. 根据需要设置其他参数 - 例如时间范围、拆分依据、可视化效果、大小和调色板。

![处于编辑模式的指标图表的屏幕截图](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>指标图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 目标资源类型 | “存储”或“虚拟机”。 |
| `Resources` | 要从中获取指标值的一组资源 | MyStorage1 |
| `Namespace` | 包含该指标的命名空间 | 存储 > Blob |
| `Metric` | 要可视化的指标 | 存储 > Blob > 事务 |
| `Aggregation` | 要应用到指标的聚合函数 | Sum、Count、Average 等 |
| `Time Range` | 用于查看指标的时间范围 | 过去 1 小时、过去 24 小时，等等 |
| `Visualization` | 要使用的可视化效果 | 面积图、条形图、折线图、散点图、网格 |
| `Split By` | （可选）按维度拆分指标 | 按地理类型列出事务 |
| `Size` | 控件的垂直大小 | 小、中或大 |
| `Color palette` | 要在图表中使用的调色板。 使用 `Split by` 参数时将被忽略 | 蓝色、绿色、红色，等等 |

## <a name="grids"></a>网格

网格或表是向用户呈现数据的常用方式。 工作簿允许用户为网格列单独设置样式，以便在报告中提供丰富的 UI。

以下示例显示了一个网格，其中组合了图标、热度地图和缩微条形图，以呈现复杂的信息。 该工作簿还提供了排序控件、搜索框和“转到分析”按钮。

![基于日志的网格的屏幕截图](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>添加基于日志的网格

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析（例如，内存低于阈值的 VM）的 KQL
5. 将可视化效果设置为“网格”
6. 根据需要设置其他参数 - 例如时间范围、大小、调色板和图例。

![基于日志的网格查询的屏幕截图](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>磁贴

磁贴非常适合用于在工作簿中呈现摘要数据。 下图显示了磁贴的常见用例 - 位于详细网格顶层的应用级摘要。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tiles-summary.png)

工作簿磁贴支持显示标题、副标题、大文本、图标、基于指标的渐变、缩微折线图/条形图、页脚等。

### <a name="adding-a-tile"></a>添加磁贴

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。 
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 将“大小”设置为“完整”
6. 将可视化效果设置为“磁贴”
7. 单击“磁贴设置”按钮打开“设置”窗格
8. 在“磁贴字段”中，设置：
    * 标题：`name`
    * 左侧：`Requests`，呈现器：`Big Number`，调色板：`Green to Red`，最小值：`0`
    * 底部：`appName`
9. 单击窗格底部的“保存并关闭”按钮。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tile-settings.png)

这是磁贴在读取模式下的外观：

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Trees

工作簿通过树网格支持分层视图。 在树中可以展开某些行进入下一级别，以获得向下钻取体验。

以下示例显示了已可视化为树网格的容器运行状况指标（工作集大小）。 此处的顶级节点是 Azure Kubernetes 服务 (AKS) 节点，下一级别是 Pod，最终级别为容器。 请注意，仍可以像在网格中那样设置列的格式（热度地图、图标、链接）。 在本例中，基础数据源是具有 AKS 日志的 Log Analytics 工作区。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>添加树网格
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。 
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. 将可视化效果设置为“网格”
6. 单击“列设置”按钮打开“设置”窗格
7. 在底部的“树/分组依据设置”部分，设置：
    * 树类型：`Parent/Child`
    * ID 字段：`Id`
    * 父 ID 字段：`ParentId`
    * 要显示展开器的列：`Name`
    * 展开树的最高级别：`checked`
8. 在顶部的“列”部分，设置：
    * _ID_ - 列呈现器：`Hidden`
    * _父 ID_ - 列呈现器：`Hidden`
    * _请求_ - 列呈现器：`Bar`，颜色：`Blue`，最小值：`0`
9. 单击窗格底部的“保存并关闭”按钮。    

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>树设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Id Field` | 网格中每一行的唯一 ID |
| `Parent Id Field` | 当前行的父级的 ID |
| `Show the expander on` | 要显示树展开器的列。 树网格通常会隐藏其 ID 和父 ID 字段，因为这些数据非常不便于阅读。 包含易于阅读的值的字段（例如实体名称）上会显示展开器 |
| `Expand the top level of the tree` | 如果选中该展开器，树网格会在顶级位置展开。 如果你希望在默认情况下显示更多信息，此选项会很有用 |

## <a name="graphs"></a>关系图

工作簿支持根据日志中的数据可视化任意图形，以显示监视实体之间的关系。

下图显示了通过各种端口从一台计算机流入/流出外部计算机的数据。 这些数据按类型（计算机、端口与外部 IP）以不同的颜色显示，边缘大小对应于不同计算机之间的流动数据量。 基础数据来自针对 VM 连接的 KQL 查询。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>添加图形
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。 
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. 将可视化效果设置为“图形”
8. 单击“图形设置”按钮打开“设置”窗格
9. 在底部的“布局字段”中，设置：
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 在顶部的“节点格式设置”中，设置：
    * _使内容靠上_ - 使用列：`Name`，列呈现器：`Text`
    * _使内容居中_ - 使用列：`Calls`，列呈现器：`Big Number`，调色板：`None`
    * _使内容靠下_ - 使用列：`Kind`，列呈现器：`Text`
10. 单击窗格底部的“保存并关闭”按钮。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 资源管理器[部署](workbooks-automate.md)工作簿。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
