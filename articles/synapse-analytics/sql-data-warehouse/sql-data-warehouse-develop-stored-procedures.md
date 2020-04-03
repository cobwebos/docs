---
title: 使用存储过程
description: 通过在 Synapse SQL 池中实现存储过程来开发解决方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a8350f8027a78ae5692e12661f2e0d2013ab4c46
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618946"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>在 Synapse SQL 池中使用存储过程
本文提供了通过实现存储过程来开发 SQL 池解决方案的提示。

## <a name="what-to-expect"></a>期望

SQL 池支持 SQL Server 中使用的许多 T-SQL 功能。 更重要的是，可使用特定的横向扩展功能将解决方案的性能最大化。

此外，为了帮助您保持 SQL 池的规模和性能，还有其他具有行为差异的功能和功能。


## <a name="introducing-stored-procedures"></a>存储过程简介
存储过程是封装 SQL 代码的好方法，该代码存储在 SQL 池数据附近。 存储过程还帮助开发人员通过将代码封装到可管理单元中来模块化其解决方案，从而促进更高的代码可重用性。 每个存储过程还可接受参数，使其更具弹性。

SQL 池提供了简化和简化的存储过程实现。 与 SQL Server 相比，最大的区别是存储过程不是预编译代码。 

通常，对于数据仓库，与针对大量数据卷运行查询所需的时间相比，编译时间较小。 更重要的是确保存储过程代码正确优化为大型查询。 

> [!TIP]
> 目标是要节省时数、分钟数和秒数，而不是毫秒数。 因此，将存储过程视为 SQL 逻辑的容器会很有帮助。     

当 SQL 池执行存储过程时，SQL 语句将在运行时进行分析、转换和优化。 在此过程中，每个语句都转换为分布式查询。 针对数据执行的 SQL 代码与提交的查询不同。

## <a name="nesting-stored-procedures"></a>嵌套存储过程
如果存储过程调用其他存储过程或执行动态 SQL，则将内部存储过程或代码调用视为嵌套。

SQL 池最多支持八个嵌套级别。 相反，SQL Server 中的嵌套级别为 32。

最上层存储过程调用等同于嵌套级别 1。

```sql
EXEC prc_nesting
```
如果存储过程还调用另一个 EXEC，则嵌套级别将增加到 2。

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
如果第二个过程随后执行某种动态 SQL，则嵌套级别将增加到 3。

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL 池当前不支持[@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql)。 因此，您需要跟踪嵌套级别。 您不太可能超过八个嵌套级别限制。 但是，如果您这样做，则需要重新编写代码以适合此限制中的嵌套级别。

## <a name="insertexecute"></a>INSERT..EXECUTE
SQL 池不允许使用 INSERT 语句使用存储过程的结果集。 但是，可以使用替代方法。 有关示例，请参阅[临时表](sql-data-warehouse-tables-temporary.md)上的文章。 

## <a name="limitations"></a>限制
Transact-SQL 存储过程的某些方面未在 SQL 池中实现，如下所示：

* 临时存储过程
* 编号的存储过程
* 扩展的存储过程
* CLR 存储过程
* 加密选项
* 复制选项
* 表值参数
* 只读参数
* 默认参数
* 执行上下文
* return 语句

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

