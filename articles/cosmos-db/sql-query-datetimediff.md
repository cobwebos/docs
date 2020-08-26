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
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446389"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff （Azure Cosmos DB）

返回指定的开始日期和*结束**日期*之间所跨的指定 DateTimePart 边界的计数（以有符号整数值形式）。
  
## <a name="syntax"></a>语法
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>自变量
  
*DateTimePart*  
   DateTimeAdd 添加整数的日期部分。 此表列出了所有有效的 DateTimePart 参数：

| DateTimePart | 缩写        |
| ------------ | -------------------- |
| 年份         | "year"、"yyyy" 和 "yy" |
| 月份        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小时         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| 秒       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"，"ms"  |
| 微秒  | "微秒"、"mcs" |
| 纳秒   | "毫微秒"，"ns"   |

*StartDate*  
    UTC 日期和时间 ISO 8601 字符串值，格式为 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
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

*EndDate*  
   格式的 UTC 日期和时间 ISO 8601 字符串值`YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>返回类型

返回一个有符号的整数值。

## <a name="remarks"></a>备注

DateTimeDiff 将返回 `undefined` ，原因如下：

- 指定的 DateTimePart 值无效
- 开始日期或结束日期不是有效的 ISO 8601 DateTime

DateTimeDiff 将始终返回一个有符号的整数值，它是跨越的 DateTimePart 边界数的度量，而不是时间间隔的度量值。

## <a name="examples"></a>示例
  
下面的示例计算在和之间交叉的日期边界 `2020-01-01T01:02:03.1234527Z` 数 `2020-01-03T01:02:03.1234567Z` 。

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

下面的示例计算和之间跨的年边界数 `2028-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` 。

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

下面的示例计算介于和之间的小时边界数 `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` 。 即使这些日期时间值相隔超过0.99 小时，也将 `DateTimeDiff` 返回0，因为没有超过小时边界。

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
