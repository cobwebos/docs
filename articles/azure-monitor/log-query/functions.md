---
title: 在 Azure Log Analytics 中使用函数 | Microsoft Docs
description: 本文介绍如何在 Log Analytics 中使用函数从另一个查询调用查询。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884355"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>在 Azure Monitor Log Analytics 中使用函数

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-portal.md)和[查询入门](get-started-queries.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


若要通过另一个查询使用 Log Analytics 查询，可以将其另存为函数。 这使你能够通过分解复杂查询将其简化，并能够对多个查询重用通用代码。

## <a name="create-a-function"></a>创建函数

单击“保存”然后提供下表中的信息，以在 Azure 门户中创建函数。

| 设置 | Description |
|:---|:---|
| 名称           | 查询资源管理器中查询的显示名称。 |
| 另存为        | 函数 |
| 函数别名 | 在其他查询中使用该函数的短名称。 不可包含空格，必须唯一。 |
| 类别       | 用于在查询资源管理器中整理已保存的查询和函数的类别。 |

> [!NOTE]
> Log Analytics 中的函数不能包含其他函数。

> [!NOTE]
> 可在 Log Analytics 查询中保存功能，但此功能目前不适用于 Application Insights 查询。



## <a name="use-a-function"></a>使用函数
通过在另一个查询中添加其别名来使用函数。 可以像使用其他任何表一样使用它。

## <a name="example"></a>示例
以下示例查询将返回最近一天报告的所有缺失的安全更新。 使用别名 security_updates_last_day 将此查询另存为函数。 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

创建另一个查询并引用 security_updates_last_day 函数，以搜索 SQL 相关的必需安全更新。

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>后续步骤
有关 Log Analytics 查询语言的用法，请参阅其他课程：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [联接](joins.md)
- [图表](charts.md)
