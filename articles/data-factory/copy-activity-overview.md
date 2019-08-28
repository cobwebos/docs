---
title: Azure 数据工厂中的复制活动 |Microsoft Docs
description: 了解 Azure 数据工厂中的复制活动。 可以使用它将数据从支持的源数据存储复制到受支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 8af5673ff0ffef7306a13eceda86f879b5b31413
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060696"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 数据工厂中的复制活动

> [!div class="op_single_selector" title1="选择要使用的数据工厂的版本:"]
> * [版本 1](v1/data-factory-data-movement-activities.md)
> * [当前版本](copy-activity-overview.md)

在 Azure 数据工厂中, 可以使用复制活动在本地和云中的数据存储之间复制数据。 复制数据后, 可以使用其他活动进一步转换和分析数据。 你还可以使用复制活动发布商业智能 (BI) 和应用程序使用情况的转换和分析结果。

![复制活动的角色](media/copy-activity-overview/copy-activity.png)

复制活动在[集成运行时](concepts-integration-runtime.md)上执行。 对于不同的数据复制方案, 可以使用不同类型的集成运行时:

* 如果要在可通过 internet 从任何 IP 中公开访问的两个数据存储之间复制数据, 则可以使用 Azure 集成运行时执行复制活动。 此集成运行时是安全、可靠、可缩放和[全局可用](concepts-integration-runtime.md#integration-runtime-location)的。
* 将数据复制到本地或通过访问控制 (例如, Azure 虚拟网络) 在网络中的数据存储时, 需要设置自承载集成运行时。

集成运行时需要与每个源数据存储和接收器数据存储相关联。 有关复制活动如何确定要使用的集成运行时的信息, 请参阅[确定要使用的 IR](concepts-integration-runtime.md#determining-which-ir-to-use)。

若要将数据从源复制到接收器, 运行复制活动的服务将执行以下步骤:

1. 读取源数据存储中的数据。
2. 执行序列化/反序列化、压缩/解压缩、列映射等。 它基于输入数据集、输出数据集和复制活动的配置执行这些操作。
3. 将数据写入接收器/目标数据存储。

![复制活动概述](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支持的数据存储和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>支持的文件格式

您可以使用复制活动在两个基于文件的数据存储之间按原样复制文件。 在这种情况下, 无需任何序列化或反序列化即可有效地复制数据。

复制活动还可以读取和写入以下格式的文件:
- 文本
- JSON
- Avro
- ORC
- Parquet

复制活动可以压缩和解压缩带有以下编解码器的文件: 
- Gzip
- Deflate
- Bzip2
- ZipDeflate

有关详细信息, 请参阅[支持的文件和压缩格式](supported-file-formats-and-compression-codecs.md)。

例如, 你可以执行以下复制活动:

* 复制本地 SQL Server 数据库中的数据, 并以 Parquet 格式将数据写入 Azure Data Lake Storage Gen2。
* 从本地文件系统中复制文本 (CSV) 格式的文件, 并使用 Avro 格式写入 Azure Blob 存储。
* 从本地文件系统中复制压缩文件, 将其解压缩, 并将其写入 Azure Data Lake Storage Gen2。
* 从 Azure Blob 存储复制 Gzip 压缩文本 (CSV) 格式的数据, 并将其写入 Azure SQL 数据库。
* 需要序列化/反序列化或压缩/解压缩的更多活动。

## <a name="supported-regions"></a>支持的区域

启用复制活动的服务可在[Azure 集成运行时位置](concepts-integration-runtime.md#integration-runtime-location)中列出的区域和地理位置全局使用。 全局可用拓扑可确保高效的数据移动，此类移动通常避免跨区域跃点。 查看[产品 (按区域](https://azure.microsoft.com/regions/#services)), 以检查特定区域中的数据工厂和数据移动的可用性。

## <a name="configuration"></a>配置

若要在 Azure 数据工厂中使用复制活动, 需执行以下操作:

1. **为源数据存储和接收器数据存储创建链接服务。** 请参阅连接器文章的 "链接服务属性" 部分, 了解配置信息和支持的属性。 可以在本文的 "[支持的数据存储和格式](#supported-data-stores-and-formats)" 部分找到受支持的连接器列表。
2. **为源和接收器创建数据集。** 请参阅源和接收器连接器文章中的 "数据集属性" 部分, 了解配置信息和支持的属性。
3. **使用复制活动创建管道。** 接下来的部分将提供示例。

### <a name="syntax"></a>语法

以下复制活动模板包含支持的属性的完整列表。 指定适合你的方案的属性。

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

| 属性 | 描述 | 必需？ |
|:--- |:--- |:--- |
| type | 对于复制活动, 将设置为`Copy` | 是 |
| inputs | 指定你创建的指向源数据的数据集。 复制活动仅支持一个输入。 | 是 |
| outputs | 指定你创建的指向接收器数据的数据集。 复制活动仅支持单个输出。 | 是 |
| typeProperties | 指定用于配置复制活动的属性。 | 是 |
| source | 指定复制源类型和用于检索数据的相应属性。<br/><br/>有关详细信息, 请参阅[支持的数据存储和格式](#supported-data-stores-and-formats)中列出的连接器文章中的 "复制活动属性" 部分。 | 是 |
| sink | 指定复制接收器类型和用于写入数据的相应属性。<br/><br/>有关详细信息, 请参阅[支持的数据存储和格式](#supported-data-stores-and-formats)中列出的连接器文章中的 "复制活动属性" 部分。 | 是 |
| translator | 指定从源到接收器的显式列映射。 当默认的复制行为不能满足您的需求时, 此属性适用。<br/><br/>有关详细信息, 请参阅[复制活动中的架构映射](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定一个度量值, 该度量值表示[Azure 集成运行时](concepts-integration-runtime.md)用于数据复制的电源量。 这些单位以前称为云数据移动单元 (DMU)。 <br/><br/>有关详细信息, 请参阅[数据集成单元](copy-activity-performance.md#data-integration-units)。 | 否 |
| parallelCopies | 指定在从源读取数据并将数据写入接收器时希望复制活动使用的并行度。<br/><br/>有关详细信息，请参阅[并行复制](copy-activity-performance.md#parallel-copy)。 | 否 |
| enableStaging<br/>stagingSettings | 指定是否在 Blob 存储中暂存临时数据, 而不是直接将数据从源复制到接收器。<br/><br/>有关有用方案和配置详细信息的信息, 请参阅[暂存复制](copy-activity-performance.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 选择在将数据从源复制到接收器时如何处理不兼容的行。<br/><br/>有关详细信息, 请参阅[容错](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>监视

可以在 Azure 数据工厂**创作 & 监视器**UI 或以编程方式监视复制活动运行。

### <a name="monitor-visually"></a>直观地监视

若要以可视方式监视复制活动运行, 请在数据工厂中转到 "**创作 & 监视器**"。 在 "**监视**" 选项卡上, 可在 "**操作**" 列中看到一系列具有 "**查看活动运行**" 按钮的管道运行:

![监视管道运行](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

选择 "**查看活动运行**" 以查看管道运行中的活动列表。 在 "**操作**" 列中, 可以看到 "复制活动输入"、"输出"、"错误" (如果复制活动运行失败) 的链接和详细信息:

![监视活动运行](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

选择 "**操作**" 列中的 "**详细信息**" 按钮, 查看复制活动的执行详细信息和性能特征。 你会看到各种信息, 例如卷/行数/从源复制到接收器的数据文件数、吞吐量、复制活动在相应持续时间内的执行步骤, 以及用于复制方案的配置。

>[!TIP]
>在某些情况下, 你还将看到 "复制监视" 页顶部的 "**性能优化提示**"。 这些提示会告诉你确定的瓶颈, 并提供有关如何更改以提高复制吞吐量的信息。 有关示例, 请参阅本文的[性能和优化](#performance-and-tuning)部分。

示例：**从 Amazon S3 复制到 Azure Data Lake Store**
![监视活动运行详细信息](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

示例：**将 azure sql 数据库从 azure sql 数据库复制到 azure sql 数据**仓库, 其中包含暂存复制
![监视器活动运行详细信息](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>以编程方式监视

复制活动的执行详细信息和性能特征也将在**复制活动运行结果** > **输出**部分中返回。 下面是可能返回的属性的完整列表。 你将仅看到适用于你的复制方案的属性。 有关如何监视活动运行的信息, 请参阅[监视管道运行](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)。

| 属性名  | 描述 | 单位 |
|:--- |:--- |:--- |
| dataRead | 从源中读取的数据量。 | Int64 值 (以字节为单位) |
| dataWritten | 写入接收器的数据量。 | Int64 值 (以字节为单位) |
| filesRead | 从文件存储复制过程中复制的文件数。 | Int64 值（未指定单位） |
| filesWritten | 复制到文件存储期间复制的文件数。 | Int64 值（未指定单位） |
| sourcePeakConnections | 复制活动运行期间为源数据存储建立的并发连接的峰值数量。 | Int64 值（未指定单位） |
| sinkPeakConnections | 复制活动运行期间, 建立到接收器数据存储的并发连接的峰值数量。 | Int64 值（未指定单位） |
| rowsRead | 从源中读取的行数 (不适用于二进制副本)。 | Int64 值（未指定单位） |
| rowsCopied | 复制到接收器的行数 (不适用于二进制副本)。 | Int64 值（未指定单位） |
| rowsSkipped | 跳过的不兼容行的数目。 如果将设置`enableSkipIncompatibleRow`为 true, 则可以跳过不兼容的行。 | Int64 值（未指定单位） |
| copyDuration | 复制运行的持续时间。 | Int32 值 (秒) |
| throughput | 数据传输速率。 | 浮点数 (以 KBps 为单位) |
| sourcePeakConnections | 复制活动运行期间为源数据存储建立的并发连接的峰值数量。 | Int32 值 (无单位) |
| sinkPeakConnections| 复制活动运行期间, 建立到接收器数据存储的并发连接的峰值数量。| Int32 值 (无单位) |
| sqlDwPolyBase | 将数据复制到 SQL 数据仓库时是否使用 PolyBase。 | Boolean |
| redshiftUnload | 从 Redshift 复制数据时是否使用 UNLOAD。 | Boolean |
| hdfsDistcp | 从 HDFS 复制数据时是否使用 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用于使活动运行通电的集成运行时 (IR) 或运行时, 格式`<IR name> (<region if it's Azure IR>)`为。 | 文本（字符串） |
| usedDataIntegrationUnits | 复制期间的有效数据集成单位。 | Int32 值 |
| usedParallelCopies | 复制期间的有效 parallelCopies。 | Int32 值 |
| redirectRowPath | 在`redirectIncompatibleRowSettings`属性中配置的 blob 存储中跳过不兼容行的日志路径。 请参阅本文后面的[容错能力](#fault-tolerance)。 | 文本（字符串） |
| executionDetails | 有关复制活动所经历的阶段的详细信息以及相应的步骤、持续时间、配置等。 我们不建议你分析此部分, 因为它可能会更改。<br/><br/>数据工厂还报告在下`detailedDurations`各个阶段花费的详细持续时间 (以秒为单位)。 这些步骤的持续时间各不相同。 只显示应用于给定复制活动运行的持续时间:<br/>**排队持续时间**(`queuingDuration`):在集成运行时中实际开始复制活动之前的时间量。 如果使用自承载 IR 并且此值很大, 请检查 IR 容量和使用情况, 并根据工作负荷增加或缩减。 <br/>**复制前脚本持续时间**(`preCopyScriptDuration`):复制活动在 IR 上开始与复制活动在接收器数据存储中运行预复制脚本之间所经过的时间。 在配置复制前脚本时应用。 <br/>**至第一字节的时间**(`timeToFirstByte`):上一步结束时间与 IR 接收源数据存储中第一个字节之间经过的时间。 适用于非基于文件的源。 如果此值很大, 请检查并优化查询或服务器。<br/>**传输持续时间**(`transferDuration`):上一步结束与红外将所有数据从源复制到接收器之间所经过的时间。 | 阵列 |
| perfRecommendation | 复制性能优化提示。 有关详细信息, 请参阅[性能和优化](#performance-and-tuning)。 | 阵列 |

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

有关复制活动如何将源数据映射到接收器的信息, 请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

## <a name="fault-tolerance"></a>容错

默认情况下, 复制活动将停止复制数据, 并在源数据行与接收器数据行不兼容时返回失败。 若要使复制成功, 你可以将复制活动配置为跳过和记录不兼容的行并仅复制兼容的数据。 有关详细信息, 请参阅[复制活动容错](copy-activity-fault-tolerance.md)。

## <a name="performance-and-tuning"></a>性能和优化

[复制活动的性能和可伸缩性指南](copy-activity-performance.md)介绍了影响通过 Azure 数据工厂中的复制活动进行的数据移动性能的关键因素。 它还列出了测试过程中观测到的性能值, 并讨论了如何优化复制活动的性能。

在某些情况下, 当你在数据工厂中运行复制活动时, 你将看到 "[复制活动监视" 页](#monitor-visually)顶部的 "**性能优化提示**", 如下面的示例中所示。 提示告诉您为给定的副本运行确定的瓶颈。 它们还提供了有关如何更改以提高复制吞吐量的信息。 性能优化提示目前提供了一些建议, 例如, 将数据复制到 Azure SQL 数据仓库时使用 PolyBase, 在数据存储端的资源是瓶颈时增加 Azure Cosmos DB ru 或 Azure SQL 数据库 Dtu, 并删除不必要的暂存副本。

示例：**使用性能优化提示复制到 Azure SQL 数据库**

在此示例中, 在运行复制的过程中, 数据工厂会跟踪接收器 Azure SQL 数据库中的高 DTU 使用率。 这种情况会减缓写入操作的速度。 建议在 Azure SQL 数据库层上增加 Dtu:

![包含性能优化提示的复制监视](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>增量复制
利用数据工厂, 你可以以增量方式将源数据存储中的增量数据复制到接收器数据存储。 有关详细信息, [请参阅教程:以增量方式](tutorial-incremental-copy-overview.md)复制数据。

## <a name="next-steps"></a>后续步骤
请参阅以下快速入门、教程和示例：

- [将数据从同一 Azure Blob 存储帐户中的一个位置复制到另一个位置](quickstart-create-data-factory-dot-net.md)
- [将数据从 Azure Blob 存储复制到 Azure SQL 数据库](tutorial-copy-data-dot-net.md)
- [将数据从本地 SQL Server 数据库复制到 Azure](tutorial-hybrid-copy-powershell.md)
