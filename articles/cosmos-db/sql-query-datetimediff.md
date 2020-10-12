---
title: Azure Cosmos DB 查询语言中的 DateTimeDiff
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimeDiff。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b90b45072128252e8abc22d3422c84c813808119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87446389"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)

返回指定的 StartDate 和 EndDate 之间所跨的指定 DateTimePart 边界的计数（以带符号整数值的形式） 。
  
## <a name="syntax"></a>语法
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>参数
  
*DateTimePart*  
   DateTimeAdd 向其添加整数的日期部分。 此表列出了所有有效的 DateTimePart 参数：

| DateTimePart | 缩写        |
| ------------ | -------------------- |
| Year         | "year", "yyyy", "yy" |
| Month        | "month", "mm", "m"   |
| 日期          | "day", "dd", "d"     |
| Hour         | "hour", "hh"         |
| Minute       | "minute", "mi", "n"  |
| 秒       | "second", "ss", "s"  |
| Millisecond  | "millisecond", "ms"  |
| Microsecond  | "microsecond", "mcs" |
| Nanosecond   | "nanosecond", "ns"   |

*StartDate*  
    `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式的 UTC 日期和时间 ISO 8601 字符串值，其中：
  
  |格式|描述|
  |-|-|
  |YYYY|四位数的年份|
  |MM|两位数的月份（01 = 1 月，依此类推。）|
  |DD|两位数的月份日期（01 到 31）|
  |T|时间元素开头的符号|
  |hh|两位数的小时（00 到 23）|
  |MM|两位数的分钟（00 到 59）|
  |ss|两位数的秒（00 到 59）|
  |.fffffff|七位数的小数秒|
  |Z|UTC（协调世界时）指示符||
  
  有关 ISO 8601 格式的详细信息，请参阅 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

EndDate  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式的 UTC 日期和时间 ISO 8601 字符串值

## <a name="return-types"></a>返回类型

返回带符号整数值。

## <a name="remarks"></a>备注

DateTimeDiff 返回 `undefined`，原因如下：

- 指定的 DateTimePart 值无效
- StartDate 或 EndDate不是有效的 ISO 8601 DateTime

DateTimeDiff 始终返回带符号整数值，它度量跨越的 DateTimePart 边界数，而非时间间隔。

## <a name="examples"></a>示例
  
以下示例计算 `2020-01-01T01:02:03.1234527Z` 和 `2020-01-03T01:02:03.1234567Z` 之间跨越的天的边界的数目。

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

以下示例计算 `2028-01-01T01:02:03.1234527Z` 和 `2020-01-03T01:02:03.1234567Z` 之间跨越的年份边界的数目。

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

以下示例计算 `2020-01-01T01:00:00.1234527Z` 和 `2020-01-01T01:59:59.1234567Z` 之间跨越的小时边界的数目。 即使这些 DateTime 值之间相隔超过 0.99 小时，`DateTimeDiff` 仍将返回 0，因为没有超过小时边界。

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
