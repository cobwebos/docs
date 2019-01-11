---
title: 使用数据工厂向/从 Azure Cosmos DB (MongoDB API) 复制数据 | Microsoft Docs
description: 了解如何使用数据工厂将数据从受支持的源数据存储复制到 Azure Cosmos DB (MongoDB API)，或从 Azure Cosmos DB 复制到受支持的接收器存储。
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: a28d0e819243810486179e7219ad3cb48487a299
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107168"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-mongodb-api-by-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Cosmos DB (MongoDB API) 复制数据

本文概述如何使用 Azure 数据工厂中的复制活动从/向 Azure Cosmos DB (MongoDB API) 复制数据。 本文是根据总体概述复制活动的 [Azure 数据工厂中的复制活动](copy-activity-overview.md)编写的。

>[!NOTE]
>此连接器仅支持向/从 Cosmos DB MongoDB API 复制数据。 有关 SQL API，请参阅 [Cosmos DB SQL API 连接器](connector-azure-cosmos-db.md)。 目前不支持其他 API 类型。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Azure Cosmos DB (MongoDB API) 复制到任一支持的接收器数据存储，或从任一支持的源数据存储复制到 Azure Cosmos DB (MongoDB API)。 有关复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

可以使用 Azure Cosmos DB (MongoDB API) 连接器：

- 从/向 Azure Cosmos DB [MongoDB API](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) 复制数据。
- 以 **insert** 或 **upsert** 的形式写入 Azure Cosmos DB。
- 按原样导入和导出 JSON 文档，或在表格数据集中复制或粘贴数据。 示例包括 SQL 数据库和 CSV 文件。 若要在 JSON 文件或另一个 Azure Cosmos DB 集合中按原样复制或粘贴文档，请参阅[导入或导出 JSON 文档](#importexport-json-documents)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Cosmos DB (MongoDB API) 的数据工厂实体，以下部分提供了有关可用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Cosmos DB (MongoDB API) 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | **type** 属性必须设置为 **CosmosDbMongoDbApi**。 | 是 |
| connectionString |指定 Azure Cosmos DB MongoDB API 的连接字符串。 可以在 Azure 门户 -> Cosmos DB 边栏选项卡 -> 主要或辅助连接字符串中找到该字符串，其模式为 `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`。 <br/><br />将此字段标记为 SecureString 类型，以便安全地将其存储在数据工厂中。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |是 |
| database | 要访问的数据库的名称。 | 是 |
| connectVia | 用于连接到数据存储的 [ Integration Runtime](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定此属性，则使用默认的 Azure Integration Runtime。 |否 |

**示例**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 Azure Cosmos DB (MongoDB API) 数据集支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 属性必须设置为 **CosmosDbMongoDbApiCollection**。 |是 |
| collectionName |Azure Cosmos DB 集合的名称。 |是 |

**示例**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB MongoDB API linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分列出 Azure Cosmos DB (MongoDB API) 源和接收器支持的属性。

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-db-mongodb-api-as-source"></a>Azure Cosmos DB (MongoDB API) 用作源

复制活动 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 **type** 属性必须设置为 **CosmosDbMongoDbApiSource**。 |是 |
| filter | 使用查询运算符指定选择筛选器。 若要返回集合中的所有文档，请省略此参数或传递空文档 ({})。 | 否 |
| cursorMethods.project | 指定要在文档中返回用于投影的字段。 若要返回匹配文档中的所有字段，请省略此参数。 | 否 |
| cursorMethods.sort | 指定查询返回匹配文档的顺序。 请参阅 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定服务器返回的文档的最大数量。 请参阅 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 | 
| cursorMethods.skip | 指定要跳过的文档数量以及 MongoDB 开始返回结果的位置。 请参阅 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定从 MongoDB 实例的每批响应中返回的文档数量。 大多数情况下，修改批大小不会影响用户或应用程序。 Cosmos DB 限制每个批不能超过 40 MB（这是文档大小的 batchSize 数量的总和），因此如果文档很大，请减小此值。 | 否<br/>（默认值为 **100**） |

>[!TIP]
>ADF 支持在**严格模式**下使用 BSON 文档。 请确保筛选器查询处于严格模式，而不是 Shell 模式。 有关详细说明，请参阅 [MongoDB 手册](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)。

**示例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB MongoDB API input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-mongodb-api-as-sink"></a>Azure Cosmos DB (MongoDB API) 用作接收器

复制活动 **sink** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 **type** 属性必须设置为 **CosmosDbMongoDbApiSink**。 |是 |
| writeBehavior |描述如何将数据写入 Azure Cosmos DB。 允许的值为 **insert** 和 **upsert**。<br/><br/>**upsert** 的行为是，如果已存在具有相同 ID 的文档，则替换该文档；否则将插入该文档。<br /><br />**注意**：如果未在原始文档中指定 ID，或未通过列映射指定 ID，则数据工厂会自动为文档生成 ID。 这表示必须先确保文档有 ID，才能让 **upsert** 按预期工作。 |否<br />（默认值为 **insert**） |
| writeBatchSize | **writeBatchSize** 属性控制每个批中可写入的文档大小。 可尝试增大 **writeBatchSize** 的值以提高性能，并在文档大小较大时减小该值。 |否<br />（默认值为 **10,000**） |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。允许的值为 timespan。 | 否<br/>（默认值为 **00:30:00** - 30 分钟） |

**示例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
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
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>若要按原样导入 JSON 文档，请参阅[导入或导出 JSON 文档](#import-or-export-json-documents)部分；若要从表格形数据复制，请参阅[架构映射](#schema-mapping)。

## <a name="import-or-export-json-documents"></a>导入或导出 JSON 文档

使用此 Azure Cosmos DB 连接器，可以轻松地：

* 将各种源（包括 Azure Blob 存储、Azure Data Lake Store 或 Azure 数据工厂所支持的其他基于文件的存储）中的 JSON 文档导入 Azure Cosmos DB。
* 将 JSON 文档从 Azure Cosmos DB 集合导出到各种基于文件的存储。
* 在两个 Azure Cosmos DB 集合之间按原样复制文档。

若要实现这种架构不可知的复制，请跳过数据集中的“结构”（也称为“架构”）节和复制活动中的架构映射。

## <a name="schema-mapping"></a>架构映射

要将数据从 Cosmos DB MongoDB API 复制到表格接收器或进行反向复制，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

具体而言，在写入到 Cosmos DB 时，为了确保使用源数据中的正确对象 ID 填充 Cosmos DB（例如，SQL 数据库表中包含一个“id”列，你想要使用该列的值作为 MongoDB 中的文档 ID 以完成插入/更新插入操作），需要根据 MongoDB 严格模式定义 (`_id.$oid`) 设置适当的架构映射，如下所示：

![MongoDB 接收器中的映射 ID](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

完成复制活动的执行后，接收器中将生成以下 BSON ObjectId：

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
