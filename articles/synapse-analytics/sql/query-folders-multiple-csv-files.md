---
title: 使用 SQL 按需查询文件夹和多个 CSV 文件（预览）
description: SQL 按需（预览）支持使用通配符读取多个文件/文件夹，这与 Windows OS 中使用的通配符类似。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431795"
---
# <a name="query-folders-and-multiple-csv-files"></a>查询文件夹和多个 CSV 文件  

在本文中，您将学习如何在 Azure 同步分析中使用 SQL 按需（预览）编写查询。

SQL 按需支持使用通配符读取多个文件/文件夹，这与 Windows OS 中使用的通配符类似。 但是，由于允许使用多个通配符，因此存在更大的灵活性。

## <a name="prerequisites"></a>先决条件

在阅读本文的其余部分之前，请务必查看下面列出的文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>读取文件夹中的多个文件

您将使用文件夹*csv/taxi*来跟踪示例查询。 它包含 2016 年 7 月至 2018 年 6 月的纽约出租车 - 黄色出租车旅行记录数据。

*csv/出租车*中的文件以年份和月份命名：

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

每个文件具有以下结构：
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>读取文件夹中的所有文件
    
下面的示例从*csv/出租车*文件夹中读取所有 NYC 黄色出租车数据文件，并返回每年的乘客和乘车总数。 它还显示了聚合函数的使用情况。

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即列数及其数据类型）。

### <a name="read-subset-of-files-in-folder"></a>读取文件夹中的文件子集

下面的示例使用通配符从*csv/出租车*文件夹中读取 2017 年纽约市黄色出租车数据文件，并返回每个付款类型的总票价金额。

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即列数及其数据类型）。

## <a name="read-folders"></a>读取文件夹

提供给 OPENROWSET 的路径也可以是文件夹的路径。 以下部分包括这些查询类型。

### <a name="read-all-files-from-specific-folder"></a>从特定文件夹读取所有文件

您可以使用文件级通配符读取文件夹中的所有文件，如["读取文件夹中的所有文件"中](#read-all-files-in-folder)所示。 但是，有一种方法来查询文件夹并使用该文件夹中的所有文件。

如果 OPENROWSET 中提供的路径指向文件夹，则该文件夹中的所有文件都将用作查询的源。 以下查询将读取*csv/taxi*文件夹中的所有文件。

> [!NOTE]
> 请注意下面的查询中路径末尾存在 /。 它表示一个文件夹。 如果省略 /，则查询将针对名为*taxi*的文件。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即列数及其数据类型）。

### <a name="read-all-files-from-multiple-folders"></a>从多个文件夹中读取所有文件

可以使用通配符从多个文件夹中读取文件。 以下查询将从*csv*文件夹中的所有文件夹中读取所有文件，这些文件夹中的名称以*t*开头，以*i*结尾。

> [!NOTE]
> 请注意下面的查询中路径末尾存在 /。 它表示一个文件夹。 如果省略 /，则查询将针对名为*t&ast;i*的文件。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即列数及其数据类型）。

由于您只有一个与条件匹配的文件夹，因此查询结果与[读取文件夹中的所有文件](#read-all-files-in-folder)相同。

## <a name="multiple-wildcards"></a>多个通配符

您可以在不同的路径级别上使用多个通配符。 例如，您可以丰富以前的查询，以便仅从以*t*开头和以*i*开头的所有文件夹中读取包含 2017 数据的文件。

> [!NOTE]
> 请注意下面的查询中路径末尾存在 /。 它表示一个文件夹。 如果省略 /，则查询将针对名为*t&ast;i*的文件。
> 每个查询的最大限制为 10 个通配符。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即列数及其数据类型）。

由于您只有一个与条件匹配的文件夹，因此查询结果与[文件夹中文件的"读取"子集](#read-subset-of-files-in-folder)和[从特定文件夹中读取所有文件](#read-all-files-from-specific-folder)相同。 更复杂的通配符使用方案在[查询镶拍文件中](query-parquet-files.md)介绍。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[查询特定文件](query-specific-files.md)一文。
