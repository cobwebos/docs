---
title: Azure Monitor 日志查询中的函数 | Microsoft Docs
description: 本文介绍了如何在 Azure Monitor 中从一个日志查询中使用函数调用另一个查询。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 155f27b9f04db74955640aad8f582fdf05539368
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511932"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>在 Azure Monitor 日志查询中使用函数

若要将某个日志查询用于其他查询，可以将其保存为函数。 这使你能够通过分解复杂查询将其简化，并能够对多个查询重用通用代码。

## <a name="create-a-function"></a>创建函数

在 Azure 门户中单击“保存”，然后提供下表中的信息，使用 Log Analytics 创建函数。

| 设置 | 说明 |
|:---|:---|
| 名称           | 查询资源管理器中查询的显示名称。 |
| 另存为        | 函数 |
| 函数别名 | 在其他查询中使用该函数的短名称。 不可包含空格，必须唯一。 |
| Category       | 用于在查询资源管理器中整理已保存的查询和函数的类别。 |




## <a name="use-a-function"></a>使用函数
通过在另一个查询中添加其别名来使用函数。 可以像使用其他任何表一样使用它。

## <a name="function-parameters"></a>函数参数 
可以向函数添加参数，以便在调用某些变量时可以为其提供值。 当前使用参数创建函数的唯一方法是使用资源管理器模板。 有关示例，请参阅[Azure Monitor 中的日志查询资源管理器模板示例](../samples/resource-manager-log-queries.md#parameterized-function)。

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
参阅有关编写 Azure Monitor 日志查询的其他课：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [联接](joins.md)
- [图表](charts.md)
