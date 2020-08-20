---
title: Azure Cosmos DB 查询语言中的 DateTimeToTimestamp
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimeToTimestamp。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 673e6a8cb86986ff24e3ee55589d13e8a9e4fdc5
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608713"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB) 

将指定的 DateTime 转换为时间戳。
  
## <a name="syntax"></a>语法
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>参数

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

返回一个有符号的数字值，即自 Unix epoch 以来经过的当前毫秒数，即从00:00:00 星期四到 1 1970 月1日起已经过的毫秒数。

## <a name="remarks"></a>注解

`undefined`如果指定的 DateTime 值无效，则 DateTimeToTimestamp 将返回

## <a name="examples"></a>示例
  
下面的示例将日期时间转换为时间戳：

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

以下是另一示例：

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
