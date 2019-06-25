---
title: 在 Azure Monitor 中查询日志概述 |Microsoft Docs
description: 回答常见问题与日志相关查询，并可帮助你开始使用它们。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: b395b7bccbf93b56e84d5e7b5a4ed7355eaca335
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296330"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询的概述
日志查询可帮助您能够充分利用的值中的数据收集[Azure Monitor 日志](../platform/data-platform-logs.md)。 功能强大的查询语言，可加入多个表中的数据，聚合大量数据，并执行复杂的操作与最少的代码。 可以回答几乎任何问题并且只要已收集支持的数据，并了解如何构造右边的查询执行分析。

例如 Azure Monitor 中的某些功能[insights](../insights/insights-overview.md)并[解决方案](../insights/solutions-inventory.md)处理日志数据，而无需公开你的基础查询。 若要充分利用 Azure Monitor 的其他功能，应了解如何构造查询，以及如何使用它们来以交互方式分析 Azure Monitor 日志中的数据。

使用本文章为学习关于 Azure Monitor 中的日志查询的起始点。 它回答常见问题并提供指向提供更多详细信息和课程其他文档。

## <a name="how-can-i-learn-how-to-write-queries"></a>如何了解如何编写查询？
如果你想要直接跳到内容，可以开始以下教程：

- [开始使用 Azure Monitor 中的 Log Analytics](get-started-portal.md)。
- [开始使用 Azure Monitor 中的日志查询](get-started-queries.md)。

关闭基础知识后，引导完成使用自己的数据或从我们的演示环境中的数据的多个课程： 

- [使用 Azure Monitor 日志查询中的字符串](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>切勿将日志查询使用何种语言？
Azure Monitor 日志基于[Azure 数据资源管理器](/azure/data-explorer)，并使用相同的 Kusto 查询语言 (KQL) 编写的日志查询。 这是丰富的语言设计宗旨在于易于读取和作者，并且您应该可以开始使用尽可能少用指导。

请参阅[Azure 数据资源管理器 KQL 文档](/azure/kusto/query)有关完整 KQL 和上可用的不同函数的参考文档。<br>
请参阅[开始使用 Azure Monitor 中的日志查询](get-started-queries.md)使用 Azure Monitor 日志中的数据的语言的快速演练。
请参阅[Azure Monitor 日志查询语言差异](data-explorer-difference.md)KQL Azure 监视器所使用的版本中的细微差别的。

## <a name="what-data-is-available-to-log-queries"></a>哪些数据是可用于记录的查询？
在 Azure Monitor 日志中收集的所有数据都都可用于检索和分析日志查询中。 不同的数据源会将其数据写到不同的表，但可以在单个查询跨多个源分析数据中包含多个表。 在生成查询时，首先确定哪些表含要查找的数据，因此应具有至少基本了解 Azure Monitor 日志中的数据的方式。

请参阅[Azure Monitor 日志的源](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)，对于一组不同的数据源填充 Azure Monitor 日志。<br>
请参阅[的 Azure Monitor 日志结构](logs-structure.md)有关的数据的结构说明。

## <a name="what-does-a-log-query-look-like"></a>一个日志查询具有什么样的外观
查询可能与从该表中检索所有记录的单个表名称一样简单：

```Kusto
Syslog
```

或者，它无法筛选出的特定记录、 汇总，并可视化图表中的结果：

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

对于更复杂的分析，可能会使用联接在一起分析结果的多个表中检索数据。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
即使您不熟悉 KQL，您应能至少找出正在使用这些查询的基本逻辑。 他们开始的表的名称，然后添加用于筛选和处理这些数据的多个命令。 查询也可以使用任意数量的命令，并且你可以编写更复杂的查询作为您熟悉可用的不同 KQL 命令。

请参阅[开始使用 Azure Monitor 中的日志查询](get-started-queries.md)有关引入了语言和公共函数，日志查询的教程。<br>


## <a name="what-is-log-analytics"></a>什么是 Log Analytics？
Log Analytics 是用于编写日志查询和以交互方式分析其结果在 Azure 门户中的主要工具。 即使在 Azure Monitor 中其他位置使用一个日志查询，通常会编写和测试查询首先使用 Log Analytics。

可以从 Azure 门户中的多个位置启动 Log Analytics。 如何启动取决于可用到 Log Analytics 数据的作用域。 请参阅[查询范围](scope.md)的更多详细信息。

- 选择**日志**从**Azure Monitor**菜单或**Log Analytics 工作区**菜单。
- 选择**Analytics**从**概述**Application Insights 应用程序的页。
- 选择**日志**从一种 Azure 资源的菜单。

![Log Analytics](media/log-query-overview/log-analytics.png)

请参阅[开始使用 Azure Monitor 中的 Log Analytics](get-started-portal.md)介绍了几个它的功能的 Log Analytics 的教程演练。

## <a name="where-else-are-log-queries-used"></a>在其他地方使用日志查询？
除了以交互方式使用日志查询和 Log Analytics 中的其结果，其中将使用查询的 Azure Monitor 中的区域包括：

- **警报规则。** [警报规则](../platform/alerts-overview.md)主动识别工作区中数据的问题。  每个警报规则均基于定期自动运行的日志搜索。  对结果进行检查，确定是否应创建警报。
- **仪表板。** 可以将任何查询的结果固定到 [Azure 仪表板](../learn/tutorial-logs-dashboards.md)，这使得你可以将日志和指标数据一起可视化，并且还可以将其与其他 Azure 用户共享。
- **视图。**  可以使用[视图设计器](../platform/view-designer.md)创建要包含在用户仪表板中的数据的可视化效果。  日志查询提供每个视图中[磁贴](../platform/view-designer-tiles.md)和[可视化部件](../platform/view-designer-parts.md)使用的数据。  
- **导出。**  将日志数据从 Azure Monitor 导入到 Excel 或 [Power BI](../platform/powerbi.md) 中时，请创建日志查询来定义要导出的数据。
- **PowerShell。** 可从命令行运行 PowerShell 脚本或运行使用 [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) 的 Azure 自动化 runbook，从 Azure Monitor 中检索日志数据。  此 cmdlet 需要一个查询来确定要检索的数据。
- **Azure Monitor 日志 API。**  [Azure Monitor 日志 API](../platform/alerts-overview.md) 允许任何 REST API 客户端从工作区中检索日志数据。  API 请求包括针对 Azure Monitor 运行的查询，用于确定要检索的数据。


## <a name="next-steps"></a>后续步骤
- 逐步[教程在 Azure 门户中使用 Log Analytics](get-started-portal.md)。
- 逐步[查询编写教程](get-started-queries.md)。
