---
title: 将查询结果存储到存储中
description: 在本文中，你将了解如何使用 SQL 按需版本（预览版）将查询结果存储到存储。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 0e5fda5e7a5caf72df3d4c80e37a684232ca5541
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289355"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>通过 Azure Synapse Analytics 使用 SQL 按需版本（预览版）将查询结果存储到存储

在本文中，你将了解如何使用 SQL 按需版本（预览版）将查询结果存储到存储。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中执行查询的数据库。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据和用于读取这些示例中数据的外部文件格式。

按照本文中的说明创建数据源、数据库范围的凭据以及用于将数据写入到输出存储的外部文件格式。

## <a name="create-external-table-as-select"></a>Create external table as select

可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 语句将查询结果存储到存储。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>使用外部表

可以像使用常规外部表一样，使用通过 CETAS 创建的外部表。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何查询不同的文件类型，请参阅以下文章：[查询单个 CSV 文件](query-single-csv-file.md)、[查询 Parquet 文件](query-parquet-files.md)和[查询 JSON 文件](query-json-files.md)。
