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
ms.date: 6/5/2016
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 6938b92d8e5b46d908dc5b2155bdfdc89bb1dc8c
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017



---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>在 SQL 数据仓库中使用 PolyBase 的指南
本指南提供有关在 SQL 数据仓库中使用 PolyBase 的实用信息。

若要开始，请参阅[使用 PolyBase 加载数据][Load data with PolyBase]教程。

## <a name="rotating-storage-keys"></a>轮换存储密钥
有时出于安全考虑，想要更改 Blob 存储的访问密钥。

执行此任务的最佳方式是遵循称为“轮换密钥”的过程。 你可能已注意到，Blob 存储帐户有两个存储密钥。 这样你便可以转换

轮换 Azure 存储帐户密钥的过程只需简单的三个步骤

1. 创建基于辅助存储访问密钥的第二个数据库范围凭据
2. 创建基于新凭据的第二个外部数据源
3. 删除并创建指向新外部数据源的外部表

当你将所有外部表迁移到新的外部数据源时，可以执行清理任务：

1. 删除第一个外部数据源
2. 删除基于主存储访问密钥的第一个数据库范围凭据
3. 登录 Azure 并重新生成主访问密钥供下一次使用

## <a name="query-azure-blob-storage-data"></a>查询 Azure Blob 存储数据
针对外部表的查询只使用表名称，如关系表一样。

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [!NOTE]
> 对外部表的查询可能因“查询已中止 -- 从外部源读取时已达最大拒绝阈值”错误而失败。 这表示外部数据包含脏记录。 如果实际数据类型/列数目不匹配外部表的列定义，或数据不匹配指定的外部文件格式，则会将数据记录视为脏记录。 若要解决此问题，请确保外部表和外部文件格式定义正确，并且外部数据符合这些定义。 如果外部数据记录的子集是脏的，你可以通过使用 CREATE EXTERNAL TABLE DDL 中的拒绝选项，选择拒绝这些查询记录。
> 
> 

## <a name="load-data-from-azure-blob-storage"></a>从 Azure Blob 存储加载数据
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

## <a name="create-statistics-on-newly-loaded-data"></a>基于新加载的数据创建统计信息
Azure SQL 数据仓库尚不支持自动创建或自动更新统计信息。  为了获得查询的最佳性能，在首次加载数据或者在数据发生重大更改之后，创建所有表的所有列统计信息非常重要。  有关统计信息的详细说明，请参阅开发主题组中的[统计信息][Statistics]主题。  以下快速示例说明如何基于此示例中加载的表创建统计信息。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>将数据导出到 Azure Blob 存储
本部分说明如何将数据从 SQL 数据仓库导出到 Azure Blob 存储。 此示例使用 CREATE EXTERNAL TABLE AS SELECT（高性能 TRANSACT-SQL 语句）将数据从所有计算节点并行导出。

以下示例使用 dbo.Weblogs 表中的列定义和数据从 dbo 创建外部表 Weblogs2014。 外部表定义存储在 SQL 数据仓库中，SELECT 语句的结果将导出到数据源所指定的 Blob 容器下的“/archive/log2014/”目录中。 将以指定的文本文件格式导出数据。

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
 


## <a name="next-steps"></a>后续步骤
若要详细了解如何将数据转移到 SQL 数据仓库，请参阅[数据迁移概述][data migration overview]。

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

