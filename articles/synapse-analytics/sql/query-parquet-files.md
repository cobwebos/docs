---
title: 使用 SQL 按需版本（预览版）查询 Parquet 文件
description: 本文介绍如何使用 SQL 按需版本（预览版）查询 Parquet 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e9731b869b20c7d8cfc3b1e234711c818a2b7422
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744249"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 按需版本（预览版）查询 Parquet 文件

本文介绍如何使用 SQL 按需版本（预览版）编写查询，该查询将读取 Parquet 文件。

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
