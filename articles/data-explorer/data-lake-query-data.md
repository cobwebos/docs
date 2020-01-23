---
title: 使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据
description: 了解如何使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 1e5af0b45b8d2e2eceac1b653a5219a236c25467
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512906"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据

Azure Data Lake Storage 是一种高度可缩放且经济高效的 data Lake 解决方案，适用于大数据分析。 它将大规模执行和经济高效的特点融入到高性能文件系统的功能中，帮助加快见解产生的时间。 Data Lake Storage Gen2 扩展了 Azure Blob 存储功能，并且针对分析工作负载进行了优化。
 
Azure 数据资源管理器与 Azure Blob 存储和 Azure Data Lake Storage Gen2 集成，提供对 Lake 中数据的快速、缓存和索引访问。 无需先引入 Azure 数据资源管理器，即可在 lake 中分析和查询数据。 你还可以同时跨引入和 uningested 本机 lake 数据进行查询。  

> [!TIP]
> 最佳查询性能要求将数据引入到 Azure 数据资源管理器中。 Azure Data Lake Storage Gen2 在没有预先引入的情况下查询中的数据的功能只能用于不常查询的历史数据或数据。 [在 lake 中优化查询性能](#optimize-your-query-performance)以获得最佳结果。
 

## <a name="create-an-external-table"></a>创建外部表

 > [!NOTE]
 > 当前支持的存储帐户是 Azure Blob 存储或 Azure Data Lake Storage Gen2。 当前支持的数据格式为 json、csv、tsv 和 txt。

1. 使用 `.create external table` 命令在 Azure 数据资源管理器中创建外部表。 [外部表命令](/azure/kusto/management/externaltables)中记录了诸如 `.show`、`.drop`和 `.alter` 之类的其他外部表命令。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * 更精细的分区需要提高性能。 例如，对于具有每月分区表的外部表的查询，性能比包含月度分区表的查询更好。
    > * 使用分区定义外部表时，存储结构应是相同的。
例如，如果使用 yyyy/MM/dd 格式（默认值）将表定义为 DateTime 分区，则 URI 存储文件路径应为*container1/yyyy/MM/dd/all_exported_blobs*。 
    > * 如果外部表按日期时间列进行分区，则始终在查询中包含已关闭范围的时间筛选器（例如，查询 `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)`-应比此（打开的范围）一 `ArchivedProducts | where Timestamp > ago(1h)`）更好。 

1. 外部表显示在 Web UI 的左窗格中

    ![web UI 中的外部表](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>创建 json 格式的外部表

您可以使用 json 格式创建外部表。 有关详细信息，请参阅[外部表命令](/azure/kusto/management/externaltables)

1. 使用 `.create external table` 命令创建名为*ExternalTableJson*的表：

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
 
1. Json 格式需要另一个步骤来创建到列的映射，如下所示。 在下面的查询中，创建一个名为*mappingName*的特定 json 映射：

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部表权限
 
* 数据库用户可以创建外部表。 表创建者将自动成为表管理员。
* 群集、数据库或表管理员可以编辑现有表。
* 任何数据库用户或读取者都可以查询外部表。
 
## <a name="query-an-external-table"></a>查询外部表
 
若要查询外部表，请使用 `external_table()` 函数，并提供表名称作为函数参数。 查询的其余部分是标准 Kusto 查询语言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部表查询当前不支持 Intellisense。

### <a name="query-an-external-table-with-json-format"></a>使用 json 格式查询外部表

若要查询具有 json 格式的外部表，请使用 `external_table()` 函数，并提供表名称和映射名称作为函数参数。 在下面的查询中，如果未指定*mappingName* ，则将使用之前创建的映射。

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>同时查询外部数据和引入数据

您可以在同一查询中查询外部表和引入数据表。 你[`join`](/azure/kusto/query/joinoperator)或[`union`](/azure/kusto/query/unionoperator)外部表中包含来自 Azure 数据资源管理器、SQL server 或其他源的其他数据。 使用[`let( ) statement`](/azure/kusto/query/letstatement)将速记名称分配给外部表引用。

在下面的示例中，*产品*是一个引入数据表， *ArchivedProducts*是一个外部表，其中包含 Azure Data Lake Storage Gen2 中的数据：

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查询帮助群集中的*TaxiRides*外部表

*TaxiRides*示例数据集包含来自[NYC 出租车和礼车委员会](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)的纽约出租车数据。

### <a name="create-external-table-taxirides"></a>Create external table *TaxiRides* 

> [!NOTE]
> 本部分描述用于在*帮助*群集中创建*TaxiRides*外部表的查询。 由于已创建此表，因此你可以跳过此部分并执行[查询*TaxiRides*外部表数据](#query-taxirides-external-table-data)。 

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
1. 生成的表在*帮助*群集中创建：

    ![TaxiRides 外部表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查询*TaxiRides*外部表数据 

登录到[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)以查询*TaxiRides*外部表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查询*TaxiRides*外部表，无分区

对外部表*TaxiRides* [运行此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)，以描述整个数据集中每一周的某一天的搭乘。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查询显示一周内最繁忙的日子。 由于数据未分区，此查询可能需要较长时间才能返回结果（最多几分钟）。

![呈现非分区查询](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Query TaxiRides external table with 分区 

对外部表*TaxiRides* [运行此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)，显示2017年1月使用的出租车 cab 类型（黄色或绿色）。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查询使用分区，这会优化查询时间和性能。 查询筛选已分区列（pickup_datetime），并在几秒钟后返回结果。

![呈现分区查询](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以编写其他查询以在外部表*TaxiRides*上运行，并了解有关数据的更多信息。 

## <a name="optimize-your-query-performance"></a>优化查询性能

使用以下用于查询外部数据的最佳做法，在 lake 中优化查询性能。 
 
### <a name="data-format"></a>数据格式
 
使用列式格式进行分析查询，自开始：
* 只能读取与查询相关的列。 
* 列编码技术可显著减少数据大小。  
Azure 数据资源管理器支持 Parquet 和 ORC 纵栏格式。 由于优化了实现，因此建议采用 Parquet 格式。 
 
### <a name="azure-region"></a>Azure 区域 
 
确定外部数据位于 Azure 数据资源管理器群集所在的同一 Azure 区域中。 这可以降低成本和数据提取时间。
 
### <a name="file-size"></a>文件大小
 
最佳文件大小为每个文件数百 Mb （高达 1 Gb）。 避免使用需要不必要开销的许多小文件，如文件枚举过程和有限的列式格式使用。 请注意，文件数目应大于 Azure 数据资源管理器群集中的 CPU 内核数。 
 
### <a name="compression"></a>压缩
 
使用压缩可以减少从远程存储提取的数据量。 对于 Parquet 格式，请使用内部 Parquet 压缩机制，单独压缩列组，从而允许单独读取列组。 若要验证使用压缩机制，请检查文件的名称是否如下所示：<filename>"gz. parquet" 或<filename>"parquet"，而不是<filename>"parquet"）。 
 
### <a name="partitioning"></a>分区
 
使用 "文件夹" 分区组织你的数据，该分区允许查询跳过无关的路径。 在规划分区时，请考虑查询中的文件大小和常见筛选器，例如时间戳或租户 ID。
 
### <a name="vm-size"></a>VM 大小
 
选择包含更多内核和更高网络吞吐量的 VM Sku （内存不太重要）。 有关详细信息，请参阅[为 Azure 数据资源管理器群集选择正确的 VM SKU](manage-cluster-choose-sku.md)。

## <a name="next-steps"></a>后续步骤

使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据。 了解如何[编写查询](write-queries.md)并从数据派生更多见解。
