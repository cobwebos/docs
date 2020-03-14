---
title: Azure 数据工厂中的复制活动
description: 了解 Azure 数据工厂中的复制活动。 可以使用它将数据从支持的源数据存储复制到受支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 616cdc0387d5c5cf3c2980ae1cfbc10e3c1119f4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261354"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 数据工厂中的复制活动

> [!div class="op_single_selector" title1="选择要使用的数据工厂的版本："]
> * [版本 1](v1/data-factory-data-movement-activities.md)
> * [当前版本](copy-activity-overview.md)

在 Azure 数据工厂中，可以使用复制活动在本地和云中的数据存储之间复制数据。 复制数据后，可以使用其他活动进一步转换和分析数据。 你还可以使用复制活动发布商业智能（BI）和应用程序使用情况的转换和分析结果。

![复制活动的角色](media/copy-activity-overview/copy-activity.png)

复制活动在[集成运行时](concepts-integration-runtime.md)上执行。 对于不同的数据复制方案，可以使用不同类型的集成运行时：

* 如果要在可通过 internet 从任何 IP 中公开访问的两个数据存储之间复制数据，则可以使用 Azure 集成运行时执行复制活动。 此集成运行时是安全、可靠、可缩放和[全局可用](concepts-integration-runtime.md#integration-runtime-location)的。
* 将数据复制到本地或通过访问控制（例如，Azure 虚拟网络）在网络中的数据存储时，需要设置自承载集成运行时。

集成运行时需要与每个源数据存储和接收器数据存储相关联。 有关复制活动如何确定要使用的集成运行时的信息，请参阅[确定要使用的 IR](concepts-integration-runtime.md#determining-which-ir-to-use)。

若要将数据从源复制到接收器，运行复制活动的服务将执行以下步骤：

1. 读取源数据存储中的数据。
2. 执行序列化/反序列化、压缩/解压缩、列映射等。 它基于输入数据集、输出数据集和复制活动的配置执行这些操作。
3. 将数据写入接收器/目标数据存储。

![“复制活动”概述](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支持的数据存储和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>支持的文件格式

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

您可以使用复制活动在两个基于文件的数据存储之间按原样复制文件，在这种情况下，将在不进行任何序列化或反序列化的情况下有效地复制数据。 此外，还可以分析或生成给定格式的文件，例如，可以执行以下操作：

* 复制本地 SQL Server 数据库中的数据，并以 Parquet 格式写入 Azure Data Lake Storage Gen2。
* 从本地文件系统中复制文本（CSV）格式的文件，并使用 Avro 格式写入 Azure Blob 存储。
* 从本地文件系统中复制压缩文件，将其解压缩，然后将提取的文件写入 Azure Data Lake Storage Gen2。
* 从 Azure Blob 存储复制 Gzip 压缩文本（CSV）格式的数据，并将其写入 Azure SQL 数据库。
* 需要序列化/反序列化或压缩/解压缩的更多活动。

## <a name="supported-regions"></a>支持的区域

启用复制活动的服务可在[Azure 集成运行时位置](concepts-integration-runtime.md#integration-runtime-location)中列出的区域和地理位置全局使用。 全局可用拓扑可确保高效的数据移动，此类移动通常避免跨区域跃点。 查看[产品（按区域](https://azure.microsoft.com/regions/#services)），以检查特定区域中的数据工厂和数据移动的可用性。

## <a name="configuration"></a>配置

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

通常，若要在 Azure 数据工厂中使用复制活动，需要执行以下操作：

1. **为源数据存储和接收器数据存储创建链接服务。** 可以在本文的 "[支持的数据存储和格式](#supported-data-stores-and-formats)" 部分找到受支持的连接器列表。 请参阅连接器文章的 "链接服务属性" 部分，了解配置信息和支持的属性。 
2. **为源和接收器创建数据集。** 请参阅源和接收器连接器文章中的 "数据集属性" 部分，了解配置信息和支持的属性。
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

| properties | 说明 | 必需？ |
|:--- |:--- |:--- |
| type | 对于复制活动，将设置为 `Copy` | 是 |
| inputs | 指定你创建的指向源数据的数据集。 复制活动仅支持一个输入。 | 是 |
| outputs | 指定你创建的指向接收器数据的数据集。 复制活动仅支持单个输出。 | 是 |
| typeProperties | 指定用于配置复制活动的属性。 | 是 |
| source | 指定复制源类型和用于检索数据的相应属性。<br/>有关详细信息，请参阅[支持的数据存储和格式](#supported-data-stores-and-formats)中列出的连接器文章中的 "复制活动属性" 部分。 | 是 |
| 接收器 | 指定复制接收器类型和用于写入数据的相应属性。<br/>有关详细信息，请参阅[支持的数据存储和格式](#supported-data-stores-and-formats)中列出的连接器文章中的 "复制活动属性" 部分。 | 是 |
| 转换器 | 指定从源到接收器的显式列映射。 当默认的复制行为不能满足您的需求时，此属性适用。<br/>有关详细信息，请参阅[复制活动中的架构映射](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定一个度量值，该度量值表示[Azure 集成运行时](concepts-integration-runtime.md)用于数据复制的电源量。 这些单位以前称为云数据移动单元（DMU）。 <br/>有关详细信息，请参阅[数据集成单元](copy-activity-performance.md#data-integration-units)。 | 否 |
| parallelCopies | 指定在从源读取数据并将数据写入接收器时希望复制活动使用的并行度。<br/>有关详细信息，请参阅[并行复制](copy-activity-performance.md#parallel-copy)。 | 否 |
| 保护区 | 指定在数据复制期间是否保留元数据/Acl。 <br/>有关详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md)。 |否 |
| enableStaging<br/>stagingSettings | 指定是否在 Blob 存储中暂存临时数据，而不是直接将数据从源复制到接收器。<br/>有关有用方案和配置详细信息的信息，请参阅[暂存复制](copy-activity-performance.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 选择在将数据从源复制到接收器时如何处理不兼容的行。<br/>有关详细信息，请参阅[容错](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>监视

你可以以直观方式和编程方式监视 Azure 数据工厂中的复制活动运行。 有关详细信息，请参阅[监视复制活动](copy-activity-monitoring.md)。

## <a name="incremental-copy"></a>增量复制

利用数据工厂，你可以以增量方式将源数据存储中的增量数据复制到接收器数据存储。 有关详细信息，请参阅[教程：增量复制数据](tutorial-incremental-copy-overview.md)。

## <a name="performance-and-tuning"></a>性能和优化

[复制活动监视](copy-activity-monitoring.md)体验显示每个活动运行的复制性能统计信息。 [复制活动的性能和可伸缩性指南](copy-activity-performance.md)介绍了影响通过 Azure 数据工厂中的复制活动进行的数据移动性能的关键因素。 它还列出了测试过程中观测到的性能值，并讨论了如何优化复制活动的性能。

## <a name="resume-from-last-failed-run"></a>从上次失败的运行恢复

复制活动支持从上一次失败的运行中恢复，当你在基于文件的存储之间按二进制格式复制大量文件，并选择将文件夹/文件层次结构从源复制到接收器，例如，将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2。 它适用于以下基于文件的连接器： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)和[SFTP](connector-sftp.md)。

可以通过以下两种方式利用复制活动恢复：

- **活动级别重试：** 可以设置复制活动的重试次数。 在管道执行过程中，如果此复制活动运行失败，则下一次自动重试将从上一个试用的故障点开始。
- **从失败的活动重新运行：** 管道执行完成后，还可以在 ADF UI 监视视图中或以编程方式触发从失败的活动中重新运行。 如果失败的活动是复制活动，则该管道将只能从此活动重新运行，但也会从上一个运行的故障点恢复。

    ![复制恢复](media/copy-activity-overview/resume-copy.png)

请注意以下几点：

- 恢复发生在文件级别。 如果复制活动在复制文件时失败，则在下一次运行时，将重新复制此特定文件。
- 若要使恢复正常工作，请不要在重新运行时更改复制活动设置。
- 从 Amazon S3、Azure Blob、Azure Data Lake Storage Gen2 和 Google 云存储复制数据时，复制活动可以从任意数量的复制文件恢复。 虽然作为源的其他基于文件的连接器的其余部分，当前复制活动支持从有限数量的文件中恢复，通常是在数十个文件的范围内，并且根据文件路径的长度而变化;在重新运行期间，将重新复制超出此数量的文件。

对于二进制文件复制以外的其他方案，将从开头开始重新运行复制活动。

## <a name="preserve-metadata-along-with-data"></a>保留元数据以及数据

在将数据从源复制到接收器的情况下，在进行 data lake 迁移的情况下，你还可以选择使用复制活动来保存元数据和 Acl 以及数据。 有关详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md)。

## <a name="schema-and-data-type-mapping"></a>架构和数据类型映射

有关复制活动如何将源数据映射到接收器的信息，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

## <a name="fault-tolerance"></a>容错

默认情况下，复制活动将停止复制数据，并在源数据行与接收器数据行不兼容时返回失败。 若要使复制成功，你可以将复制活动配置为跳过和记录不兼容的行并仅复制兼容的数据。 有关详细信息，请参阅[复制活动容错](copy-activity-fault-tolerance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下快速入门、教程和示例：

- [将数据从同一 Azure Blob 存储帐户中的一个位置复制到另一个位置](quickstart-create-data-factory-dot-net.md)
- [将数据从 Azure Blob 存储复制到 Azure SQL 数据库](tutorial-copy-data-dot-net.md)
- [将数据从本地 SQL Server 数据库复制到 Azure](tutorial-hybrid-copy-powershell.md)
