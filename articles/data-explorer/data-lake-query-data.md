---
title: 使用 Azure 数据资源管理器的 Azure Data Lake 中查询数据
description: 了解如何使用 Azure 数据资源管理器的 Azure Data Lake 中查询数据。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453172"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器 （预览版） 的 Azure Data Lake 中查询数据

Azure Data Lake 存储是高度可缩放且经济高效的 data lake 解决方案进行大数据分析。 它将大规模执行和经济高效的特点融入到高性能文件系统的功能中，帮助加快见解产生的时间。 Data Lake Storage Gen2 扩展了 Azure Blob 存储功能，并且针对分析工作负载进行了优化。
 
Azure 数据资源管理器与 Azure Blob 存储和 Azure 数据湖存储第 2 代，提供了快速、 缓存，并编制索引到 lake 中的数据的访问权限。 可以分析和查询如果没有以前引入到 Azure 数据资源管理器中的数据。 您还可以查询分布的数据引入和 uningested 本机 lake 同时。  

> [!TIP]
> 最佳查询性能要求数据引入到 Azure 数据资源管理器。 将在 Azure 数据湖存储第 2 代中的数据查询而无需以前引入的功能仅应该用于历史数据或很少进行查询的数据。
 
## <a name="optimize-query-performance-in-the-lake"></a>优化 lake 中的查询性能 

* 为了提高的性能和优化的查询时的分区数据。
* 压缩数据，以改进性能 （gzip 的压缩效果最好，为了获得最佳性能的 lz4）。
* 使用 Azure 数据资源管理器群集所在的同一区域中使用 Azure Blob 存储或 Azure 数据湖存储第 2 代。 

## <a name="create-an-external-table"></a>创建外部表

1. 使用`.create external table`命令在 Azure 数据资源管理器中创建外部表。 其他外部表命令，如`.show`， `.drop`，并`.alter`中所述[External table 命令](/azure/kusto/management/externaltables)。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    此查询将创建每日分区*container1/yyyy/MM/dd/all_exported_blobs.csv*。 更高的性能应更精细的分区。 例如，通过外部表，其中，如上所示的每日分区的查询将具有更好的性能比每月的已分区表与这些查询。

    > [!NOTE]
    > 当前支持的存储帐户是 Azure Blob 存储或 Azure 数据湖存储第 2 代。 当前支持的数据格式包括 csv、 tsv 和 txt。

1. 外部表是在 Web UI 的左窗格中可见

    ![在 web UI 中的外部表](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>外部表的权限
 
* 数据库用户可以创建外部表。 表的创建者将自动成为表管理员。
* 群集、 数据库或表管理员可以编辑现有表。
* 任何数据库用户或读取器可以查询外部表。
 
## <a name="query-an-external-table"></a>查询外部表
 
若要查询外部表，请使用`external_table()`函数，并提供表名称作为函数参数。 查询的其余部分是标准 Kusto 查询语言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部表查询当前不支持 Intellisense。

## <a name="query-external-and-ingested-data-together"></a>一起查询外部和引入数据

您可以查询外部表和引入的数据在同一查询中的表。 您[ `join` ](/azure/kusto/query/joinoperator)或[ `union` ](/azure/kusto/query/unionoperator)具有其他数据从 Azure 数据资源管理器、 SQL 服务器或其他源的外部表。 使用[ `let( ) statement` ](/azure/kusto/query/letstatement)将简略名称分配给外部表引用。

在以下示例中，*产品*是一个引入的数据的表和*ArchivedProducts*是外部表，其中包含 Azure 数据湖存储第 2 代中的数据：

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查询*TaxiRides* help 群集中的外部表

*TaxiRides*示例数据集包含纽约市出租车数据从[NYC 出租车和礼车委员会](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)。

### <a name="create-external-table-taxirides"></a>创建外部表*TaxiRides* 

> [!NOTE]
> 本部分描述了用于创建查询*TaxiRides*中的外部表*帮助*群集。 由于已创建此表可以跳过此部分，并执行[查询*TaxiRides*外部表数据](#query-taxirides-external-table-data)。 

1. 下面的查询用于创建外部表*TaxiRides* help 群集中。 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. 生成的表中创建*帮助*群集：

    ![TaxiRides 外部表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查询*TaxiRides*外部表数据 

登录到[ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples)查询*TaxiRides*外部表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查询*TaxiRides*不分区的外部表

[运行此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)外部表上*TaxiRides*以描述每个日期是星期几的搭乘跨整个数据集。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查询显示最繁忙的日期是星期几。 由于数据未分区，此查询可能需要很长时间才能返回结果 （最多几分钟时间）。

![呈现非分区查询](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>查询分区 TaxiRides 外部表 

[运行此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)外部表上*TaxiRides*显示出租车 cab 类型 （黄色或绿色） 在 2017 年 1 月中使用。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查询使用分区，这可优化查询时间和性能。 查询已分区的列 (pickup_datetime) 进行筛选，并在几秒内返回结果。

![呈现已分区的查询](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以编写其他查询外部表上运行*TaxiRides*并了解有关数据的详细信息。 

## <a name="next-steps"></a>后续步骤

查询 Azure Data Lake 使用 Azure 数据资源管理器中的数据。 了解如何[编写查询](write-queries.md)并得出额外见解从你的数据。