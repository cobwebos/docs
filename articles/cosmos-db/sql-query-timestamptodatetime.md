---
title: Azure Cosmos DB 查询语言中的 TimestampToDateTime
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 TimestampToDateTime。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9d4b5179ea08d5d6eca03422db7dfc7c8c4b5c3e
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608715"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB) 

将指定的时间戳值转换为日期时间。
  
## <a name="syntax"></a>语法
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>参数

*Timestamp*  

一个有符号的数字值，是指自 Unix epoch 起经过的当前毫秒数。 换句话说，自00:00:00 （星期四）起已经过的毫秒数（从年 1 1970 月1日）。

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

`undefined`如果指定的时间戳值无效，则 TimestampToDateTime 将返回。

## <a name="examples"></a>示例
  
下面的示例将时间戳转换为 DateTime：

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
