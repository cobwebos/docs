---
title: 在 SQL 按需版本（预览版）中创建和使用视图
description: 本部分介绍了如何创建和使用视图，以便包装 SQL 按需版本（预览版）查询。 可以通过视图重复使用这些查询。 如果希望将 Power BI 之类的工具与 SQL 按需版本结合使用，也需使用视图。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420771"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>通过 Azure Synapse Analytics 在 SQL 按需版本（预览版）中创建和使用视图

本部分介绍了如何创建和使用视图，以便包装 SQL 按需版本（预览版）查询。 可以通过视图重复使用这些查询。 如果希望将 Power BI 之类的工具与 SQL 按需版本结合使用，也需使用视图。

## <a name="prerequisites"></a>先决条件

第一步是阅读下面的文章，确保满足创建和使用 SQL 按需版本视图的先决条件：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>创建视图

可以采用与创建常规 SQL Server 视图相同的方式来创建视图。 下面的查询创建一个视图，该视图读取 population.csv  文件。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。 如果尚未创建数据库，请阅读[首次设置](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>使用视图

可以在查询中使用视图，其方式与在 SQL Server 查询中使用视图的方式相同。

以下查询演示了如何使用在[创建视图](#create-a-view)中创建的 population_csv  视图。 它按降序返回国家/地区名称及其 2019 年的人口。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。 如果尚未创建数据库，请阅读[首次设置](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>后续步骤

若要了解如何查询不同的文件类型，请参阅以下文章：[查询单个 CSV 文件](query-single-csv-file.md)、[查询 Parquet 文件](query-parquet-files.md)和[查询 JSON 文件](query-json-files.md)。
