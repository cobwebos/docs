---
title: GetCurrentDateTime in Azure Cosmos DB query language
description: Learn about SQL system function GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351022"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Returns the current UTC (Coordinated Universal Time) date and time as an ISO 8601 string.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Return types
  
  Returns the current UTC date and time ISO 8601 string value in the format <ph id="ph1">`YYYY-MM-DDThh:mm:ss.sssZ`</ph> where:
  
  |||
  |-|-|
  |YYYY|four-digit year|
  |MM|two-digit month (01 = January, etc.)|
  |DD|two-digit day of month (01 through 31)|
  |T|signifier for beginning of time elements|
  |hh|two digit hour (00 through 23)|
  |mm|two digit minutes (00 through 59)|
  |ss|two digit seconds (00 through 59)|
  |.sss|three digits of decimal fractions of a second|
  |Z|UTC (Coordinated Universal Time) designator||
  
  For more information on the ISO 8601 format, see <bpt id="p1">[</bpt>ISO_8601<ept id="p1">](https://en.wikipedia.org/wiki/ISO_8601)</ept>

## <a name="remarks"></a>Remarks

  GetCurrentDateTime() is a nondeterministic function. 
  
  The result returned is UTC.

## <a name="examples"></a>Examples
  
  The following example shows how to get the current UTC Date Time using the GetCurrentDateTime() built-in function.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Here is an example result set.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>Next steps

- <bpt id="p1">[</bpt>Date and time functions Azure Cosmos DB<ept id="p1">](sql-query-date-time-functions.md)</ept>
- <bpt id="p1">[</bpt>System functions Azure Cosmos DB<ept id="p1">](sql-query-system-functions.md)</ept>
- <bpt id="p1">[</bpt>Introduction to Azure Cosmos DB<ept id="p1">](introduction.md)</ept>
