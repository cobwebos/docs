---
title: 复制活动中的架构映射
description: 了解复制数据时，Azure 数据工厂中的复制活动如何将架构和数据类型从源数据映射到接收器数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423616"
---
# <a name="schema-mapping-in-copy-activity"></a>复制活动中的架构映射

本文介绍了 Azure 数据工厂复制活动在执行数据复制时如何将架构映射和数据类型从源数据映射到接收器数据。

## <a name="schema-mapping"></a>架构映射

将数据从源复制到接收器时，将应用列映射。 默认情况下，复制活动**按列名称将源数据映射到接收器**。 可以指定[显式映射](#explicit-mapping)，根据需要自定义列映射。 更具体地说，复制活动包含以下内容：

1. 从源中读取数据并确定源架构
2. 使用默认列映射按名称映射列，或者在指定时应用显式列映射。
3. 将数据写入接收器

### <a name="explicit-mapping"></a>显式映射

可以指定要在复制活动中映射的列 > `translator` -> `mappings` 属性。 下面的示例定义管道中的复制活动，将数据从分隔文本复制到 Azure SQL 数据库。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

具有 `source` 和 `sink`的 `translator` -> `mappings` > 对象支持以下属性：

| 属性 | 说明                                                  | 必需 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | 源或接收器列的名称。                           | 是      |
| ordinal  | 列索引。 从1开始。 <br>在未使用标题行的情况下使用带分隔符的文本时应用和要求。 | 是       |
| 路径     | 要提取或映射的每个字段的 JSON 路径表达式。 适用于层次结构数据，如 MongoDB/REST。<br>对于根对象下的字段，JSON 路径以根 $ 开头对于 `collectionReference` 属性选择的数组中的字段，JSON 路径从数组元素开始。 | 是       |
| type     | 源或接收器列的数据工厂临时数据类型。 | 是       |
| 区域性  | Source 或 sink 列的区域性。 <br>当类型为 `Datetime` 或 `Datetimeoffset`时应用。 默认为 `en-us`。 | 是       |
| format   | 当类型为 `Datetime` 或 `Datetimeoffset`时要使用的格式字符串。 请参阅[自定义日期和时间格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)，了解如何设置日期时间格式。 | 是       |

除了具有 `source` 和 `sink`的对象之外，`translator` -> `mappings` 还支持以下属性：

| 属性            | 说明                                                  | 必需 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 仅当分层数据（如 MongoDB/REST）为源时才受支持。<br>若要进行迭代操作，以同一模式从**数组字段中**的对象提取数据并按行和对象进行转换，请指定要进行交叉应用的该数组的 JSON 路径。 | 是       |

### <a name="alternative-column-mapping"></a>备用列映射

您可以指定复制活动 > `translator` -> `columnMappings`，以在表格格式的数据之间进行映射。 在这种情况下，输入和输出数据集都需要 "结构" 部分。 列映射支持将源数据集“structure”中的所有列或列子集接收器数据集“structure”中的所有列。 以下是导致异常的错误条件：

* 源数据存储查询结果中没有输入数据集“structure”部分中指定的列名称。
* 接收器数据存储（如果具有预定义架构）没有输出数据集“structure”部分中指定的列名称。
* 接收器数据集“structure”中的列数量多于或少于映射中指定的数量。
* 重复的映射。

在下面的示例中，输入数据集具有一个结构，它指向本地 Oracle 数据库中的一个表。

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

在此示例中，输出数据集具有一个结构，并指向 Salesfoce 中的表。

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

以下 JSON 定义管道中的复制活动。 使用**转换器** -> **columnMappings**属性将源中的列映射到接收器中的列。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

如果使用语法 `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 指定列映射，则仍然按原样支持它。

### <a name="alternative-schema-mapping"></a>备用架构映射

您可以指定 "复制活动->" `translator` -> "`schemaMapping`" 以在分层形状的数据和表格格式的数据之间进行映射，例如从 MongoDB/REST 复制到文本文件并从 Oracle 复制到 Azure Cosmos DB 的 API for MongoDB。 复制活动 `translator` 部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动转换器的 type 属性必须设置为： **TabularTranslator** | 是 |
| schemaMapping | 键值对的集合，表示**从源端到接收器端**的映射关系。<br/>- **Key：** 表示源。 对于 "**表格源**"，请指定在数据集结构中定义的列名称;对于 "**分层源**"，请为每个要提取和映射的字段指定 JSON 路径表达式。<br>- **值：** 表示接收器。 对于**表格接收器**，指定数据集结构中定义的列名称;对于**分层接收器**，请为每个要提取和映射的字段指定 JSON 路径表达式。 <br>对于层次结构数据，对于根对象下的字段，JSON 路径以根 $ 开头对于 `collectionReference` 属性选择的数组中的字段，JSON 路径从数组元素开始。  | 是 |
| collectionReference | 若要进行迭代操作，以同一模式从**数组字段中**的对象提取数据并按行和对象进行转换，请指定要进行交叉应用的该数组的 JSON 路径。 仅当分层数据为源时，才支持此属性。 | 是 |

**示例：从 MongoDB 复制到 Oracle：**

例如，如果 MongoDB 文档的内容如下：

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

而你需要按以下格式通过平展数组中数据（order_pd 和 order_price）的方式将其复制到 Azure SQL 表中，并使用常见的根信息（数字、日期和城市）进行交叉联接：

| orderNumber | orderDate | order_pd | order_price | 城市 |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

将架构映射规则配置为以下复制活动 JSON 示例：

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>数据类型映射

复制活动按照以下 2 个步骤执行源类型到接收器类型的映射：

1. 从本机源类型转换为 Azure 数据工厂临时数据类型
2. 从 Azure 数据工厂临时数据类型转换为本机接收器类型

在每个连接器主题中的“数据类型映射”部分，可以找到本机类型和临时类型之间的映射。

### <a name="supported-data-types"></a>支持的数据类型

数据工厂支持以下临时数据类型：在[数据集结构](concepts-datasets-linked-services.md#dataset-structure-or-schema)配置中配置类型信息时，可以指定以下值：

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
