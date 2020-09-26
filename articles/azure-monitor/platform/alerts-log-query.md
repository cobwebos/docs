---
title: 优化日志警报查询 |Microsoft Docs
description: 编写有效警报查询的建议
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: 7f03858b2427b2a2069ebe2c9d06425e7a741e2b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294353"
---
# <a name="optimizing-log-alert-queries"></a>优化日志警报查询
本文介绍如何编写和转换 [日志警报](alerts-unified-log.md) 查询以实现最佳性能。 优化的查询可减少延迟并加载频繁运行的警报。

## <a name="how-to-start-writing-an-alert-log-query"></a>如何开始编写警报日志查询

警报查询从查询用于指示问题的 [Log Analytics 中的日志数据](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) 开始。 您可以使用 " [警报查询示例" 主题](../log-query/saved-queries.md) 来了解可以发现的内容。 您还可以 [开始编写您自己的查询](../log-query/get-started-portal.md)。 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>指示问题的查询，而不是警报

生成警报流以将指示问题的结果转换为警报。 例如，在以下情况下，查询如下所示：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果用户的目的是发出警报，则当此事件类型发生时，警报逻辑将追加 `count` 到查询。 将运行的查询将是：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

无需向查询添加警报逻辑，甚至可能会导致问题。 在上面的示例中，如果您 `count` 在查询中包括，它将始终生成值1，因为警报服务将执行的操作 `count` `count` 。

### <a name="avoid-limit-and-take-operators"></a>避免 `limit` 和 `take` 运算符

`limit` `take` 在查询中使用和可能会增加延迟并加载警报，因为结果不会随时间而保持一致。 仅在需要时才使用它。

## <a name="log-query-constraints"></a>日志查询约束
[Azure Monitor 中的日志查询](../log-query/log-query-overview.md) 以表、 [`search`](/azure/kusto/query/searchoperator) 或 [`union`](/azure/kusto/query/unionoperator) 运算符开头。

日志警报规则的查询应始终以表开头来定义明确的作用域，从而提高查询性能和结果的相关性。 警报规则中的查询经常运行，因此使用 `search` 和 `union` 可能会导致额外的开销，从而增加警报的延迟，因为它需要跨多个表进行扫描。 这些运算符还降低了警报服务优化查询的能力。

我们不支持创建或修改使用 or 运算符的日志警报规则 `search` `union` ，而需要进行跨资源查询。

例如，以下警报查询的作用域限定为 " _SecurityEvent_ " 表，并搜索特定的事件 ID。 它是查询必须处理的唯一表。

``` Kusto
SecurityEvent
| where EventID == 4624
```

使用 [跨资源查询](../log-query/cross-workspace-query.md) 的日志警报规则不受此更改的影响，因为跨资源查询使用类型 `union` （它将查询范围限制为特定资源）。 下面的示例是有效的日志警报查询：

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> 新的[SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)支持[跨资源查询](../log-query/cross-workspace-query.md)。 如果仍使用 [旧版 Log Analytics 警报 API](api-alerts.md) 来创建日志警报，可以在 [此处](alerts-log-api-switch.md)了解切换。

## <a name="examples"></a>示例
下面的示例包括使用和的日志查询， `search` `union` 并提供可用于修改这些查询以在警报规则中使用的步骤。

### <a name="example-1"></a>示例 1
您希望使用以下使用检索性能信息的查询创建日志警报规则 `search` ： 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

若要修改此查询，请首先使用以下查询来标识属性所属的表：

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

此查询结果将显示 CounterName 属性来自 Perf 表 。

您可以使用此结果创建将用于警报规则的以下查询：

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>示例 2
您希望使用以下使用检索性能信息的查询创建日志警报规则 `search` ： 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

若要修改此查询，请首先使用以下查询来标识属性所属的表：

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

此查询结果将显示 ObjectName 和 CounterName 属性来自 Perf 表  。

您可以使用此结果创建将用于警报规则的以下查询：

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>示例 3

要使用以下查询创建日志警报规则，该查询使用 `search` 和 `union` 来检索性能信息： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

若要修改此查询，请首先使用以下查询来标识查询第一部分中属性所属的表： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

此查询结果将显示所有这些属性来自 Perf 表。

现在，将 `union` 与 `withsource` 命令配合使用，确定哪个源表提供了每行。

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

此查询结果将显示这些属性也来自 Perf 表。

您可以使用这些结果来创建用于预警规则的以下查询： 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>示例 4
您希望使用联接两个查询结果的以下查询创建日志警报规则 `search` ：

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

若要修改此查询，请首先使用以下查询来标识包含左侧联接中属性的表： 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

结果指示左侧联接中的属性属于 SecurityEvent 表。 

现在使用以下查询来标识包含右侧联接中属性的表： 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
结果指示联接右侧的属性属于 _检测信号_ 表。

您可以使用这些结果来创建用于预警规则的以下查询： 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>后续步骤
- 了解 Azure Monitor 中的[日志警报](alerts-log.md)。
- 了解[日志查询](../log-query/log-query-overview.md)。
