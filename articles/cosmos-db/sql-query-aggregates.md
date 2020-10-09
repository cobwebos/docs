---
title: Azure Cosmos DB 中的聚合函数
description: 了解 SQL 聚合函数语法以及 Azure Cosmos DB 支持的聚合函数类型。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79464455"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的聚合函数

聚合函数对 `SELECT` 子句中的一组值执行计算，并返回单个值。 例如，以下查询返回 `Families` 容器中的项计数。

## <a name="examples"></a>示例

```sql
    SELECT COUNT(1)
    FROM Families f
```

结果有：

```json
    [{
        "$1": 2
    }]
```

也可以使用 VALUE 关键字来仅返回聚合的标量值。 例如，以下查询将值的计数作为单个值返回：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

结果有：

```json
    [ 2 ]
```

还可以将聚合与筛选器结合使用。 例如，以下查询返回包含 `WA` 州地址的项计数。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

结果有：

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>聚合函数的类型

SQL API 支持以下聚合函数。 `SUM` 和 `AVG` 对数值执行运算，`COUNT`、`MIN` 和 `MAX` 可以处理数字、字符串、布尔值和 null。

| 函数 | 说明 |
|-------|-------------|
| COUNT | 在表达式中返回项的数目。 |
| SUM   | 在表达式中返回所有值的总和。 |
| 最小值   | 在表达式中返回最小值。 |
| MAX   | 在表达式中返回最大值。 |
| 平均值   | 在表达式中返回多个值的平均值。 |

还可以基于数组迭代的结果进行聚合。

> [!NOTE]
> 在 Azure 门户的数据资源管理器中，聚合查询可以仅基于一个查询页面聚合部分结果。 SDK 跨所有页面生成单个累计值。 若要使用代码执行聚合查询，需要 .NET SDK 1.12.0、.NET Core SDK 1.1.0，或者 Java SDK 1.9.5 或更高版本。

## <a name="remarks"></a>备注

这些聚合系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 如果希望对某个属性执行 `COUNT`、`SUM`、`MIN`、`MAX` 或 `AVG` 运算，则应[在索引策略中包含相关路径](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [系统函数](sql-query-system-functions.md)
- [用户定义的函数](sql-query-udfs.md)