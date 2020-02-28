---
title: Azure Monitor 视图中的筛选器 |Microsoft Docs
description: 使用 Azure Monitor 视图中的筛选器，用户可以通过特定属性的值筛选视图中的数据，而无需修改视图本身。  本文介绍如何使用筛选器并添加一个筛选器到自定义视图。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658568"
---
# <a name="filters-in-azure-monitor-views"></a>Azure Monitor 视图中的筛选器
使用[Azure Monitor 视图](view-designer.md)中的**筛选器**，用户可以通过特定属性的值筛选视图中的数据，而无需修改视图本身。  例如，可以允许视图的用户在视图中筛选仅来自特定计算机或特定计算器组的数据。  可以在单个视图上创建多个筛选器，以便用户按多个属性筛选数据。  本文介绍如何使用筛选器并添加一个筛选器到自定义视图。

## <a name="using-a-filter"></a>使用筛选器
单击视图顶部的 "日期时间范围"，以打开可更改视图的日期时间范围的下拉箭头。

![筛选器示例](media/view-designer-filters/filters-example-time.png)

单击 **+** 以添加为视图定义的使用自定义筛选器的筛选器。 从下拉列表中为筛选器选择一个值或键入一个值。 通过单击 **+** 来继续添加筛选器。 


![筛选器示例](media/view-designer-filters/filters-example-custom.png)

如果移除筛选器的所有值，则将不再应用该筛选器。


## <a name="creating-a-filter"></a>创建筛选器

[编辑视图](view-designer.md)时，从“筛选器”选项卡创建筛选器。  筛选器适用于视图全局并应用于视图的所有部分。  

![筛选器设置](media/view-designer-filters/filters-settings.png)

下表描述了筛选器的设置。

| 设置 | 说明 |
|:---|:---|
| 字段名称 | 用于筛选的字段的名称。  此字段必须与**查询值**的 "汇总" 字段相匹配。 |
| 查询值 | 运行查询以填充用户的筛选器下拉列表。  此查询必须使用[汇总](/azure/kusto/query/summarizeoperator)或[distinct](/azure/kusto/query/distinctoperator)来提供特定字段的唯一值，并且必须与**字段名称**匹配。  可以使用 [sort](/azure/kusto/query/sortoperator) 对显示给用户的值进行排序。 |
| 标记 | 在支持筛选器的查询中使用同时向用户显示的字段的名称。 |

### <a name="examples"></a>示例

下表包括一些常用筛选器示例。  

| 字段名称 | 查询值 | 标记 |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | 计算机 |
| EventLevelName | Event &#124; distinct EventLevelName | severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>修改视图查询

为使筛选器发挥作用，必须修改视图中的任何查询，以按选定制进行筛选。  如果不修改视图中的任何查询，则用户选择的任何值都不起作用。

在查询中使用筛选器值的语法是： 

    where ${filter name}  

例如，如果您的视图有一个返回事件并使用名为 "_计算机_" 的筛选器的查询，则可以使用以下查询。

    Event | where ${Computers} | summarize count() by EventLevelName

如果添加另一个名为 Severity 的筛选器，则可以利用以下查询同时使用两个筛选器。

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>后续步骤
* 深入了解可添加到自定义视图的[可视化效果部件](view-designer-parts.md)。
