---
title: 使用 Azure 数据工厂从 Teradata Vantage 复制数据 |Microsoft Docs
description: 利用数据工厂服务的 Teradata 连接器，你可以将数据从 Teradata Vantage 复制到数据工厂支持的数据存储作为接收器。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: f17a7ef2131662cdb9ef4d138303556215810fba
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959013"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Teradata Vantage 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
>
> * [版本 1](v1/data-factory-onprem-teradata-connector.md)
> * [当前版本](connector-teradata.md)

本文概述如何使用 Azure 数据工厂中的复制活动从 Teradata Vantage 复制数据。 它基于[复制活动概述](copy-activity-overview.md)进行构建。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Teradata Vantage 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Teradata 连接器支持：

- Teradata **版本 14.10、15.0、15.10、16.0、16.10 和 16.20**。
- 使用**基本**或**Windows**身份验证复制数据。
- 从 Teradata 源进行并行复制。 有关详细信息，请参阅[从 Teradata 并行复制](#parallel-copy-from-teradata)部分。

> [!NOTE]
>
> 在发布自承载集成运行时 v 3.18 之后，Azure 数据工厂将升级 Teradata 连接器。 仍支持使用以前的 Teradata 连接器的任何现有工作负荷。 不过，对于新的工作负荷，最好使用新的工作负荷。 请注意，新路径需要一组不同的链接服务、数据集和复制源。 有关配置的详细信息，请参阅以下各节。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

集成运行时提供内置的 Teradata 驱动程序，从版本3.18 开始。 无需手动安装任何驱动程序。 驱动程序需要自承载C++集成运行时计算机上的 "Visual 可再发行2012更新 4"。 如果尚未安装，请从[此处](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)下载。

对于早于3.18 的任何自承载集成运行时版本，请在集成运行时计算机上安装[Teradata 的 .Net Data Provider for](https://go.microsoft.com/fwlink/?LinkId=278886)14 或更高版本。 

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Teradata 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Teradata 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为**Teradata**。 | 是 |
| connectionString | 指定连接到 Teradata 实例所需的信息。 请参阅以下示例。<br/>你还可以将密码放在 Azure Key Vault 中，并将`password`配置从连接字符串中提取出来。 有关更多详细信息，请参阅[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| username | 指定用于连接到 Teradata 的用户名。 当使用 Windows 身份验证时适用。 | 否 |
| password | 指定为用户名指定的用户帐户的密码。 此外，还可以选择[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 <br>当使用 Windows 身份验证或在 Key Vault 中引用密码进行基本身份验证时适用。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |是 |

**使用基本身份验证的示例**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**使用 Windows 身份验证的示例**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 仍支持以下负载。 不过，今后应使用新的。

**先前的有效负载：**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

本部分提供 Teradata 数据集支持的属性列表。 有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。

从 Teradata 复制数据时，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为`TeradataTable`。 | 是 |
| database | Teradata 实例的名称。 | 否（如果指定了活动源中的“query”） |
| 表 | Teradata 实例中表的名称。 | 否（如果指定了活动源中的“query”） |

**示例：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`类型数据集仍受支持。 但是，我们建议使用新的数据集。

**先前的有效负载：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 Teradata 源支持的属性列表。 有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="teradata-as-source"></a>以 Teradata 作为源

>[!TIP]
>若要使用数据分区有效地从 Teradata 加载数据，请从 Teradata 部分中的[并行复制](#parallel-copy-from-teradata)中了解详细信息。

从 Teradata 复制数据时，复制活动的 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为`TeradataSource`。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区加载时，需要在查询中挂接任何对应的内置分区参数。 有关示例，请参阅[从 Teradata 并行复制](#parallel-copy-from-teradata)部分。 | 否（如果指定了数据集中的表） |
| partitionOptions | 指定用于从 Teradata 加载数据的数据分区选项。 <br>允许的值为：**None**（默认值）、**Hash** 和 **DynamicRange**。<br>启用分区选项（即，不`None`是）时，从 Teradata 并行加载数据的并行度由复制活动的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当 partition 选项不`None`为时应用。 | 否 |
| partitionColumnName | 指定将由范围分区或哈希分区用于并行复制的源列的名称。 如果未指定，则自动检测表的主索引，并将其用作分区列。 <br>当 partition 选项为`Hash`或`DynamicRange`时应用。 如果使用查询来检索源数据，请在 where 子句`?AdfHashPartitionCondition`中`?AdfRangePartitionColumnName`使用挂钩或。 请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分中的示例。 | 否 |
| partitionUpperBound | 要向其复制数据的分区列的最大值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，则在`?AdfRangePartitionUpbound` WHERE 子句中挂接。 有关示例，请参阅[从 Teradata 并行复制](#parallel-copy-from-teradata)部分。 | 否 |
| partitionLowerBound | 要向其复制数据的分区列的最小值。 <br>当 partition 选项为`DynamicRange`时应用。 如果使用查询来检索源数据，则在 WHERE 子句`?AdfRangePartitionLowbound`中挂接。 有关示例，请参阅[从 Teradata 并行复制](#parallel-copy-from-teradata)部分。 | 否 |

> [!NOTE]
>
> `RelationalSource`类型复制源仍受支持，但它不支持 Teradata 中的新内置并行加载（分区选项）。 但是，我们建议使用新的数据集。

**示例：使用不带分区的基本查询复制数据**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>从 Teradata 进行并行复制

数据工厂 Teradata 连接器提供内置数据分区，用于并行复制 Teradata 中的数据。 可以在复制活动的**源表**上查找数据分区选项。

![分区选项的屏幕截图](./media/connector-teradata/connector-teradata-partition-options.png)

启用分区副本时，数据工厂对 Teradata 源运行并行查询以按分区加载数据。 并行度由 "复制" 活动[`parallelCopies`](copy-activity-performance.md#parallel-copy)的设置控制。 例如，如果将设置`parallelCopies`为4，则数据工厂会同时生成并运行基于指定分区选项和设置的四个查询，每个查询将从 Teradata 中检索部分数据。

建议使用数据分区启用并行复制，尤其是在从 Teradata 加载大量数据时。 下面是针对不同方案的建议配置。 将数据复制到基于文件的数据存储时，将 recommanded 写入文件夹作为多个文件（仅指定文件夹名称），在这种情况下，性能比写入单个文件更好。

| 应用场景                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大表的完全加载。                                   | **分区选项**：哈希。 <br><br/>在执行期间，数据工厂会自动检测 PK 列，对其应用哈希，并按分区复制数据。 |
| 使用自定义查询加载大量数据。                 | **分区选项**：哈希。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于应用哈希分区的列。 如果未指定，数据工厂会自动检测在 Teradata 数据集中指定的表的 PK 列。<br><br>在执行期间，数据工厂`?AdfHashPartitionCondition`将替换为哈希分区逻辑，并将发送到 Teradata。 |
| 使用自定义查询加载大量数据，使用一个整数列，其中包含均匀分布的范围分区值。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以对具有整数数据类型的列进行分区。<br>**分区上限**和**分区下限**：指定是否要针对分区列进行筛选，以便仅检索下限和上限之间的数据。<br><br>在执行期间，数据工厂`?AdfRangePartitionColumnName`会`?AdfRangePartitionUpbound`将、 `?AdfRangePartitionLowbound`和替换为每个分区的实际列名称和值范围，并将发送到 Teradata。 <br>例如，如果将 "ID" 列的下限设置为1，将上限设置为80，并将 "并行副本" 设置为 "4"，则数据工厂将按4个分区检索数据。 它们的 Id 分别介于 [1，20]、[21，40]、[41、60] 和 [61，80] 之间。 |

**示例：使用哈希分区进行查询**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata 的数据类型映射

从 Teradata 复制数据时，将应用以下映射。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Teradata 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| 字符 |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |不受支持。 在源查询中应用显式强制转换。 |
| 整数 |Int32 |
| Interval Day |不受支持。 在源查询中应用显式强制转换。 |
| Interval Day To Hour |不受支持。 在源查询中应用显式强制转换。 |
| Interval Day To Minute |不受支持。 在源查询中应用显式强制转换。 |
| Interval Day To Second |不受支持。 在源查询中应用显式强制转换。 |
| Interval Hour |不受支持。 在源查询中应用显式强制转换。 |
| Interval Hour To Minute |不受支持。 在源查询中应用显式强制转换。 |
| Interval Hour To Second |不受支持。 在源查询中应用显式强制转换。 |
| Interval Minute |不受支持。 在源查询中应用显式强制转换。 |
| Interval Minute To Second |不受支持。 在源查询中应用显式强制转换。 |
| Interval Month |不受支持。 在源查询中应用显式强制转换。 |
| Interval Second |不受支持。 在源查询中应用显式强制转换。 |
| Interval Year |不受支持。 在源查询中应用显式强制转换。 |
| Interval Year To Month |不受支持。 在源查询中应用显式强制转换。 |
| 数量 |Double |
| 期间（日期） |不受支持。 在源查询中应用显式强制转换。 |
| 期间（时间） |不受支持。 在源查询中应用显式强制转换。 |
| 期间（带时区的时间） |不受支持。 在源查询中应用显式强制转换。 |
| 期间（时间戳） |不受支持。 在源查询中应用显式强制转换。 |
| 期间（带时区的时间戳） |不受支持。 在源查询中应用显式强制转换。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| 时间戳 |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不受支持。 在源查询中应用显式强制转换。 |
| Xml |不受支持。 在源查询中应用显式强制转换。 |


## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
