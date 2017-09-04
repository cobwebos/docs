---
title: "OMS Log Analytics 中的日志搜索 | Microsoft Docs"
description: "需要日志搜索才能检索来自 Log Analytics 的任何数据。  本文介绍新的日志搜索在 Log Analytics 中的用法以及创建搜索之前需要了解的概念。"
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
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 0f27db7018e398f71a8d7bd0b86e643367b15875
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---
# <a name="understanding-log-searches-in-log-analytics"></a>了解 Log Analytics 中的日志搜索

> [!NOTE]
> 本文介绍 Azure Log Analytics 中使用新查询语言的日志搜索。  若要了解有关新语言的详细信息和工作区升级过程，请参阅[将 Azure Log Analytics 工作区升级到新日志搜索](log-analytics-log-search-upgrade.md)。  
>
> 如果尚未将工作区升级到新查询语言，应参考[在 Log Analytics 中使用日志搜索查找数据](log-analytics-log-searches.md)。

需要日志搜索才能检索来自 Log Analytics 的任何数据。  无论是要在门户中分析数据、将警报规则配置为在特定条件下发出通知，还是要使用 Log Analytics API 检索数据，都需要使用日志搜索指定所需数据。  本文介绍日志搜索在 Log Analytics 中的用法以及创建搜索之前应了解的概念。 请参阅[后续步骤](#next-steps)部分，了解有关创建和编辑日志搜索的详细信息，以及有关查询语言的参考信息。

## <a name="where-log-searches-are-used"></a>在何处使用日志搜索

在 Log Analytics 中使用日志搜索的方式包括以下几种：

- **门户。** 可使用[日志搜索门户](log-analytics-log-search-log-search-portal.md)或[高级分析门户](https://go.microsoft.com/fwlink/?linkid=856587)在存储库中执行交互式数据分析。  这样便可采用各种格式和可视化效果编辑查询并分析结果。  创建的大多数查询会在其中一个门户中启动，然后在用户已验证查询按预期工作后，查询将被复制。
- **警报规则。** [警报规则](log-analytics-alerts.md)主动识别工作区中数据的问题。  每个警报规则均基于定期自动运行的日志搜索。  对结果进行检查，确定是否应创建警报。
- **视图。**  可以使用[视图设计器](log-analytics-view-designer.md)创建要包含在用户仪表板中的数据的可视化效果。  日志搜索提供每个视图中[磁贴](log-analytics-view-designer-tiles.md)和[可视化部件](log-analytics-view-designer-parts.md)使用的数据。  可以从可视化部件向下钻取到日志搜索门户，对数据执行进一步的分析。
- **导出。**  将数据从 Log Analytics 工作区导出到 Excel 或 [Power BI](log-analytics-powerbi.md) 中时，创建日志搜索以定义要导出的数据。
- **PowerShell。** 可从命令行运行 PowerShell 脚本或运行使用 [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) 的 Azure 自动化 runbook，从 Log Analytics 中检索数据。  此 cmdlet 需要一个查询来确定要检索的数据。
- **Log Analytics API。**  [Log Analytics 日志搜索 API](log-analytics-log-search-api.md) 允许任何 REST API 客户端从工作区中检索数据。  API 请求包括针对 Log Analytics 运行的查询，用于确定要检索的数据。

![日志搜索](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics 数据的组织方式
生成查询时，首先确定哪些表含要查找的数据。 每个[数据源](log-analytics-data-sources.md)和[解决方案](../operations-management-suite/operations-management-suite-solutions.md)在 Log Analytics 工作区的专用表中存储其数据。  每个数据源和解决方案的文档包括其创建的数据类型名称及其每个属性的说明。     许多查询只需要单个表中的数据，但其他查询可能会使用多种选项以包含多个表中的数据。

![表](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>编写查询
Log Analytics 中日志搜索的核心是[扩展的查询语言](https://docs.loganalytics.io/)，使用户能够通过多种方式检索并分析存储库中的数据。  这一查询语言也用于 [Application Insights](../application-insights/app-insights-analytics.md)。  要创建 Log Analytics 中的日志搜索，务必应了解如何编写查询。  通常先使用基本查询，随着要求变得复杂，逐渐开始使用更高级的功能。

查询的基本结构是一个源表，后跟一系列以竖线字符 `|` 隔开的运算符。  可以将多个运算符链接起来以优化数据和执行高级函数。

例如，假设要查找过去一天中错误事件最多的前 10 台计算机。

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

或者，要查找在最后一天中没有检测信号的计算机。

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

来一个折线图如何？显示过去一周中每台计算机的处理器利用率

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

从这些快速示例可以看出，无论处理何种数据，查询的结构是类似的。  可以将其分解为不同的步骤，其中从一条命令生成的数据会通过管道发送到下一条命令。

有关 Azure Log Analytics 查询语言的完整文档，包括教程和语言参考，请参阅 [Azure Log Analytics 查询语言文档](https://docs.loganalytics.io/)。

## <a name="next-steps"></a>后续步骤

- 了解[用于创建并编辑日志搜索的门户](log-analytics-log-search-portals.md)。
- 查看使用新查询语言的[查询编写教程](https://go.microsoft.com/fwlink/?linkid=856078)。

