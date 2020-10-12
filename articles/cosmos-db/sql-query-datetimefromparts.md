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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261708"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)

返回根据输入值构造的字符串 DateTime 值。
  
## <a name="syntax"></a>语法
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>参数
  
numberYear 年份的整数值，格式为 `YYYY`

numberMonth  
   月份的整数值，格式为 `MM`

numberDay  
   日期的整数值，格式为 `DD`

numberHour（可选）小时的整数值，格式为 `hh`

numberMinute（可选）分钟的整数值，格式为 `mm`

numberSecond（可选）秒的整数值，格式为 `ss`

numberOfFractionsOfSecond（可选）表示秒的小数部分的整数值，格式为 `.fffffff`

## <a name="return-types"></a>返回类型

以 `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式返回 UTC 日期和时间 ISO 8601 字符串值，其中：
  
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

## <a name="remarks"></a>备注

如果指定的整数将创建无效的 DateTime，则 DateTimeFromParts 将返回 `undefined`。

如果未指定可选参数，则其值为 0。

## <a name="examples"></a>示例

以下是仅包含构造 DateTime 所需的参数的示例：

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

以下是另一个示例，该示例还使用一些可选参数来构造 DateTime：

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

以下是另一个示例，该示例还使用所有可选参数来构造 DateTime：

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
