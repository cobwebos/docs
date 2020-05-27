---
title: 关于使用 Azure Synapse Analytics 中 SQL On-Demand（预览）的最佳做法
description: 使用 SQL On-Demand（预览）时应知道的建议和最佳做法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 86678365d1510199247e8a1aaa48ec844d07de32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592927"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>关于使用 Azure Synapse Analytics 中 SQL On-Demand（预览）的最佳做法

本文介绍了一组关于使用 SQL On-Demand（预览）的最佳做法。 SQL On-Demand 是 Azure Synapse Analytics 中的额外资源。

## <a name="general-considerations"></a>一般注意事项

借助 SQL On-Demand，可以查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能。 因此，所有作为查询目标的文件都是 SQL On-Demand 外部的。 与从存储中读取文件相关的所有操作都可能会对查询性能产生影响。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>归置 Azure 存储帐户和 SQL On-Demand

为了最大限度地减少延迟，请归置 Azure 存储帐户和 SQL On-Demand 终结点。 在创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果使用 SQL On-Demand 访问其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域，那么远程区域和终结点区域之间的数据网络传输延迟将会增加。

## <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用和服务可以访问你的存储帐户。 如果应用、服务和 SQL On-Demand 工作负荷生成的合并 IOPS 或吞吐量超出存储帐户上限，就会发生存储限制。 这样一来，查询性能会受到严重不利影响。

一旦检测到存储限制，SQL On-Demand 就会内置处理这种情况。 SQL On-Demand 会以较慢的节奏向存储发出请求，直到限制消除。

> [!TIP]
> 为了获得最佳的查询执行效果，不得在查询执行期间让存储帐户负重执行其他工作负荷。

## <a name="prepare-files-for-querying"></a>准备文件以供查询

如果可以，尽可能准备文件来提升性能：

- 将 CSV 和 JSON 转换为 Parquet（Parquet 是分栏格式）。 由于经过压缩，它的文件大小小于包含相同数据的 CSV 或 JSON 文件。 SQL On-Demand 读取此类文件所需的时间和存储请求会更少。
- 如果查询目标是一个大文件，那么把它拆分为多个较小文件将会让你受益匪浅。
- 尽量让 CSV 文件大小小于 10GB。
- 对于单个 OPENROWSET 路径或外部表 LOCATION，最好有相等大小的文件。
- 通过将分区存储到不同的文件夹或文件名，对数据进行分区（请查看[使用 filename 和 filepath 函数定目标到特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)）。

## <a name="push-wildcards-to-lower-levels-in-path"></a>将通配符推送到路径中的较低级别

可以在路径中使用通配符来[查询多个文件和文件夹](develop-storage-files-overview.md#query-multiple-files-or-folders)。 SQL On-Demand 使用存储 API 列出存储帐户中与第一个 * 后面的指定路径匹配的文件，并删除与指定路径不匹配的文件。 如果有多个文件与第一个通配符后面的指定路径匹配，减少初始文件列表可以提升性能。

## <a name="use-appropriate-data-types"></a>使用适当的数据类型

查询中使用的数据类型会影响性能。 以下做法可以提升性能： 

- 使用可容纳最大可能值的最小数据大小。
  - 如果最大字符值长度为 30 个字符，请使用长度为 30 的字符数据类型。
  - 如果所有字符列值都是固定大小的，请使用 char 或 nchar。 否则，请使用 varchar 或 nvarchar。
  - 如果最大整数列值为 500，请使用 smallint，因为它是可容纳此值的最小数据类型。 可以在[此处](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)找到整数数据类型范围。
- 如果可以，尽可能使用 varchar 和 char，而不是 nvarchar 和 nchar。
- 如果可以，尽可能使用基于整数的数据类型。 排序、联接和分组操作在整数上的执行速度比字符数据快。
- 如果使用的是架构推理，请查看[推理数据类型](#check-inferred-data-types)。

## <a name="check-inferred-data-types"></a>查看推理数据类型

[架构推理](query-parquet-files.md#automatic-schema-inference)有助于快速编写查询，并浏览数据，而无需了解文件架构。 这种简便的代价是，推理数据类型大于实际数据类型。 当源文件中没有足够的信息来确保使用适当的数据类型时，就会发生这种情况。 例如，Parquet 文件不包含关于最大字符列长度的元数据，SQL On-Demand 将它推理为 varchar(8000)。 

可以使用 [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) 来查看得到的查询数据类型。

下面的示例展示了如何优化推理数据类型。 使用了过程来显示推理数据类型。 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

下面是结果集：

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

知道查询的推理数据类型后，就可以指定相应的数据类型：

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函数定目标到特定分区

数据通常是以分区形式组织。 可以指示 SQL On-Demand 查询特定文件夹和文件。 此函数可减少查询需要读取和处理的文件数和数据量。 额外的好处是，将会提升性能。

有关详细信息，请查看 [filename](develop-storage-files-overview.md#filename-function) 和 [filepath](develop-storage-files-overview.md#filepath-function) 函数，以及有关如何[查询特定文件](query-specific-files.md)的示例。

> [!TIP]
> 请始终将 filepath 和 fileinfo 函数的结果强制转换为适当的数据类型。 如果使用字符数据类型，请确保使用了适当的长度。

> [!NOTE]
> 除了为 Apache Spark for Azure Synapse Analytics 中创建的每个表自动创建的表以外，外部表暂不支持用于删除分区的 filepath 和 fileinfo 函数。

如果存储的数据没有进行分区，不妨对它进行分区，这样就可以使用这些函数来优化以这些文件为目标的查询。 如果你在 SQL On-Demand 中[查询已分区 Spark 表](develop-storage-files-spark-tables.md)，查询会自动只以所需文件为目标。

## <a name="use-parser_version-20-for-querying-csv-files"></a>使用 PARSER_VERSION 2.0 查询 CSV 文件

查询 CSV 文件时，可以使用性能优化的分析器。 有关详细信息，请查看 [PARSER_VERSION](develop-openrowset.md)。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md) 是 SQL On-Demand 中最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据，并将 SELECT 查询结果导出到存储帐户中的一组文件。

可以使用 CETAS 将查询的常用部分（如联接的引用表）存储到一组新的文件中。 接下来，可以联接到这一个外部表，而不是在多个查询中重复常用联接。

随着 CETAS 生成 Parquet 文件，统计信息将会在第一个查询以此外部表为目标时自动创建，从而提升性能。

## <a name="aad-pass-through-performance"></a>AAD 直通性能

借助 SQL On-Demand，可以使用 AAD 直通或 SAS 凭据来访问存储中的文件。 与 SAS 相比，使用 AAD 直通可能会降低性能。 

在 AAD 直通性能得到改善前，如需提升性能，请尝试使用 SAS 凭据来访问存储。

## <a name="next-steps"></a>后续步骤

有关常见问题和解决方案，请查阅[故障排除](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。 如果使用的是 SQL 池，而不是 SQL On-Demand，请参阅[关于使用 SQL 池的最佳做法](best-practices-sql-pool.md)一文，以了解具体指指南。
