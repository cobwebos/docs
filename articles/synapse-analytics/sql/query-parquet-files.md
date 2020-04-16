---
title: 使用 SQL 按需查询 Parquet 文件（预览）
description: 在本文中，您将学习如何使用 SQL 按需（预览）查询 Parquet 文件。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431691"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure 同步分析中使用 SQL 按需（预览）查询 Parquet 文件

在本文中，您将学习如何使用 SQL 按需（预览）编写查询，该 SQL 将读取 Parquet 文件。

## <a name="prerequisites"></a>先决条件

在阅读本文的其余部分之前，请查看以下文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="dataset"></a>数据集

您可以查询 Parquet 文件，就像读取 CSV 文件一样。 唯一的区别是，应将 FILEFORMAT 参数设置为 PARQUET。 本文中的示例显示了读取 Parquet 文件的细节。

> [!NOTE]
> 读取镶木地板文件时，不必在 OPENROWSET WITH 子句中指定列。 SQL 按需将使用 Parquet 文件中的元数据，并按名称绑定列。

您将使用文件夹*镶木地板/出租车*进行示例查询。 它包含纽约出租车 - 黄色出租车旅行记录数据从2016年7月。 至2018年6月。

数据按年和月进行分区，文件夹结构如下：

- 年份=2016年
  - 月=6
  - ...
  - 月=12
- 年份=2017
  - 月=1
  - ...
  - 月=12
- 年份=2018
  - 月=1
  - ...
  - 月=6

## <a name="query-set-of-parquet-files"></a>查询镶木地板文件集

查询 Parquet 文件时，只能指定感兴趣的列。

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

读取 Parquet 文件时，不需要使用 OPENROWSET WITH 子句。 列名称和数据类型会自动从 Parquet 文件中读取。

下面的示例显示了 Parquet 文件的自动架构推理功能。 它不指定架构即可返回 2017 年 9 月的行数。

> [!NOTE]
> 读取 Parquet 文件时，不必在"OPENROWSET WITH"子句中指定列。 在这种情况下，SQL 按需查询服务将利用 Parquet 文件中的元数据，并按名称绑定列。

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

此示例中提供的数据集被划分为（分区）到单独的子文件夹中。 您可以使用文件路径函数定位特定分区。 此示例显示 2017 年前三个月的票价金额（按年份、月份和 payment_type。

> [!NOTE]
> SQL 按需查询与 Hive/Hadoop 分区方案兼容。

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

镶木地板文件包含每列的类型描述。 下表描述了如何将镶木地板类型映射到 SQL 本机类型。

| 镶木地板类型 | 镶木地板逻辑类型（注释） | SQL 数据类型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| 二进制 / BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |瓦尔查尔\*（UTF8排序） |
| BINARY |STRING |瓦尔查尔\*（UTF8排序） |
| BINARY |枚举|瓦尔查尔\*（UTF8排序） |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |瓦尔恰尔（最大\*） （UTF8 排序） |
| BINARY |布森 |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL |varchar（最大），序列化为标准化格式 |
| INT32 |INT（8，真） |smallint |
| INT32 |INT （16， 真） |smallint |
| INT32 |INT （32， 真） |int |
| INT32 |INT（8，假） |tinyint |
| INT32 |INT （16， 假） |int |
| INT32 |INT （32， 假） |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |Decimal |
| INT32 |时间 （米莉斯 ）|time |
| INT64 |INT （64， 真） |bigint |
| INT64 |INT （64， 假 ） |十进制 （20，0） |
| INT64 |DECIMAL |Decimal |
| INT64 |时间（MICROS / NANOS） |time |
|INT64 |时间戳 （米里斯 / MICROS / NANOS） |datetime2 |
|[复杂类型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |列表 |瓦尔恰尔（最大），序列化为JSON |
|[复杂类型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|瓦尔恰尔（最大），序列化为JSON |

## <a name="next-steps"></a>后续步骤

前进到下一篇文章，了解如何查询[Parquet 嵌套类型](query-parquet-nested-types.md)。
