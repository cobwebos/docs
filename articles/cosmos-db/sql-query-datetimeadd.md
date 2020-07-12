---
title: Azure Cosmos DB 查询语言中的 DateTimeAdd
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimeAdd。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 0b2741a3d2b013ba7bd97038eb4ba4512f36af11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261709"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB) 

返回将指定数字值作为) 到指定日期时间字符串的有符号整数添加 (所得的日期时间字符串值  
  
## <a name="syntax"></a>语法
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>参数
  
*DateTimePart*  
   DateTimeAdd 添加整数的日期部分。 此表列出了所有有效的 DateTimePart 参数：

| DateTimePart | 缩写        |
| ------------ | -------------------- |
| 年龄         | "year"、"yyyy" 和 "yy" |
| 月份        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小时         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| 第二个       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"，"ms"  |
| 微秒  | "微秒"、"mcs" |
| 纳秒   | "毫微秒"，"ns"   |

*numeric_expr*  
   将添加到指定 DateTime 的 DateTimePart 中的有符号整数值

*DateTime*  
   UTC 日期和时间 ISO 8601 字符串值，格式为 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |格式|说明|
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

## <a name="return-types"></a>返回类型

返回格式为的 UTC 日期和时间 ISO 8601 字符串值，格式为 `YYYY-MM-DDThh:mm:ss.fffffffZ` ：
  
  |格式|说明|
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

## <a name="remarks"></a>注解

DateTimeAdd 将返回 `undefined` ，原因如下：

- 指定的 DateTimePart 值无效
- 指定的 numeric_expr 不是有效的整数
- 参数或结果中的 DateTime 不是有效的 ISO 8601 日期时间。

## <a name="examples"></a>示例
  
下面的示例将1个月添加到日期时间：`2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

下面的示例从日期时间中减去2小时：`2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
