---
title: Azure Monitor 中的日志查询概述 |Microsoft Docs
description: 解答有关日志查询的常见问题，并帮助你开始使用日志查询。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 6cd2a28ddbe970385ff44deec2158c257937982e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82591646"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询概述
日志查询可帮助你充分利用 [Azure Monitor 日志](../platform/data-platform-logs.md)中收集的数据的价值。 使用功能强大的查询语言，只需编写极少量的代码即可联接多个表中的数据、聚合大型数据集，以及执行复杂的操作。 只要收集了支持数据，并且你了解如何构造适当的查询，就几乎能够解答任何问题和执行分析。

Azure Monitor 中的某些功能（例如[见解](../insights/insights-overview.md)和[解决方案](../insights/solutions-inventory.md)）可以处理日志数据，且不会在基础查询中透露你的身份。 若要充分利用 Azure Monitor 的其他功能，应该了解如何构造查询，以及如何使用它们以交互方式分析 Azure Monitor 日志中的数据。

可以使用本文作为起点来了解 Azure Monitor 中的日志查询。 其中解答了常见问题，并提供了包含更多详细信息的其他文档和课程的链接。

## <a name="how-can-i-learn-how-to-write-queries"></a>如何学习查询编写方法？
如果你想要直奔主题，可从以下教程着手：

- [Azure Monitor 中的 Log Analytics 入门](get-started-portal.md)
- [Azure Monitor 中的日志查询入门](get-started-queries.md)。

掌握基础知识后，可从以下文章着手，使用自己的数据或者演示环境中的数据学完多篇课程： 

- [在 Azure Monitor 日志查询中使用字符串](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>日志查询使用哪种语言？
Azure Monitor 日志基于 [Azure 数据资源管理器](/azure/data-explorer)，日志查询是使用相同的 Kusto 查询语言 (KQL) 编写的。 这是一种旨在方便阅读和创作的丰富语言，只需接受少量的指导，你就可以开始使用它。

有关 KQL 的完整文档以及有关各种可用功能的参考，请参阅 [Azure 数据资源管理器 KQL 文档](/azure/kusto/query)。<br>
有关使用 Azure Monitor 日志中的数据的快速语言演练，请参阅 [Azure Monitor 中的日志查询入门](get-started-queries.md)。
有关 Azure Monitor 使用的 KQL 版本的次要差别，请参阅 [Azure Monitor 日志查询语言的差别](data-explorer-difference.md)。

## <a name="what-data-is-available-to-log-queries"></a>日志查询可以使用哪些数据？
在日志查询中，可以检索和分析 Azure Monitor 日志中收集的所有数据。 不同的数据源会将其数据写入不同的表，但你可以在单个查询中包含多个表，以分析多个源中的数据。 生成查询时，首先需要确定哪些表包含你要查找的数据，因此，你至少应该对 Azure Monitor 日志中的数据构建方式有一个基本的了解。

有关填充 Azure Monitor 日志的不同数据源列表，请参阅 [Azure Monitor 日志的源](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)。<br>
有关数据构建方式的说明，请参阅 [Azure Monitor 日志的结构](logs-structure.md)。

## <a name="what-does-a-log-query-look-like"></a>日志查询的大致形式是怎样的？
查询可以很简单，只包含一个用于从相应的表中检索所有记录的表名称：

```Kusto
Syslog
```

或者，它可以针对特定的记录进行筛选、汇总记录，然后在图表中将结果可视化：

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

对于更复杂的分析，可以使用联接从多个表中检索数据，以统一分析结果。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
即使你不熟悉 KQL，也至少应该能够猜想到这些查询使用的基本逻辑。 日志查询以表名称开头，然后添加多个命令用于筛选和处理这些数据。 一个查询可以使用任意数量的命令，在熟悉可用的不同 KQL 命令后，你可以编写更复杂的查询。

有关介绍语言和常用功能的日志查询教程，请参阅 [Azure Monitor 中的日志查询入门](get-started-queries.md)。<br>


## <a name="what-is-log-analytics"></a>什么是 Log Analytics？
Log Analytics 是 Azure 门户中用于编写日志查询以及以交互方式分析其结果的主要工具。 即使在 Azure Monitor 中的其他位置使用某个日志查询，通常你也会先使用 Log Analytics 编写和测试该查询。

可以从 Azure 门户中的多个位置启动 Log Analytics。 Log Analytics 可用的数据范围由其启动方式决定。 有关更多详细信息，请参阅[查询范围](scope.md)。

- 从“Azure Monitor”菜单或“Log Analytics 工作区”菜单中选择“日志”。   
- 选择 Application Insights 应用程序的 "**概述**" 页中的 "**日志**"。
- 从 Azure 资源的菜单中选择“日志”。 

![Log Analytics](media/log-query-overview/log-analytics.png)

有关介绍 Log Analytics 的多种功能的教程演练，请参阅 [Azure Monitor 中的 Log Analytics 入门](get-started-portal.md)。

## <a name="where-else-are-log-queries-used"></a>还可在其他哪些位置使用日志查询？
除了在 Log Analytics 中以交互方式处理日志查询及其结果以外，还可以在 Azure Monitor 中的以下区域使用查询：

- **警报规则。** [警报规则](../platform/alerts-overview.md)主动识别工作区中数据的问题。  每个警报规则均基于定期自动运行的日志搜索。  对结果进行检查，确定是否应创建警报。
- **仪表板。** 可以将任何查询的结果固定到 [Azure 仪表板](../learn/tutorial-logs-dashboards.md)，这使得你可以将日志和指标数据一起可视化，并且还可以将其与其他 Azure 用户共享。
- **视图。**  可以使用[视图设计器](../platform/view-designer.md)创建要包含在用户仪表板中的数据的可视化效果。  日志查询提供每个视图中[磁贴](../platform/view-designer-tiles.md)和[可视化部件](../platform/view-designer-parts.md)使用的数据。  
- **导出。**  将日志数据从 Azure Monitor 导入到 Excel 或 [Power BI](../platform/powerbi.md) 中时，请创建日志查询来定义要导出的数据。
- **PowerShell。** 可从命令行运行 PowerShell 脚本或运行使用 [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) 的 Azure 自动化 runbook，从 Azure Monitor 中检索日志数据。  此 cmdlet 需要一个查询来确定要检索的数据。
- **Azure Monitor 日志 API。**  [Azure Monitor 日志 API](https://dev.loganalytics.io) 允许任何 REST API 客户端从工作区中检索日志数据。  API 请求包括针对 Azure Monitor 运行的查询，用于确定要检索的数据。


## <a name="next-steps"></a>后续步骤
- 演练[有关在 Azure 门户中使用 Log Analytics 的教程](get-started-portal.md)。
- 演练[有关编写查询的教程](get-started-queries.md)。
