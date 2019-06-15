---
title: 在 Azure SQL 数据仓库中使用 T-SQL 循环 | Microsoft Docs
description: 有关在开发解决方案时在 Azure SQL 数据仓库中使用 T-SQL 循环和替换游标的技巧。
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c321bc4e493799a50ada4dd91faf2d2ebdee8aba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65850458"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>在 SQL 数据仓库中使用 T-SQL 循环
有关在开发解决方案时在 Azure SQL 数据仓库中使用 T-SQL 循环和替换游标的技巧。

## <a name="purpose-of-while-loops"></a>WHILE 循环的用途

SQL 数据仓库支持对重复执行的语句块使用 [WHILE](/sql/t-sql/language-elements/while-transact-sql) 循环。 只要指定的条件都成立，或者在代码专门使用 BREAK 关键字终止循环之前，此 WHILE 循环将一直继续。 循环适合用于替换 SQL 代码中定义的游标。 幸运的是，几乎所有以 SQL 代码编写的游标都是快进的只读变体。 因此，[WHILE] 循环是用于替换游标的绝佳替代方案。

## <a name="replacing-cursors-in-sql-data-warehouse"></a>在 SQL 数据仓库中替换游标
但是，在一头扎进去之前，你应该问自己以下问题：“是否可以重写此游标以使用基于集的操作？”。 在许多情况下，答案是肯定的，通常这也是最佳方法。 基于集的操作的执行速度通常比迭代性的逐行方法要快得多。

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

