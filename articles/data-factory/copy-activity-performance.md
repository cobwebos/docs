---
title: Azure 数据工厂中的复制活动性能和优化指南 |Microsoft Docs
description: 了解在使用复制活动时影响 Azure 数据工厂中数据移动性能的关键因素。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967361"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>复制活动性能和优化指南
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本:"]
> * [版本 1](v1/data-factory-copy-activity-performance.md)
> * [当前版本](copy-activity-performance.md)


Azure 数据工厂复制活动提供一流的安全、可靠且高性能的数据加载解决方案。 你可以使用它跨各种云和本地数据存储每天复制数十 tb 的数据。 快速数据加载性能是确保你可以专注于核心大数据问题的关键: 构建高级分析解决方案, 并深入了解所有这些数据。

Azure 提供了一系列企业级数据存储和数据仓库解决方案。 复制活动提供高度优化的数据加载体验, 易于配置和设置。 使用单个复制活动, 可以将数据加载到:

* 1\.2 GBps 的 Azure SQL 数据仓库。
* 1\.0 GBps 中的 Azure Blob 存储。
* 1\.0 GBps Azure Data Lake Store。

本文介绍：

* 支持的源数据存储和接收器数据存储的[性能参考数字](#performance-reference), 有助于规划项目。
* 可在不同情况下提高复制吞吐量的功能, 包括[数据集成单元](#data-integration-units)(DIUs)、[并行复制](#parallel-copy)和[暂存复制](#staged-copy)。
* [性能优化指南](#performance-tuning-steps): 如何调整性能和影响复制性能的关键因素。

> [!NOTE]
> 如果你不熟悉一般的复制活动, 请在阅读本文之前参阅[复制活动概述](copy-activity-overview.md)。
>

## <a name="performance-reference"></a>性能参考

作为参考, 下表显示了基于内部测试的单个复制活动运行中给定源和接收器对的复制吞吐量数目 (以 MBps 为单位)。 为了进行比较, 它还演示了[数据集成单元](#data-integration-units)或[自承载集成运行时可伸缩性](concepts-integration-runtime.md#self-hosted-integration-runtime)(多节点) 的不同设置如何有助于提高复制性能。

![性能矩阵](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> 当复制活动在 Azure integration runtime 上运行时, 允许的最小数据集成单元 (以前称为数据移动单元) 是两个。 如果未指定, 请参阅[数据集成单元](#data-integration-units)中使用的默认数据集成单元。

**需要注意的要点：**

* 吞吐量是使用以下公式计算的: [从源读取的数据大小]/[复制活动运行持续时间]。
* 表中的性能参考数字是通过在单个复制活动运行中使用[TPC-H](http://www.tpc.org/tpch/)数据集来衡量的。 基于文件的存储的测试文件是大小为 10 GB 的多个文件。
* 在 Azure 数据存储中，源和接收器位于同一 Azure 区域。
* 对于本地和云数据存储之间的混合复制, 每个自承载集成运行时节点都在与数据存储区不同的计算机上运行, 并且具有以下规范。 单个活动运行时，复制操作仅使用测试计算机的一小部分 CPU、内存或网络带宽。
    <table>
    <tr>
        <td>CPU</td>
        <td>32 核 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>内存</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>网络</td>
        <td>Internet 接口：10 Gbps；Intranet 接口：40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> 使用更多 DIUs 可以实现更高的吞吐量。 例如, 对于 100 DIUs, 可将数据从 Azure Blob 存储复制到 1.0 GBps 的 Azure Data Lake Store。 有关此功能和支持的方案的详细信息, 请参阅[数据集成单元](#data-integration-units)部分。 

## <a name="data-integration-units"></a>数据集成单元

数据集成单元是表示 Azure 数据工厂中单个单元的幂 (CPU、内存和网络资源分配的组合) 的度量值。 数据集成单元仅适用于[Azure 集成运行](concepts-integration-runtime.md#azure-integration-runtime)时, 但不适用于[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。

要使复制活动运行的最小 DIUs 是两个。 如果未指定，下表列出了不同复制方案中使用的默认 DIU 数目：

| 复制方案 | 服务决定的默认 DIU 数目 |
|:--- |:--- |
| 在基于文件的存储之间复制数据 | 介于4和32之间, 具体取决于文件的数量和大小 |
| 将数据复制到 Azure SQL 数据库或 Azure Cosmos DB |介于4和16之间, 具体取决于接收器 Azure SQL 数据库或 Cosmos DB 的层 (Dtu 数/ru 数) |
| 所有其他复制方案 | 4 |

若要替代此默认值，请如下所示指定 **dataIntegrationUnits** 属性的值。 **DataIntegrationUnits**属性的*允许值*最大为256。 复制操作在运行时使用的*实际 DIU 数*等于或小于配置的值，具体取决于数据模式。 有关为特定复制源和接收器配置更多单元时可能获得的性能增益级别的信息，请参阅[性能参考](#performance-reference)。

监视活动运行时, 可以在复制活动输出中看到用于每个副本运行的 DIUs。 有关详细信息, 请参阅[复制活动监视](copy-activity-overview.md#monitoring)。

> [!NOTE]
> 仅当你将多个文件从 Azure 存储、Azure Data Lake Storage、Amazon S3、Google Cloud Storage、cloud FTP 或云 SFTP 复制到任何其他云数据存储时, 才会将设置为大于4的 DIUs。
>

**示例**

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>数据集成单元计费影响

请记住, 根据复制操作的总时间进行收费。 为数据移动计费的总持续时间是 DIUs 之间的持续时间之和。 如果复制作业过去使用 2 个云单元花费 1 小时，现在使用 8 个云单元花费 15 分钟，则总费用几乎相同。

## <a name="parallel-copy"></a>并行复制

你可以使用**parallelCopies**属性来指示你希望复制活动使用的并行度。 可以将此属性视为可以从源读取或写入接收器数据存储的复制活动中的最大线程数。

对于每个复制活动运行, Azure 数据工厂会确定要用于将数据从源数据存储复制到目标数据存储的并行副本数。 它使用的默认并行复制数取决于使用的源和接收器的类型。

| 复制方案 | 由服务确定的默认并行复制计数 |
| --- | --- |
| 在基于文件的存储之间复制数据 |取决于文件大小以及用于在两个云数据存储之间复制数据的 DIUs 的数量, 或自承载集成运行时计算机的物理配置。 |
| 将数据从任何源存储复制到 Azure 表存储 |4 |
| 所有其他复制方案 |1 |

> [!TIP]
> 在基于文件的存储之间复制数据时, 默认行为通常会提供最佳吞吐量。 默认行为是根据源文件模式自动确定的。

若要控制托管数据存储的计算机上的负载或优化复制性能, 可以覆盖默认值并为**parallelCopies**属性指定一个值。 该值必须是大于或等于 1 的整数。 在运行时, 为了获得最佳性能, 复制活动使用小于或等于您设置的值的值。

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

**需要注意的要点：**

* 在基于文件的存储之间复制数据时, **parallelCopies**将确定文件级别的并行度。 单个文件内的分块会自动透明地自动执行。 它设计为对给定的源数据存储类型使用最佳的块大小, 以并行和与**parallelCopies**并行加载数据。 数据移动服务在运行时用于复制操作的并行复制的实际数量不超过所拥有的文件数。 如果复制行为是**mergeFile**, 则复制活动无法利用文件级并行度。
* 如果从不基于文件的存储 ( [Oracle](connector-oracle.md#oracle-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [Sap 表](connector-sap-table.md#sap-table-as-source)和[SAP 开放中心](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)连接器除外) 将数据复制到基于文件的存储, 则数据移动服务忽略**parallelCopies**属性。 即使指定了并行性，在此情况下也不适用。
* **ParallelCopies**属性与**dataIntegrationUnits**直角。 前者跨所有数据集成单元进行计数。
* 为**parallelCopies**属性指定值时, 请考虑源数据存储和接收器数据存储上的负载增加。 此外, 如果复制活动可供自承载集成运行时 (例如, 用于混合副本), 请考虑负载增加到自承载集成运行时。 当对同一数据存储运行相同活动的多个活动或并发运行时, 尤其会发生此负载增加。 如果注意到数据存储或自承载集成运行时占用负载, 则减小**parallelCopies**值以减轻负载。

## <a name="staged-copy"></a>暂存复制

将数据从源数据存储复制到接收器数据存储时，可能会选择使用 Blob 存储作为过渡暂存存储。 暂存在以下情况下特别有用：

- **需要通过 PolyBase 将数据从各种数据存储引入 SQL 数据仓库。** SQL 数据仓库使用 PolyBase 作为高吞吐量机制，将大量数据加载到 SQL 数据仓库中。 源数据必须位于 Blob 存储或 Azure Data Lake Store 中, 并且必须满足其他条件。 从 Blob 存储或 Azure Data Lake Store 以外的数据存储加载数据时，可通过过渡暂存 Blob 存储激活数据复制。 在这种情况下, Azure 数据工厂会执行所需的数据转换, 以确保它满足 PolyBase 的要求。 然后，它使用 PolyBase 将数据高效加载到 SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有时, 通过慢速网络连接执行混合数据移动 (即从本地数据存储复制到云数据存储) 需要一段时间。** 若要提高性能, 可以使用暂存复制来压缩本地数据, 以便减少将数据移动到云中的暂存数据存储的时间。 然后, 你可以在将数据加载到目标数据存储中之前, 将数据解压缩到临时存储中。
- **不需要在防火墙中打开端口80和端口443之外的端口, 因为有企业 IT 策略。** 例如，将数据从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器时，需要对 Windows 防火墙和公司防火墙激活端口 1433 上的出站 TCP 通信。 在此方案中, 暂存复制可以利用自承载集成运行时首先通过 HTTP 或端口443上的 HTTPS 将数据复制到 Blob 存储暂存实例。 然后, 它可以将数据从 Blob 存储暂存加载到 SQL 数据库或 SQL 数据仓库。 在此流中，不需要启用端口 1433。

### <a name="how-staged-copy-works"></a>暂存复制的工作原理

激活暂存功能时，首先将数据从源数据存储复制到暂存 Blob 存储（自带）。 然后，将数据从暂存数据存储复制到接收器数据存储。 Azure 数据工厂自动管理两阶段流程。 数据移动完成后, Azure 数据工厂还会清理临时存储中的临时数据。

![暂存复制](media/copy-activity-performance/staged-copy.png)

使用暂存存储激活数据移动时, 可以指定是否要在将数据从源数据存储移到临时或暂存数据存储区之前压缩数据, 然后在从临时或暂存 dat 移动数据之前对数据进行解压缩接收器数据存储的存储区。

目前, 不能在通过不同的自承载 IRs 连接的两个数据存储之间复制数据, 无需进行暂存复制。 对于这种情况, 你可以配置两个显式链接的复制活动, 以便从源复制到过渡, 然后从过渡到接收器。

### <a name="configuration"></a>配置

在复制活动中配置**enableStaging**设置, 以指定在将数据加载到目标数据存储之前, 是否要在 Blob 存储中对数据进行暂存。 将**enableStaging**设置为`TRUE`时, 请指定下表中列出的其他属性。 还需要为过渡创建 Azure 存储或存储共享访问签名链接服务 (如果没有)。

| 属性 | 说明 | 默认值 | 必填 |
| --- | --- | --- | --- |
| enableStaging |指定是否要通过过渡暂存存储复制数据。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 链接服务的名称，这指用作过渡暂存存储的存储实例。 <br/><br/> 不能将存储与共享访问签名一起使用, 以通过 PolyBase 将数据加载到 SQL 数据仓库。 可在其他任何情况下使用它。 |不适用 |将 **enableStaging** 设置为 TRUE 时，则为是 |
| path |指定要包含此暂存数据的 Blob 存储路径。 如果不提供路径, 服务将创建一个容器来存储临时数据。 <br/><br/> 只在使用具有共享访问签名的存储时，或者要求临时数据位于特定位置时才指定路径。 |不适用 |否 |
| enableCompression |指定是否应在将数据复制到目标之前对其进行压缩。 此设置可减少传输的数据量。 |False |否 |

>[!NOTE]
> 如果在启用了压缩的情况下使用暂存复制, 则不支持暂存 blob 链接服务的服务主体或 MSI 身份验证。

下面是复制活动的示例定义, 其中包含上表中所述的属性:

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

按两个步骤收费: 复制持续时间和复制类型。

* 在云复制期间 (将数据从云数据存储复制到另一个云数据存储) 使用过渡时, Azure 集成运行时所获得的两个阶段都将对 [步骤1和步骤2的复制持续时间总和] x [云复制单元价格] 进行收费。
* 在混合复制期间 (将数据从本地数据存储复制到云数据存储) 使用过渡时, 自承载集成运行时可获得一个阶段, 你需要支付 [混合复制持续时间] x [混合复制单元价格] + [云复制持续时间]x [云复制单元价格]。

## <a name="performance-tuning-steps"></a>性能优化步骤

执行以下步骤可使用复制活动来优化 Azure 数据工厂服务的性能。

1. **建立基线。** 在开发阶段, 通过对代表性数据示例使用复制活动来测试管道。 收集[复制活动监视](copy-activity-overview.md#monitoring)后的执行详细信息和性能特征。

2. **诊断和优化性能。** 如果观察到的性能不符合您的预期, 请确定性能瓶颈。 然后，优化性能以消除或减少瓶颈的影响。

    在某些情况下, 当你在 Azure 数据工厂中运行复制活动时, 将在 "[复制活动监视" 页](copy-activity-overview.md#monitor-visually)上看到 "性能优化提示" 消息, 如以下示例中所示。 该消息将告知你为给定的副本运行标识的瓶颈。 它还会指导你了解如何更改以提高复制吞吐量。 性能优化提示当前提供如下建议:

    - 在将数据复制到 Azure SQL 数据仓库时使用 PolyBase。
    - 当数据存储端的资源是瓶颈时, 增加 Azure Cosmos DB 请求单位或 Azure SQL 数据库 Dtu (数据库吞吐量单位)。
    - 删除不必要的暂存副本。

    性能优化规则也将逐渐丰富。

    示例：**通过性能优化提示复制到 Azure SQL 数据库**

    在此示例中, 在运行复制的过程中, Azure 数据工厂会通知接收器 Azure SQL 数据库已达到高 DTU 使用率, 从而减慢了写入操作的速度。 建议增加具有更多 Dtu 的 Azure SQL 数据库层。 

    ![包含性能优化提示的复制监视](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    此外，以下是一些常见的注意事项。 本文不涵盖性能诊断的完整说明。

   * 性能功能：
     * [并行复制](#parallel-copy)
     * [数据集成单元](#data-integration-units)
     * [暂存复制](#staged-copy)
     * [自承载集成运行时可伸缩性](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [自承载 Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [源](#considerations-for-the-source)
   * [接收器](#considerations-for-the-sink)
   * [序列化和反序列化](#considerations-for-serialization-and-deserialization)
   * [压缩](#considerations-for-compression)
   * [列映射](#considerations-for-column-mapping)
   * [其他注意事项](#other-considerations)

3. **将配置扩展到整个数据集。** 对执行结果和性能满意后, 可以扩展定义和管道, 以覆盖整个数据集。

## <a name="considerations-for-self-hosted-integration-runtime"></a>自承载集成运行时的注意事项

如果复制活动在自承载集成运行时上运行, 请注意以下事项:

**设置**：建议使用专用计算机托管 integration runtime。 请参阅[使用自承载集成运行时的注意事项](concepts-integration-runtime.md)。

**横向扩展**：具有一个或多个节点的单个逻辑自承载集成运行时可以同时为多个复制活动运行提供服务。 如果对混合数据移动的需求非常繁重, 其中包含大量并发复制活动运行或需要复制大量数据, 请考虑向[外扩展自承载集成运行时](create-self-hosted-integration-runtime.md#high-availability-and-scalability), 以便预配更多资源以提供副本。

## <a name="considerations-for-the-source"></a>有关源的注意事项

### <a name="general"></a>常规

请确保基础数据存储未被运行或对其运行的其他工作负荷占用。

有关 Microsoft 数据存储, 请参阅特定于数据存储的[监视和优化主题](#performance-reference)。 这些主题可帮助用户了解数据存储性能特征、了解如何尽量缩短响应时间以及最大化吞吐量。

* 如果将数据从 Blob 存储复制到 SQL 数据仓库, 请考虑使用 PolyBase 来提高性能。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果将数据从 HDFS 复制到 Azure Blob 存储或 Azure Data Lake Store, 请考虑使用 DistCp 来提高性能。 有关详细信息, 请参阅[使用 DistCp 从 HDFS 复制数据](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果将数据从 Redshift 复制到 Azure SQL 数据仓库、Azure BLob 存储或 Azure Data Lake Store, 请考虑使用 UNLOAD 来提高性能。 有关详细信息, 请参阅[使用 UNLOAD 从 Amazon Redshift 复制数据](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>基于文件的数据存储

* **平均文件大小和文件计数**：复制活动一次传输一个文件的数据。 在移动相同数据量的情况下，如果数据由许多小文件而不是几个大文件组成，则由于每个文件都有启动阶段，总吞吐量会较低。 如果可能, 请将小文件合并为较大的文件, 以获得更高的吞吐量。
* **文件格式和压缩**：有关提高性能的更多方法，请参阅[序列化和反序列化注意事项](#considerations-for-serialization-and-deserialization)和[压缩注意事项](#considerations-for-compression)部分。

### <a name="relational-data-stores"></a>关系数据存储

* **数据模式**：表架构会影响复制吞吐量。 如果行大小较小, 则比复制相同量的数据量更好。 原因是数据库可以更有效地检索包含较少行的较少批量数据。
* **查询或存储过程**：优化在复制活动源中指定的查询或存储过程的逻辑, 以便更有效地获取数据。

## <a name="considerations-for-the-sink"></a>有关接收器的注意事项

### <a name="general"></a>常规

请确保基础数据存储未被运行或对其运行的其他工作负荷占用。

有关 Microsoft 数据存储, 请参阅特定于数据存储的[监视和优化主题](#performance-reference)。 这些主题可帮助用户了解数据存储性能特征、了解如何尽量缩短响应时间以及最大化吞吐量。

* 如果将数据从任何数据存储复制到 Azure SQL 数据仓库, 请考虑使用 PolyBase 来提高性能。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果将数据从 HDFS 复制到 Azure Blob 存储或 Azure Data Lake Store, 请考虑使用 DistCp 来提高性能。 有关详细信息, 请参阅[使用 DistCp 从 HDFS 复制数据](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果将数据从 Redshift 复制到 Azure SQL 数据仓库、Azure Blob 存储或 Azure Data Lake Store, 请考虑使用 UNLOAD 来提高性能。 有关详细信息, 请参阅[使用 UNLOAD 从 Amazon Redshift 复制数据](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>基于文件的数据存储

* **复制行为**：如果从其他基于文件的数据存储复制数据, 复制活动将通过**copyBehavior**属性提供三个选项。 它将保留层次结构、平展层次结构或合并文件。 保留或平展层次结构有少量的性能开销或没有性能开销，但合并文件会导致性能开销增加。
* **文件格式和压缩**：有关提高性能的更多方法，请参阅[序列化和反序列化注意事项](#considerations-for-serialization-and-deserialization)和[压缩注意事项](#considerations-for-compression)部分。

### <a name="relational-data-stores"></a>关系数据存储

* **复制行为和性能含义**：可以通过不同的方式将数据写入 SQL 接收器。 在[有关将数据载入 Azure SQL 数据库的最佳做法](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database)中了解详细信息。

* **数据模式和批大小**：
  * 表架构会影响复制吞吐量。 复制相同数据量时，较大行大小会比较小行大小提供更好的性能，因为数据库可以更有效地提交较少的数据批次。
  * 复制活动在一系列批处理中插入数据。 可使用  **writeBatchSize** 属性设置批中的行数。 如果数据的行较小，可设置具有更高值的 **writeBatchSize** 属性，从更低的批开销和更高的吞吐量获益。 如果数据的行大小较大，请谨慎增加  **writeBatchSize**。 较高的值可能会导致复制失败（因为数据库负载过重）。

### <a name="nosql-stores"></a>NoSQL 存储

* 对于**表存储**：
  * **分区**：将数据写入交错分区会显着降低性能。 按分区键对源数据进行排序, 以便在一个分区之后有效地将数据插入到一个分区中。 或者, 您可以调整逻辑以便将数据写入单个分区中。

## <a name="considerations-for-serialization-and-deserialization"></a>序列化和反序列化注意事项

当输入数据集或输出数据集是文件时, 可以进行序列化和反序列化。 有关复制活动支持的文件格式的详细信息, 请参阅[支持的文件和压缩格式](supported-file-formats-and-compression-codecs.md)。

**复制行为**：

* 在基于文件的数据存储之间复制文件：
  * 当输入和输出数据集具有相同或没有文件格式设置时, 数据移动服务将在不进行任何序列化或反序列化的情况下执行*二进制副本*。 与源和接收器文件格式设置彼此不同的情况相比，这提供的吞吐量更高。
  * 如果输入和输出数据集都采用文本格式, 且仅编码类型不同, 则数据移动服务仅对转换进行编码。 它不进行任何序列化和反序列化，与二进制复制相比，这会产生一些性能开销。
  * 如果输入和输出数据集具有不同的文件格式或不同的配置 (如分隔符), 数据移动服务会反序列化源数据以进行流式传输、转换, 然后将其序列化为您所指示的输出格式。 与其他方案相比，此操作会产生更多的性能开销。
* 如果将文件复制到不是基于文件的数据存储中, 例如从基于文件的存储复制到关系存储区, 则需要序列化或反序列化步骤。 此步骤将造成大量的性能开销。

**文件格式**：选择的文件格式可能会影响复制性能。 例如，Avro 是一种将元数据与数据一起存储的压缩二进制格式。 它在 Hadoop 生态系统中对处理和查询具有广泛的支持。 Avro 对于序列化和反序列化更昂贵, 因此, 与文本格式相比, 复制吞吐量更小。 

在选择整个处理流中使用的文件格式时，应考虑全面。 开头为:

- 数据存储在何处、源数据存储区或从外部系统提取数据的格式。
- 存储、分析处理和查询的最佳格式。
- 对于报表和可视化工具, 应采用何种格式将数据导出到数据市场。

有时，读取和写入性能不是最佳的文件格式可能对于整体分析处理来说却是不错的选择。

## <a name="considerations-for-compression"></a>压缩注意事项

当输入或输出数据集是文件时, 可以将复制活动设置为在将数据写入目标时执行压缩或解压缩。 选择压缩时，请在输入/输出 (I/O) 和 CPU 之间进行权衡。 压缩数据会花费额外的计算资源。 但反过来减少了网络 I/O 和存储。 根据您的数据, 您可能会发现总的复制吞吐量会提高。

**编解码器**：每中压缩编解码器各有优点。 例如，虽然 bzip2 复制吞吐量最低，但使用 bzip2 可获得最佳的 Hive 查询性能，因为可将其拆分处理。 Gzip 是最均衡的选项, 最常使用。 选择最适合端到端方案的编解码器。

**级别**：对于每个压缩编解码器，有以下两个选择：最快压缩和最佳压缩。 最快的压缩选项可尽可能快地压缩数据, 即使生成的文件未以最佳方式压缩。 最佳压缩选项花费更多时间进行压缩，产生最小量的数据。 可对这两个选项进行测试，确定可为方案提供最佳整体性能的选项。

**注意事项**：若要在本地存储和云之间复制大量数据，请考虑使用启用了压缩的[暂存复制](#staged-copy)。 当企业网络的带宽和 Azure 服务的带宽是限制因素, 并且你希望输入数据集和输出数据集都处于未压缩形式时, 使用临时存储很有用。

## <a name="considerations-for-column-mapping"></a>列映射注意事项

您可以将复制活动中的**columnMappings**属性设置为将所有或输入列的子集映射到输出列。 数据移动服务从源读取数据后，它需要先对数据执行列映射，再将数据写入接收器。 这一额外处理会降低复制吞吐量。

如果源数据存储可查询，例如，如果存储是关系存储（如 SQL 数据库或 SQL Server），或者是 NoSQL 存储（如表存储或 Azure Cosmos DB），请考虑将列筛选和重排序逻辑推送到**查询**属性，而不使用列映射。 这样一来, 当数据移动服务从源数据存储中读取数据时, 就会发生投影, 这种情况更有效。

从[复制活动架构映射](copy-activity-schema-and-type-mapping.md)中了解详细信息。

## <a name="other-considerations"></a>其他注意事项

如果要复制的数据大小较大, 则可以调整业务逻辑以进一步对数据进行分区。 您可以计划复制活动更频繁地运行, 以减少运行的每个复制活动的数据大小。

请注意需要 Azure 数据工厂同时连接到同一数据存储的数据集和复制活动的数量。 许多并发复制作业可能会限制数据存储，并导致性能下降，复制作业内部重试，甚至在某些情况下导致执行失败。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>示例方案：从本地 SQL server 复制到 Blob 存储

**场景**：生成管道是为了以 CSV 格式将数据从本地 SQL server 复制到 Blob 存储。 要使复制作业更快，应将 CSV 文件压缩为 bzip2 格式。

**测试和分析**：复制活动的吞吐量小于 2 MBps, 这比性能基准更慢。

**性能分析和优化**：为排除性能问题，可查看数据的处理和移动方式。

- **读取数据**：集成运行时打开与 SQL Server 的连接并发送查询。 SQL Server 通过 intranet 将数据流发送到 integration runtime 来做出响应。
- **序列化和压缩数据**：集成运行时将数据流序列化为 CSV 格式, 并将数据压缩为 bzip2 流。
- **写入数据**：集成运行时通过 internet 将 bzip2 流上传到 Blob 存储。

如您所见, 数据以流式处理顺序方式进行处理和移动:SQL Server > LAN> Integration Runtime > WAN > Blob 存储。 整体性能由管道中的最小吞吐量封闭。

![数据流](./media/copy-activity-performance/case-study-pic-1.png)

以下的一个或多个因素可能会导致性能瓶颈：

* **源**：SQL Server 本身由于负载过重而吞吐量低。
* **自承载集成运行时**:
  * **LAN**：Integration Runtime 的位置离 SQL Server 计算机很远，且带宽连接低。
  * **Integration Runtime**：Integration Runtime 已达到执行以下操作的负载限制：
    * **序列化**：将数据流序列化为 CSV 格式时吞吐量缓慢。
    * **压缩**：你选择了慢速压缩编解码器 (例如, bzip2), 它使用内核 i7 2.8 MBps。
  * **WAN**：企业网络和 Azure 服务之间的带宽较低, 例如 T1 = 1544 kbps;T2 = 6312 kbps。
* **接收器**：Blob 存储吞吐量低。 这种情况不太可能是因为其服务级别协议 (SLA) 保证最小 60 MBps。

在这种情况下，bzip2 数据压缩可能会拖慢整个管道的速度。 切换到 gzip 压缩编解码器可能会缓解此瓶颈。

## <a name="references"></a>参考资料

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure 存储, 包括 Blob 存储和表存储:[Azure 存储可伸缩性目标](../storage/common/storage-scalability-targets.md)和[azure 存储性能和可伸缩性清单](../storage/common/storage-performance-checklist.md)。
* Azure SQL 数据库：可以[监视性能](../sql-database/sql-database-single-database-monitor.md)并检查数据库事务单位 (DTU) 百分比。
* Azure SQL 数据仓库：其功能以数据仓库单位 (Dwu) 度量。 请参阅[管理 AZURE SQL 数据仓库中的计算能力 (概述)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的性能级别](../cosmos-db/performance-levels.md)。
* 本地 SQL Server：[监视和优化性能](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地文件服务器：[文件服务器的性能优化](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章:

- [复制活动概述](copy-activity-overview.md)
- [复制活动架构映射](copy-activity-schema-and-type-mapping.md)
- [复制活动容错](copy-activity-fault-tolerance.md)
