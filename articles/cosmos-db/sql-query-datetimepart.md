---
title: Azure Cosmos DB 查询语言中的 DateTimePart
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimePart。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 87663c18ddaa5da6740a0f54aa5f2812cbb06af8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227078"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB) 

返回指定日期时间之间指定 DateTimePart 的值。
  
## <a name="syntax"></a>语法
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>参数
  
*DateTimePart*  
   DateTimePart 将返回值的日期部分。 此表列出了所有有效的 DateTimePart 参数：

| DateTimePart | 缩写        |
| ------------ | -------------------- |
| 年龄         | "year"、"yyyy" 和 "yy" |
| 月份        | "month"、"mm"、"m"   |
| 天          | "day"、"dd"、"d"     |
| 小时         | "hour"、"hh"         |
| Minute       | "minute"、"mi"、"n"  |
| 秒       | "second"、"ss"、"s"  |
| Millisecond  | "毫秒"，"ms"  |
| 微秒  | "微秒"、"mcs" |
| 纳秒   | "毫微秒"，"ns"   |

*DateTime*  
   格式的 UTC 日期和时间 ISO 8601 字符串值 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>返回类型

返回一个正整数值。

## <a name="remarks"></a>备注

DateTimePart 将返回 `undefined` ，原因如下：

- 指定的 DateTimePart 值无效
- DateTime 不是有效的 ISO 8601 DateTime

此系统函数不会使用索引。

## <a name="examples"></a>示例

下面是返回月份整数值的示例：

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

下面是返回微秒数的示例：

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
