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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303896"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime （Azure Cosmos DB）
 以 ISO 8601 字符串的形式返回当前 UTC （协调世界时）日期和时间。
  
## <a name="syntax"></a>语法
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>返回类型
  
  返回格式 `YYYY-MM-DDThh:mm:ss.fffffffZ` 的当前 UTC 日期和时间 ISO 8601 字符串值，格式为：
  
  |||
  |-|-|
  |YYYY|四位数年份|
  |MM|两位数月份（01 = 一月，等等）|
  |DD|两位数日期（01到31）|
  |T|signifier for time 元素开始|
  |hh|两位数的小时数（00到23）|
  |mm|两位数分钟（00到59）|
  |ss|两位数秒（00到59）|
  |。 fffffff|七位数的秒小数部分|
  |Z|UTC （协调世界时）指示符||
  
  有关 ISO 8601 格式的详细信息，请参阅[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>备注

  GetCurrentDateTime （）是不确定性函数。 
  
  返回的结果为 UTC。

  精度为7位，准确度为100毫微秒。

## <a name="examples"></a>示例
  
  下面的示例演示如何使用 GetCurrentDateTime （）内置函数获取当前的 UTC 日期时间。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 下面是一个示例结果集。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
