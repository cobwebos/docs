---
title: Azure Cosmos DB 查询语言中的 TicksToDateTime
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 TicksToDateTime。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608716"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

将指定的时钟周期值转换为日期/时间。
  
## <a name="syntax"></a>语法
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>参数

Ticks  

一个有符号的数值，即自 Unix 时间以来 100 纳秒时钟周期的当前数字。 换句话说，它是自 1970 年 1 月 1 日星期四 00:00:00 以来 100 纳秒时钟周期的数字。

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

如果指定的时钟周期值无效，TicksToDateTime 将返回 `undefined`。

## <a name="examples"></a>示例
  
以下示例将时钟周期转换为日期/时间：

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
