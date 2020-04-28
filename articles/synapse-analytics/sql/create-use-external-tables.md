---
title: 在 SQL 按需版本（预览版）中创建和使用外部表
description: 本部分介绍如何在 SQL 按需版本（预览版）中创建和使用外部表。 如果希望在 SQL 按需版本中控制对外部数据的访问，并且希望将 Power BI 之类的工具与 SQL 按需版本结合使用，则可使用外部表。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420791"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>通过 Azure Synapse Analytics 在 SQL 按需版本（预览版）中创建和使用外部表

本部分介绍如何在 SQL 按需版本（预览版）中创建和使用外部表。 如果希望在 SQL 按需版本中控制对外部数据的访问，并且希望将 Power BI 之类的工具与 SQL 按需版本结合使用，则可使用外部表。

## <a name="prerequisites"></a>先决条件

第一步是阅读下面的文章，确保满足创建和使用 SQL 按需版本外部表的先决条件：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>创建外部表

创建外部表的方式可以与创建常规 SQL Server 外部表的方式相同。 下面的查询创建一个外部表，该表读取 population.csv  文件。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用所创建的数据库。 如果尚未创建数据库，请阅读[首次设置](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>使用外部表

可以在查询中使用外部表，其方式与在 SQL Server 查询中使用外部表的方式相同。

以下查询演示了如何使用已在[创建外部表](#create-an-external-table)部分中创建的 population  外部表。 它按降序返回国家/地区名称及其 2019 年的人口。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用所创建的数据库。 如果尚未创建数据库，请阅读[首次设置](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>后续步骤

若要了解如何将查询结果存储到存储，请参阅[将查询结果存储到存储](../sql/create-external-table-as-select.md)。
