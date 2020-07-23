---
title: 复制活动中的架构和数据类型映射
description: 了解 Azure 数据工厂中的复制活动如何将架构和数据类型从源数据映射到接收器数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: b48fb28a56cdc1c836233cd2bd03a1f9e750a0a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249646"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>复制活动中的架构和数据类型映射
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍 Azure 数据工厂复制活动如何执行从源数据到接收器数据的架构映射和数据类型映射。

## <a name="schema-mapping"></a>架构映射

### <a name="default-mapping"></a>默认映射

默认情况下，复制活动按**列名**以区分大小写的方式将源数据映射到接收器。 如果接收器不存在（例如，写入到文件中），则源字段名称将作为接收器名称保存。 此类默认映射支持从执行到接收器的灵活的架构和架构偏移，并将源数据存储返回的所有数据复制到接收器。

如果源是无标题行的文本文件，则需要[显式映射](#explicit-mapping)，因为源不包含列名称。

### <a name="explicit-mapping"></a>显式映射

你还可以指定显式映射，以根据你的需要自定义从源到接收器的列/字段映射。 通过显式映射，可以仅将部分源数据复制到接收器，或将源数据映射到具有不同名称的接收器，或改变表格/层次结构数据的形状。 复制活动：

1. 从源读取数据并确定源架构。
2. 应用已定义的映射。
3. 将数据写入接收器。

了解以下内容的详细信息：

- [表格源到表格接收器](#tabular-source-to-tabular-sink)
- [表格接收器的分层源](#hierarchical-source-to-tabular-sink)
- [表格/分层源到层次结构接收器](#tabularhierarchical-source-to-hierarchical-sink)

可在数据工厂创作 UI > 复制活动-> 映射 "选项卡上配置映射，或以编程方式在复制活动 > 属性中指定映射 `translator` 。 `translator`  ->  `mappings` 数组 > 的对象 > `source` 和 `sink` （它们指向用于映射数据的特定列/字段）支持以下属性。

| Property | 描述                                                  | 必需 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | 源或接收器列/字段的名称。 适用于表格源和接收器。 | 是      |
| 序号  | 列索引。 从1开始。 <br>在使用带分隔符的文本但没有标头行时应用，为必填项。 | 否       |
| path     | 要提取或映射的每个字段的 JSON 路径表达式。 适用于分层源和接收器，例如 Cosmos DB、MongoDB 或 REST 连接器。<br>对于根对象下的字段，JSON 路径以 root 开头 `$` ; 对于属性所选择的数组中的字段 `collectionReference` ，json 路径从数组元素开始，而不是 `$` 。 | 否       |
| type     | 源或接收器列的数据工厂临时数据类型。 通常，不需要指定或更改此属性。 了解有关[数据类型映射](#data-type-mapping)的详细信息。 | 否       |
| culture  | 源或接收器列的区域性。 当类型为 `Datetime` 或 `Datetimeoffset` 时应用。 默认为 `en-us`。<br>通常，不需要指定或更改此属性。 了解有关[数据类型映射](#data-type-mapping)的详细信息。 | 否       |
| format   | 当类型为 `Datetime` 或 `Datetimeoffset` 时要使用的格式字符串。 请参阅[自定义日期和时间格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)，了解如何设置日期时间格式。 通常，不需要指定或更改此属性。 了解有关[数据类型映射](#data-type-mapping)的详细信息。 | 否       |

`translator`除外，还支持以下属性 `mappings` ：

| Property            | 描述                                                  | 必须 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 从分层源（例如 Cosmos DB、MongoDB 或 REST 连接器）复制数据时应用。<br>若要进行迭代操作，以同一模式从**数组字段中**的对象提取数据并按行和对象进行转换，请指定要进行交叉应用的该数组的 JSON 路径。 | 否       |

#### <a name="tabular-source-to-tabular-sink"></a>表格源到表格接收器

例如，将数据从 Salesforce 复制到 Azure SQL 数据库并显式映射三列：

1. 在 "复制活动-> 映射" 选项卡上，单击 "**导入架构**" 按钮以导入源和接收器架构。

2. 映射所需字段并排除/删除其余字段。

![将表格映射到表格](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

可以在复制活动负载中将相同的映射配置为以下内容（请参阅 `translator` ）：

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

若要从带分隔符的文本文件复制数据而不使用标头行，则这些列将按序号而不是名称来表示。 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>表格接收器的分层源

将数据从分层源复制到表格接收器时，复制活动支持以下功能：

- 从对象和数组中提取数据。
- 交叉使用数组中的相同模式应用多个对象，在这种情况下，可以在表格结果中将一个 JSON 对象转换为多个记录。

对于更高级的分层到表格转换，可以使用[数据流](concepts-data-flow-overview.md)。 

例如，如果有包含以下内容的源 MongoDB 文档：

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

并且您希望使用标题行将它复制到具有以下格式的文本文件中：通过平展数组中的数据 *（order_pd 和 order_price）* ，并使用公用根信息 *（number、date 和 city）* 进行交叉联接：

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | 西雅图 |
| 01          | 20170122  | P2       | 13          | 西雅图 |
| 01          | 20170122  | P3       | 231         | 西雅图 |

可在数据工厂创作 UI 上定义此类映射：

1. 在 "复制活动-> 映射" 选项卡上，单击 "**导入架构**" 按钮以导入源和接收器架构。 在导入架构时，数据工厂会示例前几个对象，如果没有显示任何字段，则可以将其添加到层次结构中的正确层上，将鼠标悬停在现有字段名称上，然后选择添加节点、对象或数组。

2. 选择要从中迭代和提取数据的数组。 它将自动填充为**集合引用**。 注意对于此类操作，只支持单阵列。

3. 将所需字段映射到接收器。 数据工厂自动确定层次结构端对应的 JSON 路径。

![使用 UI 将层次结构映射到表格](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

还可以切换到**高级编辑器**，在这种情况下，可以直接查看和编辑字段的 JSON 路径。 如果选择在此视图中添加新映射，请指定 JSON 路径。

![使用高级编辑器将层次结构映射到表格](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

可以在复制活动负载中将相同的映射配置为以下内容（请参阅 `translator` ）：


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>表格/分层源到层次结构接收器

用户体验流程类似于到[表格接收器的层次结构源](#hierarchical-source-to-tabular-sink)。 

将数据从表格源复制到分层接收器时，不支持写入对象内的数组。

将数据从层次结构源复制到分层接收器时，还可以通过选择对象/数组并映射到接收器而不触及内部字段，来保留整个层的层次结构。

对于更高级的数据重整形转换，您可以使用[数据流](concepts-data-flow-overview.md)。 

### <a name="parameterize-mapping"></a>参数化映射

如果要创建模板化管道以动态复制大量对象，请确定是否可以利用[默认映射](#default-mapping)，或者是否需要为各个对象定义[显式映射](#explicit-mapping)。

如果需要显式映射，可以执行以下操作：

1. 使用管道级别的对象类型定义参数，例如 `mapping` 。

2. 参数化映射：在复制活动-> 映射 "选项卡上，选择添加动态内容并选择上面的参数。 活动有效负载如下所示：

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. 构造要传入到映射参数的值。 它应该是定义的整个对象 `translator` ，请参阅[显式映射](#explicit-mapping)部分中的示例。 例如，对于表格格式的源到表格的接收器副本，该值应为 `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` 。

## <a name="data-type-mapping"></a>数据类型映射

复制活动执行源类型到接收器类型的映射，如下流所示： 

1. 从源本机数据类型转换为 Azure 数据工厂临时数据类型。
2. 根据需要自动转换过渡数据类型，以匹配相应的接收器类型，适用于[默认映射](#default-mapping)和[显式映射](#explicit-mapping)。
3. 从 Azure 数据工厂临时数据类型转换为接收器本机数据类型。

复制活动当前支持以下临时数据类型： Boolean、Byte、Byte array、Datetime、DatetimeOffset、Decimal、Double、GUID、Int16、Int32、Int64、SByte、Single、String、Timespan、UInt16、UInt32 和 UInt64。

在临时类型和接收器之间支持以下数据类型转换。

| Source\Sink | 布尔 | Byte Array | 小数 | 日期/时间<small>（1）</small> | Float 点<small>（2）</small> | GUID | Integer <small>（3）</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| 布尔     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Byte Array  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| 日期/时间   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| 小数     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Float 点 | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Integer     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

（1）日期/时间包含 DateTime 和 DateTimeOffset。

（2）浮点型包含 Single 和 Double。

（3）整数包括 SByte、Byte、Int16、UInt16、Int32、UInt32、Int64 和 UInt64。

> [!NOTE]
> - 在表格数据之间进行复制时，当前支持此类数据类型转换。 不支持分层的源/接收器，这意味着源和接收器临时类型之间没有系统定义的数据类型转换。
> - 此功能适用于最新的数据集模型。 如果在 UI 中未看到此选项，请尝试创建一个新数据集。

数据类型转换（在 programmatical 创作部分下）的复制活动中支持以下属性 `translator` ：

| Property                         | 描述                                                  | 必需 |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | 启用新的数据类型转换体验。 <br>由于向后兼容性，默认值为 false。<br><br>对于自2020年6月晚些时间以来通过数据工厂创作 UI 创建的新复制活动，默认情况下会启用此数据类型转换以获得最佳体验，你可以在适用方案的 "复制活动-> 映射" 选项卡上查看以下类型转换设置。 <br>若要以编程方式创建管道，需要将 `typeConversion` 属性显式设置为 true 以启用它。<br>对于在此功能发布之前创建的现有复制活动，您不会在数据工厂创作 UI 上看到用于向后兼容的类型转换选项。 | 否       |
| typeConversionSettings           | 类型转换设置的组。 当 `typeConversion` 设置为时应用 `true` 。 以下属性都是此组的所有属性。 | 否       |
| *下`typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | 在从 DatetimeOffset 到 Datetime 时，在将源数据转换为具有不同类型的接收器时允许截断数据。 <br>默认值为 True。 | 否       |
| treatBooleanAsNumber             | 将布尔值视为数字，例如，将布尔值视为1。<br>默认值为 false。 | 否       |
| dateTimeFormat                   | 在不带时区偏移量的日期和字符串之间进行转换时的格式字符串，例如 `yyyy-MM-dd HH:mm:ss.fff` 。  有关详细信息，请参阅[自定义日期和时间格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | 否       |
| dateTimeOffsetFormat             | 在具有时区偏移量的日期和字符串之间进行转换时，设置格式字符串，例如 `yyyy-MM-dd HH:mm:ss.fff zzz` 。  有关详细信息，请参阅[自定义日期和时间格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | 否       |
| timeSpanFormat                   | 转换时间段与字符串之间的格式字符串，例如 `dd\.hh\:mm` 。 有关详细信息，请参阅[自定义 TimeSpan 格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-timespan-format-strings)。 | 否       |
| culture                          | 转换类型时要使用的区域性信息，例如 `en-us` 或 `fr-fr` 。 | 否       |

**示例：**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>旧模型

> [!NOTE]
> 对于向后兼容性，仍支持以下模型将源列/字段映射到接收器。 建议使用[架构映射](#schema-mapping)中提到的新模型。 数据工厂创作 UI 已切换为生成新模型。

### <a name="alternative-column-mapping-legacy-model"></a>备用列映射（旧模型）

您可以指定 "复制活动->" `translator`  ->  `columnMappings` 来映射表格格式的数据。 在这种情况下，输入和输出数据集都需要“structure”节。 列映射支持将源数据集“structure”中的所有列或列子集接收器数据集“structure”中的所有列  。 以下是导致异常的错误条件：

- 源数据存储查询结果中没有输入数据集“structure”部分中指定的列名称。
- 接收器数据存储（如果具有预定义架构）没有输出数据集“structure”部分中指定的列名称。
- 接收器数据集“structure”中的列数量多于或少于映射中指定的数量。
- 重复的映射。

在以下示例中，输入数据集有一个结构，并且它指向本地 Oracle 数据库中的表。

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

在本示例中，输出数据集有一个结构，并指向 Salesfoce 中的表。

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

以下 JSON 定义管道中的复制活动。 使用 **translator** -> **columnMappings** 属性将源中的列映射到接收器中的列。

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

### <a name="alternative-schema-mapping-legacy-model"></a>备用架构映射（旧模型）

你可以指定 "复制活动-> `translator`  ->  `schemaMapping` " 来映射分层的数据和表格格式的数据，例如，从 MongoDB/REST 复制到文本文件，并从 Oracle 复制到 Azure Cosmos DB 的适用于 MongoDB 的 API。 复制活动 `translator` 部分支持以下属性：

| 属性            | 描述                                                  | 必需 |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | 复制活动转换器的 type 属性必须设置为：**TabularTranslator** | 是      |
| schemaMapping       | 键值对的集合，代表**从源端到接收器端**的映射关系。<br/>- **键：** 代表源。 对于**表格源**，指定数据集结构中定义的列名称；对于**分层源**，指定要提取和映射的每个字段的 JSON 路径表达式。<br>- **值：** 代表接收器。 对于**表格接收器**，指定数据集结构中定义的列名称；对于**分层接收器**，指定要提取和映射的每个字段的 JSON 路径表达式。 <br>在使用分层数据时，对于根对象下的字段，JSON 路径以根 $ 开头；对于按 `collectionReference` 属性选择的数组中的字段，JSON 路径以数组元素开头。 | 是      |
| collectionReference | 若要进行迭代操作，以同一模式从**数组字段中**的对象提取数据并按行和对象进行转换，请指定要进行交叉应用的该数组的 JSON 路径。 仅当分层数据为源时，才支持此属性。 | 否       |

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

而你需要按以下格式通过平展数组中数据（order_pd 和 order_price）的方式将其复制到 Azure SQL 表中，并使用常见的根信息（数字、日期和城市）  进行交叉联接： 

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | 西雅图 |
| 01          | 20170122  | P2       | 13          | 西雅图 |
| 01          | 20170122  | P3       | 231         | 西雅图 |

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

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
