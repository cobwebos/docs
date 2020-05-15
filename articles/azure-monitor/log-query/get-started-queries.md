---
title: Azure Monitor 中的日志查询入门 | Microsoft Docs
description: 本文提供了有关在 Azure Monitor 中编写日志查询的入门教程。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77670173"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Azure Monitor 中的日志查询入门

> [!NOTE]
> 如果要从至少一台虚拟机收集数据，则可以在自己的环境中完成此练习。 如果没有，请使用[演示环境](https://portal.loganalytics.io/demo)，其中包含大量示例数据。

在本教程中，你将学习在 Azure Monitor 中编写日志查询。 具体内容包括：

- 了解查询结构
- 将查询结果排序
- 筛选查询结果
- 指定时间范围
- 选择结果中要包含的字段
- 定义和使用自定义字段
- 聚合和分组结果

有关在 Azure 门户中使用 Log Analytics 的教程，请参阅 [Azure Monitor Log Analytics 入门](get-started-portal.md)。<br>
有关 Azure Monitor 中日志查询的更多详细信息，请参阅 [Azure Monitor 中的日志查询概述](log-query-overview.md)。

以下随附本教程的视频版本：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>编写新查询
查询可以从表名或 *search* 命令开始。 首先应从表名开始，因为它为查询定义了明确的范围，并可以改善查询性能和结果的相关性。

> [!NOTE]
> Azure Monitor 使用的 Kusto 查询语言区分大小写。 语言关键字通常以小写编写。 在查询中使用表或列名时，请确保使用正确的大小写，如架构窗格中所示。

### <a name="table-based-queries"></a>基于表的查询
Azure Monitor 在表中组织日志数据，每个表由多个列组成。 所有表和列都显示在 Analytics 门户中的 Log Analytics 中的架构窗格内。 找到所需的表，然后看看其中的一些数据：

```Kusto
SecurityEvent
| take 10
```

上面所示的查询从 *SecurityEvent* 表返回 10 个结果（不遵循特定的顺序）。 这是大致浏览表并了解其结构和内容的常用方法。 让我们探讨表的生成方式：

* 查询从表名 *SecurityEvent* 开始 - 此部分定义查询的范围。
* 竖线 (|) 字符分隔命令，第一个命令的输出是后一个命令的输入。 可以添加任意数目的管道元素。
* 管道下面是 **take** 命令，它从表中返回特定数目的任意记录。

实际上，即使不添加 `| take 10`，我们也可以运行查询 - 该查询仍然有效，只不过它最多会返回 10,000 个结果。

### <a name="search-queries"></a>搜索查询
搜索查询的结构化程度不高，通常更适合用于查找在任何列中包含特定值的记录：

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

此查询在 *SecurityEvent* 表中搜索包含短语“Cryptographic”的记录。 返回并显示了其中的 10 条记录。 如果省略 `in (SecurityEvent)` 部分并直接运行 `search "Cryptographic"`，则搜索将遍历所有表，因此花费的时间更长且更低效。 

> [!WARNING]
> 搜索查询通常比基于表的查询慢，因为它们必须处理更多的数据。 

## <a name="sort-and-top"></a>sort 和 top
虽然 **take** 可用于获取一些记录，但选择和显示的结果不遵循特定的顺序。 若要获取排序的视图，可按首选列**排序**：

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

不过，这可能会返回过多的结果，此外可能需要一段时间。 上述查询按 TimeGenerated 列将整个 SecurityEvent 表排序。  然后，Analytics 门户将结果限制为仅显示 10,000 条记录。 当然，这种方法不是最佳的。

仅获取最新 10 条记录的最佳方式是使用 **top**，它会在服务器端将整个表排序，然后返回前几条记录：

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

降序是默认的排序顺序，因此我们通常省略 **desc** 参数。输出如下所示：

![前 10 条记录](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where：按条件筛选
顾名思义，筛选器可按特定的条件筛选数据。 这是将查询结果限制为相关信息的最常用方法。

若要将筛选器添加到查询，请使用 **where** 运算符，后接一个或多个条件。 例如，以下查询只返回 _Level_ 等于 _8_ 的 *SecurityEvent* 记录：

```Kusto
SecurityEvent
| where Level == 8
```

编写筛选器条件时，可使用以下表达式：

| 表达式 | 说明 | 示例 |
|:---|:---|:---|
| == | 检查相等性<br>（区分大小写） | `Level == 8` |
| =~ | 检查相等性<br>（不区分大小写） | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | 检查不相等性<br>（两个表达式相同） | `Level != 4` |
| *and*、*or* | 需在条件之间使用| `Level == 16 or CommandLine != ""` |

若要按多个条件进行筛选，可以使用 **and**：

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

或者使用竖线逐个分隔多个 **where** 元素：

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> 值可以有不同的类型，因此可能需要将其强制转换，以针对正确的类型执行比较。 例如，SecurityEvent *Level* 列的类型是字符串，因此必须将其强制转换为 *int* 或 *long* 等数字类型，然后才能对其使用数字运算符：`SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>指定时间范围

### <a name="time-picker"></a>时间选取器
时间选取器位于“运行”按钮的旁边，指示我们只查询过去 24 小时的记录。 这是应用到所有查询的默认时间范围。 如果只要获取过去一个小时的记录，请选择“过去一小时”并再次运行查询。 

![时间选取器](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>查询中的时间筛选器
还可以通过将时间筛选器添加到查询来定义自己的时间范围。 最好是紧靠在表名的后面添加时间筛选器： 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

在上面的时间筛选器中，`ago(30m)` 表示“30 分钟之前”，因此，此查询仅返回过去 30 分钟的记录。 其他时间单位包括天 (2d)、分钟 (25m) 和秒 (10s)。


## <a name="project-and-extend-select-and-compute-columns"></a>投影和扩展：选择和计算列
使用**投影**可以选择要包含在结果中的特定列：

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

前面的示例生成以下输出：

![查询投影结果](media/get-started-queries/project.png)

还可以使用**投影**来重命名列，并定义新列。 以下示例使用项目执行以下操作：

* 仅选择 *Computer* 和 *TimeGenerated* 原始列。
* 将 *Activity* 列重命名为 *EventDetails*。
* 创建名为 *EventCode* 的新列。 **substring()** 函数用于仅获取 Activity 字段中的前四个字符。


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extend** 保留结果集中的所有原始列，并定义其他列。 下面的查询使用 extend 来添加 EventCode 列   。 请注意，此列可能不会显示在表结果的末尾，在这种情况下，需要展开记录的详细信息才能查看它。

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Summarize：聚合行组
使用 **summarize** 可以根据一个或多个列标识记录组，并向其应用聚合。 summarize  最常见的用途是计数  ，可以返回每个组中的结果数。

以下查询检查过去一小时的所有 *Perf* 记录，按 *ObjectName* 将其分组，然后统计每个组中的记录数： 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

有时，按多个维度定义组会很有利。 这些值的每个唯一组合定义了一个单独的组：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

另一个常见用途是对每个组执行数学或统计计算。 例如，以下查询计算每台计算机的平均 *CounterValue*：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

遗憾的是，此查询的结果没有意义，因为我们混合了不同的性能计数器。 若要使此结果更有意义，应单独针对 *CounterName* 和 *Computer* 的每个组合计算平均值：

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>按时间列汇总
此外，分组结果可以基于时间列或其他连续值。 不过，只是汇总 `by TimeGenerated` 会针对时间范围内的每一毫秒创建组，因为这些值是唯一的。 

若要创建基于连续值的组，最好是使用 **bin** 将范围划分为可管理的单位。 以下查询分析 *Perf* 记录，这些记录度量特定计算机上的可用内存 (*Available MBytes*)。 它计算过去 7 天中每 1 小时时段的平均值：

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

为了使输出更清晰，请选择在时间图表中显示不同时段的可用内存：

![查询不同时段的内存](media/get-started-queries/chart.png)



## <a name="next-steps"></a>后续步骤

- 有关在日志查询中使用字符串数据的详细信息，请参阅[在 Azure Monitor 日志查询中使用字符串](string-operations.md)。
- 有关在日志查询中聚合数据的详细信息，请参阅 [Azure Monitor 日志查询中的高级聚合](advanced-aggregations.md)。
- 有关如何联接多个表中的数据的信息，请参阅 [Azure Monitor 日志查询中的联接](joins.md)。
- 在 [KQL 语言参考](/azure/kusto/query/)中获取有关完整 Kusto 查询语言的文档。
