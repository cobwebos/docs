---
title: Azure Monitor 日志查询中的函数 | Microsoft Docs
description: 本文介绍了如何在 Azure Monitor 中从一个日志查询中使用函数调用另一个查询。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 5fb9e48a6d6a0b95b61478a7877e9b46dd8963e9
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649399"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>在 Azure Monitor 日志查询中使用函数

若要将某个日志查询用于其他查询，可以将其保存为函数。 这使你能够通过分解复杂查询将其简化，并能够对多个查询重用通用代码。

## <a name="create-a-function"></a>创建函数

通过 Azure 门户中的 Log Analytics，单击“保存”然后提供下表中的信息来创建一个函数。

| 设置 | 说明 |
|:---|:---|
| 名称           | 查询资源管理器中查询的显示名称。 |
| 另存为        | 函数 |
| 函数别名 | 在其他查询中使用该函数的短名称。 不可包含空格，必须唯一。 |
| 类别       | 用于在查询资源管理器中整理已保存的查询和函数的类别。 |




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
参阅有关编写 Azure Monitor 日志查询的其他课：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [联接](joins.md)
- [图表](charts.md)
