---
title: 将查询结果存储到存储中
description: 在本文中，你将了解如何使用 SQL 按需版本（预览版）将查询结果存储到存储。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691862"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>通过 Azure Synapse Analytics 使用 SQL 按需版本（预览版）将查询结果存储到存储

在本文中，你将了解如何使用 SQL 按需版本（预览版）将查询结果存储到存储。

## <a name="prerequisites"></a>先决条件

第一步是阅读下面的文章，确保满足先决条件：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Create external table as select

可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 语句将查询结果存储到存储。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。 如果尚未创建数据库，请阅读[首次设置](query-data-storage.md#first-time-setup)。 需要更改 MyDataSource 外部数据源的 LOCATION，使之指向你有写入权限的位置。 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>使用所创建的外部表

可以像使用常规外部表一样，使用通过 CETAS 创建的外部表。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。 如果尚未创建数据库，请阅读[首次设置](query-data-storage.md#first-time-setup)。

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

若要了解如何查询不同的文件类型，请参阅以下文章：[查询单个 CSV 文件](query-single-csv-file.md)、[查询 Parquet 文件](query-parquet-files.md)和[查询 JSON 文件](query-json-files.md)。
