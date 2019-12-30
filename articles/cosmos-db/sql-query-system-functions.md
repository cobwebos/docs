---
title: Azure Cosmos DB 查询语言中的系统函数
description: 了解 Azure Cosmos DB 中内置的和用户定义的 SQL 系统函数。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870524"
---
# <a name="system-functions-azure-cosmos-db"></a>系统函数（Azure Cosmos DB）

 Cosmos DB 提供多个内置 SQL 函数。 内置函数的类别如下所示。  
  
|函数组|描述|Operations|  
|--------------|-----------------|-----------------| 
|[数组函数](sql-query-array-functions.md)|该数组函数对数组输入值执行操作，并返回数值、布尔值或数组值。 | [ARRAY_CONCAT](sql-query-array-concat.md)、 [ARRAY_CONTAINS](sql-query-array-contains.md)、 [ARRAY_LENGTH](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[日期和时间函数](sql-query-date-time-functions.md)|日期和时间函数允许以两种格式获取当前的 UTC 日期和时间;数值为 Unix epoch （以毫秒为单位）或符合 ISO 8601 格式的字符串。 | [GetCurrentDateTime](sql-query-getcurrentdatetime.md)、 [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[数学函数](sql-query-mathematical-functions.md)|每个数学函数均执行一个计算，通常基于作为参数提供的输出值，并返回数值。 | [ABS](sql-query-abs.md)、 [ACOS](sql-query-acos.md)、 [ASIN](sql-query-asin.md)、 [ATAN](sql-query-atan.md)、 [ATN2](sql-query-atn2.md)、[天花板](sql-query-ceiling.md)、 [COS](sql-query-cos.md)、 [COT](sql-query-cot.md)、[度数](sql-query-degrees.md)、 [EXP](sql-query-exp.md)、 [FLOOR](sql-query-floor.md)、 [LOG](sql-query-log.md)、 [LOG10](sql-query-log10.md)、 [PI](sql-query-pi.md)、 [POWER](sql-query-power.md)、 [RADIANS](sql-query-radians.md)、 [RAND](sql-query-rand.md)、 [ROUND](sql-query-round.md)、 [SIGN](sql-query-sign.md)、 [SIN](sql-query-sin.md)、 [SQRT](sql-query-sqrt.md)、[方形](sql-query-square.md)、 [TAN](sql-query-tan.md)、 [TRUNC](sql-query-trunc.md) |
|[空间函数](sql-query-spatial-functions.md)|该空间函数对控件对象输入值执行操作，并返回数值或布尔值。 | [ST_DISTANCE](sql-query-st-distance.md)、 [ST_INTERSECTS](sql-query-st-intersects.md)、 [ST_ISVALID](sql-query-st-isvalid.md)、 [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)、 [ST_WITHIN](sql-query-st-within.md) |
|[字符串函数](sql-query-string-functions.md)|该字符串函数对字符串输入值执行操作，并返回字符串、数值或布尔值。 | [CONCAT](sql-query-concat.md)、 [CONTAINS](sql-query-contains.md)、 [ENDSWITH](sql-query-endswith.md)、 [INDEX_OF](sql-query-index-of.md)、 [LEFT](sql-query-left.md)、 [LENGTH](sql-query-length.md)、 [LOWER](sql-query-lower.md)、 [LTRIM](sql-query-ltrim.md)、 [REPLACE](sql-query-replace.md)、 [REPLICATE](sql-query-replicate.md)StringToArray、 [REVERSE](sql-query-reverse.md)、 [RIGHT](sql-query-right.md)、 [RTRIM](sql-query-rtrim.md)、 [STARTSWITH](sql-query-startswith.md)、 [ ](sql-query-stringtoarray.md)、 [StringToBoolean](sql-query-stringtoboolean.md)、 [StringToNull](sql-query-stringtonull.md)、 [StringToNumber](sql-query-stringtonumber.md)、 [StringToObject](sql-query-stringtoobject.md)、 [SUBSTRING](sql-query-substring.md)、 [ToString](sql-query-tostring.md)、 [TRIM](sql-query-trim.md)、 [UPPER](sql-query-upper.md) |
|[类型检查函数](sql-query-type-checking-functions.md)|类型检查函数使你能够检查 SQL 查询内表达式的类型。 | [IS_ARRAY](sql-query-is-array.md)、 [IS_BOOL](sql-query-is-bool.md)、 [IS_DEFINED](sql-query-is-defined.md)、 [IS_NULL](sql-query-is-null.md)、 [IS_NUMBER](sql-query-is-number.md)、 [IS_OBJECT](sql-query-is-object.md)、 [IS_PRIMITIVE](sql-query-is-primitive.md)、 [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>内置与用户定义的函数（Udf）

如果当前正在使用内置函数可用的用户定义函数（UDF），则相应的内置函数将更快运行，更有效率。

## <a name="built-in-versus-ansi-sql-functions"></a>内置与 ANSI SQL 函数

Cosmos DB 函数和 ANSI SQL 函数之间的主要区别在于，Cosmos DB 函数旨在适合无架构和混合架构数据。 例如，如果某个属性缺失或具有一个非数值（如 `unknown`），则将跳过该项，而不是返回错误。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [数组函数](sql-query-array-functions.md)
- [日期和时间函数](sql-query-date-time-functions.md)
- [数学函数](sql-query-mathematical-functions.md)
- [空间函数](sql-query-spatial-functions.md)
- [字符串函数](sql-query-string-functions.md)
- [类型检查函数](sql-query-type-checking-functions.md)
- [用户定义函数](sql-query-udfs.md)
- [聚合](sql-query-aggregates.md)
