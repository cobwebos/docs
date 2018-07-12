---
title: 使用数据工厂向/从 Azure Cosmos DB 复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从支持的源数据存储复制到 Azure Cosmos DB，或从 Cosmos DB 复制到支持的接收器存储。
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
ms.openlocfilehash: 92b45c1038fd099926360dc80802ababf0e8ee93
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37052760"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Cosmos DB 复制数据

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-azure-documentdb-connector.md)
> * [当前版本](connector-azure-cosmos-db.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从/向 Azure Cosmos DB (SQL API) 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure Cosmos DB 复制到任一支持的接收器数据存储，或从任一支持的源数据存储复制到 Azure Cosmos DB。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Azure Cosmos DB 连接器支持：

- Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)。
- 按原样导入/导出 JSON 文档，或从/向表格数据集（例如 SQL 数据库、CSV 文件等）复制数据。

要向/从 JSON 文件或另一 Cosmos DB 集合原样复制文档，请参阅[导入/导出 JSON 文档](#importexport-json-documents)。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Cosmos DB 的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Cosmos DB 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**CosmosDb**。 | 是 |
| connectionString |指定连接 Azure Cosmos DB 数据库所需的信息。 注意：需在连接字符串中指定数据库信息，如以下示例所示。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Azure Cosmos DB 数据集支持的属性列表。

要从/向 Azure Cosmos DB 复制数据，请将数据集的 type 属性设置为“DocumentDbCollection”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**DocumentDbCollection** |是 |
| collectionName |Cosmos DB 文档集合的名称。 |是 |

**示例：**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>数据工厂的构架

对于无架构的数据存储（如 Azure Cosmos DB），复制活动使用下列方式之一推断架构。 因此，最好在 structure 部分指定数据结构，除非要[按原样导入/导出 JSON 文档](#importexport-json-documents)。

*. 如果使用数据集定义中的**结构**属性指定数据的结构，数据工厂服务会将此结构作为架构。 在这种情况下，如果行不包含列的值，则为其提供 null 值。
*. 如果不使用数据集定义中的**结构**属性指定数据结构，数据工厂服务将通过使用数据中的第一行来推断架构。 在这种情况下，如果第一行不包含完整架构，则复制操作的结果中将丢失部分列。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Cosmos DB 源和接收器支持的属性列表。

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB 作为源

要从 Azure Cosmos DB 复制数据，请将复制活动中的源类型设置为“DocumentDbCollectionSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**DocumentDbCollectionSource** |是 |
| query |指定要读取数据的 Cosmos DB 查询。<br/><br/>示例：`SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，则执行的 SQL 语句为：`select <columns defined in structure> from mycollection` |
| nestingSeparator |特殊字符，用于指示文档是嵌套的以及如何展平结果集。<br/><br/>例如，如果 Cosmos DB 查询返回嵌套结果 `"Name": {"First": "John"}`，当 nestedSeparator 是点时，复制活动将以值为“John”的“Name.First”标识列名称。 |否（默认值为点 `.`） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB 作为接收器

要将数据复制到 Azure Cosmos DB 数据，请将复制活动中的接收器类型设置为“DocumentDbCollectionSink”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**DocumentDbCollectionSink** |是 |
| nestingSeparator |源列名称中的特殊字符，指示需要嵌套的文档。 <br/><br/>例如，当nestedSeparator 是点时，输出数据集结构中的 `Name.First` 在 Cosmos DB 文档中生成以下 JSON 结构：`"Name": {"First": "[value maps to this column from source]"}`。 |否（默认值为点 `.`） |

**示例：**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>导入/导出 JSON 文档

使用此 Cosmos DB 连接器，可以轻松地

* 将各种源中的 JSON 文档导入 Cosmos DB，包括 Azure Blob、Azure Data Lake Store 或 Azure 数据工厂所支持的其他基于文件的存储。
* 将 JSON 文档从 Cosmos DB 集合导出到各种基于文件的存储。
* 在两个 Cosmos DB 集合之间按原样复制文档。

要实现此类“架构不可知”复制：

* 使用复制数据工具时，选中“原样导出到 JSON 文件或 Cosmos DB 集合”选项。
* 使用活动创作时，请勿指定 Cosmos DB 数据集中的“结构”（亦称为架构）部分，也不要指定复制活动中 Cosmos DB 源/接收器的“nestingSeparator”属性。 导入或导出 JSON 文件时，在相应的文件存储数据集中，指定“JsonFormat”作为格式类型，并适当配置“filePattern”（请参阅 [JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)部分以了解详细信息），然后不指定“结构”（亦称为架构）部分并跳过其他格式设置。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
