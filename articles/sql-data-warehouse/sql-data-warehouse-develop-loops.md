---
title: 在 Azure SQL 数据仓库中使用 T-SQL 循环 | Microsoft Docs
description: 有关在开发解决方案时在 Azure SQL 数据仓库中使用 T-SQL 循环和替换游标的技巧。
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>在 SQL 数据仓库中使用 T-SQL 循环
有关在开发解决方案时在 Azure SQL 数据仓库中使用 T-SQL 循环和替换游标的技巧。

## <a name="purpose-of-while-loops"></a>WHILE 循环的用途

SQL 数据仓库支持对重复执行的语句块使用 [WHILE](/sql/t-sql/language-elements/while-transact-sql) 循环。 只要指定的条件都成立，或者在代码专门使用 BREAK 关键字终止循环之前，此 WHILE 循环将一直继续。 循环适合用于替换 SQL 代码中定义的游标。 幸运的是，几乎所有以 SQL 代码编写的游标都是快进的只读变体。 因此，[WHILE] 循环是用于替换游标的绝佳替代方案。

## <a name="replacing-cursors-in-sql-data-warehouse"></a>在 SQL 数据仓库中替换游标
但是，在深入学习之前，应该先自问以下问题：“此游标是否可重写以使用基于集的操作？”。 在许多情况下，答案是肯定的，通常这也是最佳方法。 基于集的操作的执行速度通常比迭代性的逐行方法要快得多。

可以轻松使用循环构造来替换快进只读游标。 下面是一个简单的示例。 此代码示例将更新数据库中每个表的统计信息。 通过迭代循环中的表，每个命令将依次执行。

首先，创建一个临时表，其中包含用于标识各个语句的唯一行号：

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

其次，初始化执行循环所需的变量：

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

现在，每次对一个语句执行一次循环：

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最后，将第一个步骤创建的临时表删除

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

