---
title: 使用 Azure 数据工厂从 Netezza 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Netezza 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 93fed398748dc793f0021758b5c24b6ba6d54782
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680637"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Netezza 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Netezza 复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

>[!TIP]
>对于从 Netezza 迁移到 Azure 的数据迁移方案，请从[使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 Azure](data-migration-guidance-netezza-azure-sqldw.md) 了解更多信息。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Netezza 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)


可以将数据从 Netezza 复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

Netezza 连接器支持从源进行并行复制。 有关详细信息，请参阅[从 Netezza 进行并行复制](#parallel-copy-from-netezza)部分。

Azure 数据工厂提供内置驱动程序以启用连接。 无需要手动安装任何驱动程序即可使用此连接器。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>入门

可以通过使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建使用复制活动的管道。 有关如何创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 Netezza 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Netezza 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | “type”属性必须设置为“Netezza”。 | 是 |
| connectionString | 用于连接到 Netezza 的 ODBC 连接字符串。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `pwd` 配置。 有关更多详细信息，请参阅以下示例和[将凭据存储在 Azure 密钥保管库中](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`。 下表介绍了更多可以设置的属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| SecurityLevel | 驱动程序用于连接到数据存储区的安全级别 (SSL/TLS)。 示例：`SecurityLevel=preferredSecured`。 支持的值是：<br/>-  仅不安全 (onlyUnSecured)：驱动程序不使用 SSL。<br/>-  首选不安全 (preferredUnSecured)（默认）：如果服务器提供了选择，则驱动程序不使用 SSL。 <br/>-  首选安全 (preferredSecured)：如果服务器提供了选择，则驱动程序使用 SSL。 <br/>-  仅安全 (onlySecured)：除非有 SSL 连接，否则驱动程序不会连接。 | 否 |
| CaCertFile | 服务器使用的 SSL 证书的完整路径。 示例： `CaCertFile=<cert path>;`| 是，如果启用了 SSL |

**示例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储密码**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

本部分提供 Netezza 数据集支持的属性列表。

若要查看可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。

若要从 Netezza 复制数据，请将数据集的 type 属性设置为“NetezzaTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **NetezzaTable** | 是 |
| schema | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 Netezza 源支持的属性列表。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。

### <a name="netezza-as-source"></a>以 Netezza 作为源

>[!TIP]
>若要详细了解如何使用数据分区从 Netezza 高效加载数据，请参阅[从 Netezza 进行并行复制](#parallel-copy-from-netezza)部分。

若要从 Netezza 复制数据，请将复制活动中的 source 类型设置为“NetezzaSource”。 复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 必须将复制活动源的 type 属性设置为“NetezzaSource”。 | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 示例： `"SELECT * FROM MyTable"` | 否（如果指定了数据集中的“tableName”） |
| partitionOptions | 指定用于从 Netezza 加载数据的数据分区选项。 <br>允许值为： **None** （默认值）、 **DataSlice**和**DynamicRange**。<br>启用分区选项（即，该选项不为 `None`）时，用于从 Netezza 数据库并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| partitionColumnName | 指定并行复制范围分区使用的源列（**整数类型**）的名称。 如果未指定，系统会自动检测表的主键并将其用作分区列。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionColumnName`。 请参阅[从 Netezza 进行并行复制](#parallel-copy-from-netezza)部分的示例。 | 否 |
| partitionUpperBound | 要从中复制数据的分区列的最大值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 如需示例，请参阅[从 Netezza 进行并行复制](#parallel-copy-from-netezza)部分。 | 否 |
| partitionLowerBound | 要从中复制数据的分区列的最小值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 如需示例，请参阅[从 Netezza 进行并行复制](#parallel-copy-from-netezza)部分。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>从 Netezza 进行并行复制

数据工厂 Netezza 连接器提供内置的数据分区，用于从 Netezza 并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。

![分区选项的屏幕截图](./media/connector-netezza/connector-netezza-partition-options.png)

启用分区复制时，数据工厂将对 Netezza 源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则数据工厂会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Netezza 数据库检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Netezza 数据库加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从大型表进行完整加载。                                   | **分区选项**：数据切片。 <br><br/>在执行期间，数据工厂自动根据 [Netezza 的内置数据切片](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)将数据分区，并按分区复制数据。 |
| 使用自定义查询加载大量数据。                 | **分区选项**：数据切片。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`。<br>执行期间，数据工厂将 `?AdfPartitionCount`（并行复制数在复制活动中设置）和 `?AdfDataSliceCondition` 替换为数据切片分区逻辑，并将其发送到 Netezza。 |
| 使用自定义查询加载大量数据，某个整数列包含均匀分布的范围分区值。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以针对整数数据类型的列进行分区。<br>**分区上限**和**分区下限**：指定是否希望根据分区列进行筛选，以便仅检索下限和上限之间的数据。<br><br>在执行期间，数据工厂会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound` 和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名和值范围，并将其发送到 Netezza。 <br>例如，如果为分区列“ID”设置了下限 1、上限 80，并将并行复制设置为 4，则数据工厂会按 4 个分区检索数据。 其 ID 分别介于 [1, 20]、[21, 40]、[41, 60] 和 [61, 80] 之间。 |

**示例：使用数据切片分区进行查询**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
