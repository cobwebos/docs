---
title: Azure Log Analytics 视图中的筛选器 | Microsoft Docs
description: Log Analytics 视图中的筛选器使得用户可以在不修改视图本身的情况下，以特定属性的值筛选数据。  本文介绍如何使用筛选器并添加一个筛选器到自定义视图。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="filters-in-log-analytics-views"></a>Log Analytics 视图中的筛选器
[Log Analytics 视图](log-analytics-view-designer.md) 中的筛选器使得用户可以在不修改视图本身的情况下，以特定属性的值在视图中筛选数据。  例如，可以允许视图的用户在视图中筛选仅来自特定计算机或特定计算器组的数据。  可以在单个视图上创建多个筛选器，以便用户按多个属性筛选数据。  本文介绍如何使用筛选器并添加一个筛选器到自定义视图。

## <a name="using-a-filter"></a>使用筛选器
单击“筛选器”打开视图的筛选器窗格。  在筛选器窗格中，为可在视图中使用的任何筛选器选择时间范围和值。  选择筛选器时，将显示可用值的列表。  可以选择一个或多个值，也可以键入值。 视图自动更新为基于指定的值进行筛选。 

如果未对筛选器选择任何值，则该筛选器不会应用于视图。  如果移除筛选器的所有值，则将不再应用该筛选器。


![筛选器示例](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>创建筛选器

[编辑视图](log-analytics-view-designer.md)时，从“筛选器”选项卡创建筛选器。  筛选器适用于视图全局并应用于视图的所有部分。  

![筛选器设置](media/log-analytics-view-designer/filters-settings.png)

下表描述了筛选器的设置。

| 设置 | 说明 |
|:---|:---|
| 字段名称 | 用于筛选的字段的名称。  字段名称必须与“查询值”中的汇总字段匹配。 |
| 查询值 | 运行查询以填充用户的筛选器下拉列表。  必须使用 [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 或 [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) 提供特定字段的唯一值，且必须与“字段名称”匹配。  可以使用 [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) 对显示给用户的值进行排序。 |
| 标记 | 在支持筛选器的查询中使用同时向用户显示的字段的名称。 |

### <a name="examples"></a>示例

下表包括一些常用筛选器示例。  

| 字段名称 | 查询值 | 标记 |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | 计算机 |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>修改视图查询

为使筛选器发挥作用，必须修改视图中的任何查询，以按选定制进行筛选。  如果未修改视图中的任何查询，那么用户选择的任何值都不会产生效果。

在查询中使用筛选器值的语法是： 

    where ${filter name}  

例如，如果视图有一个查询返回事件并使用名为 Computers 的筛选器，则可以使用以下查询。

    Event | where ${Computers} | summarize count() by EventLevelName

如果添加另一个名为 Severity 的筛选器，则可以利用以下查询同时使用两个筛选器。

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>后续步骤
* 深入了解可添加到自定义视图的[可视化效果部件](log-analytics-view-designer-parts.md)。