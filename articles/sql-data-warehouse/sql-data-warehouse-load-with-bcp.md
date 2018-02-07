---
title: "使用 bcp 将数据载入 SQL 数据仓库 | Microsoft Docs"
description: "了解什么是 bcp，以及如何将它用于数据仓库方案。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 55211e29149cd334421bd8723d47278a19afbfbb
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="load-data-with-bcp"></a>使用 bcp 加载数据

**[bcp](/sql/tools/bcp-utility.md)** 是一个命令行批量加载实用工具，用于在 SQL Server、数据文件和 SQL 数据仓库之间复制数据。 使用 bcp 实用程序可将大量的行导入 SQL 数据仓库表，或将 SQL Server 表中的数据导出到数据文件。 除非与 queryout 选项一起使用，否则 bcp 不需要 Transact-SQL 方面的知识。

bcp 是将较小数据集移入和移出 SQL 数据仓库数据库的快速轻松方式。 通过 bcp 加载/提取数据时，建议的确切数据量取决于 Azure 的网络连接。  随时可以使用 bcp 加载和提取小型维度表。 但是，若要加载和提取大量数据，我们建议使用 Polybase 而不要使用 bcp。 PolyBase 适用于 SQL 数据仓库的大规模并行处理体系结构。

使用 bcp 可以：

* 使用命令行实用工具将数据载入 SQL 数据仓库。
* 使用命令行实用工具从 SQL 数据仓库提取数据。

本教程：

* 使用 bcp in 命令将数据导入表中
* 使用 bcp out 命令从表中导出数据

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>先决条件
要逐步完成本教程，需要：

* 一个 SQL 数据仓库数据库。
* bcp 和 sqlcmd 命令行实用工具。 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=36433)下载这些工具。 

### <a name="data-in-ascii-or-utf-16-format"></a>采用 ASCII 或 UTF-16 格式的数据
如果使用自己的数据尝试学习本教程，则数据需要使用 ASCII 或 UTF-16 编码，因为 bcp 不支持 UTF-8。 

PolyBase 支持 UTF-8，但尚不支持 UTF-16。 若要使用 bcp 执行数据导出，然后使用 PolyBase 执行数据加载，将数据从 SQL Server 导出后，需要将其转换为 UTF-8。 

## <a name="import-data-into-sql-data-warehouse"></a>将数据导入 SQL 数据仓库
在本教程中，会在 Azure SQL 数据仓库中创建一个表，然后将数据导入该表。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>步骤 1：在 Azure SQL 数据仓库中创建表
在命令提示符下，使用 sqlcmd 运行以下查询，以在实例上创建表：

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

有关创建表的详细信息，请参阅[表概述](sql-data-warehouse-tables-overview.md)或[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md) 语法。
 

### <a name="step-2-create-a-source-data-file"></a>步骤 2：创建源数据文件
打开记事本，将以下几行数据复制到新文本文件，然后将此文件保存到本地临时目录，路径为 C:\Temp\DimDate2.txt。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 请务必记得 bcp.exe 不支持 UTF-8 文件编码。 使用 bcp.exe 时，请使用 ASCII 文件或 UTF-16 编码的文件。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>步骤 3：连接并导入数据
在 bcp 中，可以使用以下命令来连接并导入数据（相应地替换其中的值）：

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

可以使用 sqlcmd 运行以下查询来验证是否已加载数据：

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

该查询应返回以下结果：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |#N/A |4 |
| 20150501 |#N/A |4 |
| 20150601 |#N/A |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |#N/A |
| 20151101 |4 |#N/A |
| 20151201 |4 |#N/A |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>步骤 4：基于新加载的数据创建统计信息
加载数据后，最后一步是创建或更新统计信息。 这有助于提升查询性能。 有关详细信息，请参阅[统计信息](sql-data-warehouse-tables-statistics.md)。 以下 sqlcmd 示例在包含新加载的数据的表中创建统计信息。


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>从 SQL 数据仓库导出数据
本教程基于 Azure SQL 数据仓库中的某个表创建数据文件。 它会导出在上一部分中导入的数据。 结果将保存到名为 DimDate2_export.txt 的文件中。

### <a name="step-1-export-the-data"></a>步骤 1：导出数据
在 bcp 实用程序中，可以使用以下命令来连接并导出数据（相应地替换其中的值）：

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
可以通过打开新文件来验证是否已正确导出数据。 该文件中的数据应与以下文本匹配：

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 由于分布式系统的性质，数据顺序在不同 SQL 数据仓库数据库之间可能不同。 另一种做法是使用 bcp 的 **queryout** 函数来编写查询提取，而不是导出整个表。
> 
> 

## <a name="next-steps"></a>后续步骤
若要设计加载过程，请参阅 [加载概述] (sql-data-warehouse-design-elt-data-loading]。  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
