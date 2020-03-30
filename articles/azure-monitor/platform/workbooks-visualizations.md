---
title: Azure 监视器工作簿可视化效果
description: 了解所有 Azure 监视器工作簿可视化组件，包括 - 文本、图表、网格、树和图形。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658024"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure 监视器工作簿可视化效果

Azure 监视器工作簿支持多种不同样式的可视化，以满足您的报告需求。 本文提供了每种可视化类型的示例。

## <a name="text"></a>Text

工作簿允许作者在其工作簿中包含文本块。 文本可以是遥测的人工分析、帮助用户解释数据的信息、节标题等。

![Apdex 文本表的屏幕截图](./media/workbooks-visualizations/apdex.png)

文本通过标记控件添加，该控件提供完整的格式控制。

![生成呈现表的原始标记的屏幕截图](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>添加文本控件

1. 通过单击 **"编辑**"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加文本"** 链接将文本控件添加到工作簿。
3. 将标记添加到控件。
4. 单击"**完成编辑"** 按钮以查看格式化的文本。

> [!TIP]
> 使用此[标记备忘单](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)了解不同的格式选项。

## <a name="charts"></a>图表

工作簿允许将监视数据作为图表显示。 支持的图表类型包括线、条形图、条形线、区域、散点图、饼图和时间。 作者可以选择自定义图表的高度、宽度、调色板、图例、标题、无数据消息等。

工作簿支持日志和指标数据源的图表。 

### <a name="adding-a-log-chart"></a>添加日志图表

1. 通过单击 **"编辑**"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加"查询**链接将日志查询控件添加到工作簿。
3. 选择查询类型作为**日志**、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入[KQL](https://docs.microsoft.com/azure/kusto/query/)进行分析（例如，请求趋势）。
5. 将可视化设置为：**区域**、**条形**图、**条形（分类）、****线**、**饼图**、**散点**或**时间**。
6. 根据需要设置其他参数 - 如时间范围、可视化效果、大小、调色板和图例。

![编辑模式下日志图表的屏幕截图](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>日志图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查询类型 | 日志、Azure 资源图等 |
| `Resource Type` | 要定位的资源类型 | 应用程序见解、日志分析或 Azure 优先 |
| `Resources` | 一组资源，用于从 | MyApp1 |
| `Time Range` | 查看日志图表的时间窗口 | 最后一小时，最后24小时等 |
| `Visualization` | 要使用的可视化效果 | 区域， 条形， 线， 饼图， 散点， 时间， 条分类 |
| `Size` | 控件的垂直大小 | 小、中、大或满 |
| `Color palette` | 要在图表中使用的调色板。 在多度量或分段模式下忽略。 | 蓝色、绿色、红色等 |
| `Legend` | 用于图例的聚合函数 | 值或最大值的总和或平均值，最小值，第一个值，最后值 |
| `Query` | 以图表可视化预期格式返回数据的任何 KQL 查询 | _请求\|使序列 请求 = 计数（） 默认值 = 从之前（1d）到现在（） 步骤 1h 的时间戳上的 0_ |

### <a name="adding-a-metric-chart"></a>添加指标图表

1. 通过单击 **"编辑**"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加指标"** 链接将指标控件添加到工作簿。
3. 选择资源类型（例如，存储帐户）、要定位的资源、指标命名空间和名称以及要使用的聚合。
4. 根据需要设置其他参数 - 如时间范围、拆分、可视化、大小和调色板。

![编辑模式下指标图表的屏幕截图](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>指标图表参数

| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 要定位的资源类型 | 存储或虚拟机。 |
| `Resources` | 一组资源，用于从 | 我的存储1 |
| `Namespace` | 带有指标的命名空间 | 存储> Blob |
| `Metric` | 要可视化的指标 | 存储> blob >事务 |
| `Aggregation` | 要应用于指标的聚合函数 | 总和、计数、平均值等 |
| `Time Range` | 查看指标的时间窗口 | 最后一小时，最后24小时等 |
| `Visualization` | 要使用的可视化效果 | 面积， 条形， 线， 散点， 网格 |
| `Split By` | 可以选择拆分维度上的指标 | 按地理位置类型的交易记录 |
| `Size` | 控件的垂直大小 | 小、中或大 |
| `Color palette` | 要在图表中使用的调色板。 如果使用参数，`Split by`则忽略 | 蓝色、绿色、红色等 |

## <a name="grids"></a>网格

网格或表是向用户显示数据的常见方法。 工作簿允许用户单独设置网格列的样式，为其报表提供丰富的 UI。

下面的示例显示了一个网格，该网格结合了图标、热图和火花柱来显示复杂的信息。 工作簿还提供排序、搜索框和转到分析按钮。

![基于日志的网格屏幕截图](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>添加基于日志的网格

1. 通过单击 **"编辑**"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加"查询**链接将日志查询控件添加到工作簿。
3. 选择查询类型作为**日志**、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入 KQL 进行分析（例如，内存低于阈值的 VM）
5. 将可视化设置为**网格**
6. 如果需要，设置其他参数 - 如时间范围、大小、调色板和图例。

![基于日志的网格查询屏幕截图](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>磁贴

磁贴是在工作簿中显示摘要数据的非常有用的方法。 下图显示了磁贴的常见用例 - 详细网格之上的应用级别摘要。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tiles-summary.png)

工作簿磁贴支持显示标题、字幕、大文本、图标、基于指标的渐变、火花线/条形、页脚等。

### <a name="adding-a-tile"></a>添加磁贴

1. 通过单击 _"编辑_"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加"查询**链接将日志查询控件添加到工作簿。 
3. 选择查询类型作为**日志**、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入 KQL 进行分析
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 将大小设置为 **"完整"**
6. 将可视化设置为**磁贴**
7. 单击"**磁贴设置"** 按钮以打开"设置"窗格
8. 在**磁贴字段**中 ，设置：
    * 标题：`name`
    * 左： `Requests`、 渲`Big Number`染器： `Green to Red`、 调色板： 、 最小值：`0`
    * 底部：`appName`
9. 单击窗格底部的 **"保存和关闭**"按钮。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tile-settings.png)

这是切片在读取模式下的外观：

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Trees

工作簿通过树网格支持分层视图。 树允许某些行可扩展到下一个级别，以便进行向下钻取体验。

下面的示例显示了可视化为树网格的容器运行状况指标（工作集大小）。 此处的顶级节点是 Azure Kubernetes 服务 （AKS） 节点，下一级是 pod，最终级别是容器。 请注意，您仍然可以像网格一样设置列的格式（热图、图标、链接）。 在这种情况下，基础数据源是具有 AKS 日志的日志分析工作区。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>添加树网格
1. 通过单击 _"编辑_"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加"查询**链接将日志查询控件添加到工作簿。 
3. 选择查询类型作为**日志**、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入 KQL 进行分析
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
5. 将可视化设置为**网格**
6. 单击"**列设置"** 按钮以打开设置窗格
7. 在底部的 **"树/分组按设置"** 部分中，设置：
    * 树类型：`Parent/Child`
    * ID 字段：`Id`
    * 父 ID 字段：`ParentId`
    * 在以下方面显示扩展器：`Name`
    * 展开树的顶层：`checked`
8. 在顶部的 _"列"_ 部分中，设置：
    * _Id_ - 列渲染器：`Hidden`
    * _父 Id_ - 列呈现器：`Hidden`
    * _请求_- 列渲`Bar`染器： `Blue`， 颜色： ， 最小值：`0`
9. 单击窗格底部的 _"保存和关闭_"按钮。    

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>树设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Id Field` | 网格中每行的唯一 ID |
| `Parent Id Field` | 当前行的父项的 ID |
| `Show the expander on` | 要显示树扩展器的列。 树网格通常隐藏其 ID 和父 ID 字段，因为它们不太可读。 相反，扩展程序出现在具有更可读值的字段中 - 类似于实体的名称 |
| `Expand the top level of the tree` | 如果选中，树网格将在顶层展开。 默认情况下，如果要显示更多信息，则非常有用 |

## <a name="graphs"></a>关系图

工作簿支持基于日志数据可视化任意图形，以显示监视实体之间的关系。

下图显示了通过各种端口进出外部计算机的计算机进出的计算机的数据。 它按类型着色（计算机与端口与外部 IP），边缘大小对应于之间流动的数据量。 基础数据来自针对 VM 连接的 KQL 查询。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>添加图形
1. 通过单击 _"编辑_"工具栏项，将工作簿切换到编辑模式。
2. 使用 **"添加"查询**链接将日志查询控件添加到工作簿。 
3. 选择查询类型作为**日志**、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入 KQL 进行分析
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
7. 将可视化设置为**图形**
8. 单击"**图形设置"** 按钮以打开设置窗格
9. 在底部的_布局字段中_，设置：
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 在顶部的_节点格式设置_中，设置：
    * _热门内容_- 使用`Name`列： 、 列呈现器：`Text`
    * _中心内容_- 使用`Calls`列： 、`Big Number`列渲染器： 、 调色板：`None`
    * _底部内容_- 使用`Kind`列： ， 列呈现器：`Text`
10. 单击窗格底部的 _"保存和关闭_"按钮。

![磁贴摘要视图的屏幕截图](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>后续步骤

* [使用](workbooks-automate.md)Azure 资源管理器部署工作簿。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
