---
title: Azure Monitor Log Analytics 记录中的标准属性 | Microsoft Docs
description: 介绍 Azure Monitor Log Analytics 中多种数据类型共有的属性。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955861"
---
# <a name="standard-properties-in-log-analytics-records"></a>Log Analytics 记录中的标准属性
[Log Analytics](../log-analytics/log-analytics-queries.md) 中的数据以一组记录的形式存储，每个记录都有一个特定的数据类型，该数据类型具有一组独特属性。 许多数据类型都具有在多种类型中通用的标准属性。 本文介绍这些属性，并提供如何在查询中使用它们的示例。

其中一些属性仍在实现过程中，因此你可能会在某些数据类型中看到它们，但在其他数据类型中却看不到。


## <a name="resourceid"></a>_ResourceId
**_ResourceId** 属性包含与记录关联的资源的唯一标识符。 这为你提供了一个标准属性，用于将查询范围限定为仅来自特定资源的记录，或者跨多个表联接相关数据。

对于 Azure 资源，**_ResourceId** 的值是 [Azure 资源 ID URL](../azure-resource-manager/resource-group-template-functions-resource.md)。 该属性目前仅限于 Azure 资源，但它将扩展到 Azure 之外的资源，例如本地计算机。 

### <a name="examples"></a>示例
以下示例显示了一个联接每台计算机的性能和事件数据的查询。 它显示 ID 为 _101_ 且处理器利用率超过 50% 的所有事件。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

以下示例显示了将 _AzureActivity_ 记录与 _SecurityEvent_ 记录联接的查询。 它显示了登录到这些计算机的用户的所有活动操作。

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

## <a name="next-steps"></a>后续步骤

- 详细阅读如何[存储 Log Analytics 数据](../log-analytics/log-analytics-queries.md)。
- 获取有关[在 Log Analytics 中编写查询](../log-analytics/query-language/get-started-queries.md)的课程。
- 获取有关[在 Log Analytics 查询中联接表](../log-analytics/query-language/joins.md)的课程。