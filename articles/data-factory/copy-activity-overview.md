---
title: Azure 数据工厂中的复制活动
description: 了解 Azure 数据工厂中的复制活动。 可以使用复制活动将数据从支持的源数据存储复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jingwang
ms.openlocfilehash: 3a1e5ed7d9ca14c03483cb6afe6b6318c6a90764
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440586"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 数据工厂中的复制活动

> [!div class="op_single_selector" title1="选择要使用的数据工厂的版本："]
> * [版本 1](v1/data-factory-data-movement-activities.md)
> * [当前版本](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

### <a name="supported-file-formats"></a>支持的文件格式

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

可以使用复制活动在两个基于文件的数据存储之间按原样复制文件，在这种情况下，无需任何序列化或反序列化即可高效复制数据。 此外，还可以分析或生成给定格式的文件。例如，可以执行以下操作：

* 从 SQL Server 数据库复制数据，并将数据以 Parquet 格式写入 Azure Data Lake Storage Gen2。
* 从本地文件系统中复制文本 (CSV) 格式文件，并将其以 Avro 格式写入 Azure Blob 存储。
* 从本地文件系统复制压缩文件，动态解压缩，然后将提取的文件写入 Azure Data Lake Storage Gen2。
* 从 Azure Blob 存储复制 Gzip 压缩文本 (CSV) 格式的数据，并将其写入 Azure SQL 数据库。
* 需要序列化/反序列化或压缩/解压缩的其他许多活动。

## <a name="supported-regions"></a>支持的区域

支持复制活动的服务已在 [Azure Integration Runtime 位置](concepts-integration-runtime.md#integration-runtime-location)中所列的区域和地理位置全球发布。 全局可用拓扑可确保高效的数据移动，此类移动通常避免跨区域跃点。 请参阅[各区域推出的产品](https://azure.microsoft.com/regions/#services)，检查数据工厂和数据移动是否可在特定的区域中使用。

## <a name="configuration"></a>配置

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

通常，若要使用 Azure 数据工厂中的复制活动，需要执行以下操作：

1. **创建用于源数据存储和接收器数据存储的链接服务。** 在本文的[支持的数据存储和格式](#supported-data-stores-and-formats)部分可以找到支持的连接器列表。 有关配置信息和支持的属性，请参阅连接器文章的“链接服务属性”部分。 
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

| properties | 说明 | 必需？ |
|:--- |:--- |:--- |
| type | 对于复制活动，请设置为 `Copy` | 是 |
| inputs | 指定创建的指向源数据的数据集。 复制活动仅支持单个输入。 | 是 |
| outputs | 指定创建的指向接收器数据的数据集。 复制活动仅支持单个输出。 | 是 |
| typeProperties | 指定用于配置复制活动的属性。 | 是 |
| source | 指定复制源类型以及用于检索数据的相应属性。<br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 接收器 | 指定复制接收器类型以及用于写入数据的相应属性。<br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 转换器 | 指定从源到接收器的显式列映射。 当默认复制行为无法满足需求时，此属性适用。<br/>有关详细信息，请参阅[复制活动中的架构映射](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定一个度量值，用于表示 [Azure Integration Runtime](concepts-integration-runtime.md) 在复制数据时的算力。 这些单位以前称为云数据移动单位 (DMU)。 <br/>有关详细信息，请参阅[数据集成单位](copy-activity-performance-features.md#data-integration-units)。 | 否 |
| parallelCopies | 指定从源读取数据和向接收器写入数据时想要复制活动使用的并行度。<br/>有关详细信息，请参阅[并行复制](copy-activity-performance-features.md#parallel-copy)。 | 否 |
| 保护区 | 指定在数据复制期间是否保留元数据/ACL。 <br/>有关详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md)。 |否 |
| enableStaging<br/>stagingSettings | 指定是否将临时数据分阶段存储在 Blob 存储中，而不是将数据直接从源复制到接收器。<br/>有关有用的方案和配置详细信息，请参阅[分阶段复制](copy-activity-performance-features.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 选择将数据从源复制到接收器时如何处理不兼容的行。<br/>有关详细信息，请参阅[容错](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>监视

可通过视觉和编程方式监视在 Azure 数据工厂中运行的复制活动。 有关详细信息，请参阅[监视复制活动](copy-activity-monitoring.md)。

## <a name="incremental-copy"></a>增量复制

数据工厂可让你以递增方式将增量数据从源数据存储复制到接收器数据存储。 有关详细信息，请参阅[教程：以增量方式复制数据](tutorial-incremental-copy-overview.md)。

## <a name="performance-and-tuning"></a>性能和优化

[复制活动监视](copy-activity-monitoring.md)体验向你显示每个活动运行的复制性能统计信息。 [复制活动性能和可伸缩性指南](copy-activity-performance.md)中描述了哪些关键因素会影响通过 Azure 数据工厂中的复制活动移动数据时的性能。 其中还列出了在测试期间观测到的性能值，并介绍了如何优化复制活动的性能。

## <a name="resume-from-last-failed-run"></a>从上次失败的运行恢复

在基于文件的存储之间以二进制格式按原样复制大量文件，并选择将文件夹/文件层次结构从源保存到接收器（例如，将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2）时，复制活动支持从上次失败的运行中恢复。 它适用于以下基于文件的连接器： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、 [文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)和 [SFTP](connector-sftp.md)。

可以通过下述两种方式利用复制活动恢复功能：

- **活动级别重试：** 可以设置复制活动的重试计数。 在管道执行过程中，如果此复制活动运行失败，则下一次自动重试将从上一次试用的故障点开始。
- **从失败的活动重新运行：** 管道执行完成后，还可以通过 ADF UI 监视视图或编程方式触发从失败的活动重新运行的操作。 如果失败的活动是复制活动，则该管道将不仅从此活动重新运行，而且也会从上一个运行的故障点恢复。

    ![复制恢复](media/copy-activity-overview/resume-copy.png)

要注意的几点：

- 恢复发生在文件级别。 如果复制活动在复制文件时失败，则在下一次运行时，会重新复制此特定文件。
- 若要正常使用恢复功能，请不要在两次重新运行之间更改复制活动设置。
- 从 Amazon S3、Azure Blob、Azure Data Lake Storage Gen2 和 Google Cloud Storage 复制数据时，复制活动可以从任意数量的已复制文件恢复。 虽然就其他作为源的基于文件的连接器来说，目前复制活动只支持从有限数量的文件中恢复（通常是在数万个文件的范围内，因文件路径的长度而异），但超出此数量的文件将在重新运行期间重新复制。

对于二进制文件复制以外的其他情况，将从头开始重新运行复制活动。

## <a name="preserve-metadata-along-with-data"></a>保留元数据和数据

在将数据从源复制到接收器时，在进行 Data Lake 迁移这样的情况下，还可以选择使用复制活动来保存元数据和 ACL 以及数据。 有关详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md)。

## <a name="schema-and-data-type-mapping"></a>架构和数据类型映射

有关复制活动如何将源数据映射到接收器的信息，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

## <a name="add-additional-columns-during-copy"></a>在复制过程中添加其他列

除了将数据从源数据存储复制到接收器外，还可以进行配置，以便添加要一起复制到接收器的其他数据列。 例如：

- 从基于文件的源复制时，将相对文件路径存储为一个附加列，用以跟踪数据来自哪个文件。
- 添加包含 ADF 表达式的列，以附加 ADF 系统变量（例如管道名称/管道 ID），或存储来自上游活动输出的其他动态值。
- 添加一个包含静态值的列以满足下游消耗需求。

可以在复制活动源选项卡上找到以下配置： 

![在复制活动中添加其他列](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>此功能适用于最新的数据集模型。 如果在 UI 中未看到此选项，请尝试创建一个新数据集。

若要以编程方式对其进行配置，请在复制活动源中添加 `additionalColumns` 属性：

| 属性 | 说明 | 必需 |
| --- | --- | --- |
| additionalColumns | 添加要复制到接收器的其他数据列。<br><br>`additionalColumns` 数组下的每个对象都表示一个额外的列。 `name` 定义列名称，`value` 表示该列的数据值。<br><br>允许的数据值为：<br>-  **`$$FILEPATH`** - 一个保留变量，指示将源文件的相对路径存储在数据集中指定的文件夹路径。 应用于基于文件的源。<br>- **表达式**<br>- **静态值** | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>自动创建接收器表

将数据复制到 SQL 数据库/Azure Synapse Analytics 时，如果目标表不存在，则复制活动支持基于源数据自动创建该表。 它旨在帮助快速开始加载数据并评估 SQL 数据库/Azure Synapse Analytics。 进行数据引入之后，可以根据需要查看和调整接收器表架构。

将数据从任何源复制到以下接收器数据存储时，支持此功能。 可以在 *ADF 创作 UI* （> *复制活动接收器* – > *表选项* – > *自动创建表*或 `tableOption` 复制活动接收器负载中的属性）中找到选项。

- [Azure SQL 数据库](connector-azure-sql-database.md)
- [Azure SQL 数据库托管实例](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics（以前称为 SQL 数据仓库）](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![创建接收器表](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>容错

默认情况下，如果源数据行与接收器数据行不兼容，复制活动将停止复制数据，并返回失败结果。 要使复制成功，可将复制活动配置为跳过并记录不兼容的行，仅复制兼容的数据。 有关详细信息，请参阅[复制活动容错](copy-activity-fault-tolerance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下快速入门、教程和示例：

- [在同一 Azure Blob 存储帐户中将数据从一个位置复制到另一个位置](quickstart-create-data-factory-dot-net.md)
- [将数据从 Azure Blob 存储复制到 Azure SQL 数据库](tutorial-copy-data-dot-net.md)
- [将数据从 SQL Server 数据库复制到 Azure](tutorial-hybrid-copy-powershell.md)
