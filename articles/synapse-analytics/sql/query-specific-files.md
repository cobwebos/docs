---
title: 在查询中使用文件元数据
description: OPENROWSET 函数提供有关查询中使用的每个文件的文件和路径信息，以便根据文件名和/或文件夹路径筛选或分析数据。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431548"
---
# <a name="using-file-metadata-in-queries"></a>在查询中使用文件元数据

SQL 按需查询服务可以处理多个文件和文件夹，如[查询文件夹和多个文件](query-folders-multiple-csv-files.md)文章中所述。 在本文中，您将了解如何在查询中使用有关文件和文件夹名称的元数据信息。

有时，您可能需要知道哪个文件或文件夹源与结果集中的特定行相关联。

可以使用 函数`filepath`并`filename`返回结果集中的文件名和/或路径。 或者，您可以使用它们根据文件名和/或文件夹路径筛选数据。 这些函数在语法部分[文件名函数](develop-storage-files-overview.md#filename-function)和[文件路径函数](develop-storage-files-overview.md#filepath-function)中描述。 下面您将找到样品的简要说明。

## <a name="prerequisites"></a>先决条件

在阅读本文的其余部分之前，请查看以下先决条件：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="functions"></a>函数

### <a name="filename"></a>Filename

此函数返回该行源自的文件名。

以下示例读取 2017 年最后三个月的 NYC 黄色出租车数据文件，并返回每个文件的乘车次数。 查询的 OPENROWSET 部分指定将读取哪些文件。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

下面的示例演示如何在 WHERE 子句中使用*filename（）* 来筛选要读取的文件。 它访问查询 OPENROWSET 部分中的整个文件夹，并筛选 WHERE 子句中的文件。

您的结果将与上一个示例相同。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>文件路径

文件路径函数返回完整或部分路径：

- 当调用没有参数时，它将返回行源自的完整文件路径。
- 使用参数调用时，它将返回与参数中指定位置上的通配符匹配的部分路径。 例如，参数值 1 将返回与第一个通配符匹配的路径的一部分。

以下示例显示 2017 年最后三个月的 NYC 黄色出租车数据文件。 它返回每个文件路径的行驶次数。 查询的 OPENROWSET 部分指定将读取哪些文件。

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

下面的示例演示如何在 WHERE 子句中使用*filepath（）* 来筛选要读取的文件。

您可以在查询的 OPENROWSET 部分中使用通配符，并筛选 WHERE 子句中的文件。 您的结果将与上一个示例相同。

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>后续步骤

在下一篇文章中，您将学习如何[查询 Parquet 文件](query-parquet-files.md)。
