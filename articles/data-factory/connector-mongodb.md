---
title: "使用 Azure 数据工厂从 MongoDB 复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Mongo DB 复制到支持的接收器数据存储。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6f11d4184b5edbcb80411f146ae01ecfe62b6e91
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>使用 Azure 数据工厂从 MongoDB 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-on-premises-mongodb-connector.md)
> * [版本 2 - 预览版](connector-mongodb.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 MongoDB 数据库复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 MongoDB 连接器](v1/data-factory-on-premises-mongodb-connector.md)。


## <a name="supported-scenarios"></a>支持的方案

可以将数据从 MongoDB 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 MongoDB 连接器支持：

- **2.4、2.6、3.0 和 3.2 版本**的 MongoDB。
- 使用基本或匿名身份验证复制数据。

## <a name="prerequisites"></a>先决条件

要从不可公开访问的 MongoDB 数据库复制数据，需要设置自承载集成运行时。 要了解详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。 集成运行时提供内置 MongoDB 驱动程序，因此从/向 MongoDB 复制数据时，无需手动安装任何驱动程序。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板通过复制活动创建管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 MongoDB 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

MongoDB 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：**MongoDb** |是 |
| server |MongoDB 服务器的 IP 地址或主机名。 |是 |
| 端口 |MongoDB 服务器用于侦听客户端连接的 TCP 端口。 |否（默认值为 27017） |
| databaseName |要访问的 MongoDB 数据库名称。 |是 |
| authenticationType | 用于连接 MongoDB 数据库的身份验证类型。<br/>允许的值为：Basic 和 Anonymous。 |是 |
| username |用于访问 MongoDB 的用户帐户。 |是（如果使用基本身份验证）。 |
| password |用户密码。 将此字段标记为 SecureString。 |是（如果使用基本身份验证）。 |
| authSource |要用于检查身份验证凭据的 MongoDB 数据库名称。 |不会。 对于基本身份验证，默认使用管理员帐户和使用 databaseName 属性指定的数据库。 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 MongoDB 数据集支持的属性列表。

要从 MongoDB 复制数据，请将数据集的 type 属性设置为“MongoDbCollection”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**MongoDbCollection** | 是 |
| collectionName |MongoDB 数据库中集合的名称。 |是 |

**示例：**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }

```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 MongoDB 源支持的属性列表。

### <a name="mongodb-as-source"></a>以 MongoDB 作为源

要从 MongoDB 复制数据，请将复制活动中的源类型设置为“MongoDbSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**MongoDbSource** | 是 |
| query |使用自定义 SQL-92 查询读取数据。 例如，select * from MyTable。 |否（如果指定了数据集中的“collectionName”） |

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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> 指定 SQL 查询时，请注意 DateTime 的格式。 例如： `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="schema-by-data-factory"></a>数据工厂的构架

Azure 数据工厂服务通过使用 MongoDB 集合中**最新的 100 个文档**来推断该集合的架构。 如果这 100 个文档不包含完整架构，则在复制操作期间可能忽略某些列。

## <a name="data-type-mapping-for-mongodb"></a>MongoDB 的数据类型映射

从 MongoDB 复制数据时，以下映射用于从 MongoDB 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| MongoDB 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| 二进制 |Byte[] |
| 布尔 |布尔 |
| 日期 |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| 对象 |重新标准化为平展列，以“_”作为嵌套分隔符 |

> [!NOTE]
> 要了解对使用虚拟表的数组的支持，请参阅[支持使用虚拟表的复杂类型](#support-for-complex-types-using-virtual-tables)一节。
>
> 目前，不支持以下 MongoDB 数据类型：DBPointer、JavaScript、Max/Min key、Regular Expression、Symbol、Timestamp、Undefined。

## <a name="support-for-complex-types-using-virtual-tables"></a>支持使用虚拟表的复杂类型

Azure 数据工厂使用内置的 ODBC 驱动程序连接到 MongoDB 数据库，并从中复制数据。 对于数组或文档间不同类型的对象等复杂类型，该驱动程序会将数据重新标准化到相应虚拟表中。 具体而言，如果表中包含此类列，该驱动程序会生成以下虚拟表：

* **基表**，其中包含与实际表相同的数据（复杂类型列除外）。 基表使用与其所表示的实际表相同的名称。
* 对于每个复杂类型列生成一个**虚拟表**，这会扩展嵌套数据。 使用实际表名称、分隔符“_”和数组或对象的名称，对虚拟表命名。

虚拟表引用实际表中的数据，以使驱动程序能访问非规范化的数据。 通过查询和联接虚拟表，可访问 MongoDB 数组的内容。

### <a name="example"></a>示例

例如，此处 ExampleTable 为 MongoDB 表，其中“发票”列的每个单元格包含对象数组，“评级”列包含标量类型数组。

| _id | 客户名称 | 发票 | 服务级别 | 评级 |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |银牌服务 |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |金牌服务 |[1,2] |

该驱动程序会生成多个虚拟表来表示此单个表。 第一个虚拟表是名为“ExampleTable”的基表，如下例所示。 基表包含原始表中的所有数据，但已省略数组中的数据，这些数据会在虚拟表中展开。

| _id | 客户名称 | 服务级别 |
| --- | --- | --- |
| 1111 |ABC |银牌服务 |
| 2222 |XYZ |金牌服务 |

下表显示在示例中表示原始数组的虚拟表。 这些表包含以下项：

* 通过 _id 列返回到原始主键列的引用，该列与原始数组的行对应
* 原始数组中数据位置的指示
* 该数组中每个元素展开的数据

**“ExampleTable_Invoices”表：**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | 折扣 |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |吐司炉 |456 |0.2 |
| 1111 |1 |124 |烤箱 |1235 |0.2 |
| 2222 |0 |135 |冰箱 |12543 |0.0 |

**“ExampleTable_Ratings”表：**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |#N/A |


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
