---
title: Azure Monitor 日志记录中的标准属性 | Microsoft Docs
description: 介绍 Azure Monitor 日志中多种数据类型共有的属性。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/18/2019
ms.author: bwren
ms.openlocfilehash: b9a4a0a18e120a2843e23d44b03c0fe53b0d84fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68370673"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Azure Monitor 日志中的标准属性
Azure Monitor 日志中的数据[作为一组记录存储在 Log Analytics 工作区或 Application Insights 应用程序](../log-query/logs-structure.md)中，每条记录都具有特定的数据类型，该数据类型包含一组惟一的属性。 许多数据类型都具有在多种类型中通用的标准属性。 本文介绍这些属性，并提供如何在查询中使用它们的示例。

> [!NOTE]
> 某些标准 propertis 不会显示在 Log Analytics 中的架构视图或 intellisense 中, 除非在输出中显式指定属性, 否则它们不会显示在查询结果中。

## <a name="timegenerated-and-timestamp"></a>TimeGenerated 和 timestamp
**TimeGenerated** (Log Analytics 工作区) 和**时间戳**(Application Insights 应用程序) 属性包含数据源创建记录的日期和时间。 有关更多详细信息, 请参阅[Azure Monitor 中的日志数据引入时间](data-ingestion-time.md)。

**TimeGenerated**和**timestamp**提供用于按时间筛选或汇总的通用属性。 为 Azure 门户中的视图或仪表板选择时间范围时，它使用 TimeGenerated 或 timestamp 来筛选结果。 

### <a name="examples"></a>示例

以下查询返回过去一周内每天创建的错误事件数。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

以下查询返回过去一周内每天创建的异常数。

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
TimeReceived 属性包含 Azure 云中 Azure Monitor 摄取点接收记录的日期和时间。  **\_** 这对于识别数据源和云之间的延迟问题非常有用。 例如, 可能是网络问题导致延迟, 同时从代理发送数据。 有关更多详细信息, 请参阅[Azure Monitor 中的日志数据引入时间](data-ingestion-time.md)。

以下查询提供代理中事件记录的平均延迟 (按小时)。 这包括从代理到云中的时间, 以及记录可用于日志查询的总时间。

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type 和 itemType
**Type**（Log Analytics 工作区）和 **itemType**（Application Insights 应用程序）属性保存从中检索记录的表的名称，也可以将其视为记录类型。 此属性在将多个表的记录进行组合的查询中非常有用，例如，使用 `search` 运算符区分不同类型的记录的那些查询。 在某些地方， **$table** 可以用来替代 **Type**。

### <a name="examples"></a>示例
以下查询返回过去一小时内按类型收集的记录计数。

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
ItemId 属性保存记录的唯一标识符。  **\_**


## <a name="_resourceid"></a>\_ResourceId
**\_ResourceId** 属性包含与记录关联的资源的唯一标识符。 这为你提供了一个标准属性，用于将查询范围限定为仅来自特定资源的记录，或者跨多个表联接相关数据。

对于 Azure 资源， **_ResourceId** 的值是 [Azure 资源 ID URL](../../azure-resource-manager/resource-group-template-functions-resource.md)。 该属性目前仅限于 Azure 资源，但它将扩展到 Azure 之外的资源，例如本地计算机。

> [!NOTE]
> 某些数据类型已具有包含 Azure 资源 ID 或至少包含其一部分（例如订阅 ID）的字段。 虽然为了实现向后兼容而保留了这些字段，但是建议使用 _ResourceId 来执行交叉关联，因为它将更为一致。

### <a name="examples"></a>示例
以下查询联接每台计算机的性能和事件数据。 它显示 ID 为 _101_ 且处理器利用率超过 50% 的所有事件。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

以下查询将 _AzureActivity_ 记录与 _SecurityEvent_ 记录进行联接。 它显示了登录到这些计算机的用户的所有活动操作。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

以下查询分析 **_ResourceId**，并聚合每个 Azure 订阅的计费数据量。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

请谨慎使用这些 `union withsource = tt *` 查询，因为跨数据类型执行扫描的开销很大。

## <a name="_isbillable"></a>\_IsBillable
**\_IsBillable** 属性指定是否对引入的数据进行计费。 **\_IsBillable** 等于 _false_ 的数据是免费收集的，不会向你的 Azure 帐户收费。

### <a name="examples"></a>示例
若要获取发送计费数据类型的计算机列表，请使用以下查询：

> [!NOTE]
> 请谨慎使用带 `union withsource = tt *` 的查询，因为跨数据类型执行扫描的开销很大。 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

这可以扩展为返回每小时发送计费数据类型的计算机数量：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
**\_BilledSize** 属性指定 **\_IsBillable** 为 true 时将向 Azure 帐户计费的数据字节大小。


### <a name="examples"></a>示例
若要查看每台计算机引入的可计费事件的大小，请使用 `_BilledSize` 属性（以字节为单位提供大小）：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

若要查看每个订阅引入的可计费事件的大小，请使用以下查询：

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

若要查看每个资源组引入的可计费事件的大小，请使用以下查询：

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


若要查看每台计算机引入的事件数，请使用以下查询：

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

若要查看每台计算机引入的可计费事件数，请使用以下查询： 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

若要查看特定计算机中可计费数据类型的计数，请使用以下查询：

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[存储 Azure Monitor 日志数据](../log-query/log-query-overview.md)。
- 获取有关[编写日志查询](../../azure-monitor/log-query/get-started-queries.md)的课程。
- 获取有关[在日志查询中联接表](../../azure-monitor/log-query/joins.md)的课程。
