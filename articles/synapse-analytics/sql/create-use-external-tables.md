---
title: 在 SQL 按需版本（预览版）中创建和使用外部表
description: 本部分介绍如何在 SQL 按需版本（预览版）中创建和使用外部表。 如果希望在 SQL 按需版本中控制对外部数据的访问，并且希望将 Power BI 之类的工具与 SQL 按需版本结合使用，则可使用外部表。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d830ee28eb1f5befc3ad778a6b82c291d1e49d02
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206506"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>通过 Azure Synapse Analytics 在 SQL 按需版本（预览版）中创建和使用外部表

本部分介绍如何在 SQL 按需版本（预览版）中创建和使用[外部表](develop-tables-external-tables.md)。 如果希望在 SQL 按需版本中控制对外部数据的访问，并且希望将 Power BI 之类的工具与 SQL 按需版本结合使用，则可使用外部表。 外部表可以访问两种类型的存储：
- 公用存储，用户可访问其中的公用存储文件。
- 受保护存储，用户使用 SAS 凭据、Azure AD 标识或 Synapse 工作区的托管标识访问存储文件。

## <a name="prerequisites"></a>先决条件

第一步是创建将在其中创建表的数据库。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建此示例中使用的以下对象：
- DATABASE SCOPED CREDENTIAL `sqlondemand`，可以访问受 SAS 保护的 `https://sqlondemandstorage.blob.core.windows.net` Azure 存储帐户。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERNAL DATA SOURCE `sqlondemanddemo`，引用了使用 SAS 密钥保护的演示存储帐户；以及 EXTERNAL DATA SOURCE `YellowTaxi`，引用了位置 `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` 上公开可用的 Azure 存储帐户。

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- 文件格式 `QuotedCSVWithHeaderFormat` 和 `ParquetFormat`，描述 CSV 和 parquet 文件类型。

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

本文中的查询将在示例数据库上执行，并使用这些对象。 

## <a name="create-an-external-table-on-protected-data"></a>对受保护的数据创建外部表

可以创建用于访问 Azure 存储帐户中数据的外部表，该帐户允许具有某些 Azure AD 标识或 SAS 密钥的用户进行访问。 创建外部表的方式可以与创建常规 SQL Server 外部表的方式相同。 

下面的查询将创建一个外部表，该表读取 SynapseSQL 演示 Azure 存储帐户中的 population.csv 文件，该帐户使用 `sqlondemanddemo` 数据源来进行引用，并受名为 `sqlondemand` 的数据库范围的凭据保护。 

数据源和数据库范围的凭据在[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)中创建。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>对公共数据创建外部表

可以创建外部表，从位于公开可用的 Azure 存储的文件中读取数据。 此[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)将创建以下查询中所使用的公用外部数据源和 Parquet 文件格式定义：

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>使用外部表

可以在查询中使用[外部表](develop-tables-external-tables.md)，其方式与在 SQL Server 查询中使用外部表的方式相同。

以下查询演示了如何使用已在上一部分中创建的 population 外部表。 它按降序返回国家/地区名称及其 2019 年的人口。

> [!NOTE]
> 更改查询中的第一行（即 [mydbname]），以便使用你创建的数据库。

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
