---
title: "在 SQL 数据仓库中分配变量 | Microsoft 文档"
description: "有关在开发解决方案时于 Azure SQL 数据仓库中分配 Transact-SQL 变量的技巧。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5ec9f7ad24dad833758d3b981fd4d8735119d813


---
# <a name="assign-variables-in-sql-data-warehouse"></a>在 SQL 数据仓库中分配变量
SQL 数据仓库中的变量是使用 `DECLARE` 语句或 `SET` 语句设置的。

以下各项是设置变量值的完全有效方式：

## <a name="setting-variables-with-declare"></a>使用 DECLARE 设置变量
使用 DECLARE 初始化变量是在 SQL 数据仓库中设置变量值的最灵活方式之一。

```sql
DECLARE @v  int = 0
;
```

你还可以使用 DECLARE 一次性设置多个变量。 可以使用 `SELECT` 或 `UPDATE` 来实现此目的：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

你无法在同一 DECLARE 语句中初始化和使用某个变量。 为了演示要点，**不**允许出现以下示例中的情况，因为 @p1 已在同一个 DECLARE 语句中初始化和使用。 这会导致错误。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>使用 SET 设置值
SET 是设置单个变量的很常见方法。

以下所有示例都是使用 SET 设置变量的有效方式：

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

一次只能使用 SET 设置一个变量。 但是，如上所示，允许使用复合运算符。

## <a name="limitations"></a>限制
不能使用 SELECT 或 UPDATE 来分配变量。

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[部署概述][部署概述]。

<!--Image references-->

<!--Article references-->
[部署概述]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


