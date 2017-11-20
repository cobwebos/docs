---
title: "Azure Log Analytics 有哪些变化？ | Microsoft Docs"
description: "本文提供有关将 Log Analytics 升级到新的查询语言的常见问题解答。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: bwren
ms.openlocfilehash: 783223a37c2a13c9affbf382209ca2aa4f1ba4c7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Azure Log Analytics 有哪些变化？
将 Log Analytics 工作区[升级到新的查询语言](log-analytics-log-search-new.md)之后，除了查询语言本身以外，还应该注意其他几项改进和更改。  本文简要介绍旧版与升级后的工作区之间的变化，并提供每项更改的详细内容的链接。 

请参阅 [Log Analytics 新日志搜索常见问题解答和已知问题](log-analytics-log-search-faq.md)，获取所有已知升级问题的说明，以及常见问题的解答。  

## <a name="query-language"></a>查询语言
Log Analytics 升级的主要更改体现在，新的查询语言相比前一种语言有了重大改进。  

[过渡到 Azure Log Analytics 的新查询语言](log-analytics-log-search-transition.md)中对旧版语言和新语言支持的常见操作做了直接比较。  [Azure Log Analytics 查询语言](https://docs.loganalytics.io)中提供了有关新语言的完整文档。


## <a name="computer-groups"></a>计算机组
创建计算机组的方法未发生更改，不过，现在必须为每个组提供唯一的别名。  基于日志搜索的计算机组必须使用新语言的语法。

在日志搜索中使用计算机组需遵循新语法。  不能使用 $ComputerGroups 函数，每个计算机组现在将实现为具有唯一别名的函数。  可以像使用其他任何函数一样在日志搜索中使用别名。  

[Log Analytics 日志搜索中的计算机组](log-analytics-computer-groups.md)中提供了详细信息。


## <a name="log-search-portals"></a>日志搜索门户
现在，除了使用日志搜索门户创建和运行日志搜索以外，还可以使用高级分析门户，它提供明显改进的编辑功能。

[Azure Log Analytics 中用于创建和编辑日志查询的门户](log-analytics-log-search-portals.md)中提供了这两个门户的简要说明。  可以在[分析门户入门](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal)中逐步学习有关新门户的教程。

## <a name="alerts"></a>警报
在升级的工作区中，警报的工作方式相同，但是，必须使用新语言编写它们运行的查询。  在升级之前创建的所有现有警报规则会自动转换为新语言。  可以在[了解 Log Analytics 中的警报](log-analytics-alerts.md)中获取更多详细信息。

通过警报发送的 Runbook 和 Webhook 的有效负载格式已发生更改。  可以在[将操作添加到 Log Analytics 中的警报规则](log-analytics-alerts-actions.md)中获取新的有效负载格式的详细信息。

## <a name="dashboards"></a>仪表板
[仪表板](log-analytics-dashboards.md)即将弃用。  可以继续使用工作区升级前添加到仪表板的任何磁贴，但无法编辑这些磁贴或创建新磁贴。  使用视图设计器可以创建功能集比仪表板更丰富的自定义视图。

[使用视图设计器在 Log Analytics 中创建自定义视图](log-analytics-view-designer.md)中提供了有关视图设计器的详细信息。

## <a name="power-bi"></a>Power BI
将 Log Analytics 数据导出到 Power BI 的过程在升级后的工作区中已更改，升级之前创建的所有现有计划将被禁用。  

[将 Log Analytics 数据导出到 Power BI](log-analytics-powerbi.md) 中提供了详细信息。

## <a name="powershell"></a>PowerShell
用于从 PowerShell 运行日志搜索的 Get-AzureRmOperationalInsightsSearchResults 不适用于升级后的工作区。  可以在升级后的工作区中使用 Invoke-LogAnalyticsQuery 实现此功能。

[Azure Log Analytics REST API - PowerShell Cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) 中提供了详细信息。

## <a name="log-search-api"></a>日志搜索 API
日志搜索 REST API 已发生更改，使用旧版的所有解决方案需要更新才能使用新 API 版本。   

[Azure Log Analytics REST API](https://dev.loganalytics.io/) 中提供了有关新版 API 的详细信息。

## <a name="next-steps"></a>后续步骤

- 请参阅 [Log Analytics 新日志搜索常见问题解答和已知问题](log-analytics-log-search-faq.md)，获取所有已知升级问题的说明，以及常见问题的解答。