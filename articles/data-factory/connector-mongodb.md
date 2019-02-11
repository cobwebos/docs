---
title: 使用 Azure 数据工厂从 MongoDB 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Mongo DB 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562196"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>使用 Azure 数据工厂从 MongoDB 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 MongoDB 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

>[!IMPORTANT]
>ADF 发布了这个新版本的 MongoDB 连接器，它提供更好的本机 MongoDB 支持。 如果在解决方案中使用的是以前的 MongoDB 连接器，且该连接器“按原样”支持后向兼容性，请参阅 [MongoDB 连接器（旧版）](connector-mongodb-legacy.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 MongoDB 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 MongoDB 连接器最高支持版本 3.4。

## <a name="prerequisites"></a>先决条件

要从不可公开访问的 MongoDB 数据库复制数据，需要设置自承载集成运行时。 要了解详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 MongoDB 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

MongoDB 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：MongoDbV2 |是 |
| connectionString |指定 MongoDB 连接字符串，例如 `mongodb://[username:password@]host[:port][/[database][?options]]`。 请参阅 [MongoDB 连接字符串手册](https://docs.mongodb.com/manual/reference/connection-string/)获取详细信息。 <br/><br />将此字段标记为 SecureString 类型，以便安全地将其存储在数据工厂中。 此外，还可以[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 |是 |
| database | 要访问的数据库的名称。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure Integration Runtime 时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 MongoDB 数据集支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：MongoDbV2Collection | 是 |
| collectionName |MongoDB 数据库中集合的名称。 |是 |

**示例：**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 MongoDB 源支持的属性列表。

### <a name="mongodb-as-source"></a>以 MongoDB 作为源

复制活动源部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：MongoDbV2Source | 是 |
| filter | 使用查询运算符指定选择筛选器。 若要返回集合中的所有文档，请省略此参数或传递空文档 ({})。 | 否 |
| cursorMethods.project | 指定要在文档中返回用于投影的字段。 若要返回匹配文档中的所有字段，请省略此参数。 | 否 |
| cursorMethods.sort | 指定查询返回匹配文档的顺序。 请参阅 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定服务器返回的文档的最大数量。 请参阅 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 |
| cursorMethods.skip | 指定要跳过的文档数量以及 MongoDB 开始返回结果的位置。 请参阅 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定从 MongoDB 实例的每批响应中返回的文档数量。 大多数情况下，修改批大小不会影响用户或应用程序。 Cosmos DB 限制每个批不能超过 40 MB（这是文档大小的 batchSize 数量的总和），因此如果文档很大，请减小此值。 | 否<br/>（默认值为 **100**） |

>[!TIP]
>ADF 支持在**严格模式**下使用 BSON 文档。 请确保筛选器查询处于严格模式，而不是 Shell 模式。 有关详细说明，请参阅 [MongoDB 手册](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

## <a name="export-json-documents-as-is"></a>按原样导出 JSON 文档

可以使用此 MongoDB 连接器将 JSON 文档按原样从 MongoDB 集合导出到各种基于文件的存储或 Azure Cosmos DB。 若要实现此类“架构不可知”复制，请跳过数据集中的“结构”（也称为“架构”）部分和复制活动中的架构映射。

## <a name="schema-mapping"></a>架构映射

若要将数据从 MongoDB 复制到表格接收器，请参阅[架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
