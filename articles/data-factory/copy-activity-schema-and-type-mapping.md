---
title: 复制活动中的架构映射 | Microsoft Docs
description: 了解复制数据时，Azure 数据工厂中的复制活动如何将架构和数据类型从源数据映射到接收器数据。
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
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615612"
---
# <a name="schema-mapping-in-copy-activity"></a>复制活动中的架构映射
本文介绍在执行数据复制操作时，Azure 数据工厂复制活动如何将架构和数据类型从源数据映射到接收器数据。

## <a name="column-mapping"></a>列映射

在表格形式的数据之间复制数据时，适用列映射。 复制活动默认按列名称将源数据映射到接收器除非配置了[显式列映射](#explicit-column-mapping)。 更具体地说，复制活动包含以下内容：

1. 从源中读取数据并确定源架构

    * 对于数据存储/文件格式具有预定义架构的数据源，例如具有元数据的数据库/文件（vro/ORC/Parquet/带标头的文本），从查询结果或文件元数据中提取源架构。
    * 对于具有灵活架构的数据源（如 Azure 表/Cosmos DB），根据查询结果推断源架构。 可以通过在数据集中配置“structure”来覆盖它。
    * 对于不包含标头的文本文件，使用模式“Prop_0”、“Prop_1”、...生成默认列名称，可通过在数据集中配置“structure”来覆盖它。
    * 对于 Dynamics 源，必须在数据集“structure”部分提供架构信息。

2. 如果指定了显式列映射，则会应用它。

3. 将数据写入接收器

    * 对于具有预定义架构的数据存储，数据写入具有相同名称的列。
    * 对于不具有固定架构和文件格式的数据存储，将基于源架构生成列名称/元数据。

### <a name="explicit-column-mapping"></a>显式列映射

可在复制活动的“typeProperties”部分指定“columnMappings”，以进行显式列映射。 在此方案中，“structure”部分对于输入和输出数据集都是是必需的。 列映射支持将源数据集“structure”中的所有列或列子集接收器数据集“structure”中的所有列。 以下是导致异常的错误条件：

* 源数据存储查询结果中没有输入数据集“structure”部分中指定的列名称。
* 接收器数据存储（如果具有预定义架构）没有输出数据集“structure”部分中指定的列名称。
* 接收器数据集“structure”中的列数量多于或少于映射中指定的数量。
* 重复的映射。

#### <a name="explicit-column-mapping-example"></a>显式列映射示例

在本示例中，输入表有一个结构，并指向本地 SQL 数据库中的表。

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

在本示例中，输出表有一个结构，并指向 Azure SQL 数据库中的表。

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

以下 JSON 定义管道中的复制活动。 使用 translator 属性将源中的列映射到接收器中的列 (columnMappings)。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
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

**列映射流：**

![列映射流](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>架构映射

在分层数据和表格形式的数据之间复制数据（例如，将数据从 MongoDB/REST 复制到文本文件以及从 SQL 复制到 MangoDB 的 Auzre Cosmos DB API）时，适用架构映射。 复制活动 `translator` 部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动转换器的 type 属性必须设置为：**TabularTranslator** | 是 |
| schemaMapping | 键 / 值对的集合，它表示映射关系**源一方要接收端从**。<br/>- **密钥：** 表示源。 有关**表格源**，指定列名称，如数据集结构中定义; 有关**层次结构的源**，为列指定 JSON 路径表达式以提取和映射的每个字段。<br/>- **值：** 表示接收器。 有关**表格接收器**，指定列名称，如数据集结构中定义; 有关**分层接收器**，为列指定 JSON 路径表达式以提取和映射的每个字段。 <br/> JSON 路径以根 $; 对于分层数据，为根对象下的字段的启动有关选择的数组中的字段，`collectionReference`从数组元素的属性，JSON 路径开始。  | 是 |
| collectionReference | 若要进行迭代操作，以同一模式从**数组字段中**的对象提取数据并按行和对象进行转换，请指定要进行交叉应用的该数组的 JSON 路径。 仅当分层数据为源时，才支持此属性。 | 否 |

**示例：从 MongoDB 复制到 SQL：**

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

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | 西雅图 |
| 01 | 20170122 | P2 | 13 | 西雅图 |
| 01 | 20170122 | P3 | 231 | 西雅图 |

将架构映射规则配置为以下复制活动 JSON 示例：

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number", 
                "orderDate": "$.date", 
                "order_pd": "prod", 
                "order_price": "price",
                "city": " $.city[0].name"
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

数据工厂支持以下临时数据类型：在[数据集结构](concepts-datasets-linked-services.md#dataset-structure)配置中配置类型信息时，可以指定以下值：

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

### <a name="explicit-data-type-conversion"></a>显式数据类型转换

将数据复制到具有固定架构的数据存储（如 SQL Server/Oracle）时，如果源和接收器在相同列具有不同类型，则应在源端声明显式类型转换：

* 对于文件源（如 CSV/Avro），应通过具有完整列列表的源结构（源端列名称和接收器端类型）声明类型转换
* 对于关系源（如 SQL/Oracle），应通过查询语句中的显式类型转换实现类型转换。

## <a name="when-to-specify-dataset-structure"></a>何时指定数据集“structure”

在以下情况下，必须在数据集中指定“structure”：

* 复制期间，对文件源应用[显式数据类型转换](#explicit-data-type-conversion)（输入数据集）
* 复制期间，应用[显式列映射](#explicit-column-mapping)（输入和输出数据集）
* 从 Dynamics 365/CRM 源复制（输入数据集）
* 源不是 JSON 文件时，作为嵌套对象复制到 Cosmos DB（输出数据集）

在以下情况下，建议在数据集中指定“structure”：

* 从无标头文本文件复制（输入数据集）。 可将文本文件的列名称指定为与相应接收器列的列名称相同，以避免配置显式的列映射。
* 从具有灵活架构的数据存储（如 Azure 表/Cosmos DB）复制（输入数据集），以确保每次活动运行期间复制所需数据（列），而不让复制活动基于首行推断架构。


## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动容错](copy-activity-fault-tolerance.md)
- [复制活动性能](copy-activity-performance.md)
