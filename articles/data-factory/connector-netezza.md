---
title: 使用 Azure 数据工厂从 Netezza 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Netezza 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440554"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Netezza 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Netezza 复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

>[!TIP]
>对于从 Netezza 到 Azure 的数据迁移方案，请参阅[使用 Azure 数据工厂将数据从本地 Netezza 服务器迁移到 azure](data-migration-guidance-netezza-azure-sqldw.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Netezza 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)


可以将数据从 Netezza 复制到任何支持的接收器数据存储。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

Netezza 连接器支持从源进行并行复制。 有关详细信息，请参阅[从 Netezza 并行复制](#parallel-copy-from-netezza)部分。

Azure 数据工厂提供内置驱动程序以启用连接。 无需要手动安装任何驱动程序即可使用此连接器。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>开始体验

可以通过使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建使用复制活动的管道。 有关如何创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 Netezza 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Netezza 链接服务支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | “type”属性必须设置为“Netezza”。 | 是 |
| connectionString | 用于连接到 Netezza 的 ODBC 连接字符串。 <br/>还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `pwd` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`。 下表介绍了更多可以设置的属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| SecurityLevel | 驱动程序用于连接到数据存储区的安全级别 (SSL/TLS)。 示例：`SecurityLevel=preferredSecured`。 支持的值为：<br/>-  仅不安全 (onlyUnSecured)：驱动程序不使用 SSL。<br/>-  首选不安全 (preferredUnSecured)（默认）：如果服务器提供了选择，则驱动程序不使用 SSL。 <br/>-  首选安全 (preferredSecured)：如果服务器提供了选择，则驱动程序使用 SSL。 <br/>-  仅安全 (onlySecured)：除非有 SSL 连接，否则驱动程序不会连接。 | 否 |
| CaCertFile | 服务器使用的 SSL 证书的完整路径。 示例： `CaCertFile=<cert path>;`| 是，如果启用了 SSL |

**示例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
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

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **NetezzaTable** | 是 |
| 架构 | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表的名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 支持此属性是为了向后兼容。 为新的工作负荷使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

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
>若要使用数据分区有效地从 Netezza 加载数据，请从 Netezza 部分中的[并行复制](#parallel-copy-from-netezza)中了解详细信息。

若要从 Netezza 复制数据，请将复制活动中的 source 类型设置为“NetezzaSource”。 复制活动 **source** 节支持以下属性：

| 属性 | Description | 需要 |
|:--- |:--- |:--- |
| type | 必须将复制活动源的 type 属性设置为“NetezzaSource”。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 示例： `"SELECT * FROM MyTable"` | 否（如果指定了数据集中的“tableName”） |
| partitionOptions | 指定用于从 Netezza 加载数据的数据分区选项。 <br>允许值为： **None** （默认值）、 **DataSlice**和**DynamicRange**。<br>启用分区选项（即，不 `None`）时，从 Netezza 数据库并发加载数据的并行度由复制活动上[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 | 否 |
| partitionSettings | 指定数据分区设置的组。 <br>当 partition 选项不 `None`时应用。 | 否 |
| partitionColumnName | 指定**整数类型**的源列名称，将由范围分区用于并行复制。 如果未指定，则表的主键为 autodetected，并用作分区列。 <br>`DynamicRange`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfRangePartitionColumnName`。 请参阅[并行复制 From Netezza](#parallel-copy-from-netezza)部分中的示例。 | 否 |
| partitionUpperBound | 要向其复制数据的分区列的最大值。 <br>`DynamicRange`分区选项时应用。 如果使用 query 检索源数据，则在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 有关示例，请参阅[从 Netezza 并行复制](#parallel-copy-from-netezza)部分。 | 否 |
| partitionLowerBound | 要向其复制数据的分区列的最小值。 <br>`DynamicRange`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 有关示例，请参阅[从 Netezza 并行复制](#parallel-copy-from-netezza)部分。 | 否 |

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

## <a name="parallel-copy-from-netezza"></a>从 Netezza 并行复制

数据工厂 Netezza 连接器提供内置数据分区，用于并行复制 Netezza 中的数据。 可以在复制活动的**源表**上查找数据分区选项。

![分区选项的屏幕截图](./media/connector-netezza/connector-netezza-partition-options.png)

启用分区副本时，数据工厂对 Netezza 源运行并行查询以按分区加载数据。 并行度由复制活动上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 例如，如果将 `parallelCopies` 设置为4，则数据工厂会根据指定的分区选项和设置同时生成并运行四个查询，每个查询将从 Netezza 数据库中检索部分数据。

建议使用数据分区启用并行复制，尤其是从 Netezza 数据库加载大量数据时。 下面是针对不同方案的建议配置。 将数据复制到基于文件的数据存储时，将 recommanded 写入文件夹作为多个文件（仅指定文件夹名称），在这种情况下，性能比写入单个文件更好。

| 方案                                                     | 建议设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大表的完全加载。                                   | **分区选项**：数据切片。 <br><br/>在执行期间，数据工厂会根据[Netezza 的内置数据切片](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)自动对数据进行分区，并按分区复制数据。 |
| 使用自定义查询加载大量数据。                 | **分区选项**：数据切片。<br>**查询**： `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`。<br>在执行过程中，数据工厂会替换 `?AdfPartitionCount` （在复制活动上设置了并行复制号）并将 `?AdfDataSliceCondition` 与数据切片分区逻辑一起，并将其发送到 Netezza。 |
| 使用自定义查询加载大量数据，使用一个整数列，其中包含均匀分布的范围分区值。 | **分区选项**：动态范围分区。<br>**查询**： `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以对具有整数数据类型的列进行分区。<br>**分区上限**和**分区下限**：指定是否希望根据分区列进行筛选，以便仅检索下限和上限之间的数据。<br><br>在执行期间，数据工厂会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名称和值范围，并发送到 Netezza。 <br>例如，如果将 "ID" 列的下限设置为1，将上限设置为80，并将 "并行副本" 设置为 "4"，则数据工厂将按4个分区检索数据。 它们的 Id 分别介于 [1，20]、[21，40]、[41、60] 和 [61，80] 之间。 |

**示例：带数据切片分区的查询**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**示例：包含动态范围分区的查询**

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

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
