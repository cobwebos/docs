---
title: Azure 数据工厂中的复制活动性能和优化指南 |Microsoft Docs
description: 了解如何使用复制活动时，会影响 Azure 数据工厂中的数据移动性能的关键因素。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 3ea89e9f6a6bb8a4c377c70bbe1b5540d3b74d44
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341250"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>复制活动性能和优化指南
> [!div class="op_single_selector" title1="选择要使用的 Azure 数据工厂的版本："]
> * [版本 1](v1/data-factory-copy-activity-performance.md)
> * [当前版本](copy-activity-performance.md)


Azure 数据工厂复制活动提供一流的安全、 可靠且高性能数据，将加载解决方案。 您可以使用它到数万亿字节的数据跨多个丰富每天的副本在本地和云数据存储。 快速的数据加载性能是确保你可以专注于核心大数据问题的关键： 构建高级的分析解决方案和从所有数据获得深入见解。

Azure 提供一系列企业级数据存储和数据仓库解决方案。 复制活动提供了高度优化的数据加载体验，易于配置和设置。 包含单个复制活动，您可以将数据加载到：

* 在 1.2 GBps 的 azure SQL 数据仓库。
* 在 1.0 GBps 的 azure Blob 存储。
* 在 1.0 GBps 的 azure Data Lake Store。

本文介绍：

* [性能参考数字](#performance-reference)有关支持的源和接收器数据存储来帮助你规划你的项目。
* 功能可以提高复制吞吐量在不同的方案，其中包括[数据集成单位](#data-integration-units)(DIUs)[并行复制](#parallel-copy)，并[暂存复制](#staged-copy)。
* [性能优化指南](#performance-tuning-steps)如何优化性能以及可能会影响复制性能的关键因素。

> [!NOTE]
> 如果不熟悉的复制活动在一般情况下，请参阅[复制活动概述](copy-activity-overview.md)阅读这篇文章之前。
>

## <a name="performance-reference"></a>性能参考

作为参考下, 表显示的复制吞吐量数目以 mbps 为单位的给定源和接收器对单个复制活动运行中的基于内部测试。 有关比较，它还演示了如何将不同的设置[数据集成单位](#data-integration-units)或[自承载集成运行时可伸缩性](concepts-integration-runtime.md#self-hosted-integration-runtime)（多个节点） 可帮助复制性能。

![性能矩阵](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> 当复制活动在 Azure 集成运行时上运行时，最小允许的数据集成单位 （以前称为数据移动单元数） 为两个。 如果未指定，请参阅中所用的默认数据集成单位[数据集成单位](#data-integration-units)。

**需要注意的要点：**

* 通过使用以下公式计算吞吐量: [从源读取数据的大小] / [复制活动运行持续时间]。
* 表中的性能参考数字使用测量得出[TPC-H](http://www.tpc.org/tpch/)单次复制活动运行中的数据集。 有 10 GB 大小的多个文件的基于文件的存储的测试文件。
* 在 Azure 数据存储中，源和接收器位于同一 Azure 区域。
* 在本地和云之间的混合复制的数据存储是独立于数据存储区具有以下规格的计算机上运行自承载的集成运行时的每个节点。 单个活动运行时，复制操作仅使用测试计算机的一小部分 CPU、内存或网络带宽。
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
> 可以通过使用多个 DIUs 实现更高的吞吐量。 例如，使用 100 DIUs，您可以将数据复制从 Azure Blob 存储到 Azure Data Lake Store 在 1.0 GBps。 有关此功能和受支持的方案的详细信息，请参阅[数据集成单位](#data-integration-units)部分。 

## <a name="data-integration-units"></a>数据集成单位

数据集成单位是度量值，代表单个单位在 Azure 数据工厂的能力 （CPU、 内存和网络资源分配的组合）。 数据集成单元仅适用于[Azure 集成运行时](concepts-integration-runtime.md#azure-integration-runtime)，但不是[自承载集成运行时](concepts-integration-runtime.md#self-hosted-integration-runtime)。

若要让复制活动运行的最小 DIUs 为两个。 如果未指定，下表列出了不同复制方案中使用的默认 DIU 数目：

| 复制方案 | 服务决定的默认 DIU 数目 |
|:--- |:--- |
| 在基于文件的存储之间复制数据 | 介于 4 和根据的数量和大小的文件的 32 之间 |
| 所有其他复制方案 | 4 |

若要替代此默认值，请如下所示指定 **dataIntegrationUnits** 属性的值。 *允许的值*有关**dataIntegrationUnits**属性是最多 256 个。 复制操作在运行时使用的*实际 DIU 数*等于或小于配置的值，具体取决于数据模式。 有关为特定复制源和接收器配置更多单元时可能获得的性能增益级别的信息，请参阅[性能参考](#performance-reference)。

您可以看到 DIUs 用于监视活动运行时在复制活动输出中运行每个副本。 有关详细信息，请参阅[将复制活动监视](copy-activity-overview.md#monitoring)。

> [!NOTE]
> 设置的 DIUs 大于四个当前仅适用于将多个文件复制从 Azure 存储、 Azure Data Lake 存储、 Amazon S3、 Google 云存储，云 FTP，或任何其他云数据存储到云 SFTP。
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

请记住，要根据对你收费的复制操作的总时间。 数据移动的总持续时间向你收费均 DIUs 的持续时间总和。 如果复制作业过去使用 2 个云单元花费 1 小时，现在使用 8 个云单元花费 15 分钟，则总费用几乎相同。

## <a name="parallel-copy"></a>并行复制

可以使用**parallelCopies**属性以指示你想要使用的复制活动的并行度。 可以将此属性视为最大的复制活动内，可以从您的源读取或写入到接收器数据存储在并行线程数。

对于每个复制活动运行，Azure 数据工厂确定要用于将数据从源数据存储和目标数据存储复制的并行复制数。 默认使用的并行复制数取决于源和接收器使用的类型。

| 复制方案 | 由服务确定的默认并行复制计数 |
| --- | --- |
| 在基于文件的存储之间复制数据 |取决于文件和用于两个云数据存储或自承载的集成运行时计算机的物理配置之间复制数据的 DIUs 数的大小。 |
| 将数据从任何源数据存储复制到 Azure 表存储 |4 |
| 所有其他复制方案 |第 |

> [!TIP]
> 复制之间基于文件的存储的数据时，默认行为会给您的最佳吞吐量。 默认行为是自动确定。

若要控制托管数据的计算机上的负载将存储，或优化复制性能，可以重写默认值，并为指定值**parallelCopies**属性。 该值必须是大于或等于 1 的整数。 在运行时，为了获得最佳性能，复制活动使用小于或等于的值设置一个值。

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

* 当您基于文件的存储之间复制数据时**parallelCopies**确定文件级别的并行度。 单个文件内的区块化会发生情况下自动、 透明地。 它旨在使用最适合区块大小对于给定的源数据存储区类型要并行加载数据并与正交**parallelCopies**。 数据移动服务在运行时用于复制操作的并行复制的实际数量不超过所拥有的文件数。 如果复制行为是**mergeFile**，复制活动无法利用文件级别的并行度。
* 指定的值时**parallelCopies**属性，请考虑在源上的负载增加和接收器数据存储。 如果复制活动提供支持，其，例如，对于混合复制，还考虑到自承载的集成运行时负载增加。 此负载增加尤其是当您具有多个活动或针对相同的数据存储运行的相同活动的并发运行时，会发生情况。 如果您注意到，数据存储区或自承载的集成运行时负载过重，减少**parallelCopies**值以减轻负载。
* 数据移动服务时的不是基于文件的存储区是基于文件的存储复制数据时，将忽略**parallelCopies**属性。 即使指定了并行性，在此情况下也不适用。
* **ParallelCopies**属性是按与正交**dataIntegrationUnits**。 前者跨所有数据集成单元进行计数。

## <a name="staged-copy"></a>暂存复制

将数据从源数据存储复制到接收器数据存储时，可能会选择使用 Blob 存储作为过渡暂存存储。 暂存在以下情况下特别有用：

- **你想要将数据从各种数据存储引入到通过 PolyBase 的 SQL 数据仓库。** SQL 数据仓库使用 PolyBase 作为高吞吐量机制，将大量数据加载到 SQL 数据仓库中。 源数据必须位于 Blob 存储或 Azure Data Lake Store，并且它必须满足其他条件。 从 Blob 存储或 Azure Data Lake Store 以外的数据存储加载数据时，可通过过渡暂存 Blob 存储激活数据复制。 在这种情况下，Azure 数据工厂执行所需的数据转换，以确保其满足 PolyBase 的要求。 然后，它使用 PolyBase 将数据高效加载到 SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有时需要一段时间才能执行混合数据移动 （即，将从本地复制数据存储到云数据存储） 通过慢速网络连接。** 若要提高性能，可以使用暂存的复制压缩，这样只要花更少的时间将数据移到云中的暂存数据存储在本地数据。 然后可以解压缩在暂存存储中的数据之前加载到目标数据存储。
- **您不想在防火墙中打开端口 80 和 443 以外的端口，由于企业 IT 策略。** 例如，将数据从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器时，需要对 Windows 防火墙和公司防火墙激活端口 1433 上的出站 TCP 通信。 在此方案中，暂存的复制可以充分利用自承载的集成运行时首先将数据复制到 Blob 存储暂存实例通过 HTTP 或 HTTPS 端口 443 上。 然后它可以将数据加载到 SQL 数据库或 SQL 数据仓库从 Blob 存储暂存。 在此流中，不需要启用端口 1433。

### <a name="how-staged-copy-works"></a>暂存复制的工作原理

激活暂存功能时，首先将数据从源数据存储复制到暂存 Blob 存储（自带）。 然后，将数据从暂存数据存储复制到接收器数据存储。 Azure 数据工厂自动管理两阶段流。 数据移动完成后，azure 数据工厂还清除暂存存储中的临时数据。

![暂存复制](media/copy-activity-performance/staged-copy.png)

当使用暂存存储激活数据移动时，可以指定希望将源数据中移动数据之前先压缩数据存储复制到过渡或暂存数据存储区，然后先解压缩数据移动从过渡或暂存 dat 之前存储到的接收器数据存储。

目前，不能使用暂存存储在两个本地数据存储之间复制数据。

### <a name="configuration"></a>配置

配置**enableStaging**设置将复制活动中，指定是否想要之前加载到目标数据存储在 Blob 存储中暂存的数据。 当您将设置**enableStaging**到`TRUE`，指定下表中列出的其他属性。 此外需要创建 Azure 存储或存储共享访问签名链接服务用于暂存如果还没有。

| 属性 | 说明 | 默认值 | 需要 |
| --- | --- | --- | --- |
| enableStaging |指定是否要通过过渡暂存存储复制数据。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 链接服务的名称，这指用作过渡暂存存储的存储实例。 <br/><br/> 不能使用共享的访问签名与存储将数据载入 SQL 数据仓库通过 PolyBase。 可在其他任何情况下使用它。 |不适用 |将 **enableStaging** 设置为 TRUE 时，则为是 |
| path |指定要包含此暂存数据的 Blob 存储路径。 如果未提供路径，该服务将创建一个容器用于存储临时数据。 <br/><br/> 只在使用具有共享访问签名的存储时，或者要求临时数据位于特定位置时才指定路径。 |不适用 |否 |
| enableCompression |指定是否应复制到目标前压缩数据。 此设置可减少传输的数据量。 |False |否 |

>[!NOTE]
> 如果已启用，压缩的服务主体使用暂存的复制或不支持用于暂存 blob 链接服务的 MSI 身份验证。

下面是与前面的表中描述的属性的复制活动的示例定义：

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

收费基于两个步骤： 复制持续时间和复制类型。

* 当您使用转储在云复制，将数据复制期间从云数据存储到另一个云数据存储，同时阶段由 Azure 集成运行时提供支持、 收费 [总和的步骤 1 和步骤 2 的复制持续时间] x [云复制单元价格]。
* 在使用过渡期间混合复制，将数据从本地数据存储复制到云数据存储，一个阶段由自承载的集成运行时提供支持，要支付 [混合复制持续时间] x [混合复制单元价格] + [云复制持续时间]x [云复制单元价格]。

## <a name="performance-tuning-steps"></a>性能优化步骤

执行以下步骤来优化 Azure 数据工厂服务使用复制活动的性能。

1. **建立基线。** 在开发阶段，测试通过对代表性数据示例的复制活动的管道。 收集执行详细信息和以下性能特征[将复制活动监视](copy-activity-overview.md#monitoring)。

2. **诊断和优化性能。** 如果您观察到的性能不满足你的期望，确定性能瓶颈。 然后，优化性能以消除或减少瓶颈的影响。

    在某些情况下，在 Azure 数据工厂中，运行复制活动时您，请参阅"性能优化提示"的消息[将复制活动监视页](copy-activity-overview.md#monitor-visually)，如以下示例所示。 该消息告诉被标记为给定的复制运行的瓶颈。 它还将引导您在要将更改为提高复制吞吐量。 性能优化提示当前提供的建议，例如：

    - 使用 PolyBase 将数据复制到 Azure SQL 数据仓库时。
    - 在数据存储区端资源出现瓶颈时增加 Azure Cosmos DB 请求单位或 Azure SQL 数据库 Dtu （数据库吞吐量单位）。
    - 删除不必要的暂存的复制。

    性能优化规则也将逐渐丰富。

    示例：**复制到 Azure SQL 数据库性能优化提示**

    在此示例中，运行时，复制期间 Azure 数据工厂会注意到的接收器 Azure SQL 数据库达到高的 DTU 使用率，减慢写入操作。 建议是增加更多的 Dtu 的 Azure SQL 数据库层。 

    ![包含性能优化提示的复制监视](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    此外，以下是一些常见的注意事项。 本文不涵盖性能诊断的完整说明。

   * 性能功能：
     * [并行复制](#parallel-copy)
     * [数据集成单元](#data-integration-units)
     * [暂存复制](#staged-copy)
     * [自承载的集成运行时可伸缩性](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [自承载 Integration Runtime](#considerations-for-self-hosted-integration-runtime)
   * [源](#considerations-for-the-source)
   * [接收器](#considerations-for-the-sink)
   * [序列化和反序列化](#considerations-for-serialization-and-deserialization)
   * [压缩](#considerations-for-compression)
   * [列映射](#considerations-for-column-mapping)
   * [其他注意事项](#other-considerations)

3. **配置扩展至整个数据集。** 执行结果和性能感到满意后，可以展开定义和管道以覆盖整个数据集。

## <a name="considerations-for-self-hosted-integration-runtime"></a>自承载的集成运行时的注意事项

如果复制活动在自承载的集成运行时上运行，请注意以下各项：

**设置**：我们建议使用专用的计算机主机集成运行时。 请参阅[有关使用注意事项自承载集成运行时](concepts-integration-runtime.md)。

**横向扩展**：具有一个或多个节点的单个逻辑自承载的集成运行时可以在同一时间同时提供多个复制活动运行。 如果你非常需要的大量并发复制活动运行或要复制的数据量很大的混合数据移动，请考虑[横向扩展自承载的 integration runtime](create-self-hosted-integration-runtime.md#high-availability-and-scalability)来预配到更多资源授权复制。

## <a name="considerations-for-the-source"></a>有关源的注意事项

### <a name="general"></a>常规

请确保基础数据存储区不因上或针对其运行的其他工作负荷。

有关 Microsoft 数据存储，请参阅[监视和优化主题](#performance-reference)特定于数据存储。 这些主题可帮助用户了解数据存储性能特征、了解如何尽量缩短响应时间以及最大化吞吐量。

* 如果将数据从 Blob 存储复制到 SQL 数据仓库中，请考虑使用 PolyBase 来提高性能。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果将数据从 HDFS 复制到 Azure Blob 存储或 Azure Data Lake Store，请考虑使用 DistCp 来提高性能。 有关详细信息，请参阅[使用 DistCp 从 HDFS 复制数据](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果将数据从 Redshift 复制到 Azure SQL 数据仓库、 Azure BLob 存储或 Azure Data Lake Store 中，请考虑使用 UNLOAD 来提高性能。 有关详细信息，请参阅[使用 UNLOAD 从 Amazon Redshift 复制数据](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>基于文件的数据存储

* **平均文件大小和文件计数**：复制活动一次传输一个文件的数据。 在移动相同数据量的情况下，如果数据由许多小文件而不是几个大文件组成，则由于每个文件都有启动阶段，总吞吐量会较低。 如果可能，将小文件合并到更大的文件，以获得更高的吞吐量。
* **文件格式和压缩**：有关提高性能的更多方法，请参阅[序列化和反序列化注意事项](#considerations-for-serialization-and-deserialization)和[压缩注意事项](#considerations-for-compression)部分。

### <a name="relational-data-stores"></a>关系数据存储

* **数据模式**：表架构会影响复制吞吐量。 较大的行大小提供更好的性能比比较小行大小，复制相同数量的数据。 原因是数据库可以更有效地检索包含较少行的较少批量数据。
* **查询或存储过程**：优化要更有效地提取数据的复制活动源中指定的查询或存储的过程的逻辑。

## <a name="considerations-for-the-sink"></a>有关接收器的注意事项

### <a name="general"></a>常规

请确保基础数据存储区不因上或针对其运行的其他工作负荷。

有关 Microsoft 数据存储，请参阅[监视和优化主题](#performance-reference)特定于数据存储。 这些主题可帮助用户了解数据存储性能特征、了解如何尽量缩短响应时间以及最大化吞吐量。

* 如果将数据从任何数据存储复制到 Azure SQL 数据仓库时，请考虑使用 PolyBase 来提高性能。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果将数据从 HDFS 复制到 Azure Blob 存储或 Azure Data Lake Store，请考虑使用 DistCp 来提高性能。 有关详细信息，请参阅[使用 DistCp 从 HDFS 复制数据](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果将数据从 Redshift 复制到 Azure SQL 数据仓库、 Azure Blob 存储或 Azure Data Lake Store 中，请考虑使用 UNLOAD 来提高性能。 有关详细信息，请参阅[使用 UNLOAD 从 Amazon Redshift 复制数据](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>基于文件的数据存储

* **复制行为**：复制活动从不同的基于文件的数据存储复制数据时，如果有三个选项通过**copyBehavior**属性。 它将保留层次结构、平展层次结构或合并文件。 保留或平展层次结构有少量的性能开销或没有性能开销，但合并文件会导致性能开销增加。
* **文件格式和压缩**：有关提高性能的更多方法，请参阅[序列化和反序列化注意事项](#considerations-for-serialization-and-deserialization)和[压缩注意事项](#considerations-for-compression)部分。

### <a name="relational-data-stores"></a>关系数据存储

* **复制行为和性能的含义**:有不同的方式将数据写入 SQL 接收器。 了解详细信息[最佳做法，用于将数据加载到 Azure SQL 数据库](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database)。

* **数据模式和批大小**：
  * 表架构会影响复制吞吐量。 复制相同数据量时，较大行大小会比较小行大小提供更好的性能，因为数据库可以更有效地提交较少的数据批次。
  * 复制活动在一系列批次中插入数据。 可使用  **writeBatchSize** 属性设置批中的行数。 如果数据的行较小，可设置具有更高值的 **writeBatchSize** 属性，从更低的批开销和更高的吞吐量获益。 如果数据的行大小较大，请谨慎增加  **writeBatchSize**。 较高的值可能会导致复制失败（因为数据库负载过重）。

### <a name="nosql-stores"></a>NoSQL 存储

* 对于**表存储**：
  * **分区**：将数据写入交错分区会显着降低性能。 对源数据排序的分区键，以便插入数据有效地为一个分区后另一个。 或者，您可以调整逻辑将数据写入到单个分区。

## <a name="considerations-for-serialization-and-deserialization"></a>序列化和反序列化注意事项

当输入数据集或输出数据集是文件，会发生序列化和反序列化。 通过复制活动支持的文件格式的详细信息，请参阅[受支持的文件和压缩格式](supported-file-formats-and-compression-codecs.md)。

**复制行为**：

* 在基于文件的数据存储之间复制文件：
  * 输入和输出数据集这两个具有相同或没有文件格式设置、 数据移动服务时执行*二进制副本*而无需任何序列化或反序列化。 与源和接收器文件格式设置彼此不同的情况相比，这提供的吞吐量更高。
  * 当输入和输出数据集这两种是文本格式且只有编码类型不同，数据移动服务仅进行编码转换。 它不进行任何序列化和反序列化，与二进制复制相比，这会产生一些性能开销。
  * 输入和输出数据集这两个具有不同的文件格式或不同的配置，如分隔符、 数据移动服务时，反序列化源数据以流式传输、 转换，然后将其序列化到所指定的输出格式。 与其他方案相比，此操作会产生更多的性能开销。
* 不是基于，例如，从基于文件的存储到关系存储文件的数据存储来回复制文件时则需要序列化或反序列化步骤。 此步骤将造成大量的性能开销。

**文件格式**：选择的文件格式可能会影响复制性能。 例如，Avro 是一种将元数据与数据一起存储的压缩二进制格式。 它在 Hadoop 生态系统中对处理和查询具有广泛的支持。 Avro 进行序列化和反序列化，这会导致比文本格式的复制吞吐量较低的成本更高。 

在选择整个处理流中使用的文件格式时，应考虑全面。 开始：

- 格式将数据存储中的源数据存储，或要从外部系统中提取。
- 用于存储、 分析处理和查询的最佳格式。
- 在何种格式中应将数据导出到数据集市、 供报表和可视化工具。

有时，读取和写入性能不是最佳的文件格式可能对于整体分析处理来说却是不错的选择。

## <a name="considerations-for-compression"></a>压缩注意事项

如果输入或输出数据集文件，可以设置复制活动要执行压缩或解压缩，因为它将数据写入到目标。 选择压缩时，请在输入/输出 (I/O) 和 CPU 之间进行权衡。 压缩数据会花费额外的计算资源。 但反过来减少了网络 I/O 和存储。 具体取决于你的数据，可能会看到整体复制吞吐量的提升。

**编解码器**：每中压缩编解码器各有优点。 例如，虽然 bzip2 复制吞吐量最低，但使用 bzip2 可获得最佳的 Hive 查询性能，因为可将其拆分处理。 Gzip 是最平衡的选项，并且它具有最常用的。 选择最适合端到端方案的编解码器。

**级别**：对于每个压缩编解码器，有以下两个选择：最快压缩和最佳压缩。 即使不以最佳方式压缩生成的文件，最快的压缩的选项将尽可能快地压缩数据。 最佳压缩选项花费更多时间进行压缩，产生最小量的数据。 可对这两个选项进行测试，确定可为方案提供最佳整体性能的选项。

**注意事项**：若要在本地存储和云之间复制大量数据，请考虑使用启用了压缩的[暂存复制](#staged-copy)。 在公司网络和 Azure 服务的带宽是限制因素，并希望输入数据集和输出数据集这两个处于未压缩形式时，使用过渡存储很有帮助。

## <a name="considerations-for-column-mapping"></a>列映射注意事项

可以设置**columnMappings**映射所有或将输入列与输出列的子集到复制活动中的属性。 数据移动服务从源读取数据后，它需要先对数据执行列映射，再将数据写入接收器。 这一额外处理会降低复制吞吐量。

如果源数据存储可查询，例如，如果存储是关系存储（如 SQL 数据库或 SQL Server），或者是 NoSQL 存储（如表存储或 Azure Cosmos DB），请考虑将列筛选和重排序逻辑推送到**查询**属性，而不使用列映射。 这样一来，虽然数据移动服务从是高效得多的源数据存储中读取数据时会发生投影。

了解详细信息[将复制活动架构映射](copy-activity-schema-and-type-mapping.md)。

## <a name="other-considerations"></a>其他注意事项

如果想要复制的数据大小较大，可以调整你的业务逻辑以对数据进行进一步分区。 您可以计划复制活动更频繁地运行，以减少运行每个复制活动的数据大小。

谨慎对待数量的数据集，并复制需要 Azure 数据工厂同时连接到同一数据存储区的活动。 许多并发复制作业可能会限制数据存储，并导致性能下降，复制作业内部重试，甚至在某些情况下导致执行失败。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>示例方案：从本地 SQL server 复制到 Blob 存储

**场景**：构建管道，以将数据从本地 SQL server 复制到 Blob 存储以 CSV 格式。 要使复制作业更快，应将 CSV 文件压缩为 bzip2 格式。

**测试和分析**：将复制活动的吞吐量为小于 2 MBps，这是比性能基准慢得多。

**性能分析和优化**：为排除性能问题，可查看数据的处理和移动方式。

- **读取数据**：集成运行时打开到 SQL Server 的连接并发送查询。 SQL Server 响应通过集成运行时通过 intranet 向发送的数据流。
- **序列化和压缩数据**：集成运行时序列化该数据流为 CSV 格式和压缩为 bzip2 流数据。
- **写入数据**：集成运行时上载到 Blob 存储通过 internet 将 bzip2 流。

正如您所看到的处理和流式处理顺序方式移动数据：SQL Server > LAN> Integration Runtime > WAN > Blob 存储。 管道中最小吞吐量入进行的整体性能。

![数据流](./media/copy-activity-performance/case-study-pic-1.png)

以下的一个或多个因素可能会导致性能瓶颈：

* **源**：SQL Server 本身由于负载过重而吞吐量低。
* **自承载集成运行时**:
  * **LAN**：Integration Runtime 的位置离 SQL Server 计算机很远，且带宽连接低。
  * **Integration Runtime**：Integration Runtime 已达到执行以下操作的负载限制：
    * **序列化**：将数据流序列化为 CSV 格式时吞吐量缓慢。
    * **压缩**：您已选择慢速压缩编解码器，例如，bzip2，即采用 Core i7 2.8 MBps。
  * **WAN**：企业网络与你的 Azure 服务之间的带宽较低，例如，T1 = 1,544 kbps;T2 = 6,312 kbps。
* **接收器**：Blob 存储吞吐量低。 这种情况下是不太可能，因为其服务级别协议 (SLA) 保证至少有 60 MBps。

在这种情况下，bzip2 数据压缩可能会拖慢整个管道的速度。 切换到 gzip 压缩编解码器可能会缓解此瓶颈。

## <a name="references"></a>参考

下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure 存储，包括 Blob 存储和表存储：[Azure 存储可伸缩性目标](../storage/common/storage-scalability-targets.md)并[Azure 存储性能和可伸缩性清单](../storage/common/storage-performance-checklist.md)。
* Azure SQL 数据库：你可以[监视性能](../sql-database/sql-database-single-database-monitor.md)和检查数据库事务单位 (DTU) 百分比。
* Azure SQL 数据仓库：其功能以数据仓库单位 (Dwu)。 请参阅[管理的计算 Azure SQL 数据仓库 （概述） 中的能力](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的性能级别](../cosmos-db/performance-levels.md)。
* 本地 SQL Server：[监视和调整适用于性能](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地文件服务器：[文件服务器的性能优化](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动架构映射](copy-activity-schema-and-type-mapping.md)
- [复制活动容错](copy-activity-fault-tolerance.md)
