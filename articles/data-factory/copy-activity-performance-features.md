---
title: 复制活动性能优化功能
description: 了解有助于优化 Azure 数据工厂中的复制活动性能的主要功能
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
ms.openlocfilehash: a31c6229220142acea9ded571128ab54c50d34b7
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125779"
---
# <a name="copy-activity-performance-optimization-features"></a>复制活动性能优化功能

本文概述了可在 Azure 数据工厂中利用的复制活动性能优化功能。

## <a name="data-integration-units"></a>数据集成单元

数据集成单元是表示 Azure 数据工厂中单个单元的幂（CPU、内存和网络资源分配的组合）的度量值。 数据集成单元仅适用于[Azure 集成运行](concepts-integration-runtime.md#azure-integration-runtime)时，但不适用于[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。

允许复制活动运行的允许 DIUs**介于2到256之间**。 如果未指定或在用户界面上选择 "自动"，则数据工厂会根据源接收器对和数据模式动态应用最佳 DIU 设置。 下表列出了不同复制方案中受支持的 DIU 范围和默认行为：

| 复制方案 | 支持的 DIU 范围 | 服务决定的默认 DIU 数目 |
|:--- |:--- |---- |
| 文件存储之间 |- **从或复制到单个文件**：2-4 <br>- **从和复制到多个文件**：2-256，具体取决于文件的数量和大小 <br><br>例如，如果从包含4个大型文件的文件夹复制数据，并选择保留层次结构，则最大有效 DIU 为 16;选择合并文件时，最大有效 DIU 为4。 |介于4和32之间，具体取决于文件的数量和大小 |
| 从文件存储到非文件存储 |**从单个文件复制**- ：2-4 <br/>**从多个文件 - 复制**：2-256，具体取决于文件的数量和大小 <br/><br/>例如，如果从包含4个大文件的文件夹复制数据，则最大有效 DIU 为16。 |- **复制到 AZURE SQL 数据库或 Azure Cosmos DB**：4到16之间，具体取决于接收器层（dtu/ru）和源文件模式<br>使用 PolyBase 或 COPY 语句 - **复制到 Azure Synapse Analytics** ：2<br>-其他方案：4 |
| 从非文件存储到文件存储 |在写入文件夹时，- **从已启用分区选项的数据存储复制**（包括[Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)）：2-256，写入到一个文件时为2-4。 请注意，每个源数据分区最多可以使用4个 DIUs。<br>- **其他方案**：2-4 |**从 REST 或 HTTP 复制**- ：1<br/>使用 UNLOAD**从 Amazon Redshift 复制**- ：2<br>- **其他方案**：4 |
| 非文件存储之间 |在写入文件夹时，- **从已启用分区选项的数据存储复制**（包括[Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)）：2-256，写入到一个文件时为2-4。 请注意，每个源数据分区最多可以使用4个 DIUs。<br/>- **其他方案**：2-4 |**从 REST 或 HTTP 复制**- ：1<br>- **其他方案**：4 |

可以在复制活动监视视图或活动输出中看到用于每个副本运行的 DIUs。 有关详细信息，请参阅[复制活动监视](copy-activity-monitoring.md)。 若要重写此默认值，请按如下所示指定 `dataIntegrationUnits` 属性的值。 复制操作在运行时使用的*实际 DIU 数*等于或小于配置的值，具体取决于数据模式。

将向你收费 **\* 复制持续时间 \* unit price/DIU 小时**。 请参阅[此处](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)的当前价格。 每个订阅类型可能应用本地货币和单独折扣。

**示例：**

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

## <a name="self-hosted-integration-runtime-scalability"></a>自承载集成运行时可伸缩性

如果要实现更高的吞吐量，可以纵向扩展或横向扩展自承载 IR：

- 如果自承载 IR 节点上的 CPU 和可用内存未充分利用，但并发作业的执行达到了限制，则应通过增加可在节点上运行的并发作业数来向上缩放。  有关说明，请参阅[此处](create-self-hosted-integration-runtime.md#scale-up)。
- 另一方面，如果 CPU 在自承载 IR 节点上很高，或者可用内存较低，则可以添加新节点以帮助在多个节点之间横向扩展负载。  有关说明，请参阅[此处](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

请注意，在以下情况下，单个复制活动执行可以利用多个自承载 IR 节点：

- 从基于文件的存储中复制数据，具体取决于文件的数量和大小。
- 根据数据分区数，从已启用分区选项的数据存储（包括[Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [SAP HANA](connector-sap-hana.md#sap-hana-as-source)、 [SAP 表](connector-sap-table.md#sap-table-as-source)和[sap 开放式集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）复制数据。

## <a name="parallel-copy"></a>并行复制

可以在复制活动上设置并行复制（`parallelCopies` 属性），以指示希望复制活动使用的并行度。 可以将此属性视为复制活动中的最大线程数，这些线程从源读取数据或将数据写入接收器数据存储。

并行复制与[数据集成单元](#data-integration-units)或[自承载 IR 节点相互独立](#self-hosted-integration-runtime-scalability)。 它在所有 DIUs 或自承载 IR 节点上计算。

对于每个复制活动运行，默认情况下，Azure 数据工厂会根据源接收器对和数据模式动态应用最佳的并行复制设置。 

> [!TIP]
> 并行复制的默认行为通常提供最佳吞吐量，即根据源接收器对、数据模式和 DIUs 数或自承载 IR 的 CPU/内存/节点计数自动确定。 请参阅在何时优化并行复制时对[复制活动性能进行故障排除](copy-activity-performance-troubleshooting.md)。

下表列出了并行复制行为：

| 复制方案 | 并行复制行为 |
| --- | --- |
| 文件存储之间 | `parallelCopies` 确定**文件级别**的并行度。 每个文件中的分块在自动和透明地下发生。 它旨在使用给定数据存储类型最适合的块区大小来并行加载数据。 <br/><br/>并发复制活动在运行时使用的实际数量不超过您拥有的文件数。 如果复制行为**mergeFile**到文件接收器中，复制活动将无法利用文件级并行度。 |
| 从文件存储到非文件存储 | -在将数据复制到 Azure SQL 数据库或 Azure Cosmos DB 时，默认的并行复制也依赖于接收器层（Dtu/ru 的数目）。<br>-在将数据复制到 Azure 表时，默认的并行复制为4。 |
| 从非文件存储到文件存储 | -从分区启用选项的数据存储（包括[Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [SAP HANA](connector-sap-hana.md#sap-hana-as-source)、 [SAP 表](connector-sap-table.md#sap-table-as-source)和[sap 开放式集线器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）复制数据时，默认的并行复制为4。 并发复制活动在运行时使用的实际数量不超过数据分区的数目。 使用自承载 Integration Runtime 并复制到 Azure Blob/ADLS Gen2 时，请注意每个 IR 节点的最大有效并行复制为4或5。<br>-对于其他方案，并行复制不会生效。 即使指定了并行性，也不会应用它。 |
| 非文件存储之间 | -在将数据复制到 Azure SQL 数据库或 Azure Cosmos DB 时，默认的并行复制也依赖于接收器层（Dtu/ru 的数目）。<br/>-在将数据复制到 Azure 表时，默认的并行复制为4。 |

若要控制托管数据存储的计算机上的负载或优化复制性能，可以覆盖默认值并为 `parallelCopies` 属性指定值。 该值必须是大于或等于 1 的整数。 在运行时，为了获得最佳性能，复制活动使用小于或等于您设置的值的值。

当你为 `parallelCopies` 属性指定值时，会将源数据存储和接收器数据存储的负载增加。 如果复制活动可供其使用，还应考虑到自承载集成运行时的负载增加。 当对同一数据存储运行相同活动的多个活动或并发运行时，尤其会发生此负载增加。 如果注意到数据存储或自承载集成运行时占用负载，则减小 `parallelCopies` 值以减轻负载。

**示例：**

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

- **需要通过 PolyBase 将数据从各种数据存储引入 SQL 数据仓库。** SQL 数据仓库使用 PolyBase 作为高吞吐量机制，将大量数据加载到 SQL 数据仓库中。 源数据必须位于 Blob 存储或 Azure Data Lake Store 中，并且必须满足其他条件。 从 Blob 存储或 Azure Data Lake Store 以外的数据存储加载数据时，可通过过渡暂存 Blob 存储激活数据复制。 在这种情况下，Azure 数据工厂会执行所需的数据转换，以确保它满足 PolyBase 的要求。 然后，它使用 PolyBase 将数据高效加载到 SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有时，通过慢速网络连接执行混合数据移动（即从本地数据存储复制到云数据存储）需要一段时间。** 若要提高性能，可以使用暂存复制来压缩本地数据，以便减少将数据移动到云中的暂存数据存储的时间。 然后，你可以在将数据加载到目标数据存储中之前，将数据解压缩到临时存储中。
- **不需要在防火墙中打开端口80和端口443之外的端口，因为有企业 IT 策略。** 例如，将数据从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器时，需要对 Windows 防火墙和公司防火墙激活端口 1433 上的出站 TCP 通信。 在此方案中，暂存复制可以利用自承载集成运行时首先通过 HTTP 或端口443上的 HTTPS 将数据复制到 Blob 存储暂存实例。 然后，它可以将数据从 Blob 存储暂存加载到 SQL 数据库或 SQL 数据仓库。 在此流中，不需要启用端口 1433。

### <a name="how-staged-copy-works"></a>暂存复制的工作原理

激活暂存功能时，首先将数据从源数据存储复制到暂存 Blob 存储（自带）。 然后，将数据从暂存数据存储复制到接收器数据存储。 Azure 数据工厂自动管理两阶段流程。 数据移动完成后，Azure 数据工厂还会清理临时存储中的临时数据。

![暂存复制](media/copy-activity-performance/staged-copy.png)

使用暂存存储激活数据移动时，可以指定是否要在将数据从源数据存储移到临时或暂存数据存储之前压缩数据，然后在从临时或暂存移动数据之前对数据进行解压缩数据存储区到接收器数据存储。

目前，不能在通过不同的自承载 IRs 连接的两个数据存储之间复制数据，无需进行暂存复制。 对于这种情况，你可以配置两个显式链接的复制活动，以便从源复制到过渡，然后从过渡到接收器。

### <a name="configuration"></a>配置

在复制活动中配置**enableStaging**设置，以指定在将数据加载到目标数据存储之前，是否要在 Blob 存储中对数据进行暂存。 将**enableStaging**设置为 `TRUE`时，请指定下表中列出的其他属性。 还需要为过渡创建 Azure 存储或存储共享访问签名链接服务（如果没有）。

| properties | 说明 | 默认值 | 必选 |
| --- | --- | --- | --- |
| enableStaging |指定是否要通过过渡暂存存储复制数据。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 链接服务的名称，这指用作过渡暂存存储的存储实例。 <br/><br/> 不能将存储与共享访问签名一起使用，以通过 PolyBase 将数据加载到 SQL 数据仓库。 可在其他任何情况下使用它。 |空值 |将 **enableStaging** 设置为 TRUE 时，则为是 |
| 路径 |指定要包含此暂存数据的 Blob 存储路径。 如果不提供路径，服务将创建一个容器来存储临时数据。 <br/><br/> 只在使用具有共享访问签名的存储时，或者要求临时数据位于特定位置时才指定路径。 |空值 |否 |
| enableCompression |指定是否应在将数据复制到目标之前对其进行压缩。 此设置可减少传输的数据量。 |False |否 |

>[!NOTE]
> 如果在启用了压缩的情况下使用暂存复制，则不支持暂存 blob 链接服务的服务主体或 MSI 身份验证。

下面是复制活动的示例定义，其中包含上表中所述的属性：

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

按两个步骤收费：复制持续时间和复制类型。

* 在云复制期间（将数据从云数据存储复制到另一个云数据存储）使用过渡时，Azure 集成运行时所获得的两个阶段都将对 [步骤1和步骤2的复制持续时间总和] x [云复制单元价格] 进行收费。
* 在混合复制期间（将数据从本地数据存储复制到云数据存储）使用过渡时，自承载集成运行时可获得一个阶段，你需要支付 [混合复制持续时间] x [混合复制单元价格] + [云复制持续时间]x [云复制单元价格]。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能和可伸缩性指南](copy-activity-performance.md)
- [复制活动性能疑难解答](copy-activity-performance-troubleshooting.md)
- [使用 Azure 数据工厂将数据从 data lake 或数据仓库迁移到 Azure](data-migration-guidance-overview.md)
- [将数据从 Amazon S3 迁移到 Azure 存储](data-migration-guidance-s3-azure-storage.md)