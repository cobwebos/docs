---
title: 在 Azure Monitor 中分析 Log Analytics 数据 | Microsoft Docs
description: 需要日志搜索才能检索来自 Log Analytics 的任何数据。  本文介绍新的日志搜索在 Log Analytics 中的用法以及创建搜索之前需要了解的概念。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 6e587f79f0f77446ea5a61cbb989f3f1c3655e0a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741753"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>在 Azure Monitor 中分析 Log Analytics 数据

Azure Monitor 收集的日志数据存储在 Log Analytics 工作区中，该工作区基于 [Azure 数据资源管理器](/data-explorer)。 它从各种源收集遥测数据，并使用[数据资源管理器的查询语言](/kusto)来检索和分析数据。

> [!NOTE]
> Log Analytics 以前在 Azure 中被视为其自己的服务。 现在它被视为 Azure Monitor 的一部分，其主要功能是存储日志数据并使用其查询语言分析日志数据。 以前被视为 Log Analytics 的一部分的功能（例如，用于数据收集的 Windows 和 Linux 代理；用于可视化现有数据的视图；以及主动向你通知问题的警报）没有更改，但现在被视为 Azure Monitor 的一部分。



## <a name="log-queries"></a>日志查询

你需要执行日志查询来检索来自 Log Analytics 的任何数据。  无论是要[在门户中分析数据](log-analytics-log-search-portals.md)、[配置警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)以在特定条件下发出通知，还是要使用 [Log Analytics API](https://dev.loganalytics.io/) 检索数据，都需要使用查询来指定所需数据。  本文介绍了日志查询在 Log Analytics 中的用法以及创建查询之前应了解的概念。



## <a name="where-log-queries-are-used"></a>在何处使用日志查询

在 Log Analytics 中使用查询的方式包括以下几种：

- **门户。** 可以在 [Azure 门户](log-analytics-log-search-portals.md)中对日志数据执行交互式分析。  这样便可采用各种格式和可视化效果编辑查询并分析结果。  
- **警报规则。** [警报规则](log-analytics-alerts.md)主动识别工作区中数据的问题。  每个警报规则均基于定期自动运行的日志搜索。  对结果进行检查，确定是否应创建警报。
- **仪表板。** 可以将任何查询的结果固定到 [Azure 仪表板]()，这使得你可以将日志和指标数据一起可视化，并且还可以将其与其他 Azure 用户共享。 
- **视图。**  可以使用[视图设计器](log-analytics-view-designer.md)创建要包含在用户仪表板中的数据的可视化效果。  日志查询提供每个视图中[磁贴](log-analytics-view-designer-tiles.md)和[可视化部件](log-analytics-view-designer-parts.md)使用的数据。  
- **导出。**  将数据从 Log Analytics 工作区导入到 Excel 或 [Power BI](log-analytics-powerbi.md) 中时，你将创建日志查询来定义要导出的数据。
- **PowerShell。** 可从命令行运行 PowerShell 脚本或运行使用 [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) 的 Azure 自动化 runbook，从 Log Analytics 中检索数据。  此 cmdlet 需要一个查询来确定要检索的数据。
- **Log Analytics API。**  [Log Analytics 日志搜索 API](log-analytics-log-search-api.md) 允许任何 REST API 客户端从工作区中检索日志数据。  API 请求包括针对 Log Analytics 运行的查询，用于确定要检索的数据。

![日志搜索](media/log-analytics-queries/queries-overview.png)

## <a name="write-a-query"></a>编写查询
Log Analytics 使用[数据资源管理器查询语言的一个版本](query-language/get-started-queries.md)以各种方式检索和分析日志数据。  通常先使用基本查询，随着要求变得复杂，逐渐开始使用更高级的功能。

查询的基本结构是一个源表，后跟一系列以竖线字符 `|` 隔开的运算符。  可以将多个运算符链接起来以优化数据和执行高级函数。

例如，假设要查找过去一天中错误事件最多的前 10 台计算机。

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

或者，要查找在最后一天中没有检测信号的计算机。

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

来一个折线图如何？显示过去一周中每台计算机的处理器利用率

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

从这些快速示例可以看出，无论处理何种数据，查询的结构是类似的。  可以将其分解为不同的步骤，其中从一条命令生成的数据会通过管道发送到下一条命令。

还可在订阅中跨 Log Analytics 工作区查询数据。

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics 数据的组织方式
生成查询时，首先确定哪些表含要查找的数据。 各种类型的数据被划分到每个 [Log Analytics 工作区](log-analytics-quick-create-workspace.md)中的专用表中。  各种数据源的文档包括其创建的数据类型的名称及其每个属性的说明。  许多查询只需要单个表中的数据，但其他查询可能会使用多种选项以包含多个表中的数据。

虽然 [Application Insights](../application-insights/app-insights-overview.md) 将诸如请求、异常、跟踪和使用情况之类的应用程序数据存储在 Log Analytics 中，但此数据存储在与其他日志数据不同的分区中。 你可以使用相同的查询语言来访问此数据，但必须使用 [Application Insights 控制台](../application-insights/app-insights-analytics.md)或 [Application Insights REST API](https://dev.applicationinsights.io/) 来访问它。 可以使用[跨资源查询](log-analytics-cross-workspace-search.md)来将 Application Insights 数据与 Log Analytics 中的其他数据配合使用。


![表](media/log-analytics-queries/queries-tables.png)







## <a name="next-steps"></a>后续步骤

- 了解[用于创建并编辑日志搜索的门户](log-analytics-log-search-portals.md)。
- 查看使用新查询语言的[查询编写教程](log-analytics-tutorial-viewdata.md)。
