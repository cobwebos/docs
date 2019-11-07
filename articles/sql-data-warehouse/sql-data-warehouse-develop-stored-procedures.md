---
title: 使用存储过程
description: 有关在开发解决方案时实现 Azure SQL 数据仓库中的存储过程的技巧。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e6e1144043cbbbc8124785351e1e56a776b84527
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692808"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>使用 SQL 数据仓库中的存储过程
有关在开发解决方案时实现 Azure SQL 数据仓库中的存储过程的技巧。

## <a name="what-to-expect"></a>期望

SQL 数据仓库支持 SQL Server 中使用的许多 T-SQL 功能。 更重要的是，可使用特定的横向扩展功能将解决方案的性能最大化。

但是，为了保持 SQL 数据仓库的缩放性和性能，还有一些具有行为差异的功能以及其他不支持的功能。


## <a name="introducing-stored-procedures"></a>存储过程简介
存储过程很适合用于封装 SQL 代码；将它存储在数据仓库中数据附近。 通过将代码封装成可管理的单位，促使代码有更大的可重复使用性，存储过程帮助开发人员将其解决方案模块化。 每个存储过程还可接受参数，使其更具弹性。

SQL 数据仓库提供简化且流畅的存储过程实现。 相比于 SQL Server，最大差异是存储过程不是预先编译的代码。 在数据仓库中，与针对大型数据卷运行查询所用的时间相比，编译时间非常少。 保证存储过程代码针对大量查询正确优化更为重要。 目标是要节省时数、分钟数和秒数，而不是毫秒数。 因此，将存储过程视为 SQL 逻辑的容器更有帮助。     

SQL 数据仓库执行存储过程时，SQL 语句在运行时进行解析、转换和优化。 在此过程中，每个语句都转换为分布式查询。 针对数据执行的 SQL 代码与提交的查询不同。

## <a name="nesting-stored-procedures"></a>嵌套存储过程
如果存储过程调用其他存储过程或执行动态 SQL，则将内部存储过程或代码调用视为嵌套。

SQL 数据仓库最多支持 8 个嵌套级别。 这与 SQL Server 稍有不同。 SQL Server 中的嵌套级别为 32。

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

请注意，SQL 数据仓库当前不支持 [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql)。 需要跟踪嵌套级别。 不太可能超过 8 个嵌套级别的限制，但如果超过了，则需要重新修改代码，以使它符合限制内的嵌套级别。

## <a name="insertexecute"></a>INSERT..EXECUTE
SQL 数据仓库不允许通过 INSERT 语句使用存储过程的结果集。 但是，可以使用替代方法。 有关示例，请参阅[临时表](sql-data-warehouse-tables-temporary.md)上的文章。 

## <a name="limitations"></a>限制
SQL 数据仓库中未实现 Transact-SQL 存储过程的某些方面。

它们具有以下特点：

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
有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。

