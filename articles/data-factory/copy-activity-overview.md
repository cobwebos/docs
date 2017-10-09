---
title: "Azure 数据工厂中的复制活动 | Microsoft Docs"
description: "了解 Azure 数据工厂中可用于将数据从支持的源数据存储复制到支持的接收器数据存储的复制活动。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 784b9489911be0d9a559b6fe7795a6c2cbdb09fc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 数据工厂中的复制活动

## <a name="overview"></a>概述

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-data-movement-activities.md)
> * [版本 2 - 预览版](copy-activity-overview.md)

在 Azure 数据工厂中，可使用复制活动在本地和云数据存储之间复制数据。 复制数据后，可对其进一步执行转换和分析操作。 还可使用复制活动发布有关商业智能 (BI) 和应用程序消耗的转换和分析结果。

![复制活动的角色](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 的数据工厂。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动](v1/data-factory-data-movement-activities.md)。

复制活动在[集成运行时](concepts-integration-runtime.md)上执行。 对于不同的数据复制方案，可以利用不同风格的集成运行时：

* 在两个可公开访问的数据存储间复制数据时，复制活动可由 **Azure 集成运行时**授权，该活动安全可靠、可缩放并可[全局访问](concepts-integration-runtime.md#integration-runtime-location)。
* 当从本地数据存储或位于具有访问控制的网络（如 Azure 虚拟网络）中的数据存储中复制数据，或将数据复制到此处时，需要安装**自承载集成运行时**来授权数据复制。

集成运行时需要与每个源数据存储和接收器数据存储相关联。 了解复制活动如何[决定使用哪个 IR](concepts-integration-runtime.md#determining-which-ir-to-use) 的详细信息。

将数据从源复制到接收器，复制活动要经过以下步骤。 为复制活动提供支持的服务可：

1. 读取源数据存储中的数据。
2. 执行序列化/反序列化、压缩/解压缩、列映射等。此服务基于输入数据集、输出数据集和复制活动的配置执行这些操作。
3. 将数据写入接收器/目标数据存储。

![复制活动概述](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支持的数据存储和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>支持的文件格式

可以使用复制活动**复制**基于文件的两个数据存储间的文件，这种情况下，数据可以高效复制而无需任何序列化/反序列化。

复制活动还支持以指定格式（**Text、JSON、Avro、ORC 和 Parquet**）从文件中读取并写入到文件，并且支持压缩编解码器 **GZip、Deflate、BZip2 和 ZipDeflate**。 有关详细信息，请参阅[支持的文件和压缩格式](supported-file-formats-and-compression-codecs.md)。

例如，可执行以下复制活动：

* 从本地 SQL Server 中复制数据，并将其以 ORC 格式写入 Azure Data Lake Store。
* 从本地文件系统中复制文本 (CSV) 格式文件，并将其以 Avro 格式写入 Azure Blob。
* 从本地文件系统中复制压缩文件，并将其解压缩然后传到 Azure Data Lake Store。
* 从 Azure Blob 复制 GZip 压缩文本 (CSV) 格式的数据，并将其写入 Azure SQL 数据库。

## <a name="configuration"></a>配置

要使用 Azure 数据工厂中的复制活动，需要执行以下操作：

1. **创建用于源数据存储和接收器数据存储的链接服务。** 有关如何配置支持的属性，请参阅连接器文章的“链接服务属性”部分。 受支持的连接器列表可以在[受支持的数据存储和格式](#supported-data-stores-and-formats)部分找到。
2. **为源和接收器创建数据集。** 有关如何配置支持的属性，请参阅源和接收器连接器文章的“数据集属性”部分。
3. **借助复制活动创建管道。** 接下来的部分将提供示例。  

### <a name="syntax"></a>语法

以下复制活动模板列出了详细的受支持属性。 指定适合你的方案的属性。

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
                "ColumnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
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

### <a name="syntax-details"></a>语法详细信息

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动的类型属性必须设置为：**复制** | 是 |
| inputs | 指定创建的指向源数据的数据集。 复制活动仅支持单个输入。 | 是 |
| outputs | 指定创建的指向接收器数据的数据集。 复制活动仅支持单个输出。 | 是 |
| typeProperties | 一组用来配置复制活动的属性。 | 是 |
| 源 | 指定有关如何检索数据的复制源类型和相应的属性。<br/><br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 接收器 | 指定有关如何写入数据的复制接收器类型和相应的属性。<br/><br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 转换器 | 指定从源到接收器的显式列映射。 当默认复制行为无法满足需求时适用。<br/><br/>有关详细信息，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。 | 否 |
| cloudDataMovementUnits | 指定 [Azure 集成运行时](concepts-integration-runtime.md)的强度来授权数据复制。<br/><br/>有关详细信息，请参阅[云数据移动单位](copy-activity-performance.md)。 | 否 |
| parallelCopies | 指定从源读取数据和向接收器写入数据时想要复制活动使用的并行度。<br/><br/>详细信息请参阅[并行复制](copy-activity-performance.md#parallel-copy)。 | 否 |
| enableStaging<br/>stagingSettings | 选择将临时数据分阶段存储在 aa blob 存储中，而不是将数据直接从源复制到接收器。<br/><br/>请参阅[暂存复制](copy-activity-performance.md#staged-copy)了解有用的方案和配置详细信息。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 选择将数据从源复制到接收器时如何处理不兼容的行。<br/><br/>有关详细信息，请参阅[容错](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>监视

复制活动执行详细信息和性能特性将返回到“复制活动运行结果 -> 输出”部分。 下面是详细列表。 了解如何从[快速入门监视部分](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)监视活动运行。 可将方案的性能和配置与内部测试中复制活动的[性能参考](copy-activity-performance.md#performance-reference)进行比较。

| 属性名称  | 说明 | 计价单位 |
|:--- |:--- |:--- |
| dataRead | 从源中读取的数据大小 | Int64 值（字节） |
| DataWritten | 写入接收器的数据大小 | Int64 值（字节） |
| rowsCopied | 正在复制（不适用于二进制复制）的行数。 | Int64 值（未指定单位） |
| rowsSkipped | 跳过的不兼容行数。 可以通过将“enableSkipIncompatibleRow”设置为 true 来启用该功能。 | Int64 值（未指定单位） |
| throughput | 数据传输比率 | 浮点数 (KB/s) |
| copyDuration | 复制持续时间 | Int32 值（秒） |
| sqlDwPolyBase | 如果将数据复制到 SQL 数据仓库时使用了 PolyBase。 | 布尔 |
| redshiftUnload | 如果从 Redshift 复制数据时使用了 UNLOAD。 | 布尔 |
| hdfsDistcp | 如果从 HDFS 复制数据时使用了 DistCp。 | 布尔 |
| effectiveIntegrationRuntime | 以“`<IR name> (<region if it's Azure IR>)`”格式显示运行活动时使用的集成运行时。 | 文本（字符串） |
| usedCloudDataMovementUnits | 复制期间有效的云数据移动单位。 | Int32 值 |
| redirectRowPath | 在“redirectIncompatibleRowSettings”下配置的 blob 存储中跳过的不兼容行的日志路径。 请参阅下面的示例。 | 文本（字符串） |
| billedDuration | 数据移动所需的持续时间。 | Int32 值（秒） |

```json
"output": {
    "dataRead": 1024,
    "dataWritten": 2048,
    "rowsCopies": 100,
    "rowsSkipped": 2,
    "throughput": 1024.0,
    "copyDuration": 3600,
    "redirectRowPath": "https://<account>.blob.core.windows.net/<path>/<pipelineRunID>/",
    "redshiftUnload": true,
    "sqlDwPolyBase": true,
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 8,
    "billedDuration": 28800
}
```

## <a name="schema-and-data-type-mapping"></a>架构和数据类型映射

请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)，其中说明了复制活动如何将源数据映射到接收器。

## <a name="fault-tolerance"></a>容错

默认情况下，如果遇到源和接收器之间数据不兼容，复制活动将停止复制数据，并返回故障。 您可以显式配置以跳过并记录不兼容的行，仅复制那些兼容的数据，以便成功复制。 有关详细信息，请参阅[复制活动容错](copy-activity-fault-tolerance.md)。

## <a name="performance-and-tuning"></a>性能和优化

请参阅[复制活动性能和优化指南](copy-activity-performance.md)，其中介绍了影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素。 还列出了在内部测试期间观察到的性能，并讨论了优化复制活动性能的多种方法。

## <a name="next-steps"></a>后续步骤
请参阅以下快速入门、教程和示例：

- [在同一 Azure Blob 存储中将数据从一个位置复制到另一个位置](quickstart-create-data-factory-dot-net.md)
- [将数据从 Azure Blob 存储复制到 Azure SQL 数据库](tutorial-copy-data-dot-net.md)
- [将数据从本地 SQL Server 复制到 Azure](tutorial-hybrid-copy-powershell.md)

