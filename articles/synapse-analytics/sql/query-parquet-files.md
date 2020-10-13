---
title: 使用 SQL 按需版本（预览版）查询 Parquet 文件
description: 本文介绍如何使用 SQL 按需版本（预览版）查询 Parquet 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 35eef6951f844ab60caec70033e41e23a7920d3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288301"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 按需版本（预览版）查询 Parquet 文件

本文介绍如何使用 SQL 按需版本（预览版）编写查询，该查询将读取 Parquet 文件。

## <a name="quickstart-example"></a>快速入门示例

`OPENROWSET` 函数可以通过提供文件的 URL 来读取 parquet 文件的内容。

### <a name="read-parquet-file"></a>读取 parquet 文件

查看文件内容的最简单方法 `PARQUET` 是提供要函数的文件 URL `OPENROWSET` 并指定 parquet `FORMAT` 。 如果该文件公开可用，或者您的 Azure AD 标识可以访问此文件，则您应该能够使用如下例所示的查询查看该文件的内容：

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

请确保访问此文件。 如果文件受 SAS 密钥或自定义 Azure 标识保护，则需要为 [sql 登录设置服务器级别凭据](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。

### <a name="data-source-usage"></a>数据源使用情况

前面的示例使用文件的完整路径。 作为替代方法，你可以创建一个外部数据源，其中包含指向存储根文件夹的位置，并使用该数据源和函数中的文件的相对路径 `OPENROWSET` ：

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

如果使用 SAS 密钥或自定义标识来保护数据源，则可以 [使用数据库范围凭据配置数据源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

### <a name="explicitly-specify-schema"></a>显式指定架构

`OPENROWSET` 使您能够使用子句显式指定要读取的列 `WITH` ：

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

在以下部分中，可以了解如何查询各种类型的 PARQUET 文件。

## <a name="prerequisites"></a>先决条件

第一步是创建数据库，其中包含了引用[纽约黄色出租车](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)存储帐户的数据源。 然后通过对该数据库执行[安装脚本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)来初始化这些对象。 此安装脚本将创建数据源、数据库范围的凭据以及在这些示例中使用的外部文件格式。

## <a name="dataset"></a>数据集

本示例中使用[纽约黄色出租车](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)数据集。 可以使用与[读取 CSV 文件](query-parquet-files.md)相同的方式查询 Parquet 文件。 唯一的区别是 `FILEFORMAT` 参数应设置为 `PARQUET`。 本文中的示例演示了关于读取 Parquet 文件的详细信息。

## <a name="query-set-of-parquet-files"></a>Parquet 文件的查询集

在查询 Parquet 文件时只能指定所需的列。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>自动架构推理

读取 Parquet 文件时，无需使用 OPENROWSET WITH 子句。 系统会自动从 Parquet 文件中读取列名称和数据类型。

下面的示例显示 Parquet 文件的自动架构推理功能。 该示例在不指定架构的情况下返回 2017 年 9 月的行数。

> [!NOTE]
> 读取 Parquet 文件时，无需指定 OPENROWSET WITH 子句中的列。 在这种情况下，SQL 按需查询服务将利用 Parquet 文件中的元数据，并按名称绑定列。

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>查询分区数据

此示例中提供的数据集划分（分区）为单独的子文件夹。 可以使用 filepath 函数将特定分区作为目标。 此示例显示 2017 年前三个月的费用金额（按年、月和 payment_type）。

> [!NOTE]
> SQL 按需查询与 Hive/Hadoop 分区方案兼容。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>类型映射

Parquet 文件包含每一列的类型说明。 下表介绍了如何将 Parquet 类型映射到 SQL 本机类型。

| Parquet 类型 | Parquet 逻辑类型（批注） | SQL 数据类型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*（UTF8 排序规则） |
| BINARY |STRING |varchar \*（UTF8 排序规则） |
| BINARY |ENUM|varchar \*（UTF8 排序规则） |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |varchar(max) \*（UTF8 排序规则） |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL |varchar(max)，序列化为标准化格式 |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |Decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |Decimal |
| INT64 |TIME (MICROS/NANOS) |time |
|INT64 |TIMESTAMP (MILLIS/MICROS/NANOS) |datetime2 |
|[复杂类型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |列表 |varchar(max)，序列化为 JSON |
|[复杂类型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max)，序列化为 JSON |

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何[查询 Parquet 嵌套类型](query-parquet-nested-types.md)。
