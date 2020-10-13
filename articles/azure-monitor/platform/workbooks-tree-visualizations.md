---
title: Azure Monitor 工作簿树可视化效果
description: 了解 Azure Monitor 工作簿树的所有可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663757"
---
# <a name="tree-visualizations"></a>树可视化

工作簿通过树网格支持分层视图。 在树中可以展开某些行进入下一级别，以获得向下钻取体验。

以下示例显示了已可视化为树网格的容器运行状况指标（工作集大小）。 此处的顶级节点是 Azure Kubernetes Service (AKS) 节点，下一级别为 pod，最终级别为容器。 请注意，仍可以像在网格中那样设置列的格式（热度地图、图标、链接）。 在这种情况下，基础数据源是具有 AKS 日志的 Log Analytics 工作区。

[![磁贴摘要视图的屏幕截图](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>添加树网格
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择 " **添加** "，然后选择 "添加 *查询* " 向工作簿添加日志查询控件。
3. 选择 "查询类型" 作为 " **日志**"、"资源类型" (例如，Application Insights) 和 "目标资源"。
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
6. 选择 " **列设置** " 按钮以打开 "设置" 窗格
7. 在底部的“树/分组依据设置”部分，设置：
    * 树类型：`Parent/Child`
    * ID 字段：`Id`
    * 父 ID 字段：`ParentId`
    * 要显示展开器的列：`Name`
    * 选中 *"展开树的顶级"* 复选框。
8. 在顶部的“列”部分，设置：
    * _ID_ - 列呈现器：`Hidden`
    * _父 ID_ - 列呈现器：`Hidden`
    * _请求_ - 列呈现器：`Bar`，颜色：`Blue`，最小值：`0`
9. 选择窗格底部的 " **保存并关闭** " 按钮。

[![带有以上查询和设置的磁贴摘要视图的屏幕截图。](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>树设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Id Field` | 网格中每一行的唯一 ID。 |
| `Parent Id Field` | 当前行的父项的 ID。 |
| `Show the expander on` | 要显示树展开器的列。 树形网格通常会隐藏其 ID 和父 ID 字段，因为它们不是非常可读的。 此时，扩展器将出现在具有更具可读性的值（如实体的名称）中。 |
| `Expand the top level of the tree` | 如果选中该展开器，树网格会在顶级位置展开。 如果希望默认显示详细信息，则此方法很有用。 |

## <a name="grouping-in-a-grid"></a>在网格中分组

分组允许您生成与上面类似的层次结构视图，并大大简化查询。 你确实会丢失树的内部节点上的聚合，但在某些情况下，这是可接受的。 当基础结果集无法转换为适当的自由格式（例如，警报、运行状况和指标数据）时，请使用 *Group By* 生成树视图。

## <a name="adding-a-tree-using-grouping"></a>使用分组添加树

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 选择 " **添加** "，然后选择 "添加 *查询* " 向工作簿添加日志查询控件。
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. 将可视化效果设置为 " *网格*"。
2. 选择 " **列设置" 按钮** 以打开 "设置" 窗格。
3. 在底部的“树/分组依据设置”部分，设置：
    * 树类型：`Group By`
    * 分组依据： `App`
    * 然后按： `None`
    * 选中 *"展开树的顶级"* 复选框。
4. 在顶部的“列”部分，设置：
    * *应用程序* 列呈现器： `Hidden`
    * *请求* - 列呈现器：`Bar`，颜色：`Blue`，最小值：`0`
5. 选择窗格底部的 " **保存并关闭** " 按钮。

[![显示在工作簿中创建树可视化对象的屏幕截图](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解如何 [在工作簿中创建关系图](workbooks-graph-visualizations.md)。
* 了解如何 [在工作簿中创建磁贴](workbooks-tile-visualizations.md)。
