---
title: 使用 Azure 数据资源管理器在 Azure 数据湖中查询数据
description: 了解如何使用 Azure 数据资源管理器查询 Azure 数据湖中的数据。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161743"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>使用 Azure 数据资源管理器在 Azure 数据湖中查询数据

Azure 数据湖存储是一种高度可扩展且经济高效的数据湖解决方案，适用于大数据分析。 它将高性能文件系统的强大功能与大规模和经济性相结合，帮助您加快洞察速度。 数据存储第 2 代扩展了 Azure Blob 存储功能，并针对分析工作负荷进行了优化。
 
Azure 数据资源管理器与 Azure Blob 存储和 Azure 数据湖存储（第 1 代和 Gen2）集成，提供对湖中数据的快速、缓存和索引访问。 您可以分析和查询湖中的数据，而无需事先引入 Azure 数据资源管理器。 您还可以同时查询已引入和未引入的原生湖数据。  

> [!TIP]
> 最佳的查询性能需要将数据引入 Azure 数据资源管理器。 无需事先引入即可查询外部数据的功能应仅用于很少查询的历史数据或数据。 [优化湖中的查询性能，](#optimize-your-query-performance)获得最佳效果。
 

## <a name="create-an-external-table"></a>创建外部表

 > [!NOTE]
 > 当前支持的存储帐户是 Azure Blob 存储或 Azure 数据湖存储（第 1 代和第 2 代）。

1. 使用`.create external table`命令在 Azure 数据资源管理器中创建外部表。 其他外部表命令（如`.show` `.drop`，和`.alter`）在[外部表命令](/azure/kusto/management/externaltables)中记录。

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
    > * 通过更精细的分区，预期性能会提高。 例如，对具有每日分区的外部表的查询将比那些具有每月分区表的查询具有更好的性能。
    > * 当您使用分区定义外部表时，存储结构应相同。
例如，如果表使用日期时间分区以 yyyyy/MM/dd 格式（默认）定义，则 URI 存储文件路径应为*容器1/yyyy/MM/dd/all_exported_blobs*。 
    > * 如果外部表按 datetime 列进行分区，则始终在查询中包含关闭范围的时间筛选器（例如，查询 - `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` - - 应比此（打开的范围）1 -`ArchivedProducts | where Timestamp > ago(1h)`执行得更好。 
    > * 可以使用外部表查询所有[受支持的引入格式](ingest-data-overview.md#supported-data-formats)。

1. 外部表在 Web UI 的左侧窗格中可见

    ![Web UI 中的外部表](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>创建具有 json 格式的外部表

您可以创建具有 json 格式的外部表。 有关详细信息，请参阅[外部表命令](/azure/kusto/management/externaltables)

1. 使用`.create external table`命令创建名为 *"外部表Jon"的*表：

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
 
1. Json 格式需要第二步创建映射到列，如下所示。 在以下查询中，创建名为*映射名称*的特定 json 映射：

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部表权限
 
* 数据库用户可以创建外部表。 表创建者自动成为表管理员。
* 群集、数据库或表管理员可以编辑现有表。
* 任何数据库用户或读取器都可以查询外部表。
 
## <a name="query-an-external-table"></a>查询外部表
 
要查询外部表，请使用`external_table()`函数，并将表名称作为函数参数提供。 查询的其余部分是标准的 Kusto 查询语言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部表查询当前不支持 Intellisense。

### <a name="query-an-external-table-with-json-format"></a>使用 json 格式查询外部表

要查询具有 json 格式的外部表，请使用`external_table()`函数，并提供表名称和映射名称作为函数参数。 在下面的查询中，如果未指定*映射名称*，将使用以前创建的映射。

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>一起查询外部数据和引入数据

可以在同一查询中查询外部表和引入的数据表。 您[`join`](/azure/kusto/query/joinoperator)或[`union`](/azure/kusto/query/unionoperator)外部表具有来自 Azure 数据资源管理器、SQL 服务器或其他源的其他数据。 使用[`let( ) statement`](/azure/kusto/query/letstatement)将速记名称分配给外部表引用。

在下面的示例中，*产品*是一个引入的数据表，*存档产品*是一个外部表，其中包含 Azure 数据湖存储 Gen2 中的数据：

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查询帮助群集中的*出租车*外部表

*出租车样本*数据集包含纽约市[出租车和豪华轿车委员会](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)提供的数据。

### <a name="create-external-table-taxirides"></a>创建外部表*出租车* 

> [!NOTE]
> 本节描述用于在*帮助*群集中创建*TaxiRides*外部表的查询。 由于此表已经创建，您可以跳过此部分并执行查询[*TaxiRides*外部表数据](#query-taxirides-external-table-data)。 

1. 以下查询用于在帮助群集中创建外部表*TaxiRides。* 

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
1. 生成的表是在*帮助*群集中创建的：

    ![出租车外部表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查询*出租车*外部表数据 

登录以[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)查询*出租车外部*表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查询*出租车*外部表，无需分区

在外部表*TaxiRides*上运行[此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)，以描述整个数据集中每周每一天的骑行情况。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查询显示一周中最繁忙的一天。 由于数据未分区，此查询可能需要很长时间才能返回结果（最多几分钟）。

![呈现非分区查询](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>查询带分区的外部表 

在外部表*TaxiRides*上运行[此查询](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)，显示 2017 年 1 月使用的出租车类型（黄色或绿色）。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查询使用分区，从而优化查询时间和性能。 查询筛选分区列 （pickup_datetime）并返回结果在几秒钟内。

![渲染分区查询](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以编写其他查询以在外部表*TaxiRides 上运行*，并了解有关数据的更多信息。 

## <a name="optimize-your-query-performance"></a>优化查询性能

使用以下最佳实践查询外部数据，优化湖中的查询性能。 
 
### <a name="data-format"></a>数据格式
 
使用柱格式进行分析查询，因为：
* 只能读取与查询相关的列。 
* 列编码技术可以显著减小数据大小。  
Azure 数据资源管理器支持镶木地板和 ORC 列格式。 由于优化的实施，建议采用镶木地板格式。 
 
### <a name="azure-region"></a>Azure 区域 
 
确定外部数据与 Azure 数据资源管理器群集位于同一 Azure 区域中。 这减少了成本和数据提取时间。
 
### <a name="file-size"></a>文件大小
 
最佳文件大小为每个文件数百 Mb（最多 1 Gb）。 避免许多需要不需要开销的小文件，例如文件枚举过程变慢和列格式使用有限。 请注意，文件数应大于 Azure 数据资源管理器群集中的 CPU 内核数。 
 
### <a name="compression"></a>压缩
 
使用压缩来减少从远程存储提取的数据量。 对于 Parquet 格式，请使用内部 Parquet 压缩机制单独压缩列组，从而允许您单独读取它们。 要验证压缩机制的使用，请检查文件是否命名为如下<filename>：".gz.parquet"或".snappy.parquet"，<filename>而不是".parquet.gz"。<filename> 
 
### <a name="partitioning"></a>分区
 
使用"文件夹"分区组织数据，使查询能够跳过不相关的路径。 规划分区时，请考虑查询中的文件大小和常见筛选器，如时间戳或租户 ID。
 
### <a name="vm-size"></a>VM 大小
 
选择具有更多内核和更高网络吞吐量的 VM SKU（内存不太重要）。 有关详细信息[，请参阅为 Azure 数据资源管理器群集选择正确的 VM SKU。](manage-cluster-choose-sku.md)

## <a name="next-steps"></a>后续步骤

使用 Azure 数据资源管理器查询 Azure 数据湖中的数据。 学习[编写查询](write-queries.md)并从数据中获取其他见解。
