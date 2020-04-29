---
title: 使用 SQL 点播查询 Parquet 文件（预览）
description: 本文介绍如何使用 SQL 点播（预览版）查询 Parquet 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431691"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的 SQL 点播（预览版）查询 Parquet 文件

本文介绍如何使用 SQL 点播（预览版）编写查询，该查询将读取 Parquet 文件。

## <a name="prerequisites"></a>必备条件

阅读本文其余部分之前，请先查看以下文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="dataset"></a>数据集

可以通过与读取 CSV 文件相同的方式查询 Parquet 文件。 唯一的区别在于，FILEFORMAT 参数应设置为 PARQUET。 本文中的示例演示了读取 Parquet 文件的具体内容。

> [!NOTE]
> 读取 parquet 文件时，无需指定 OPENROWSET WITH 子句中的列。 SQL 点播将使用 Parquet 文件中的元数据，并按名称绑定列。

示例查询将使用文件夹*parquet/出租车*。 它包含2016年7月的 NYC 出租车记录数据。 到6月2018。

按年和月对数据进行分区，文件夹结构如下所示：

- year = 2016
  - month = 6
  - ...
  - month = 12
- year = 2017
  - 月份 = 1
  - ...
  - month = 12
- year = 2018
  - 月份 = 1
  - ...
  - month = 6

## <a name="query-set-of-parquet-files"></a>Parquet 文件的查询集

您只能在查询 Parquet 文件时指定感兴趣的列。

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>自动架构推理

读取 Parquet 文件时，无需使用 OPENROWSET WITH 子句。 列名称和数据类型会自动从 Parquet 文件中读取。

下面的示例显示了 Parquet 文件的自动架构推理功能。 它将返回9月2017的行数，而无需指定架构。

> [!NOTE]
> 读取 Parquet 文件时，无需指定 OPENROWSET WITH 子句中的列。 在这种情况下，SQL 按需查询服务将使用 Parquet 文件中的元数据，并按名称绑定列。

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>查询分区数据

在此示例中提供的数据集分为不同的子文件夹。 您可以使用 filepath 函数以特定分区为目标。 此示例按年、月和 payment_type 显示2017的前三个月的费用金额。

> [!NOTE]
> SQL 点播查询与 Hive/Hadoop 分区方案兼容。

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>类型映射

Parquet 文件包含每一列的类型说明。 下表介绍了如何将 Parquet 类型映射到 SQL 本机类型。

| Parquet 类型 | Parquet 逻辑类型（批注） | SQL 数据类型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| 二进制/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*（UTF8 排序规则） |
| BINARY |STRING |varchar \*（UTF8 排序规则） |
| BINARY |枚举|varchar \*（UTF8 排序规则） |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |varchar （max） \*（UTF8 排序规则） |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL |varchar （max），序列化为标准化格式 |
| INT32 |INT （8，true） |smallint |
| INT32 |INT （16，true） |smallint |
| INT32 |INT （32，true） |int |
| INT32 |INT （8，false） |tinyint |
| INT32 |INT （16，false） |int |
| INT32 |INT （32，false） |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |Decimal |
| INT32 |时间（MILLIS）|time |
| INT64 |INT （64，true） |bigint |
| INT64 |INT （64，false） |decimal （20，0） |
| INT64 |DECIMAL |Decimal |
| INT64 |TIME （MICROS/NANOS） |time |
|INT64 |TIMESTAMP （MILLIS/MICROS/NANOS） |datetime2 |
|[复杂类型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |列表 |varchar （max），序列化为 JSON |
|[复杂类型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar （max），序列化为 JSON |

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何[查询 Parquet 嵌套类型](query-parquet-nested-types.md)。
