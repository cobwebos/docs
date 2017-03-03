---
title: "复制活动性能和优化指南 | Microsoft Docs"
description: "了解使用复制活动时影响 Azure 数据工厂中数据移动性能的关键因素。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: c3d96d11894f0009db004b1089c05559cafd2d43
ms.openlocfilehash: ee79612cc30f1dfefcf7dcd8af7aed7836dd528c
ms.lasthandoff: 01/06/2017


---
# <a name="copy-activity-performance-and-tuning-guide"></a>复制活动性能和优化指南
Azure 数据工厂复制活动提供安全、可靠且高性能的一流数据加载解决方案。 它允许用户在各种云和本地数据存储中每天复制数十 TB 的数据。 速度超快的数据加载性能是确保用户能专注于核心“大数据”问题的关键：构建高级分析解决方案并从所有数据获得深入见解。

Azure 提供了一组企业级数据存储和数据仓库解决方案，并且复制活动提供了高度优化的数据加载体验，易于配置和设置。 使用单个复制活动，可完成：

* 以 **1.2 GBps** 的速度将数据加载到 **Azure SQL 数据仓库**。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)。
* 以 **1.0 GBps** 的速度将数据加载到 **Azure Blob 存储**
* 以 **1.0 GBps** 的速度将数据加载到 **Azure Data Lake Store**

本文将介绍：

* 支持的源和接收器数据存储的[性能参考数字](#performance-reference)，可帮助用户规划项目；
* 可在不同情况下提高复制吞吐量的功能，包括[并行复制](#parallel-copy)、[云数据移动单元](#cloud-data-movement-units)和[暂存复制](#staged-copy)；
* 有关如何优化性能和调整影响复制性能的关键因素的[性能优化指南](#performance-tuning-steps)。

> [!NOTE]
> 如果对常规复制活动不熟悉，在阅读本文前请参阅[复制活动移动数据](data-factory-data-movement-activities.md)。
>
>

## <a name="performance-reference"></a>性能参考
![性能矩阵](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [!NOTE]
> 可通过利用比默认最大 DMU 更多的数据移动单元 (DMU) 来实现更高的吞吐量，对于云到云复制活动运行来说，默认最大的 DMU 为 8。 例如，使用 100 个 DMU，可将数据以 **1.0GBps** 的速率从 Azure Blob 复制到 Azure Data Lake Store 中。 请参阅[云数据移动单位](#cloud-data-movement-units)部分，了解有关此功能和受支持方案的相关详细信息。 要请求更多 DMU，请联系[支持](https://azure.microsoft.com/support/)。
>
>

**需要注意的要点：**
* 使用以下公式计算吞吐量：[从源读取的数据大小]/[复制活动运行持续时间]。
* 表中的性能参考数字使用单次复制活动运行中的 [TPC-H](http://www.tpc.org/tpch/) 数据集测量得出。
* 若要在云数据存储之间进行复制，请将 **cloudDataMovementUnits** 设置为 1 和 4（或 8）以进行比较。 未指定 **parallelCopies**。 有关这些功能的详细信息，请参阅[并行复制](#parallel-copy)部分。
* 在 Azure 数据存储中，源和接收器位于同一 Azure 区域。
* 对于混合（本地到云或云到本地）数据移动，网关的单个实例在独立于本地数据存储的计算机上运行。 该配置列于下一个表中。 在网关上运行单个活动时，复制操作仅使用测试计算机的一小部分 CPU、内存或网络带宽。
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
        <td>Internet 接口：10 Gbps；intranet 接口：40 Gbps</td>
    </tr>
    </table>

## <a name="parallel-copy"></a>并行复制
可**在复制活动运行中并行**从源读取数据或将数据写入目标。 此功能可提高复制操作的吞吐量，并缩短移动数据所需的时间。

此设置不同于活动定义中的**并发**属性。 **并发**属性确定**并发复制活动运行**的数量，以从不同的活动时段（凌晨 1 点到 2 点、凌晨 2 点到 3 点，凌晨 3 点到 4 点等）处理数据。 执行历史负载时，此功能非常有用。 并行复制功能适用于**单个活动运行**。

我们来看一下示例方案。 在下面的示例中，需要处理多个以前的切片。 数据工厂为每个切片运行复制活动实例（活动运行）：

* 第一个活动时段（凌晨 1 点到 2 点）的数据切片==> 活动运行 1
* 第二个活动时段（凌晨 2 点到 3 点）的数据切片==> 活动运行 2
* 第二个活动时段（凌晨 3 点到 4 点）的数据切片==> 活动运行 3

依此类推。

在此示例中，当**并发**值设置为 2 时，**活动运行 1** 和 **活动运行 2****并发**从两个活动时段复制数据，以提高数据移动性能。 但是，如果多个文件与活动运行 1 相关联，则数据移动服务一次只会将一个文件从源复制到目标位置。

### <a name="parallelcopies"></a>parallelCopies
可使用 **parallelCopies** 属性指示要让“复制活动”使用的并行度。 可将此属性视为复制活动内，可从源并行读取或并行写入接收器数据存储的最大线程数。

对于每个复制活动运行，数据工厂确定要用于将数据从源数据存储复制到目标数据存储的并行复制数。 它使用的默认并行复制数取决于使用的源和接收器类型。  

| 源和接收器 | 由服务确定的默认并行复制计数 |
| --- | --- |
| 在基于文件的存储（Blob 存储；Data Lake Store；Amazon S3；本地文件系统；本地 HDFS）之间复制数据 |介于 1 和 32 之间。 取决于文件大小以及用于在两个云数据存储之间复制数据的云数据移动单元 (DMU) 数，或用于混合复制的网关计算机的物理配置（将数据粘贴到本地数据存储或从本地数据存储复制）。 |
| 将数据**从任何源数据存储复制到 Azure 表存储** |4 |
| 所有其他源和接收器对 |1 |

默认行为通常应可提供最佳吞吐量。 但是，若要控制托管数据存储的计算机上的负载或优化复制性能，可选择替代默认值并为 **parallelCopies** 属性指定值。 该值必须介于 1 和 32 之间（两者均含）。 在运行时，为了获得最佳性能，复制活动使用小于或等于所设置的值。

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
需要注意的要点：

* 在基于文件的存储之间复制数据时，**parallelCopies** 确定文件级别的并行度。 单个文件内的区块化会自动透明地在文件下进行，它旨在对给定源数据存储类型使用最佳区块大小，以并行和正交方式将数据加载到 parallelCopies。 数据移动服务在运行时用于复制操作的并行复制的实际数量不超过所拥有的文件数。 如果复制行为是 **mergeFile**，复制活动无法利用文件级别的并行度。
* 为 ** parallelCopies ** 属性指定值时，请考虑源和接收器数据存储上的负载会增加，如果是混合复制，则网关的负载会增加。 尤其在有多个活动或针对同一数据存储运行的相同活动有并发运行时，会发生这种情况。 如果注意到数据存储或网关负载过重，请降低 **parallelCopies ** 值以减轻负载。
* 将数据从不基于文件的存储复制到基于文件的存储时，数据移动服务将忽略 ** parallelCopies ** 属性。 即使指定了并行性，在此情况下也不适用。

> [!NOTE]
> 在进行混合复制时，必须使用数据管理网关版本 1.11 或更高版本才能使用 **parallelCopies** 功能。
>
>

### <a name="cloud-data-movement-units"></a>云数据移动单位
**云数据移动单位 (DMU)** 是一种度量单位，代表单个单位在数据工厂中的能力（包含 CPU、内存、网络资源分配）。 DMU 可用于云到云复制操作，但不能用于混合复制。

默认情况下，数据工厂使用单个云 DMU 来执行单个复制活动运行。 若要替代此默认值，请如下所示指定 **cloudDataMovementUnits** 属性的值。 有关为特定复制源和接收器配置更多单元时可能获得的性能增益级别的信息，请参阅[性能参考](#performance-reference)。

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 4
        }
    }
]
```
**cloudDataMovementUnits** 属性**允许的值**为 1（默认值）、2、4 和 8。 复制操作在运行时使用的**云 DMU 的实际数量**等于或小于配置的值，具体取决于数据模式。

> [!NOTE]
> 如果需要更多云 DMU 以获得更高的吞吐量，请联系 [Azure支持](https://azure.microsoft.com/support/)。 目前仅在**将多个文件从 Blob 存储/Data Lake Store/Amazon S3/云 FTP 复制到 Blob 存储/Data Lake Store/Azure SQL 数据库**，且每个文件的大小大于或等于 16 MB 时，设置为 8 或更高才会有效。
>
>

若要更好地使用这两个属性，并提高数据移动吞吐量，请参阅[示例用例](#case-study-use-parallel-copy)。 无需配置 **parallelCopies** 即可利用默认行为。 如果已配置且 **parallelCopies** 太小，则可能不能充分利用多个云 DMU。  

### <a name="billing-impact"></a>计费影响
请**务必**记住，将根据复制操作的总时间向你收费。 如果复制作业过去使用 1 个云单元花费 1 小时，现在使用 4 个单元花费 15 分钟，则总费用几乎相同。 例如，你使用&4; 个云单元。 第一个云单元花费 10 分钟，第二个花费 10 分钟，第三个花费 5 分钟，第四个花费 5 分钟，这些都属于一个复制活动运行。 将对总复制（数据移动）时间进行收费，即 10 + 10 + 5 + 5 = 30 分钟。 使用 **parallelCopies** 不会影响计费。

## <a name="staged-copy"></a>暂存复制
将数据从源数据存储复制到接收器数据存储时，可能会选择使用 Blob 存储作为过渡暂存存储。 暂存在以下情况下特别有用：

1. **通过 PolyBase 从各种数据存储将数据引入 SQL 数据仓库**。 SQL 数据仓库使用 PolyBase 作为高吞吐量机制，将大量数据加载到 SQL 数据仓库中。 但是，源数据必须位于 Blob 存储中，并且它必须满足其他条件。 从 Blob 存储以外的数据存储加载数据时，可通过过渡暂存 Blob 存储激活数据复制。 在这种情况下，数据工厂会执行所需的数据转换，确保其满足 PolyBase 的要求。 然后，它使用 PolyBase 将数据加载到 SQL 数据仓库。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)。
2. **有时，通过速度慢的网络连接执行混合数据移动（即在本地数据存储和云数据存储之间进行复制）需要一段时间**。 为了提高性能，可压缩本地数据，缩短将数据移动到云中的暂存数据存储的时间。 然后，可先在暂存存储中解压缩数据，再将它们加载到目标数据存储。
3. **由于企业 IT 策略，不希望在防火墙中打开除端口 80 和端口 443 以外的端口**。 例如，将数据从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器时，需要对 Windows 防火墙和公司防火墙激活端口 1433 上的出站 TCP 通信。 在这种情况下，利用网关首先在端口 443 上通过 HTTP 或 HTTPS 将数据复制到 Blob 存储暂存实例。 然后，将数据从 Blob 存储暂存加载到 SQL 数据库或 SQL 数据仓库。 在此流中，不需要启用端口 1433。

### <a name="how-staged-copy-works"></a>暂存复制的工作原理
激活暂存功能时，首先将数据从源数据存储复制到暂存数据存储（自带）。 然后，将数据从暂存数据存储复制到接收器数据存储。 数据工厂自动为你管理两阶段流。 数据移动完成后，数据工厂还将清除暂存存储中的临时数据。

在云复制方案（源和接收器数据都位于云）中，未使用网关。 数据工厂服务执行复制操作。

![暂存复制：云方案](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

在混合复制方案中（源位于本地，接收器位于云中），网关将数据从源数据存储移动到暂存数据存储。 数据工厂服务将数据从暂存数据存储复制到接收器数据存储。 反向流也支持通过暂存将数据从云数据存储复制到本地数据存储。

![暂存复制：混合方案](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

使用暂存存储激活数据移动时，可指定是否要先压缩数据，再将数据从源数据存储移动到过渡数据存储或暂存数据存储，然后先解压缩数据，再将数据从过渡数据存储或暂存数据移动到接收器数据存储。

目前，不能使用暂存存储在两个本地数据存储之间复制数据。 我们预计会尽快提供此选项。

### <a name="configuration"></a>配置
在复制活动中配置 **enableStaging** 设置，指定在将数据加载到目标数据存储之前是否要在 Blob 存储中暂存。 将 **enableStaging** 设置为 TRUE 时，指定下一个表中列出的其他属性。 如果未指定，则还需要创建 Azure 存储或存储共享访问签名链接服务供暂存用。

| 属性 | 说明 | 默认值 | 必选 |
| --- | --- | --- | --- |
| **enableStaging** |指定是否要通过过渡暂存存储复制数据。 |False |否 |
| **linkedServiceName** |指定 [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 或 [AzureStorageSas ](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 链接服务的名称，这指用作过渡暂存存储的存储实例。 <br/><br/> 不能使用具有共享访问签名的存储通过 PolyBase 将数据加载到 SQL 数据仓库。 可在其他任何情况下使用它。 |不适用 |将 **enableStaging** 设置为 TRUE 时，则为是 |
| **路径** |指定要包含此暂存数据的 Blob 存储路径。 如果不提供路径，该服务将创建容器以存储临时数据。 <br/><br/> 只在使用具有共享访问签名的存储时，或者要求临时数据位于特定位置时才指定路径。 |不适用 |否 |
| **enableCompression** |指定是否应先压缩数据，再将数据复制到目标。 此设置可减少传输的数据量。 |False |否 |

以下是具有上表所述属性的复制活动的示例定义：

```json
"activities":[  
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>计费影响
基于两个步骤进行计费：复制持续时间和复制类型。

* 在云复制期间（将数据从一个云数据存储复制到另一个云数据存储）使用暂存时，需要支付 [步骤 1 和步骤 2 的复制持续时间总和] x [云复制单位价格]。
* 在混合复制（将数据从本地数据存储复制到云数据存储）期间使用暂存时，需要支付 [混合复制持续时间] x [混合复制单价] + [云复制持续时间] x [云复制单价]。

## <a name="performance-tuning-steps"></a>性能优化步骤
我们建议采用以下步骤，通过复制活动调整数据工厂服务的性能：

1. **建立基准**。 在开发阶段，通过对代表性数据示例使用复制活动来测试管道。 可使用数据工厂[切片模型](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices)来限制处理的数据量。

   使用**监视和管理应用**收集执行时间和性能特征。 在数据工厂主页上选择“监视和管理”。 在树视图中，选择“输出数据集”。 在“活动窗口”列表中，选择复制活动运行。 **活动窗口**列出了复制活动持续时间和所复制的数据大小。 **活动窗口资源管理器**中列出了吞吐量。 有关此应用的详细信息，请参阅[使用监视和管理应用来监视和管理 Azure 数据工厂管道](data-factory-monitor-manage-app.md)。

   ![活动运行详细信息](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   在本文之后的部分，可将你方案中的性能和配置与我们测试中复制活动的[性能参考](#performance-reference)进行比较。
2. **诊断和优化性能**。 如果观察到的性能不符合预期，则需要识别性能瓶颈。 然后，优化性能以消除或减少瓶颈的影响。 本文不涵盖性能诊断的完整说明，但下面是一些常见的注意事项：

   * 性能功能：
     * [并行复制](#parallel-copy)
     * [云数据移动单位](#cloud-data-movement-units)
     * [暂存复制](#staged-copy)   
   * [源](#considerations-for-the-source)
   * [接收器](#considerations-for-the-sink)
   * [序列化和反序列化](#considerations-for-serialization-and-deserialization)
   * [压缩](#considerations-for-compression)
   * [列映射](#considerations-for-column-mapping)
   * [数据管理网关](#considerations-for-data-management-gateway)
   * [其他注意事项](#other-considerations)
3. **将配置扩展至整个数据集**。 对执行结果和性能满意时，可以扩展定义和管道活动期以覆盖整个数据集。

## <a name="considerations-for-the-source"></a>有关源的注意事项
### <a name="general"></a>常规
确保基础数据存储未被在其上运行或针对其运行的其他工作负荷过渡占用。

有关 Microsoft 数据存储的信息，请参阅特定于数据存储的[监视和优化主题](#performance-reference)，帮助用户了解数据存储性能特征、尽量缩短响应时间以及最大化吞吐量。

如果将数据从 Blob 存储复制到 SQL 数据仓库，请考虑使用 **PolyBase** 来提高性能。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)。

### <a name="file-based-data-stores"></a>基于文件的数据存储
（包括 Blob 存储、Data Lake Store、Amazon S3、本地文件系统和本地 HDFS）

* **平均文件大小和文件计数**：复制活动一次传输一个文件的数据。 在移动相同数据量的情况下，如果数据由许多小文件而不是几个大文件组成，则由于每个文件都有启动阶段，总吞吐量会较低。 因此，尽可能将小文件合并为较大的文件，以获得更高的吞吐量。
* **文件格式和压缩**：有关提高性能的更多方法，请参阅[序列化和反序列化注意事项](#considerations-for-serialization-and-deserialization)和[压缩注意事项](#considerations-for-compression)部分。
* 对于其中需要**数据管理网关**的**本地文件系统**方案，请参阅[数据管理网关注意事项](#considerations-for-data-management-gateway)部分。

### <a name="relational-data-stores"></a>关系数据存储
（包括 SQL 数据库；SQL 数据仓库；Amazon Redshift；SQL Server 数据库；以及 Oracle、MySQL、DB2、Teradata、Sybase 和 PostgreSQL 数据库等。）

* **数据模式**：表架构会影响复制吞吐量。 复制相同数据量时，较大行大小可比较小行大小提供更佳的性能。 原因是数据库可以更有效地检索包含较少行的较少批量数据。
* **查询或存储过程**：优化在复制活动源中指定的查询或存储过程的逻辑，以更有效地提取数据。
* 对于需要使用**数据管理网关**的**本地关系数据库**（如 SQL Server 和 Oracle），请参阅[数据管理网关注意事项](#considerations-on-data-management-gateway)部分。

## <a name="considerations-for-the-sink"></a>有关接收器的注意事项
### <a name="general"></a>常规
确保基础数据存储未被在其上运行或针对其运行的其他工作负荷过渡占用。

有关 Microsoft 数据存储的信息，请参阅特定于数据存储的[监视和优化主题](#performance-reference)。 这些主题可帮助用户了解数据存储性能特征、了解如何尽量缩短响应时间以及最大化吞吐量。

如果将数据从 **Blob 存储**复制到 **SQL 数据仓库**，请考虑使用 **PolyBase** 来提高性能。 有关详细信息，请参阅[使用 PolyBase 将数据加载到 Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。 有关带有用例的演练，请参阅[在不到 15 分钟的时间里通过 Azure 数据工厂将 1 TB 的数据加载到 Azure SQL 数据仓库](data-factory-load-sql-data-warehouse.md)。

### <a name="file-based-data-stores"></a>基于文件的数据存储
（包括 Blob 存储、Data Lake Store、Amazon S3、本地文件系统和本地 HDFS）

* **复制行为**：如果从基于文件的不同数据存储复制数据，则复制活动可通过 **copyBehavior** 属性提供三个选项。 它将保留层次结构、平展层次结构或合并文件。 保留或平展层次结构有少量的性能开销或没有性能开销，但合并文件会导致性能开销增加。
* **文件格式和压缩**：有关提高性能的更多方法，请参阅[序列化和反序列化注意事项](#considerations-for-serialization-and-deserialization)和[压缩注意事项](#considerations-for-compression)部分。
* **Blob 存储**：目前，对于优化的数据传输和吞吐量，Blob 存储仅支持块 blob。
* 对于需要使用**数据管理网关**的**本地文件系统**方案，请参阅[数据管理网关注意事项](#considerations-for-data-management-gateway)部分。

### <a name="relational-data-stores"></a>关系数据存储
（包括 SQL 数据库、SQL 数据仓库、SQL Server 数据库和 Oracle 数据库）

* **复制行为**：根据已为 **sqlSink** 设置的属性，复制活动以不同的方式将数据写入目标数据库。
  * 数据移动服务默认使用大容量复制 API 以追加模式插入数据，这提供最佳性能。
  * 如果在接收器中配置存储过程，数据库一次会应用一行数据，而不是大容量加载。 性能会大大降低。 如果数据集较大，请考虑切换为使用 **sqlWriterCleanupScript** 属性（如适用）。
  * 如果为每个复制活动运行配置 **sqlWriterCleanupScript** 属性，该服务将触发脚本，然后使用大容量复制 API 插入数据。 例如，若要使用最新数据覆盖整个表，可指定一个脚本，先删除所有记录，再从源大容量加载新数据。
* **数据模式和批大小**：
  * 表架构会影响复制吞吐量。 复制相同数据量时，较大行大小会比较小行大小提供更好的性能，因为数据库可以更有效地提交较少的数据批次。
  * 复制活动以一系列批次插入数据。 可使用 ** writeBatchSize** 属性设置批中的行数。 如果数据的行较小，可设置具有更高值的 **writeBatchSize **属性，从更低的批开销和更高的吞吐量获益。 如果数据的行大小较大，请谨慎增加 ** writeBatchSize**。 较高的值可能会导致复制失败（因为数据库负载过重）。
* 对于需要使用**数据管理网关**的**本地关系数据库**（如 SQL Server 和 Oracle），请参阅[数据管理网关注意事项](#considerations-for-data-management-gateway)部分。

### <a name="nosql-stores"></a>NoSQL 存储
（包括表存储和 Azure DocumentDB）

* 对于**表存储**：
  * **分区**：将数据写入交错分区会显着降低性能。 按分区键对源数据进行排序，以有效率地将数据插入各个分区，或调整逻辑将数据写入单个分区。
* 对于 **DocumentDB**：
  * **批大小**：**writeBatchSize** 属性设置对 DocumentDB 服务的并行请求数以创建文档。 当增加 **writeBatchSize** 时，由于会向 DocumentDB 发送更多的并行请求，因此可以获得更好的性能。 但在写入 DocumentDB 时，请注意限制（错误消息为“请求速率高”）。 导致限制的因素很多，包括文档大小、文档中的术语数和目标集合的索引策略。 若要实现更高的复制吞吐量，请考虑使用更好的集合，例如 S3。

## <a name="considerations-for-serialization-and-deserialization"></a>序列化和反序列化注意事项
输入数据集或输出数据集是文件时，可能会发生序列化和反序列化。 目前，复制活动支持 Avro 和文本（例如，CSV 和 TSV）数据格式。

**复制行为**：

* 在基于文件的数据存储之间复制文件：
  * 输入和输出数据集具有相同的文件格式设置或没有文件格式设置时，数据移动服务将执行二进制复制，而不进行任何序列化或反序列化。 与源和接收器文件格式设置彼此不同的情况相比，这提供的吞吐量更高。
  * 输入和输出数据集都为文本格式且只有编码类型不同时，数据移动服务仅进行编码转换。 它不进行任何序列化和反序列化，与二进制复制相比，这会产生一些性能开销。
  * 当输入和输出数据集具有不同的文件格式或不同的配置时（如分隔符），数据移动服务会反序列化源数据，以进行流式传输、转换，然后将其序列化为指示的输出格式。 与其他方案相比，此操作会产生更多的性能开销。
* 向/从不基于文件的数据存储复制文件时（例如，从基于文件的存储到关系存储），需要序列化或反序列化步骤。 此步骤将造成大量的性能开销。

**文件格式**：选择的文件格式可能会影响复制性能。 例如，Avro 是一种将元数据与数据一起存储的压缩二进制格式。 它在 Hadoop 生态系统中对处理和查询具有广泛的支持。 然而，Avro 进行序列化和反序列化的代价更高，这会导致比文本格式更低的复制吞吐量。 在选择整个处理流中使用的文件格式时，应考虑全面。 首先考虑数据存储的格式（源数据存储或从外部系统提取）；存储、分析处理和查询的最佳格式；应将数据导出到数据集市、供报表和可视化工具使用的格式。 有时，读取和写入性能不是最佳的文件格式可能对于整体分析处理来说却是不错的选择。

## <a name="considerations-for-compression"></a>压缩注意事项
输入或输出数据集是文件时，可以设置复制活动，使其在将数据写入目标时执行压缩或解压缩。 选择压缩时，请在输入/输出 (I/O) 和 CPU 之间进行权衡。 压缩数据会花费额外的计算资源。 但反过来减少了网络 I/O 和存储。 根据所用数据，可能会看到整体复制吞吐量的提升。

**编解码器**：复制活动支持 gzip、bzip2 和 Deflate 压缩类型。 Azure HDInsight 可使用这三种类型进行处理。 每中压缩编解码器各有优点。 例如，虽然 bzip2 复制吞吐量最低，但使用 bzip2 可获得最佳的 Hive 查询性能，因为可将其拆分处理。 Gzip 是最平衡的选项，也是最常用的选项。 选择最适合端到端方案的编解码器。

**级别**：对于每个压缩编解码器，有以下两个选择：最快压缩和最佳压缩。 最快压缩选项可尽快压缩数据，不过无法以最佳方式压缩生成的文件。 最佳压缩选项花费更多时间进行压缩，产生最小量的数据。 可对这两个选项进行测试，确定可为你的方案提供最佳整体性能的选项。

**注意事项**：若要在本地存储和云之间复制大量数据，请考虑搭配使用过渡 Blob 存储与压缩。 当公司网络和 Azure 服务的带宽是限制因素，并希望输入数据集和输出数据集都处于未压缩形式时，使用过渡存储将非常有用。 更具体地说，可将单个复制活动分为两个复制活动。 第一个复制活动以压缩形式从源复制到过渡或暂存 blob。 第二个复制活动从暂存复制已压缩的数据，然后在写入接收器时进行解压缩。

## <a name="considerations-for-column-mapping"></a>列映射注意事项
可在复制活动中设置 ** columnMappings ** 属性，将全部或部分输入列映射到输出列。 数据移动服务从源读取数据后，它需要先对数据执行列映射，再将数据写入接收器。 这一额外处理会降低复制吞吐量。

如果源数据存储为可查询，例如，如果存储是关系存储（如 SQL 数据库或 SQL Server），或者是 NoSQL 存储（如表存储或 DocumentDB），请考虑将列筛选和重排序逻辑推送到**查询**属性，而不使用列映射。 这样，当数据移动服务从源数据存储读取数据时将发生投影，使效率更高。

## <a name="considerations-for-data-management-gateway"></a>数据管理网关注意事项
有关网关设置建议，请参阅[使用数据管理网关的注意事项](data-factory-data-management-gateway.md#considerations-for-using-gateway)。

**网关计算机环境**：我们建议使用专用计算机托管数据管理网关。 在网关计算机上使用 PerfMon 等工具，检查复制操作期间的 CPU、内存和带宽的使用情况。 如果 CPU、内存或网络带宽成为瓶颈，请切换到功能更强大的计算机。

**并发复制活动运行**：数据管理网关的单个实例可同时（或并发）为多个复制活动运行提供服务。 最大并发作业数根据网关计算机的硬件配置计算得出。 其他复制作业将排队等待，直到它们被网关选取或直到另一个作业超时为止。 为避免网关计算机上的资源争用，可分阶段执行复制活动计划，减少队列中的复制作业数，或者考虑将负载拆分到多个网关计算机上。

## <a name="other-considerations"></a>其他注意事项
如果要复制的数据大小较大，可调整业务逻辑，使用数据工厂中的切片机制进一步对数据进行分区。 然后，计划使复制活动更频繁地运行，减少每个复制活动运行的数据大小。

请谨慎对待需要数据工厂同时连接到同一数据存储的数据集数和复制活动数。 许多并发复制作业可能会限制数据存储，并导致性能下降，复制作业内部重试，甚至在某些情况下导致执行失败。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>示例方案：从本地 SQL Server 复制到 Blob 存储
**方案**：构建管道，以 CSV 格式将数据从本地 SQL Server 复制到 Blob 存储。 若要使复制作业更快，应将 CSV 文件压缩为 bzip2 格式。

**测试和分析**：复制活动的吞吐量小于 2 MBps，这比性能基准慢得多。

**性能分析和优化**：为排除性能问题，可查看数据的处理和移动方式。

1. **读取数据**：网关打开与 SQL Server 的连接并发送查询。 SQL Server 通过 Intranet 向网关发送数据流，以此进行响应。
2. **序列化和压缩数据**：网关将数据流序列化为 CSV 格式，并将数据压缩为 bzip2 流。
3. **写入数据**：网关通过 Internet 将 bzip2 流上传到 Blob 存储。

如你所见，数据将以流式处理顺序方式进行处理和移动：SQL Server > LAN> 网关 > WAN > Blob 存储。 **整体性能受管道中最小吞吐量的限制**。

![数据流](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

以下的一个或多个因素可能会导致性能瓶颈：

* **源**：SQL Server 本身由于负载过重而吞吐量低。
* **数据管理网关**：
  * **LAN**：网关的位置离 SQL Server 计算机很远，且带宽连接低。
  * **网关**：网关已达到其执行以下操作的负载限制：
    * **序列化**：将数据流序列化为 CSV 格式时吞吐量缓慢。
    * **压缩**：选择慢速压缩编解码器（例如，bzip2，其采用 Core i7，速度为 2.8 MBps）。
  * **WAN**：企业网络和 Azure 服务之间的带宽低（例如，T1 = 1,544 kbps；T2 = 6,312 kbps）。
* **接收器**：Blob 存储吞吐量低。 （这种情况不太可能发生，因为其 SLA 保证至少有 60 MBps。）

在这种情况下，bzip2 数据压缩可能会拖慢整个管道的速度。 切换到 gzip 压缩编解码器可能会缓解此瓶颈。

## <a name="sample-scenarios-use-parallel-copy"></a>示例方案：使用并行复制
**方案 I：**将 1,000 个 1 MB 的文件从本地文件系统复制到 Blob 存储。

**分析和性能优化**：例如，如果已在四核计算机上安装了网关，数据工厂将使用 16 个并行复制将文件从文件系统并发移动到 Blob 存储。 此并行执行应会导致高吞吐量。 还可显式指定并行复制数。 复制许多小文件时，并行复制通过更有效地使用资源显著帮助提高吞吐量。

![方案 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**方案 II**：将 20 个 Blob（每个 500 MB）从 Blob 存储复制到 Data Lake Store Analytics，然后优化性能。

**分析和性能优化**：在此方案中，数据工厂通过使用单个复制（**parallelCopies** 设置为 1）和单一云数据移动单位，将数据从 Blob 存储复制到 Data Lake Store。 观察到的吞吐量将接近[性能参考部分](#performance-reference)中描述的吞吐量。   

![方案 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**方案 III**：个别文件大小大于几十 MB 且总量很大。

**分析和优化性能**：因为单一云 DMU 的资源限制，增加 **parallelCopies** 不会产生更好的复制性能。 而应指定更多云 DMU，获取更多资源来执行数据移动。 请勿指定 **parallelCopies** 属性的值。 数据工厂为你处理并行度。 在此情况下，如果将 **cloudDataMovementUnits** 设置为 4，则会产生大约 4 倍的吞吐量。

![方案 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>引用
下面是有关一些受支持数据存储的性能监视和优化参考：

* Azure 存储（包括 Blob 存储和表存储）：[Azure 存储可伸缩性目标](../storage/storage-scalability-targets.md)和 [Azure存储性能和可伸缩性清单](../storage/storage-performance-checklist.md)
* Azure SQL 数据库：可[监视性能](../sql-database/sql-database-single-database-monitor.md)并检查数据库事务单位 (DTU) 百分比
* Azure SQL 数据仓库：其功能以数据仓库单位 (DWU) 衡量；请参阅[管理 Azure SQL 数据仓库中的计算能力（概述）](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure DocumentDB：[DocumentDB 中的性能级别](../documentdb/documentdb-performance-levels.md)
* 本地 SQL Server：[监视和优化性能](https://msdn.microsoft.com/library/ms189081.aspx)
* 本地文件服务器：[Performance tuning for file servers](https://msdn.microsoft.com/library/dn567661.aspx)（文件服务器性能优化）

