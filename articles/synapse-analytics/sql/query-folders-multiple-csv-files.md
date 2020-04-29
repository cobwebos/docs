---
title: 使用 SQL 点播（预览版）查询文件夹和多个 CSV 文件
description: SQL 点播（预览版）支持使用通配符读取多个文件/文件夹，这类似于 Windows 操作系统中使用的通配符。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457359"
---
# <a name="query-folders-and-multiple-csv-files"></a>查询文件夹和多个 CSV 文件  

本文介绍如何在 Azure Synapse Analytics 中使用 SQL 点播（预览版）编写查询。

SQL 点播支持使用通配符读取多个文件/文件夹，这类似于 Windows 操作系统中使用的通配符。 但是，由于允许使用多个通配符，因此存在更大的灵活性。

## <a name="prerequisites"></a>必备条件

阅读本文其余部分之前，请务必查看下面列出的文章：

- [首次设置](query-data-storage.md#first-time-setup)
- [先决条件](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>读取文件夹中的多个文件

你将使用文件夹*csv/出租车*来执行示例查询。 它包含 NYC 出租车-从7月2016日到6月 6 2018 日，记录数据。

*Csv/出租车*中的文件按年份和月份命名：

- yellow_tripdata_2016-07
- yellow_tripdata_2016-08
- yellow_tripdata_2016-09
- ...
- yellow_tripdata_2018-04
- yellow_tripdata_2018-05
- yellow_tripdata_2018-06-01.5。1

每个文件都具有以下结构：
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>读取文件夹中的所有文件
    
下面的示例从*csv/出租车*文件夹中读取所有 NYC 的黄色出租车数据文件，并返回乘客和搭乘的总次数。 它还显示聚合函数的使用情况。

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
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即，列数和数据类型）。

### <a name="read-subset-of-files-in-folder"></a>读取文件夹中的文件子集

下面的示例使用通配符从*csv/出租车*文件夹中读取 2017 NYC 的黄色出租车数据文件，并返回每个付款类型的总费用金额。

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
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即，列数和数据类型）。

## <a name="read-folders"></a>读取文件夹

您向 OPENROWSET 提供的路径也可以是指向文件夹的路径。 以下各节包含这些查询类型。

### <a name="read-all-files-from-specific-folder"></a>读取特定文件夹中的所有文件

您可以使用文件级别通配符读取文件夹中的所有文件，如[读取文件夹中的所有文件](#read-all-files-in-folder)中所示。 但是，有一种方法可以查询文件夹并使用该文件夹中的所有文件。

如果 OPENROWSET 中提供的路径指向某个文件夹，则该文件夹中的所有文件都将用作查询的源。 以下查询将读取*csv/出租车*文件夹中的所有文件。

> [!NOTE]
> 请注意以下查询中路径的末尾是否存在/。 它表示文件夹。 如果省略/，则查询将以一个名为*出租车*的文件为目标。

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
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即，列数和数据类型）。

### <a name="read-all-files-from-multiple-folders"></a>读取多个文件夹中的所有文件

可以通过使用通配符从多个文件夹读取文件。 下面的查询将从*csv*文件夹中的所有文件夹（名称以*t*开头并以*i*结尾）读取所有文件。

> [!NOTE]
> 请注意以下查询中路径的末尾是否存在/。 它表示文件夹。 如果省略/，则查询将改为针对名为*t&ast;i*的文件。

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
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即，列数和数据类型）。

由于只有一个与条件相匹配的文件夹，因此查询结果与 "[读取文件夹中的所有文件](#read-all-files-in-folder)" 相同。

## <a name="multiple-wildcards"></a>多个通配符

可以在不同的路径级别使用多个通配符。 例如，你可以将以前的查询扩充为仅读取包含2017数据的文件，从所有文件夹中的名称以*t*开头，并以*i*结尾。

> [!NOTE]
> 请注意以下查询中路径的末尾是否存在/。 它表示文件夹。 如果省略/，则查询将改为针对名为*t&ast;i*的文件。
> 每个查询的最大限制为10个通配符。

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
> 使用单个 OPENROWSET 访问的所有文件必须具有相同的结构（即，列数和数据类型）。

由于只有一个与条件相匹配的文件夹，因此查询结果与 "[在文件夹中读取文件的子集](#read-subset-of-files-in-folder)" 和 "[读取特定文件夹中的所有文件](#read-all-files-from-specific-folder)" 相同。 [查询 Parquet 文件](query-parquet-files.md)中介绍了更复杂的通配符使用方案。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[查询特定文件](query-specific-files.md)一文。
