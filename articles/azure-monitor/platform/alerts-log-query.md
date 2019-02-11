---
title: Azure Monitor 中的日志警报 | Microsoft Docs
description: 提供有关在 Azure Monitor 更新中编写日志警报的有效查询以及转换现有查询的进程的建议。
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 36be305e60806ba2cdea260fc46bc329c43284cb
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429780"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Azure Monitor 中的日志警报
[基于 Azure Monitor 日志的预警规则](alerts-unified-log.md)定期运行，因此应确保这些规则旨在将开销和延迟降至最低。 本文提供了有关编写日志警报的高效查询以及转换现有查询的过程的建议。 

## <a name="types-of-log-queries"></a>日志查询的类型
[Log Analytics 中的查询](../log-query/log-query-overview.md)以 table 或 [search](/azure/kusto/query/searchoperator) 或 [union](/azure/kusto/query/unionoperator) 运算符开头。

例如，以下查询的范围限定为 SecurityEvent 表，并搜索特定的事件 ID。 这是查询必须处理的唯一的表。

``` Kusto
SecurityEvent | where EventID == 4624 
```

以 `search` 或 `union` 开头的查询允许在一个表甚至多个表中跨多个列搜索。 以下示例显示搜索术语 Memory 的多个方法：

```Kusto
search "Memory"

search * | where == "Memory"

search ObjectName: "Memory"

search ObjectName == "Memory"

union * | where ObjectName == "Memory"
```
 

尽管 `search` 和 `union` 在数据探索期间很有用，但是在整个数据模型上搜索术语时，其效率比使用表搜索更低，因为它们必须扫描多个表。 由于预警规则中的查询是定期运行的，因此可能会导致过多的开销，从而增加警报的延迟。 由于这种开销，Azure 中对日志预警规则的查询应始终以表开始，以定义明确的范围，从而提高查询性能和结果的相关性。

## <a name="unsupported-queries"></a>不受支持的查询
从 2019 年 1 月 11 日开始，Azure 门户中将不支持创建或修改使用 `search` 或 `union` 运算符的日志预警规则。 在预警规则中使用这些运算符将返回错误消息。 现有预警规则和使用 Log Analytics API 创建和编辑的预警规则不受此更改的影响。 仍应考虑更改使用这些查询类型的任何预警规则，以提高其效率。  

使用[跨资源查询](../log-query/cross-workspace-query.md)的日志预警规则不受此更改的影响，因为跨资源查询使用 `union`，这会将查询范围限制为特定资源。 这不等效于无法使用的 `union *`。  以下示例在日志预警规则中有效：

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```
 

## <a name="examples"></a>示例
以下示例包括使用 `search` 和 `union` 的日志查询，并提供可用于修改这些查询以与预警规则配合使用的步骤。

### <a name="example-1"></a>示例 1
想要使用以下查询创建日志预警规则，该查询使用 `search` 检索性能信息： 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

若要修改此查询，请首先使用以下查询来标识属性所属的表：

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

此查询结果将显示 CounterName 属性来自 Perf 表。 

可使用此结果创建以下查询，可将该查询用于预警规则：

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>示例 2
想要使用以下查询创建日志预警规则，该查询使用 `search` 检索性能信息： 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

若要修改此查询，请首先使用以下查询来标识属性所属的表：

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

此查询结果将显示 ObjectName 和 CounterName 属性来自 Perf 表。 

可使用此结果创建以下查询，可将该查询用于预警规则：

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>示例 3

想要使用以下查询创建日志预警规则，该查询使用 `search` 和 `union` 检索性能信息： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

若要修改此查询，请首先使用以下查询来标识查询第一部分中属性所属的表： 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

此查询结果将显示所有这些属性来自 Perf 表。 

现在，将 `union` 与 `withsource` 命令配合使用，确定哪个源表提供了每行。

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

此查询结果将显示这些属性也来自 Perf 表。 

可使用这些结果创建以下查询，可将该查询用于预警规则： 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>示例 4
想要使用以下查询创建日志预警规则，该查询联接两条 `search` 查询的结果：

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
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

 
结果指示右侧联接中的属性属于 Heartbeat 表。 

可使用这些结果创建以下查询，可将该查询用于预警规则： 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>后续步骤
- 了解 Azure Monitor 中的[日志警报](alerts-log.md)。
- 了解[日志查询](../log-query/log-query-overview.md)。

