---
title: Azure 数据工厂中的复制活动
description: 了解 Azure 数据工厂中的复制活动。 可以使用复制活动将数据从支持的源数据存储复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: fa2876b88a520480813ebfb8af8219d53c32057a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075561"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 数据工厂中的复制活动

> [!div class="op_single_selector" title1="选择要使用的数据工厂的版本："]
> * [第 1 版](v1/data-factory-data-movement-activities.md)
> * [当前版本](copy-activity-overview.md)

在 Azure 数据工厂中，可以使用复制活动在本地与云数据存储之间复制数据。 复制数据后，可以使用其他活动进一步转换和分析数据。 还可使用复制活动发布有关商业智能 (BI) 和应用程序消耗的转换和分析结果。

![复制活动的角色](media/copy-activity-overview/copy-activity.png)

复制活动在[集成运行时](concepts-integration-runtime.md)上执行。 对于不同的数据复制方案，可以使用不同类型的集成运行时：

* 在可以使用任何 IP 通过 Internet 公开访问的两个数据存储之间复制数据时，可对复制活动使用 Azure 集成运行时。 此集成运行时较为安全可靠、可缩放并[全局可用](concepts-integration-runtime.md#integration-runtime-location)。
* 向/从本地数据存储或位于具有访问控制的网络（如 Azure 虚拟网络）中的数据存储复制数据时，需要安装自承载集成运行时。

集成运行时需要与每个源数据存储和接收器数据存储相关联。 有关复制活动如何确定要使用的集成运行时的信息，请参阅[确定要使用哪个 IR](concepts-integration-runtime.md#determining-which-ir-to-use)。

若要将数据从源复制到接收器，运行复制活动的服务将执行以下步骤：

1. 读取源数据存储中的数据。
2. 执行序列化/反序列化、压缩/解压缩、列映射等。 此服务基于输入数据集、输出数据集和复制活动的配置执行这些操作。
3. 将数据写入接收器/目标数据存储。

![“复制活动”概述](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支持的数据存储和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>受支持的文件格式

可以使用复制活动在两个基于文件的数据存储之间按原样复制文件。 在这种情况下，无需任何序列化或反序列化操作即可有效复制数据。

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

例如，可执行以下复制活动：

* 从本地 SQL Server 数据库复制数据，并将数据以 Parquet 格式写入 Azure Data Lake Storage Gen2。
* 从本地文件系统中复制文本 (CSV) 格式文件，并将其以 Avro 格式写入 Azure Blob 存储。
* 从本地文件系统复制压缩文件，将其解压缩并写入 Azure Data Lake Storage Gen2。
* 从 Azure Blob 存储复制 Gzip 压缩文本 (CSV) 格式的数据，并将其写入 Azure SQL 数据库。
* 需要序列化/反序列化或压缩/解压缩的其他许多活动。

## <a name="supported-regions"></a>支持的区域

支持复制活动的服务已在 [Azure Integration Runtime 位置](concepts-integration-runtime.md#integration-runtime-location)中所列的区域和地理位置全球发布。 全局可用拓扑可确保高效的数据移动，此类移动通常避免跨区域跃点。 请参阅[各区域推出的产品](https://azure.microsoft.com/regions/#services)，检查数据工厂和数据移动是否可在特定的区域中使用。

## <a name="configuration"></a>配置

若要使用 Azure 数据工厂中的复制活动，需要执行以下操作：

1. **创建用于源数据存储和接收器数据存储的链接服务。** 有关配置信息和支持的属性，请参阅连接器文章的“链接服务属性”部分。 在本文的[支持的数据存储和格式](#supported-data-stores-and-formats)部分可以找到支持的连接器列表。
2. **为源和接收器创建数据集。** 有关配置信息和支持的属性，请参阅源和接收器连接器文章的“数据集属性”部分。
3. **创建包含复制活动的管道。** 接下来的部分将提供示例。

### <a name="syntax"></a>语法

以下复制活动模板包含受支持属性的完整列表。 指定适合你的方案的属性。

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>语法详细信息

| 属性 | 说明 | 必需？ |
|:--- |:--- |:--- |
| type | 对于复制活动，请设置为 `Copy` | 是 |
| inputs | 指定创建的指向源数据的数据集。 复制活动仅支持单个输入。 | 是 |
| outputs | 指定创建的指向接收器数据的数据集。 复制活动仅支持单个输出。 | 是 |
| typeProperties | 指定用于配置复制活动的属性。 | 是 |
| source | 指定复制源类型以及用于检索数据的相应属性。<br/><br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 接收器 | 指定复制接收器类型以及用于写入数据的相应属性。<br/><br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 转换器 | 指定从源到接收器的显式列映射。 当默认复制行为无法满足需求时，此属性适用。<br/><br/>有关详细信息，请参阅[复制活动中的架构映射](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定一个度量值，用于表示 [Azure Integration Runtime](concepts-integration-runtime.md) 在复制数据时的算力。 这些单位以前称为云数据移动单位 (DMU)。 <br/><br/>有关详细信息，请参阅[数据集成单位](copy-activity-performance.md#data-integration-units)。 | 否 |
| parallelCopies | 指定从源读取数据和向接收器写入数据时想要复制活动使用的并行度。<br/><br/>有关详细信息，请参阅[并行复制](copy-activity-performance.md#parallel-copy)。 | 否 |
| enableStaging<br/>stagingSettings | 指定是否将临时数据分阶段存储在 Blob 存储中，而不是将数据直接从源复制到接收器。<br/><br/>有关有用的方案和配置详细信息，请参阅[分阶段复制](copy-activity-performance.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 选择将数据从源复制到接收器时如何处理不兼容的行。<br/><br/>有关详细信息，请参阅[容错](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>监视

可通过编程方式或在 Azure 数据工厂的“创作和监视”UI 上监视复制活动运行。

### <a name="monitor-visually"></a>直观地监视

若要直观监视复制活动运行，请转到数据工厂，然后转到“创作和监视”。 在“监视”选项卡上，可以看到一个管道运行列表，其“操作”列中提供了“查看活动运行”按钮：

![监视管道运行](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

选择“查看管道运行”以查看管道运行中的活动列表。 在“操作”列中，可以看到复制活动输入、输出、错误（如果复制活动运行失败）和详细信息的链接：

![监视活动运行](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

选择“操作”列中的“详细信息”按钮，查看复制活动的执行详细信息和性能特征。 你将看到以下信息：从源复制到接收器的数据的数量/行数/文件数、吞吐量、复制活动执行的步骤数和相应的持续时间，以及复制方案使用的配置。

>[!TIP]
>在某些方案中，你还会在复制监视页的顶部看到“性能优化提示”。 这些提示告知已识别到的瓶颈，并提供一些信息来帮助你做出更改，以提升复制吞吐量。 有关示例，请参阅本文的[性能和优化](#performance-and-tuning)部分。

**示例：从 Amazon S3 复制到 Azure Data Lake Store**
![监视活动运行详细信息](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**示例：将数据从 AZURE Sql 数据库复制到 AZURE Sql 数据仓库，并在其中暂存复制**
![监视活动运行详细信息](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>以编程方式监视

“复制活动运行结果” **“输出”部分中也会返回复制活动执行详细信息和性能特征。**  >  下面是可能返回的属性的完整列表。 只会显示适用于你的复制方案的属性。 有关如何监视活动运行的信息，请参阅[监视管道运行](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)。

| 属性名称  | 说明 | 计价单位 |
|:--- |:--- |:--- |
| dataRead | 从源读取的数据量。 | Int64 值，以字节为单位 |
| DataWritten | 写入接收器的数据量。 | Int64 值，以字节为单位 |
| filesRead | 从文件存储复制过程中复制的文件数。 | Int64 值（未指定单位） |
| filesWritten | 复制到文件存储过程中复制的文件数。 | Int64 值（未指定单位） |
| sourcePeakConnections | 复制活动运行期间与源数据存储建立的并发连接峰值数量。 | Int64 值（未指定单位） |
| sinkPeakConnections | 复制活动运行期间与接收器数据存储建立的并发连接峰值数量。 | Int64 值（未指定单位） |
| rowsRead | 从源读取的行数（不适用于二进制副本）。 | Int64 值（未指定单位） |
| rowsCopied | 复制到接收器的行数（不适用于二进制副本）。 | Int64 值（未指定单位） |
| rowsSkipped | 跳过的不兼容行数。 可通过将 `enableSkipIncompatibleRow` 设置为 true 来跳过不兼容的行。 | Int64 值（未指定单位） |
| copyDuration | 复制运行的持续时间。 | Int32 值，以秒为单位 |
| throughput | 数据传输速率。 | 浮点数，以 KBps 为单位 |
| sourcePeakConnections | 复制活动运行期间与源数据存储建立的并发连接峰值数量。 | Int32 值（无单位） |
| sinkPeakConnections| 复制活动运行期间与接收器数据存储建立的并发连接峰值数量。| Int32 值（无单位） |
| sqlDwPolyBase | 将数据复制到 SQL 数据仓库时是否使用了 PolyBase。 | Boolean |
| redshiftUnload | 从 Redshift 复制数据时是否使用了 UNLOAD。 | Boolean |
| hdfsDistcp | 从 HDFS 复制数据时是否使用了 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用来为活动运行提供支持的一个或多个集成运行时 (IR)，采用 `<IR name> (<region if it's Azure IR>)` 格式。 | 文本（字符串） |
| usedDataIntegrationUnits | 复制期间的有效数据集成单位。 | Int32 值 |
| usedParallelCopies | 复制期间的有效 parallelCopies。 | Int32 值 |
| redirectRowPath | 在 `redirectIncompatibleRowSettings` 中配置的 Blob 存储中已跳过的不兼容行的日志路径。 请参阅本文稍后的[容错](#fault-tolerance)。 | 文本（字符串） |
| executionDetails | 有关复制活动经历的各个阶段、相应步骤、持续时间、配置等的更多详细信息。 不建议分析此节，因为它有可能发生更改。<br/><br/>数据工厂还会在 `detailedDurations` 下报告各个阶段花费的详细持续时间（以秒为单位）。 这些步骤的持续时间各不相同。 只显示适用于给定复制活动运行的持续时间：<br/>**排队持续时间**（`queuingDuration`）：复制活动在集成运行时中实际开始之前的时间量。 如果使用自承载 IR，而此值较大，请检查 IR 容量和用量，并根据工作负荷进行纵向或横向扩展。 <br/>**复制脚本的持续时间**（`preCopyScriptDuration`）：复制活动在 IR 上启动的时间与复制活动完成在接收器数据存储中运行预复制脚本之间经过的时间。 配置复制前脚本时适用。 <br/>**到第一个字节的时间**（`timeToFirstByte`）：前一步骤结束与 IR 接收源数据存储中第一个字节之间经过的时间。 适用于不是基于文件的源。 如果此值很大，请检查并优化查询或服务器。<br/>**传输持续时间**（`transferDuration`）：前一步骤结束与红外将所有数据从源复制到接收器之间所经过的时间。 | Array |
| perfRecommendation | 复制性能优化提示。 有关详细信息，请参阅[性能和优化](#performance-and-tuning)。 | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>架构和数据类型映射

有关复制活动如何将源数据映射到接收器的信息，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

## <a name="fault-tolerance"></a>容错

默认情况下，如果源数据行与接收器数据行不兼容，复制活动将停止复制数据，并返回失败结果。 要使复制成功，可将复制活动配置为跳过并记录不兼容的行，仅复制兼容的数据。 有关详细信息，请参阅[复制活动容错](copy-activity-fault-tolerance.md)。

## <a name="performance-and-tuning"></a>性能和优化

[复制活动性能和可伸缩性指南](copy-activity-performance.md)中描述了哪些关键因素会影响通过 Azure 数据工厂中的复制活动移动数据时的性能。 其中还列出了在测试期间观测到的性能值，并介绍了如何优化复制活动的性能。

在某些方案中，当你运行数据工厂中的复制活动时，将在**复制活动监视页**的顶部看到“性能调优提示”，如以下示例所示。[](#monitor-visually) 这些提示告知针对给定复制运行识别到的瓶颈。 其中还提供了一些信息来帮助你做出更改，以提升复制吞吐量。 性能优化提示目前提供如下建议：在将数据复制到 Azure SQL 数据仓库时使用 PolyBase；在数据存储端资源出现瓶颈时增加 Azure Cosmos DB RU 或 Azure SQL 数据库 DTU；删除不必要的暂存副本。

**示例：使用性能优化提示复制到 Azure SQL 数据库**

在此示例中，在复制运行期间，数据工厂发现接收器 Azure SQL 数据库的 DTU 利用率很高。 这种状况会减慢写入操作的速度。 建议增加 Azure SQL 数据库层上的 DTU：

![包含性能优化提示的复制监视](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>增量复制
数据工厂可让你以递增方式将增量数据从源数据存储复制到接收器数据存储。 有关详细信息，请参阅[教程：增量复制数据](tutorial-incremental-copy-overview.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下快速入门、教程和示例：

- [在同一 Azure Blob 存储帐户中将数据从一个位置复制到另一个位置](quickstart-create-data-factory-dot-net.md)
- [将数据从 Azure Blob 存储复制到 Azure SQL 数据库](tutorial-copy-data-dot-net.md)
- [将数据从本地 SQL Server 数据库复制到 Azure](tutorial-hybrid-copy-powershell.md)
