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
ms.date: 01/14/2019
ms.author: bwren
ms.openlocfilehash: 2309e7762ad36f59e0833e675e7012ee3c459e3e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997033"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Azure Monitor 日志记录中的标准属性
Azure Monitor 中的日志数据[以一组记录的形式存储](../log-query/log-query-overview.md)，每个记录都有一个特定的数据类型，该数据类型具有一组独特属性。 许多数据类型都具有在多种类型中通用的标准属性。 本文介绍这些属性，并提供如何在查询中使用它们的示例。

其中一些属性仍在实现过程中，因此你可能会在某些数据类型中看到它们，但在其他数据类型中却看不到。

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** 属性包含创建记录的日期和时间。 它提供了一个用于按时间进行筛选或汇总的常用属性。 为 Azure 门户中的视图或仪表板选择时间范围时，它使用 TimeGenerated 来筛选结果。

### <a name="examples"></a>示例

以下查询返回过去一周内每天创建的错误事件数。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Type
**Type** 属性包含从中检索记录的表的名称，也可以将其视为记录类型。 此属性在将多个表的记录进行组合的查询中非常有用，例如，使用 `search` 运算符区分不同类型的记录的那些查询。 在某些地方，**$table** 可以用来替代 **Type**。

### <a name="examples"></a>示例
以下查询返回过去一小时内按类型收集的记录计数。

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
**\_ResourceId** 属性包含与记录关联的资源的唯一标识符。 这为你提供了一个标准属性，用于将查询范围限定为仅来自特定资源的记录，或者跨多个表联接相关数据。

对于 Azure 资源，**_ResourceId** 的值是 [Azure 资源 ID URL](../../azure-resource-manager/resource-group-template-functions-resource.md)。 该属性目前仅限于 Azure 资源，但它将扩展到 Azure 之外的资源，例如本地计算机。

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

## <a name="isbillable"></a>\_IsBillable
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

## <a name="billedsize"></a>\_BilledSize
**\_BilledSize** 属性指定 **\_IsBillable** 为 true 时将向 Azure 帐户计费的数据字节大小。

### <a name="examples"></a>示例
若要查看每台计算机引入的可计费事件的大小，请使用 `_BilledSize` 属性（以字节为单位提供大小）：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
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

若要查看向特定计算机发送数据的可计费数据类型计数，请使用以下查询：

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
