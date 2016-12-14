---
title: "SQL 数据仓库中表的数据类型 | Microsoft 文档"
description: "Azure SQL 数据仓库表的数据类型入门。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 616bb450217573ebd45060234313af418f525f89


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>SQL 数据仓库中表的数据类型
> [!div class="op_single_selector"]
> * [概述][概述]
> * [数据类型][数据类型]
> * [分布][分布]
> * [索引][索引]
> * [分区][分区]
> * [统计信息][统计信息]
> * [临时][临时]
> 
> 

SQL 数据仓库支持最常用的数据类型。  下面是 SQL 数据仓库支持的数据类型列表。  有关支持的数据类型的其他详细信息，请参阅[CREATE TABLE][CREATE TABLE]。

| **支持的数据类型** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>数据类型最佳实践
 在定义列类型时，使用可支持数据的最小数据类型，将能够改善查询性能。 这对 CHAR 和 VARCHAR 列尤其重要。 如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。 避免将所有字符列定义为较大的默认长度。 此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 [NVARCHAR][NVARCHAR]。  尽可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。

## <a name="polybase-limitation"></a>Polybase 限制
如果你是使用 Polybase 来加载表，则可对表进行定义，使可能的最大行大小（包括可变长度列的完整长度）不超过 32,767 字节。  虽然你在定义行时可以使用超出此宽度的可变长度数据，并通过 BCP 来加载行，但无法使用 Polybase 来加载此数据。  很快会增加针对宽行的 Polybase 支持。

## <a name="unsupported-data-types"></a>不支持的数据类型
如果从另一个 SQL 平台（例如 Azure SQL 数据库）迁移数据库，在迁移时，你可能会遇到 SQL 数据仓库不支持的某些数据类型。  下面是不支持的数据类型，以及一些可用于取代不支持的数据类型的备选项。

| 数据类型 | 解决方法 |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |将列拆分成多个强类型化列。 |
| [table][table] |转换成暂时表。 |
| [timestamp][timestamp] |修改代码以使用 [datetime2][datetime2] 和 `CURRENT_TIMESTAMP` 函数。  仅支持常量作为默认值，因此，不能将 current_timestamp 定义为默认约束。 如果需要从 timestamp 类型化列迁移行版本值，请对 NOT NULL 或 NULL 行版本值使用 [BINARY][BINARY](8) 或 [VARBINARY][BINARY](8)。 |
| [xml][xml] |[varchar][varchar] |
| [用户定义的类型][用户定义的类型] |尽可能转换回本机类型 |
| 默认值 |默认值仅支持文本和常量。  不支持非确定性表达式或函数，例如 `GETDATE()` 或 `CURRENT_TIMESTAMP`。 |

可以在当前 SQL 数据库上运行以下 SQL 来识别 Azure SQL 数据仓库不支持的列：

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅有关[表概述][概述]、[分布表][分布][为表编制索引][索引][将表分区][分区][维护表统计信息][统计信息]和[临时表][临时]的文章。  有关最佳实践的详细信息，请参阅 [SQL 数据仓库最佳实践][SQL 数据仓库最佳实践]。

<!--Image references-->

<!--Article references-->
[概述]: ./sql-data-warehouse-tables-overview.md
[数据类型]: ./sql-data-warehouse-tables-data-types.md
[分布]: ./sql-data-warehouse-tables-distribute.md
[索引]: ./sql-data-warehouse-tables-index.md
[分区]: ./sql-data-warehouse-tables-partition.md
[统计信息]: ./sql-data-warehouse-tables-statistics.md
[临时]: ./sql-data-warehouse-tables-temporary.md
[SQL 数据仓库最佳实践]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[用户定义的类型]: https://msdn.microsoft.com/library/ms131694.aspx



<!--HONumber=Nov16_HO3-->


