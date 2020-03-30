---
title: 复制活动性能优化功能
description: 了解可帮助您优化 Azure 数据工厂中的复制活动性能的关键功能。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/09/2020
ms.openlocfilehash: d37b4648c0a37f16fe5c9d8794bd78417c5780ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257880"
---
# <a name="copy-activity-performance-optimization-features"></a>复制活动性能优化功能

本文概述了可在 Azure 数据工厂中利用的复制活动性能优化功能。

## <a name="data-integration-units"></a>数据集成单元

数据集成单元是一种度量单位，代表单个单位在 Azure 数据工厂中的能力（包含 CPU、内存、网络资源分配）。 数据集成单元仅适用于 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)，而不适用于[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。

允许用来为复制活动运行提供支持的 DIU 数为 **2 到 256 个**。 如果未指定该数目或者在 UI 中选择“自动”，则数据工厂将根据源-接收器对和数据模式动态应用最佳的 DIU 设置。 下表列出了不同复制方案中支持的 DIU 范围和默认行为：

| 复制方案 | 支持的 DIU 系列 | 服务决定的默认 DIU 数目 |
|:--- |:--- |---- |
| 文件存储之间 |- **从文件复制或复制到单个文件**：2-4 <br>- **从多个文件复制和复制到多个文件**：2-256 取决于文件的数量和大小 <br><br>例如，如果从包含 4 个大型文件的文件夹中复制数据并选择保留层次结构，则最大有效 DIU 为 16;如果从包含 4 个大型文件的文件夹中复制数据，则最大有效 DIU 为 16;如果从具有 4 个大型文件的文件夹中复制数据，则最大有效 DIU 为 16。当您选择合并文件时，最大有效 DIU 为 4。 |4 到 32 个，具体取决于文件的数量和大小 |
| 从文件存储到非文件存储 |- **从单个文件复制**： 2-4 <br/>- **从多个文件复制**： 2-256 取决于文件的数量和大小 <br/><br/>例如，如果从包含 4 个大型文件的文件夹中复制数据，则最大有效 DIU 为 16。 |- **复制到 Azure SQL 数据库或 Azure Cosmos DB：** 根据接收器层 （DTUs/RUs） 和源文件模式，在 4 和 16 之间<br>- 使用 PolyBase 或 COPY 语句**复制到 Azure 同步分析**：2<br>- 其他方案： 4 |
| 从非文件存储到文件存储 |- **从支持分区选项的数据存储**（包括[Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata）](connector-teradata.md#teradata-as-source)复制：写入文件夹时为 2-256，写入单个文件时为 2-4。 请注意，每个源数据分区最多可以使用 4 个 DIDI。<br>- **其他方案**： 2-4 |- **从 REST 或 HTTP 复制**： 1<br/>- 使用 UnLOAD**从亚马逊红移复制**： 2<br>- **其他方案**： 4 |
| 在非文件存储之间 |- **从支持分区选项的数据存储**（包括[Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata）](connector-teradata.md#teradata-as-source)复制：写入文件夹时为 2-256，写入单个文件时为 2-4。 请注意，每个源数据分区最多可以使用 4 个 DIDI。<br/>- **其他方案**： 2-4 |- **从 REST 或 HTTP 复制**： 1<br>- **其他方案**： 4 |

您可以在复制活动监视视图或活动输出中查看用于每次运行副本的 DIU。 有关详细信息，请参阅[复制活动监视](copy-activity-monitoring.md)。 要覆盖此默认值，请指定`dataIntegrationUnits`属性的值，如下所示。 复制操作在运行时使用的*实际 DIU 数*等于或小于配置的值，具体取决于数据模式。

计费公式为 (已用 DIU 数) \* (复制持续时间) \* (单价/DIU 小时数)。**** [此网页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)上提供了当前价格。 可能会按订阅类型应用本地货币和不同的折扣。

**例子：**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>自托管集成运行时可扩展性

如果要实现更高的吞吐量，可以向上扩展或扩展自承载的 IR：

- 如果未充分利用自承载 IR 节点上的 CPU 和可用内存，但并发作业的执行达到限制，则应通过增加可在节点上运行的并发作业数来向上扩展。  有关说明，请参阅[此文](create-self-hosted-integration-runtime.md#scale-up)。
- 另一方面，如果自承载的 IR 节点上的 CPU 较高或可用内存不足，则可以添加新节点以帮助跨多个节点扩展负载。  有关说明，请参阅[此文](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

请注意，在以下方案中，单个复制活动执行可以利用多个自承载的 IR 节点：

- 根据文件的数量和大小，从基于文件的存储复制数据。
- 根据数据分区的数量，从支持分区选项的数据存储（包括[Oracle、Netezza、Teradata、SAP](connector-netezza.md#netezza-as-source) [Oracle](connector-oracle.md#oracle-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和[SAP 开放集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）复制数据。 [Teradata](connector-teradata.md#teradata-as-source)

## <a name="parallel-copy"></a>并行复制

您可以在复制活动上设置并行`parallelCopies`复制 （属性），以指示您希望复制活动使用的并行性。 可以将此属性视为复制活动中从源读取或并行写入接收器数据存储的最大线程数。

并行副本是数据[集成单元](#data-integration-units)或[自承载的 IR 节点的](#self-hosted-integration-runtime-scalability)正交。 它跨所有 DIA 或自承载的 IR 节点计数。

对于运行的每个复制活动，默认情况下，Azure 数据工厂会基于源接收器对和数据模式动态应用最佳并行复制设置。 

> [!TIP]
> 并行复制的默认行为通常为您提供最佳吞吐量，这是 ADF 根据源接收器对、数据模式和 DIA 数或自承载 IR 的 CPU/内存/节点计数自动确定的。 请参阅在何时调整并行[副本时对复制活动性能进行故障排除](copy-activity-performance-troubleshooting.md)。

下表列出了并行复制行为：

| 复制方案 | 并行复制行为 |
| --- | --- |
| 文件存储之间 | `parallelCopies`确定**文件级别的**并行性。 每个文件中的分块自动和透明地发生在下面。 它旨在对给定数据存储类型使用最佳合适的块大小来并行加载数据。 <br/><br/>活动在运行时使用的并行副本活动的实际数量不超过您拥有的文件数。 如果复制行为将**File合并**到文件接收器中，则复制活动无法利用文件级并行性。 |
| 从文件存储到非文件存储 | - 将数据复制到 Azure SQL 数据库或 Azure Cosmos DB 时，默认并行副本还取决于接收器层（DTUs/RUs 的数量）。<br>- 将数据复制到 Azure 表时，默认并行副本为 4。 |
| 从非文件存储到文件存储 | - 从支持分区选项的数据存储（包括[Oracle](connector-oracle.md#oracle-as-source)Oracle、Netezza、Teradata、SAP [Netezza](connector-netezza.md#netezza-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和 SAP[开放集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）复制数据时，默认并行复制为 4。 [Teradata](connector-teradata.md#teradata-as-source) 运行时使用的并行副本活动的实际数量不超过您拥有的数据分区数。 当使用自托管集成运行时并复制到 Azure Blob/ADLS Gen2 时，请注意最大有效并行副本是每个 IR 节点 4 或 5。<br>- 对于其他方案，并行副本不会生效。 即使指定了并行性，也不会应用它。 |
| 在非文件存储之间 | - 将数据复制到 Azure SQL 数据库或 Azure Cosmos DB 时，默认并行副本还取决于接收器层（DTUs/RUs 的数量）。<br/>- 从支持分区选项的数据存储（包括[Oracle](connector-oracle.md#oracle-as-source)Oracle、Netezza、Teradata、SAP [Netezza](connector-netezza.md#netezza-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和 SAP[开放集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）复制数据时，默认并行复制为 4。 [Teradata](connector-teradata.md#teradata-as-source)<br>- 将数据复制到 Azure 表时，默认并行副本为 4。 |

要控制托管数据存储的计算机上负载，或调整复制性能，可以重写默认值并为属性`parallelCopies`指定值。 该值必须是大于或等于 1 的整数。 在运行时，为了获得最佳性能，复制活动使用小于或等于所设置的值。

指定`parallelCopies`属性的值时，将源和接收器数据存储的负载增加考虑在内。 如果复制活动受其授权，请考虑对自托管集成运行时的负载增加。 尤其在有多个活动或针对同一数据存储运行的相同活动有并发运行时，会发生这种负载增加的情况。 如果您注意到数据存储或自托管集成运行时因负载而不堪重负，请减小`parallelCopies`该值以释放负载。

**例子：**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>暂存复制

将数据从源数据存储复制到接收器数据存储时，可能会选择使用 Blob 存储作为过渡暂存存储。 暂存在以下情况下特别有用：

- **您希望通过 PolyBase 将数据从各种数据存储引入 SQL 数据仓库。** SQL 数据仓库使用 PolyBase 作为高吞吐量机制，将大量数据加载到 SQL 数据仓库中。 源数据必须位于 Blob 存储或 Azure 数据湖存储中，并且必须满足其他条件。 从 Blob 存储或 Azure Data Lake Store 以外的数据存储加载数据时，可通过过渡暂存 Blob 存储激活数据复制。 在这种情况下，Azure 数据工厂会执行所需的数据转换，确保其满足 PolyBase 的要求。 然后，它使用 PolyBase 将数据高效加载到 SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有时，通过缓慢的网络连接执行混合数据移动（即从本地数据存储复制到云数据存储）需要一段时间。** 为了提高性能，可以使用暂存复制来压缩本地数据，缩短将数据移动到云中的暂存数据存储的时间。 然后，可先在暂存存储中解压缩数据，再将它们加载到目标数据存储。
- **由于公司 IT 策略，您不希望在防火墙中打开端口 80 和端口 443 以外的端口。** 例如，将数据从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器时，需要对 Windows 防火墙和公司防火墙激活端口 1433 上的出站 TCP 通信。 在这种情况下，暂存复制可以利用自承载集成运行时首先在端口 443 上通过 HTTP 或 HTTPS 将数据复制到 Blob 存储暂存实例。 然后，它可以将数据从 Blob 暂存存储加载到 SQL 数据库或 SQL 数据仓库中。 在此流中，不需要启用端口 1433。

### <a name="how-staged-copy-works"></a>暂存复制的工作原理

激活暂存功能时，首先将数据从源数据存储复制到暂存 Blob 存储（自带）。 然后，将数据从暂存数据存储复制到接收器数据存储。 Azure 数据工厂自动管理两阶段流。 数据移动完成后，Azure 数据工厂还将清除暂存存储中的临时数据。

![暂存复制](media/copy-activity-performance/staged-copy.png)

使用暂存存储激活数据移动时，可指定是否要先压缩数据，再将数据从源数据存储移动到过渡数据存储或暂存数据存储，然后先解压缩数据，再将数据从过渡数据存储或暂存数据移动到接收器数据存储。

目前，无论是否使用暂存复制，都无法在通过不同自承载 IR 连接的两个数据存储之间复制数据。 对于这种情况，可以配置两个显式链接的复制活动，将数据从源复制到暂存存储，然后从暂存存储复制到接收器。

### <a name="configuration"></a>Configuration

在复制活动中配置 **enableStaging** 设置，指定在将数据加载到目标数据存储之前是否要在 Blob 存储中暂存。 将 **enableStaging** 设置为 `TRUE` 时，请指定下表中列出的其他属性。 如果未指定，则还需要创建 Azure 存储或存储共享访问签名链接服务供暂存用。

| properties | 描述 | 默认值 | 必选 |
| --- | --- | --- | --- |
| enableStaging |指定是否要通过过渡暂存存储复制数据。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 链接服务的名称，这指用作过渡暂存存储的存储实例。 <br/><br/> 无法使用具有共享访问签名的存储通过 PolyBase 将数据加载到 SQL 数据仓库。 可在其他任何情况下使用它。 |空值 |将 **enableStaging** 设置为 TRUE 时，则为是 |
| 路径 |指定要包含此暂存数据的 Blob 存储路径。 如果不提供路径，该服务将创建容器以存储临时数据。 <br/><br/> 只在使用具有共享访问签名的存储时，或者要求临时数据位于特定位置时才指定路径。 |空值 |否 |
| enableCompression |指定是否应先压缩数据，再将数据复制到目标。 此设置可减少传输的数据量。 |False |否 |

>[!NOTE]
> 若使用暂存复制并启用压缩，则不支持对暂存 Blob 链接服务的服务主体或 MSI 身份验证。

以下是具有上表所述属性的复制活动的示例定义：

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>暂存复制计费影响

基于两个步骤进行计费：复制持续时间和复制类型。

* 在云复制期间（将数据从一个云数据存储复制到另一个云数据存储，两个阶段均由 Azure Integration Runtime 提供支持）使用暂存时，需要支付 [步骤 1 和步骤 2 的复制持续时间总和] x [云复制单元价格]。
* 在混合复制期间（将数据从本地数据存储复制到云数据存储，一个阶段由自承载集成运行时提供支持）使用暂存时，需要支付 [混合复制持续时间] x [混合复制单元价格] + [云复制持续时间] x [云复制单元价格]。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能和可伸缩性指南](copy-activity-performance.md)
- [排除复制活动性能的故障](copy-activity-performance-troubleshooting.md)
- [使用 Azure 数据工厂将数据从 Data Lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)