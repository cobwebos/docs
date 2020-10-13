---
title: 从 MongoDB 阿特拉斯复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 MongoDB 阿特拉斯复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: 34b0c053f4f0fea933a6e1f48d8f93e6352776b9
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946639"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>使用 Azure 数据工厂从 MongoDB 阿特拉斯复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动从 MongoDB 阿特拉斯数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 MongoDB 阿特拉斯数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 MongoDB 阿特拉斯连接器支持的 **版本最高可达 4.2**。

## <a name="prerequisites"></a>必备条件

如果使用 Azure Integration Runtime 进行复制，请确保将有效区域的 [Azure Integration Runtime](azure-integration-runtime-ip-addresses.md) ip 添加到 MongoDB 阿特拉斯 IP 访问列表。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 MongoDB 阿特拉斯连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

MongoDB 阿特拉斯链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type |Type 属性必须设置为： **MongoDbAtlas** |是 |
| connectionString |指定 MongoDB 阿特拉斯连接字符串， `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` 例如。 <br/><br /> 你还可以将连接字符串置于 Azure Key Vault 中。 有关更多详细信息，请参阅[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 |是 |
| database | 要访问的数据库的名称。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 在[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 MongoDB 阿特拉斯数据集支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **MongoDbAtlasCollection** | 是 |
| collectionName |MongoDB 阿特拉斯数据库中集合的名称。 |是 |

**示例：**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 MongoDB 阿特拉斯源支持的属性列表。

### <a name="mongodb-atlas-as-source"></a>作为源的 MongoDB 阿特拉斯

复制活动源部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为： **MongoDbAtlasSource** | 是 |
| filter | 使用查询运算符指定选择筛选器。 若要返回集合中的所有文档，请省略此参数或传递空文档 ({})。 | 否 |
| cursorMethods.project | 指定要在文档中返回用于投影的字段。 若要返回匹配文档中的所有字段，请省略此参数。 | 否 |
| cursorMethods.sort | 指定查询返回匹配文档的顺序。 请参阅 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定服务器返回的文档的最大数量。 请参阅 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 |
| cursorMethods.skip | 指定从 MongoDB 阿特拉斯开始返回结果时要跳过的文档数。 请参阅 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定从 MongoDB 阿特拉斯实例的每批响应中返回的文档数。 大多数情况下，修改批大小不会影响用户或应用程序。 Cosmos DB 限制每个批不能超过 40 MB（这是文档大小的 batchSize 数量的总和），因此如果文档很大，请减小此值。 | 否<br/> (默认值为 **100**)  |

>[!TIP]
>ADF 支持在**严格模式**下使用 BSON 文档。 请确保筛选器查询处于严格模式，而不是 Shell 模式。 有关详细说明，请参阅 [MongoDB 手册](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

可以使用此 MongoDB 阿特拉斯连接器将 JSON 文档从 MongoDB 阿特拉斯集合导出到各种基于文件的存储或 Azure Cosmos DB。 若要实现这种架构不可知的复制，请跳过数据集中的“结构”（也称为“架构”）节和复制活动中的架构映射  。

## <a name="schema-mapping"></a>架构映射

若要将数据从 MongoDB 阿特拉斯复制到表格接收器，请参阅 [架构映射](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
