---
title: 使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据
description: 了解如何使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: cd53e1386d9d6f2a38beb1661554c8cc9116169d
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494863"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据 (预览版)

Azure Data Lake Storage 是一种高度可缩放且经济高效的 data Lake 解决方案, 适用于大数据分析。 它将大规模执行和经济高效的特点融入到高性能文件系统的功能中，帮助加快见解产生的时间。 Data Lake Storage Gen2 扩展了 Azure Blob 存储功能，并且针对分析工作负载进行了优化。
 
Azure 数据资源管理器与 Azure Blob 存储和 Azure Data Lake Storage Gen2 集成, 提供对 Lake 中数据的快速、缓存和索引访问。 无需先引入 Azure 数据资源管理器, 即可在 lake 中分析和查询数据。 你还可以同时跨引入和 uningested 本机 lake 数据进行查询。  

> [!TIP]
> 最佳查询性能要求将数据引入到 Azure 数据资源管理器中。 Azure Data Lake Storage Gen2 在没有预先引入的情况下查询中的数据的功能只能用于不常查询的历史数据或数据。
 
## <a name="optimize-query-performance-in-the-lake"></a>在 lake 中优化查询性能 

* 对数据进行分区以改进性能和优化查询时间。
* 压缩数据以提高性能 (gzip 以实现最佳压缩, lz4 为获得最佳性能)。
* 将 Azure Blob 存储或 Azure Data Lake Storage Gen2 与 Azure 数据资源管理器群集使用同一区域。 

## <a name="create-an-external-table"></a>创建外部表

 > [!NOTE]
 > 当前支持的存储帐户是 Azure Blob 存储或 Azure Data Lake Storage Gen2。 当前支持的数据格式为 json、csv、tsv 和 txt。

1. `.create external table`使用命令在 Azure 数据资源管理器中创建外部表。 [外部表命令](/azure/kusto/management/externaltables)中记录了`.show`诸如`.drop`、和`.alter`之类的其他外部表命令。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    此查询创建每日分区*container1/yyyy/MM/dd/all_exported_blobs*。 更精细的分区需要提高性能。 例如, 对具有每月分区表的外部表进行查询时, 其性能比包含月度分区表的查询更好。

1. 外部表显示在 Web UI 的左窗格中

    ![web UI 中的外部表](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>创建 json 格式的外部表

您可以使用 json 格式创建外部表。 有关详细信息, 请参阅[外部表命令](/azure/kusto/management/externaltables)

1. 使用命令创建名为 ExternalTableJson 的表:  `.create external table`

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Json 格式需要另一个步骤来创建到列的映射, 如下所示。 在下面的查询中, 创建一个名为*mappingName*的特定 json 映射:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部表权限
 
* 数据库用户可以创建外部表。 表创建者将自动成为表管理员。
* 群集、数据库或表管理员可以编辑现有表。
* 任何数据库用户或读取者都可以查询外部表。
 
## <a name="query-an-external-table"></a>查询外部表
 
若要查询外部表, 请使用`external_table()`函数, 并提供表名称作为函数参数。 查询的其余部分是标准 Kusto 查询语言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部表查询当前不支持 Intellisense。

### <a name="query-an-external-table-with-json-format"></a>使用 json 格式查询外部表

若要查询具有 json 格式的外部表, 请`external_table()`使用函数, 并提供表名称和映射名称作为函数参数。 在下面的查询中, 如果未指定*mappingName* , 则将使用之前创建的映射。

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>同时查询外部数据和引入数据

您可以在同一查询中查询外部表和引入数据表。 你[`join`](/azure/kusto/query/joinoperator) [或`union`](/azure/kusto/query/unionoperator)包含来自 Azure 数据资源管理器、SQL server 或其他源的其他数据的外部表。 [`let( ) statement`](/azure/kusto/query/letstatement)使用将速记名称分配给外部表引用。

在下面的示例中,*产品*是一个引入数据表, *ArchivedProducts*是一个外部表, 其中包含 Azure Data Lake Storage Gen2 中的数据:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查询帮助群集中的*TaxiRides*外部表

*TaxiRides*示例数据集包含来自[NYC 出租车和礼车委员会](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)的纽约出租车数据。

### <a name="create-external-table-taxirides"></a>Create external table *TaxiRides* 

> [!NOTE]
> 本部分描述用于在*帮助*群集中创建*TaxiRides*外部表的查询。 由于已创建此表, 因此你可以跳过此部分并执行[查询*TaxiRides*外部表数据](#query-taxirides-external-table-data)。 

1. 以下查询用于在帮助群集中创建外部表*TaxiRides* 。 

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
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. 生成的表在*帮助*群集中创建:

    ![TaxiRides 外部表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查询*TaxiRides*外部表数据 

登录到[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)以查询*TaxiRides*外部表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查询*TaxiRides*外部表, 无分区

对外部表*TaxiRides* [运行此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=), 以描述整个数据集中每一周的某一天的搭乘。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查询显示一周内最繁忙的日子。 由于数据未分区, 此查询可能需要较长时间才能返回结果 (最多几分钟)。

![呈现非分区查询](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Query TaxiRides external table with 分区 

对外部表*TaxiRides* [运行此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==), 显示2017年1月使用的出租车 cab 类型 (黄色或绿色)。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查询使用分区, 这会优化查询时间和性能。 查询筛选已分区的列 (pickup_datetime), 并在几秒钟后返回结果。

![呈现分区查询](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以编写其他查询以在外部表*TaxiRides*上运行, 并了解有关数据的更多信息。 

## <a name="next-steps"></a>后续步骤

使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据。 了解如何[编写查询](write-queries.md)并从数据派生更多见解。