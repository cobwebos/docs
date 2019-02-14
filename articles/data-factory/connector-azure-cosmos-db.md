---
title: 使用数据工厂在 Azure Cosmos DB (SQL API) 中复制或粘贴数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从受支持的源数据存储复制到 Azure Cosmos DB (SQL API)，或从 Azure Cosmos DB 复制到受支持的接收器存储。
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820056"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Azure Cosmos DB (SQL API) 中复制或粘贴数据

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1](v1/data-factory-azure-documentdb-connector.md)
> * [当前版本](connector-azure-cosmos-db.md)

本文概述如何使用 Azure 数据工厂中的复制活动在 Azure Cosmos DB (SQL API) 中复制和粘贴数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

>[!NOTE]
>此连接器仅支持向/从 Cosmos DB SQL API 复制数据。 有关 MongoDB API，请参阅[适用于 MongoDB 的 Auzre Cosmos DB API 的连接器](connector-azure-cosmos-db-mongodb-api.md)。 目前不支持其他 API 类型。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure Cosmos DB (SQL API) 复制到任一支持的接收器数据存储，或从任一支持的源数据存储复制到 Azure Cosmos DB (SQL API)。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

可以使用 Azure Cosmos DB (SQL API) 连接器：

- 在 Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) 中复制和粘贴数据。
- 以 **insert** 或 **upsert** 的形式写入 Azure Cosmos DB。
- 按原样导入和导出 JSON 文档，或在表格数据集中复制或粘贴数据。 示例包括 SQL 数据库和 CSV 文件。 若要在 JSON 文件或另一个 Azure Cosmos DB 集合中按原样复制或粘贴文档，请参阅“导入或导出 JSON 文档”。

数据工厂与 [Azure Cosmos DB 批量执行程序库](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)集成，以便在写入 Azure Cosmos DB 时提供最佳性能。

> [!TIP]
> [数据迁移视频](https://youtu.be/5-SRNiC_qOU)将指导你完成将数据从 Azure Blob 存储复制到 Azure Cosmos DB 的步骤。 该视频还介绍了在一般情况下将数据引入 Azure Cosmos DB 的性能优化注意事项。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Cosmos DB (SQL API) 的数据工厂实体，以下部分提供有关可用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Cosmos DB (SQL API) 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **CosmosDb**。 | 是 |
| connectionString |指定连接 Azure Cosmos DB 数据库所需的信息。<br />**注意**：必须如以下示例所示，在连接字符串中指定数据库信息。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将帐户密钥放在 Azure 密钥保管库中，并从连接字符串中拉取 `accountKey` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)一文。 |是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

**示例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
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

**示例：在 Azure 密钥保管库中存储帐户密钥**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
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

本部分列出了 Azure Cosmos DB (SQL API) 数据集支持的属性。 

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 

若要在 Azure Cosmos DB (SQL API) 中复制或粘贴数据，请将数据集的 **type** 属性设置为 **DocumentDbCollection**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **DocumentDbCollection**。 |是 |
| collectionName |Azure Cosmos DB 文档集合的名称。 |是 |

**示例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
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

对于无构架的数据存储（如 Azure Cosmos DB），复制活动使用以下列表中所述的方式之一推断构架。 除非要[按原样导入或导出 JSON 文档](#import-or-export-json-documents)，否则最佳做法是在 **structure** 节中指定数据的结构。

* 如果使用数据集定义中的**结构**属性指定数据的结构，数据工厂会将此结构作为架构。 

    如果行不包含列的值，则会为列值提供 null 值。
* 如果不使用数据集定义中的 **structure** 属性指定数据结构，数据工厂服务将通过数据中的第一行来推断架构。 

    如果第一行不包含完整架构，则复制操作的结果中将丢失部分列。

## <a name="copy-activity-properties"></a>复制活动属性

本部分列出了Azure Cosmos DB (SQL API) 源和接收器支持的属性。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) 用作源

若要从 Azure Cosmos DB (SQL API) 复制数据，请将复制活动中的 **source** 类型设置为 **DocumentDbCollectionSource**。 

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **DocumentDbCollectionSource**。 |是 |
| query |指定要读取数据的 Azure Cosmos DB 查询。<br/><br/>示例：<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，则执行此 SQL 语句：`select <columns defined in structure> from mycollection` |
| nestingSeparator |特殊字符，用于指示文档是嵌套的以及如何平展结果集。<br/><br/>例如，如果 Azure Cosmos DB 查询返回嵌套结果 `"Name": {"First": "John"}`，则当 **nestedSeparator** 值为 **.**（点）时，复制活动将以值为“John”的 `Name.First` 标识列名称。 |否<br />（默认值为 **.** （点）） |

**示例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) 用作接收器

若要将数据复制到 Azure Cosmos DB (SQL API)，请将复制活动中的 **sink** 类型设置为 **DocumentDbCollectionSink**。 

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **DocumentDbCollectionSink**。 |是 |
| writeBehavior |描述如何将数据写入 Azure Cosmos DB。 允许的值为 **insert** 和 **upsert**。<br/><br/>**upsert** 的行为是，如果已存在具有相同 ID 的文档，则替换该文档；否则将插入该文档。<br /><br />**注意**：如果未在原始文档中指定 ID，或未通过列映射指定 ID，则数据工厂会自动为文档生成 ID。 这表示必须先确保文档有 ID，才能让 **upsert** 按预期工作。 |否<br />（默认值为 **insert**） |
| writeBatchSize | 数据工厂使用 [Azure Cosmos DB 批量执行程序库](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)将数据写入 Azure Cosmos DB。 **writeBatchSize** 属性控制 ADF 提供给库的文档的大小。 可尝试增加 writeBatchSize 的值以提高性能，并在文档大小较大时降低该值 - 请参阅下面的提示。 |否<br />（默认值为 **10,000**） |
| nestingSeparator |**源**列名称中的特殊字符，指示需要嵌套的文档。 <br/><br/>例如，当 **nestedSeparator** 为 **.**（点）时，输出数据集结构中的 `Name.First` 在 Azure Cosmos DB 文档中生成以下 JSON 结构： `"Name": {"First": "[value maps to this column from source]"}`  |否<br />（默认值为 **.** （点）） |

>[!TIP]
>Cosmos DB 将单个请求的大小限制为 2MB。 公式为请求大小 = 单个文档大小 * 写入批大小。 若出现“请求太大。”错误，请减少复制接收器配置中的 `writeBatchSize` 值。

**示例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>导入或导出 JSON 文档

使用此 Azure Cosmos DB(SQL API) 连接器，可以轻松地：

* 将各种源（包括 Azure Blob 存储、Azure Data Lake Store 或 Azure 数据工厂所支持的其他基于文件的存储）中的 JSON 文档导入 Azure Cosmos DB。
* 将 JSON 文档从 Azure Cosmos DB 集合导出到各种基于文件的存储。
* 在两个 Azure Cosmos DB 集合之间按原样复制文档。

若要实现“架构不可知”复制，请执行以下操作：

* 使用复制数据工具时，选择“原样导出到 JSON 文件或 Cosmos DB 集合”选项。
* 使用活动创作时，请勿在 Azure Cosmos DB 数据集中指定 **structure**（也称为 *schema*）节， 也不要在复制活动的 Azure Cosmos DB 源或接收器中指定 **nestingSeparator** 属性。 从 JSON 文件进行导入或导出到 JSON 文件时，请在相应的文件存储数据集中，将 **format** 类型指定为 **JsonFormat**，并按 [JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)部分中所述配置 **filePattern**。 然后不要指定 **structure** 节并跳过其余的格式设置。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
