---
title: Azure Cosmos DB 查询语言中的 DateTimeFromParts
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimeFromParts。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261708"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB) 

返回根据输入值构造的字符串日期时间值。
  
## <a name="syntax"></a>语法
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>参数
  
*numberYear*格式为年份的整数值`YYYY`

*numberMonth*  
   格式为月份的整数值`MM`

*numberDay*  
   格式的日期的整数值`DD`

*numberHour* (可选) 整数值，格式为`hh`

*numberMinute* (可选) 整数值，格式为分钟`mm`

*numberSecond* (可选) 整数值，格式为第二个`ss`

*numberOfFractionsOfSecond* (可选的) 整数值作为格式的秒的小数部分`.fffffff`

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
  
 有关 ISO 8601 格式的详细信息，请参阅 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>备注

如果指定的整数将创建无效的日期时间，则 DateTimeFromParts 将返回 `undefined` 。

如果未指定可选参数，则其值将为0。

## <a name="examples"></a>示例

下面的示例仅包含构造 DateTime 所需的参数：

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

下面是另一个示例，该示例还使用一些可选参数来构造 DateTime：

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

下面是另一个示例，该示例还使用所有可选参数来构造 DateTime：

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
