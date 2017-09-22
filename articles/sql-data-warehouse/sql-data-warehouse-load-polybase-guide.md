---
title: "在 SQL 数据仓库中使用 PolyBase 的指南 | Microsoft 文档"
description: "有关在 SQL 数据仓库方案中使用 PolyBase 的指导原则和建议。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 7594a0730477fe3f3bd34b0b6207478de70c7595
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>在 SQL 数据仓库中使用 PolyBase 的指南
本指南提供有关在 SQL 数据仓库中使用 PolyBase 的实用信息。

若要开始，请参阅[使用 PolyBase 加载数据][Load data with PolyBase]教程。

## <a name="rotating-storage-keys"></a>轮换存储密钥
有时出于安全考虑，想要更改 Blob 存储的访问密钥。

执行此任务的最佳方式是遵循称为“轮换密钥”的过程。 可能已注意到，Blob 存储帐户有两个存储密钥。 这样便可以转换

轮换 Azure 存储帐户密钥的过程只需简单的三个步骤

1. 创建基于辅助存储访问密钥的第二个数据库范围凭据
2. 创建基于新凭据的第二个外部数据源
3. 删除并创建指向新外部数据源的外部表

将所有外部表迁移到新的外部数据源时，可以执行清理任务：

1. 删除第一个外部数据源
2. 删除基于主存储访问密钥的第一个数据库范围凭据
3. 登录 Azure 并重新生成主访问密钥供下一次使用



## <a name="load-data-with-external-tables"></a>使用外部表加载数据
此示例将 Azure Blob 存储中的数据加载到 SQL 数据仓库数据库中。

直接存储数据可免除查询时的数据传输时间。 配合列存储索引存储数据可让分析查询的查询性能提升 10 倍。

此示例使用 CREATE TABLE AS SELECT 语句来加载数据。 新表继承查询中指定的列。 它从外部表定义继承这些列的数据类型。

CREATE TABLE AS SELECT 是高性能 TRANSACT-SQL 语句，可将数据并行加载到 SQL 数据仓库的所有计算节点。  它最初是针对分析平台系统中的大规模并行处理 (MPP) 引擎开发的，现已纳入 SQL 数据仓库。

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

请参阅 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]。

> [!NOTE]
> 使用外部表的加载可能因“查询已中止 -- 从外部源读取时已达最大拒绝阈值”错误而失败。 这表示外部数据包含脏记录。 如果实际数据类型/列数目不匹配外部表的列定义，或数据不匹配指定的外部文件格式，则会将数据记录视为脏记录。 若要解决此问题，请确保外部表和外部文件格式定义正确，并且外部数据符合这些定义。 如果外部数据记录的子集是脏的，可以通过使用 CREATE EXTERNAL TABLE DDL 中的拒绝选项，选择拒绝这些查询记录。
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>基于新加载的数据创建统计信息
Azure SQL 数据仓库尚不支持自动创建或自动更新统计信息。  为了获得查询的最佳性能，在首次加载数据或者在数据发生重大更改之后，创建所有表的所有列统计信息非常重要。  有关统计信息的详细说明，请参阅开发主题组中的[统计信息][Statistics]主题。  以下快速示例说明如何基于此示例中加载的表创建统计信息。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>使用外部表导出数据
本部分显示如何使用外部表将数据从 SQL 数据仓库导出到 Azure blob 存储。 此示例使用 CREATE EXTERNAL TABLE AS SELECT（高性能 TRANSACT-SQL 语句）将数据从所有计算节点并行导出。

以下示例使用 dbo.Weblogs 表中的列定义和数据从 dbo 创建外部表 Weblogs2014。 外部表定义存储在 SQL 数据仓库中，SELECT 语句的结果将导出到数据源所指定的 blob 容器下的“/archive/log2014/”目录中。 将以指定的文本文件格式导出数据。

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>隔离加载用户
通常需要有多个用户可将数据加载到 SQL 数据仓库中。 由于 [CREATE TABLE AS SELECT (TRANSACT-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 需要数据库的“控制”权限，因此最终将有多个用户具有所有架构的控制访问权限。 可使用 DENY CONTROL 语句限制这种情况。

示例：考虑到为部门 A 使用数据库架构 schema_A，为部门 B 使用 schema_B；让数据库用户 user_A 和 user_B 分别作为部门 A 和 B 中加载的 PolyBase 用户。 两个用户均已被授予“控制”数据库权限。
架构 A 和架构 B 的创建者现在使用 DENY 锁定其架构：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 在此情况下，现在 user_A 和 user_B 应被锁在其他部门的架构之外。
 
## <a name="polybase-performance-optimizations"></a>PolyBase 性能优化
若要让 PolyBase 实现最佳加载性能，请参考以下建议：
- 将大型压缩文件拆分为较小的压缩文件。 目前支持的压缩类型是不可拆分的。 因此，加载单个大文件会影响性能。
- 为获得最快加载速度，请加载到 round_robin 堆临时表。 这是将数据从存储层移至数据仓库最有效的方法。
- 所有文件格式有不同的性能特征。 为实现最快加载，请使用压缩的分隔文本文件。 UTF-8 和 UTF-16 之间的性能差异是最小的。
- 并置存储层和数据仓库以最小化延迟
- 如果要执行大型加载作业，请纵向扩展数据仓库。

## <a name="polybase-limitations"></a>PolyBase 限制
SQL DW 中的 PolyBase 有以下限制需要在设计加载作业时考虑：
- 单行不能宽于 1,000,000 字节。 无论表架构如何定义，这都是 true，包括 (n)varchar(max) 列。 这意味着外部表 (n)varchar(max) 列最宽可为 1,000,000 字节，而非数据类型定义的 2GB 限制。
- 将数据从 SQL Server 或 Azure SQL 数据仓库导出到 ORC 文件格式时，文字较多的列的数量可能会因 Java 内存不足的错误而被限制为 50 列。 若要解决此问题，请仅导出列的一个子集。





## <a name="next-steps"></a>后续步骤
要详细了解如何将数据转移到 SQL 数据仓库，请参阅[数据迁移概述][data migration overview]。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->

