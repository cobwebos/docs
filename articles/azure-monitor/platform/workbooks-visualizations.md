---
title: Azure Monitor 工作簿可视化效果创建交互式报表 |Microsoft 文档
description: 了解所有 Azure Monitor 工作簿可视化组件，包括文本、图表、网格、树和关系图。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d878e8b5dd49ba517f85ebb74332bc6a245c33ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165051"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor 工作簿可视化效果

Azure Monitor 工作簿支持多种不同的可视化样式，以满足您的报表需求。 本文提供了每种类型的可视化效果的示例。

## <a name="text"></a>文本

工作簿允许作者在其工作簿中包含文本块。 文本可以是对遥测数据进行人工分析、帮助用户解释数据的信息、节标题等。

![Apdex 文本表的屏幕截图](./media/workbooks-visualizations/apdex.png)

文本是通过 Markdown 控件添加的，它提供了完整的格式设置控件。

![生成呈现的表的原始 markdown 的屏幕截图](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>添加文本控件

1. 单击 "**编辑**" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加文本**" 链接向工作簿添加文本控件。
3. 将 Markdown 添加到控件。
4. 单击 "**完成编辑**" 按钮以查看格式化文本。

> [!TIP]
> 使用此[Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)备忘单来了解不同的格式设置选项。

## <a name="charts"></a>图表

工作簿允许监视数据显示为图表。 支持的图表类型包括折线图、条形图、条形分类、面积图、散点图、饼图和时间。 作者可以选择自定义图表的高度、宽度、调色板、图例、标题、非数据消息等。

工作簿支持用于日志和指标数据源的图表。 

### <a name="adding-a-log-chart"></a>添加日志图表

1. 单击 "**编辑**" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加查询**" 链接向工作簿添加日志查询控件。
3. 选择 "**日志**"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的[KQL](https://docs.microsoft.com/azure/kusto/query/) （例如，请求的趋势）。
5. 将可视化效果设置为以下其中一个：**面积** **图、条形**图、**条形图（分类）** 、**线条**、**饼图**、**散点图**或**Time**。
6. 如果需要，还需要设置其他参数-例如，时间范围、可视化效果、大小、调色板和图例。

![编辑模式下日志图表的屏幕截图](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询类型 | 日志、Azure 资源图等。 |
| `Resource Type` | 目标资源类型 | Application Insights、Log Analytics 或 Azure-优先 |
| `Resources` | 要从中获取指标值的一组资源 | MyApp1 |
| `Time Range` | 用于查看日志图表的时间范围 | 过去1小时、过去24小时等 |
| `Visualization` | 要使用的可视化效果 | 面积图、条形图、折线图、饼图、散点图、时间图、条形分类 |
| `Size` | 控件的垂直大小 | 小、中、大或完全 |
| `Color palette` | 要在图表中使用的调色板。 在多指标或分段模式下忽略。 | 蓝色、绿色、红色等。 |
| `Legend` | 要用于图例的聚合函数 | 值或 Max、Min、First、Last 值的总和或平均值 |
| `Query` | 任何 KQL 查询，以图表可视化效果所需的格式返回数据 | _\| 请求在1小时（）的时间戳（）中将 "make-series Requests = 0" 设置为默认值 = 0 （）步骤_ |

### <a name="adding-a-metric-chart"></a>添加度量值图表

1. 单击 "**编辑**" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加度量值**" 链接可向工作簿添加度量值控件。
3. 选择资源类型（例如，存储帐户）、目标资源、指标命名空间和名称，以及要使用的聚合。
4. 如果需要，请设置其他参数，如时间范围、拆分、可视化、大小和调色板。

![编辑模式下指标图表的屏幕截图](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>指标图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 目标资源类型 | 存储或虚拟机。 |
| `Resources` | 要从中获取指标值的一组资源 | MyStorage1 |
| `Namespace` | 具有指标的命名空间 | 存储 > Blob |
| `Metric` | 要可视化的度量值 | 存储 > Blob > 事务 |
| `Aggregation` | 要应用于度量值的聚合函数 | Sum、Count、Average 等 |
| `Time Range` | 要在其中查看度量值的时间范围 | 过去1小时、过去24小时等 |
| `Visualization` | 要使用的可视化效果 | 面积图、条形图、折线图、散点图、网格线 |
| `Split By` | 根据需要拆分维度上的度量值 | 按地理类型的事务 |
| `Size` | 控件的垂直大小 | 小型、中型或大型 |
| `Color palette` | 要在图表中使用的调色板。 如果使用 `Split by` 参数，则忽略此参数 | 蓝色、绿色、红色等。 |

## <a name="grids"></a>置

网格或表是向用户显示数据的常用方法。 工作簿允许用户单独对网格中的列进行样式，以便为报表提供丰富的 UI。

下面的示例显示了一个网格，该网格结合了图标、热图和 spark，以显示复杂的信息。 该工作簿还提供排序、搜索框和 "中转到分析" 按钮。

![基于日志的网格的屏幕截图](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>添加基于日志的网格

1. 单击 "**编辑**" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加查询**" 链接向工作簿添加日志查询控件。
3. 选择 "**日志**"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的 KQL （例如，内存低于阈值的 Vm）
5. 将可视化效果设置为**网格**
6. 如果需要，请设置其他参数-例如，时间范围、大小、调色板和图例。

![基于日志的网格查询的屏幕截图](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>磁贴

磁贴是在工作簿中呈现摘要数据的一种非常有用的方法。 下图显示了磁贴的一个常见用例-应用级别摘要位于详细网格之上。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tiles-summary.png)

工作簿磁贴支持显示标题、副标题、大文本、图标、基于指标的渐变、spark 线条/栏、页脚等。

### <a name="adding-a-tile"></a>添加磁贴

1. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加查询**" 链接向工作簿添加日志查询控件。 
3. 选择 "**日志**"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 将大小设置为**完整**
6. 将可视化效果设置为**磁贴**
7. 单击 "**磁贴设置**" 按钮可打开 "设置" 窗格
8. 在 "**磁贴字段**" 中，设置：
    * 标题： `name`
    * Left： `Requests`，呈现器： `Big Number`，调色板： `Green to Red`，最小值： `0`
    * 下： `appName`
9. 单击窗格底部的 "**保存并关闭**" 按钮。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tile-settings.png)

这是磁贴在读取模式下的外观：

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>林中

工作簿通过树-网格支持分层视图。 树允许将一些行扩展到下一级别以实现向下钻取体验。

下面的示例显示了可视化为树形网格的容器运行状况指标（工作集大小）。 此处的顶级节点是 Azure Kubernetes Service （AKS）节点，下一级别是 pod，最终级别为容器。 请注意，你仍可以在网格中设置列的格式，如 "热度地图"、"图标"、"链接"。 在这种情况下，基础数据源是具有 AKS 日志的 Log Analytics 工作区。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>添加树网格
1. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加查询**" 链接向工作簿添加日志查询控件。 
3. 选择 "**日志**"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的 KQL
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
5. 将可视化效果设置为**网格**
6. 单击 "**列设置**" 按钮以打开 "设置" 窗格
7. 在底部的 "**树/分组依据设置**" 部分中，设置：
    * 树类型： `Parent/Child`
    * Id 字段： `Id`
    * 父 Id 字段： `ParentId`
    * 显示以下内容的扩展器： `Name`
    * 展开树的顶层： `checked`
8. 在顶部的 "_列_" 部分中，设置：
    * _Id_ -列呈现器： `Hidden`
    * _父 Id_ -列呈现器： `Hidden`
    * _请求_-列呈现器： `Bar`，颜色： `Blue`，最小值： `0`
9. 单击窗格底部的 "_保存并关闭_" 按钮。    

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>树设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Id Field` | 网格中每行的唯一 Id |
| `Parent Id Field` | 当前行的父项的 id |
| `Show the expander on` | 要显示树扩展器的列。 树形网格通常会隐藏其 id 和父 id 字段，因为它们不是非常可读的。 而是显示在具有更具可读性的值的字段上（如实体的名称） |
| `Expand the top level of the tree` | 如果选中，则树网格将在顶级展开。 如果希望在默认情况下显示详细信息，则此方法很有用 |

## <a name="graphs"></a>图形

工作簿支持根据日志中的数据可视化任意图形，以显示监视实体之间的关系。

下图显示了在计算机中流入/流出外部计算机的各种端口的数据。 它通过类型（计算机与端口与外部 IP）进行着色，边缘大小对应于两者之间流动的数据量。 基础数据来自 KQL 查询目标 VM 连接。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>添加关系图
1. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "**添加查询**" 链接向工作簿添加日志查询控件。 
3. 选择 "**日志**"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的 KQL
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
7. 将可视化效果设置为**Graph**
8. 单击 "**图形设置**" 按钮以打开 "设置" 窗格
9. 在底部的_布局字段_中，设置：
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 在顶部的 "_节点格式设置_" 中，设置：
    * _顶部内容_-使用列： `Name`列呈现器： `Text`
    * _中心内容_-使用列： `Calls`，列呈现器： `Big Number`，调色板： `None`
    * _底部内容_-使用列： `Kind`列呈现器： `Text`
10. 单击窗格底部的 "_保存并关闭_" 按钮。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>后续步骤

* 用 Azure 资源管理器[部署](workbooks-automate.md)工作簿。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。