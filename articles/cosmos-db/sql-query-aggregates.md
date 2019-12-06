---
title: Azure Cosmos DB 中的聚合函数
description: 了解 SQL 聚合函数语法，Azure Cosmos DB 支持的聚合函数的类型。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 1ce3b18dd31944a1a4d4e6fad8fb49e63996dace
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871833"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的聚合函数

聚合函数对 SELECT 子句中的一组值执行计算，并返回单个值。 例如，下面的查询返回 `Families` 容器中项的计数：

## <a name="examples"></a>示例

```sql
    SELECT COUNT(1)
    FROM Families f
```

其结果是：

```json
    [{
        "$1": 2
    }]
```

还可以通过使用 VALUE 关键字，仅返回聚合的标量值。 例如，以下查询将值的计数作为单个值返回：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

其结果是：

```json
    [ 2 ]
```

还可以将聚合与筛选器组合在一起。 例如，下面的查询将返回地址状态为 `WA`的项的计数。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

其结果是：

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>聚合函数的类型

SQL API 支持以下聚合函数。 对数字值进行 SUM 和 AVG 运算，对数字、字符串、布尔值和 null 值使用计数、最小值和最大值。

| 函数 | 描述 |
|-------|-------------|
| COUNT | 在表达式中返回项的数目。 |
| SUM   | 在表达式中返回所有值的总和。 |
| 最小值   | 在表达式中返回最小值。 |
| MAX   | 在表达式中返回最大值。 |
| 平均值   | 在表达式中返回多个值的平均值。 |

您还可以对数组迭代的结果进行聚合。

> [!NOTE]
> 在 Azure 门户的数据资源管理器中，聚合查询可能只在一个查询页面上聚合部分结果。 SDK 跨所有页面生成单个累计值。 若要使用代码执行聚合查询，需要 .NET SDK 1.12.0、.NET Core SDK 1.1.0 或 Java SDK 1.9.5 或更高版本。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [系统函数](sql-query-system-functions.md)
- [用户定义函数](sql-query-udfs.md)