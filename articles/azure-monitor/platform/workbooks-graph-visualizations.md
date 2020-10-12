---
title: Azure Monitor 工作簿图形可视化效果
description: 了解所有 Azure Monitor 工作簿图形可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663811"
---
# <a name="graph-visualizations"></a>图形可视化效果

工作簿支持根据日志中的数据可视化任意图形，以显示监视实体之间的关系。

下图显示了在计算机中流入/流出的数据，以及外部计算机的各种端口。 这些数据按类型（计算机、端口与外部 IP）以不同的颜色显示，边缘大小对应于不同计算机之间的流动数据量。 基础数据来自针对 VM 连接的 KQL 查询。

[![磁贴摘要视图的屏幕截图](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>添加关系图
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL。

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

5. 将可视化效果设置为“图形”
6. 选择 " **图形设置** " 按钮以打开 "设置" 窗格
7. 在底部的“布局字段”中，设置：
    * 节点 Id： `Id`
    * 源 Id： `SourceId`
    * 目标 Id： `TargetId`
    * 边缘标签： `None`
    * 边缘大小： `Calls`
    * 节点大小： `None`
    * 着色类型： `Categorical`
    * 节点颜色字段： `Kind`
    * 调色板： `Pastel`
8. 在顶部的“节点格式设置”中，设置：
    * _使内容靠上_ - 使用列：`Name`，列呈现器：`Text`
    * _使内容居中_ - 使用列：`Calls`，列呈现器：`Big Number`，调色板：`None`
    * _使内容靠下_ - 使用列：`Kind`，列呈现器：`Text`
9. 选择窗格底部的 " _保存并关闭_ " 按钮。

[![带有以上查询和设置的磁贴摘要视图的屏幕截图。](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>图形设置

| 设置         | 说明                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | 选择提供关系图上节点的唯一 ID 的列。 列的值可以是字符串或数字。 |
| `Source Id`     | 选择一个列，该列为图形上的边缘提供源节点的 Id。 值必须映射到 _节点 Id_ 列中的值。 |
| `Target Id`     | 选择一个列，该列提供图形上的边缘的目标节点的 Id。 值必须映射到 _节点 Id_ 列中的值。 |
| `Edge Label`    | 选择在关系图上提供边缘标签的列。                                                            |
| `Edge Size`     | 选择一个列，该列提供边缘宽度将基于的指标。                                |
| `Node Size`     | 选择一个列，该列提供节点区域将基于的指标。                                 |
| `Coloring Type` | 用于选择节点着色方案。                                                                            |

## <a name="node-coloring-types"></a>节点颜色类型

| 颜色类型 | 说明 |
|:------------- |:------------|
| `None`        | 所有节点都具有相同的颜色。 |
| `Categorical` | 根据结果集中的列的值或类别为节点分配颜色。 在上面的示例中，颜色基于结果集的列 _类型_ 。 支持的调色板包括 `Default` 、 `Pastel` 和 `Cool tone` 。  |
| `Field Based` | 在此类型中，列提供要用于节点的特定 RGB 值。 提供最大的灵活性，但通常需要更多的工作才能实现。  |

## <a name="node-format-settings"></a>节点格式设置

图形作者可以指定向节点的不同部分进行哪些内容：顶部、左侧、中心、右侧和底部。 关系图可以使用任何呈现器工作簿支持 (文本、大数字、spark 行、图标等 ) 。

## <a name="field-based-node-coloring"></a>基于字段的节点颜色

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。
3. 选择 "查询类型" 作为 " **日志**"、"资源类型" (例如，Application Insights) 和 "目标资源"。
4. 使用查询编辑器输入用于分析的 KQL。

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
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. 将可视化效果设置为“图形”
6. 选择 " **图形设置**  " 按钮以打开 "设置" 窗格。
7. 在底部的“布局字段”中，设置：
    * 节点 Id：`Id`
    * 源 Id： `SourceId`
    * 目标 Id： `TargetId`
    * 边缘标签： `None`
    * 边缘大小： `Calls`
    * 节点大小： `Node`
    * 着色类型： `Field Based`
    * 节点颜色字段： `Color`
8. 在顶部的 " *节点格式设置* " 中，输入以下各项。
    * 在 *顶部内容*中，设置：
        * 使用列： `Name` 。
        * 列呈现器： `Text` 。
    * 在 " *中心内容*" 中，设置：
        * 使用列： `Calls`
        * 列呈现器： `Big Number`
        * 调色板： `None`
    * 在 "最 *底层内容*" 中，设置：
        * 使用列： `Kind`
        * 列呈现器： `Text` 。
9. 选择窗格底部的 " **保存并关闭" 按钮** 。

[![屏幕截图，显示了如何创建具有字段基节点着色的图形可视化效果。](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 图形也支持复合条渲染器。 若要了解详细信息，请访问 [复合栏文档](workbooks-composite-bar.md)。
* 详细了解可在工作簿中使用的 [数据源](workbooks-data-sources.md) 。
