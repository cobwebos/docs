---
title: Azure Cosmos DB 查询语言中的 GetCurrentDateTime
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 GetCurrentDateTime。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303896"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 以 ISO 8601 字符串形式返回当前 UTC（协调世界时）日期和时间。
  
## <a name="syntax"></a>语法
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>返回类型
  
  以 `YYYY-MM-DDThh:mm:ss.fffffffZ` 格式返回当前 UTC 日期和时间 ISO 8601 字符串值，其中：
  
  |||
  |-|-|
  |YYYY|四位数的年份|
  |MM|两位数的月份（01 = 1 月，依此类推。）|
  |DD|两位数的月份日期（01 到 31）|
  |T|时间元素开头的符号|
  |hh|两位数小时（00 至 23）|
  |mm|两位数分钟（00 到 59）|
  |ss|两位数秒 （00 到 59）|
  |.fffffff|七位小数秒|
  |Z|UTC（协调世界时）指示符||
  
  有关 ISO 8601 格式的详细信息，请参阅 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>备注

  GetCurrentDateTime() 是非确定性的函数。 
  
  返回的结果为 UTC。

  精度为 7 位数字，精度为 100 纳秒。

## <a name="examples"></a>示例
  
  以下示例演示如何使用 GetCurrentDateTime() 内置函数获取当前 UTC 日期时间。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 下面是示例结果集。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 简介](introduction.md)
