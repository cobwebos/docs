---
title: Azure Monitor 中的日志查询概述 |Microsoft Docs
description: 回答与日志查询相关的常见问题，并使你开始使用它们。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 08af0ff3121d6721c9f5c1457655c345f50f91e0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900271"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询概述
日志查询可帮助你充分利用[Azure Monitor 日志](../platform/data-platform-logs.md)中收集的数据的值。 利用功能强大的查询语言，可以从多个表联接数据、聚合大型数据集，并使用最少的代码执行复杂的操作。 几乎任何问题都可以得到解答，只要收集到支持数据就可以执行分析，并了解如何构造正确的查询。

Azure Monitor 中的某些功能（如[insights](../insights/insights-overview.md)和[解决方案](../insights/solutions-inventory.md)）处理日志数据，而不会向您提供基础查询。 若要充分利用 Azure Monitor 的其他功能，您应该了解如何构造查询，以及如何使用它们以交互方式分析 Azure Monitor 日志中的数据。

使用本文作为开始了解 Azure Monitor 中的日志查询的起点。 它回答了常见问题并提供指向其他文档的链接，以提供更多详细信息和课程。

## <a name="how-can-i-learn-how-to-write-queries"></a>如何才能了解如何编写查询？
若要直接转到其中的内容，可从以下教程开始：

- [Azure Monitor 中的 Log Analytics 入门](get-started-portal.md)。
- [Azure Monitor 中的日志查询入门](get-started-queries.md)。

了解基础知识后，请使用你自己的数据或从我们的演示环境开始的数据，逐步完成多个课程： 

- [在 Azure Monitor 日志查询中处理字符串](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>日志查询使用哪种语言？
Azure Monitor 日志基于[Azure 数据资源管理器](/azure/data-explorer)，使用相同的 Kusto 查询语言（KQL）编写日志查询。 这是一种丰富的语言，旨在易于阅读和创作，你应该能够使用最少的指导开始使用它。

请参阅[Azure 数据资源管理器 KQL 文档](/azure/kusto/query)，以获取有关 KQL 的完整文档和提供的不同功能的参考。<br>
有关使用 Azure Monitor 日志中的数据的快速演练，请参阅[Azure Monitor 中的日志查询入门](get-started-queries.md)。
有关 Azure Monitor 使用的 KQL 版本中的细微差异，请参阅[Azure Monitor 日志查询语言差异](data-explorer-difference.md)。

## <a name="what-data-is-available-to-log-queries"></a>日志查询可使用哪些数据？
Azure Monitor 日志中收集的所有数据都可用于检索和分析日志查询。 不同的数据源会将其数据写入不同的表中，但你可以在单个查询中包含多个表，以分析多个源中的数据。 生成查询时，首先要确定哪些表包含您要查找的数据，因此您应该至少有一个基本的了解 Azure Monitor 日志中的数据是如何构建的。

有关填充 Azure Monitor 日志的不同数据源的列表，请参阅[Azure Monitor 日志的源](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)。<br>
请参阅[Azure Monitor 日志的结构](logs-structure.md)，以了解如何对数据进行结构化。

## <a name="what-does-a-log-query-look-like"></a>日志查询看起来是什么样子？
查询可能与单个表名称一样简单，以便从该表中检索所有记录：

```Kusto
Syslog
```

也可以筛选特定记录、对其进行汇总并在图表中显示结果：

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

若要进行更复杂的分析，可以使用联接从多个表中检索数据，以便将结果一起分析。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
即使您不熟悉 KQL，您也应该至少能找出这些查询所使用的基本逻辑。 它们从表的名称开始，然后添加多个命令以筛选和处理该数据。 查询可以使用任意数量的命令，您可以在熟悉可用的不同 KQL 命令时编写更复杂的查询。

有关介绍语言和常见函数的日志查询教程，请参阅[Azure Monitor 中的日志查询入门](get-started-queries.md)。<br>


## <a name="what-is-log-analytics"></a>什么是 Log Analytics？
Log Analytics 是编写日志查询并以交互方式分析其结果的 Azure 门户中的主要工具。 即使 Azure Monitor 的其他位置使用了日志查询，通常也可以首先使用 Log Analytics 来编写和测试查询。

你可以从 Azure 门户中的几个位置开始 Log Analytics。 可用于 Log Analytics 的数据的范围取决于您的启动方式。 有关更多详细信息，请参阅[查询范围](scope.md)。

- 从 " **Azure Monitor** " 菜单或**Log Analytics 工作区**"菜单中选择"**日志**"。
- 在 Application Insights 应用程序的 "**概述**" 页中选择 "**分析**"。
- 从 Azure 资源菜单中选择 "**日志**"。

![Log Analytics](media/log-query-overview/log-analytics.png)

请参阅[在 Azure Monitor 中开始使用 Log Analytics](get-started-portal.md)的教程，其中介绍了 Log Analytics 的一些功能。

## <a name="where-else-are-log-queries-used"></a>日志查询使用的情况是什么？
除了以交互方式使用日志查询及其在 Log Analytics 中的结果，Azure Monitor 中将使用查询的区域包括以下内容：

- **警报规则。** [警报规则](../platform/alerts-overview.md)主动识别工作区中数据的问题。  每个警报规则均基于定期自动运行的日志搜索。  对结果进行检查，确定是否应创建警报。
- **仪表板。** 可以将任何查询的结果固定到 [Azure 仪表板](../learn/tutorial-logs-dashboards.md)，这使得你可以将日志和指标数据一起可视化，并且还可以将其与其他 Azure 用户共享。
- **视图。**  可以使用[视图设计器](../platform/view-designer.md)创建要包含在用户仪表板中的数据的可视化效果。  日志查询提供每个视图中[磁贴](../platform/view-designer-tiles.md)和[可视化部件](../platform/view-designer-parts.md)使用的数据。  
- **导出。**  将日志数据从 Azure Monitor 导入到 Excel 或 [Power BI](../platform/powerbi.md) 中时，请创建日志查询来定义要导出的数据。
- **PowerShell。** 你可以从命令行或 Azure 自动化 runbook 运行 PowerShell 脚本，该 runbook 使用[AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)从 Azure Monitor 检索日志数据。  此 cmdlet 需要一个查询来确定要检索的数据。
- **Azure Monitor 日志 API。**  [Azure Monitor 日志 API](https://dev.loganalytics.io) 允许任何 REST API 客户端从工作区中检索日志数据。  API 请求包括针对 Azure Monitor 运行的查询，用于确定要检索的数据。


## <a name="next-steps"></a>后续步骤
- 演练[有关在 Azure 门户中使用 Log Analytics 的教程](get-started-portal.md)。
- 演练[有关编写查询的教程](get-started-queries.md)。
