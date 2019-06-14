---
title: 查看和分析 Azure Log Analytics 中的数据 | Microsoft Docs
description: 帮助用户从 Log Analytics 日志搜索过渡到 Azure Monitor 日志查询体验。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519897"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>从 Log Analytics 日志搜索过渡到 Azure Monitor 日志
Log Analytics 中的日志搜索功能最近已由一个可以分析 Azure Monitor 日志的新体验取代。 目前，仍可以在 Azure 门户中通过“Log Analytics 工作区”页上的“日志(经典)”菜单项访问日志搜索页，但该搜索页将在 2019 年 2 月 15 日删除。   本文介绍两种体验之间的差异，以帮助你从日志搜索过渡到新体验。 

## <a name="filter-results-of-a-query"></a>筛选查询结果
在日志搜索中，提供搜索结果时会显示筛选器列表。 选择一个筛选器并单击“应用”可以使用选定的筛选器运行查询。 

![日志搜索中的筛选器](media/log-search-transition/filter-log-search.png)

在 Azure Monitor 日志中，选择“筛选器(预览)”可以显示筛选器。  单击筛选器图标可显示更多筛选器。 选择一个筛选器并单击“应用并运行”可以使用选定的筛选器运行查询。 

![日志中的筛选器](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>提取自定义字段 
在日志搜索中，可以从“列表”视图提取[自定义字段](../platform/custom-fields.md)，该视图中的字段菜单包括“从表中提取字段”操作。 

![在日志搜索中提取字段](media/log-search-transition/extract-fields-log-search.png)

在 Azure Monitor 日志中，可以从表视图提取自定义字段。 若要展开记录，可以单击该记录左侧的箭头，然后单击省略号访问“提取字段”操作。 

![在日志中提取字段](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>函数和计算机组
若要在日志搜索中保存搜索，请选择“保存的搜索”，然后单击“添加”以提供名称、类别和查询文本。   通过添加函数别名创建[计算机组](../platform/computer-groups.md)。

![保存日志搜索](media/log-search-transition/save-search-log-search.png)

若要在 Azure Monitor 日志中保存当前查询，请选择“保存”。  将“另存为”更改为“函数”，并提供一个**函数别名**以创建[函数](functions.md)。   若要使用[计算机组](../platform/computer-groups.md)的函数别名，请选择“将此查询另存为计算机组”  。

![保存日志查询](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>已保存的查询
在日志搜索中，可以通过操作栏中的“保存的搜索”项使用已保存的查询  。 在 Azure Monitor 日志中，可以从[“查询资源管理器”](../log-query/get-started-portal.md#save-queries)访问已保存的查询。

![查询资源管理器](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>向下钻取汇总行
在日志搜索中，可单击汇总查询中的行，启动列出该行中的详细记录的另一个查询。

![日志搜索向下钻取](media/log-search-transition/drilldown-search.png)

在 Azure Monitor 日志中，必须修改查询才能返回这些记录。 展开结果中的其中一行，然后单击值旁边的 **+** 将其添加到查询。 然后注释掉 **summarize** 命令并再次运行查询。

![Azure Monitor 日志向下钻取](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>执行操作
在日志搜索中，可以通过选择“执行操作”，从搜索结果[启动 Runbook](take-action.md)  。

![执行操作](media/log-search-transition/take-action-log-search.png)

在 Azure Monitor 日志中，可以[从日志查询创建警报](../platform/alerts-log.md)。 配置包含一个或多个操作的操作组，响应警报时，将运行这些操作。

![操作组](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>后续步骤

- 详细了解新的 [Azure Monitor 日志体验](get-started-portal.md)。