---
title: 使用 Azure 数据工厂从 Teradata Vantage 复制数据
description: 使用数据工厂服务的 Teradata 连接器可将数据从 Teradata Vantage 复制到数据工厂支持作为接收器的数据存储中。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 4074c50aa17bf804696060134e37055a18bd0137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680091"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Teradata Vantage 复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
>
> * [版本 1](v1/data-factory-onprem-teradata-connector.md)
> * [当前版本](connector-teradata.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 Teradata Vantage 复制数据。 本文是在[复制活动概述](copy-activity-overview.md)的基础上编写的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Teradata 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Teradata Vantage 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Teradata 连接器支持：

- Teradata **版本 14.10、15.0、15.10、16.0、16.10 和 16.20**。
- 使用**基本**或 **Windows** 身份验证复制数据。
- 从 Teradata 源进行并行复制。 有关详细信息，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。

> [!NOTE]
>
> 自承载集成运行时 v3.18 发布之后，Azure 数据工厂已升级 Teradata 连接器。 仍支持使用以前的 Teradata 连接器的任何现有工作负荷。 不过，对于新工作负荷，最好是使用新的连接器。 请注意，新路径需要一组不同的链接服务、数据集和复制源。 有关配置详细信息，请参阅以下相应部分。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

从版本 3.18 开始，集成运行时提供内置的 Teradata 驱动程序。 无需手动安装任何驱动程序。 驱动程序要求在自承载集成运行时计算机上安装“Visual C++ Redistributable 2012 Update 4”。 如果尚未安装，请在[此处](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)下载。

对于低于 3.18 的任何自承载集成运行时版本，需要在集成运行时计算机上安装[适用于 Teradata 的 .NET 数据提供程序](https://go.microsoft.com/fwlink/?LinkId=278886)版本 14 或以上。 

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Teradata 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Teradata 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **Teradata**。 | 是 |
| connectionString | 指定连接到 Teradata 实例所需的信息。 请参阅以下示例。<br/>还可以将密码放在 Azure Key Vault 中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| username | 指定用于连接到 Teradata 的用户名。 使用 Windows 身份验证时适用。 | 否 |
| password | 指定为用户名指定的用户帐户的密码。 此外，还可以选择[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 <br>使用 Windows 身份验证时，或引用 Key Vault 中用于基本身份验证的密码时适用。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |是 |

可以在连接字符串中根据情况设置更多的连接属性：

| 属性 | 说明 | 默认值 |
|:--- |:--- |:--- |
| CharacterSet | 要用于会话的字符集。 例如，`CharacterSet=UTF16`。<br><br/>此值可以是用户定义的字符集，也可以是以下预定义的字符集之一： <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>-SHIFT-JIS （Windows、DOS 兼容、KANJISJIS_0S）<br/>-EUC （Unix 兼容，KANJIEC_0U）<br/>-IBM 大型机（KANJIEBCDIC5035_0I）<br/>- KANJI932_1S0<br/>-BIG5 （TCHBIG5_1R0）<br/>-GB （SCHGB2312_1T0）<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | 默认值为 `ASCII`。 |
| MaxRespSize |SQL 请求的响应缓冲区的最大大小（以千字节（Kb）为单位）。 例如，`MaxRespSize=‭10485760‬`。<br/><br/>对于 Teradata 数据库版本16.00 或更高版本，最大值为7361536。 对于使用早期版本的连接，最大值为1048576。 | 默认值为 `65536`。 |

**示例：使用基本身份验证**

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

**示例：使用 Windows 身份验证**

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
> 仍支持以下有效负载。 不过，今后应使用新的有效负载。

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
| type | 数据集的 type 属性必须设置为 `TeradataTable`。 | 是 |
| database | Teradata 实例的名称。 | 否（如果指定了活动源中的“query”） |
| 表 | Teradata 实例中的表名。 | 否（如果指定了活动源中的“query”） |

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
> 仍支持 `RelationalTable` 类型数据集。 不过，我们建议使用新的数据集。

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
>若要详细了解如何使用数据分区从 Teradata 有效加载数据，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。

从 Teradata 复制数据时，复制活动的 **source** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为 `TeradataSource`。 | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区加载时，需要在查询中挂接任何相应的内置分区参数。 有关示例，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。 | 否（如果指定了数据集中的表） |
| partitionOptions | 指定用于从 Teradata 加载数据的数据分区选项。 <br>允许值为： **None** （默认值）、**哈希**和**DynamicRange**。<br>启用分区选项（即，该选项不为 `None`）时，用于从 Teradata 并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| partitionColumnName | 指定用于并行复制的，由范围分区或哈希分区使用的源列的名称。 如果未指定，系统会自动检测表的主索引并将其用作分区列。 <br>当分区选项是 `Hash` 或 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfHashPartitionCondition` 或 `?AdfRangePartitionColumnName`。 请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分中的示例。 | 否 |
| partitionUpperBound | 要从中复制数据的分区列的最大值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 有关示例，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。 | 否 |
| partitionLowerBound | 要从中复制数据的分区列的最小值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 有关示例，请参阅[从 Teradata 进行并行复制](#parallel-copy-from-teradata)部分。 | 否 |

> [!NOTE]
>
> 仍支持 `RelationalSource` 类型复制源，但它不支持从 Teradata 进行并行加载（分区选项）的新内置功能。 不过，我们建议使用新的数据集。

**示例：使用基本查询但不使用分区复制数据**

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

数据工厂 Teradata 连接器提供内置的数据分区，用于从 Teradata 并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。

![分区选项的屏幕截图](./media/connector-teradata/connector-teradata-partition-options.png)

启用分区复制时，数据工厂将对 Teradata 源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则数据工厂会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Teradata 检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Teradata 加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从大型表进行完整加载。                                   | **分区选项**：哈希。 <br><br/>在执行期间，数据工厂将自动检测 PK 列，对其应用哈希，然后按分区复制数据。 |
| 使用自定义查询加载大量数据。                 | **分区选项**：哈希。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于应用哈希分区的列。 如果未指定，数据工厂将自动检测 Teradata 数据集中指定的表的 PK 列。<br><br>在执行期间，数据工厂会将 `?AdfHashPartitionCondition` 替换为哈希分区逻辑，并发送到 Teradata。 |
| 使用自定义查询加载大量数据，某个整数列包含均匀分布的范围分区值。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以针对整数数据类型的列进行分区。<br>**分区上限**和**分区下限**：指定是否希望根据分区列进行筛选，以便仅检索下限和上限之间的数据。<br><br>在执行期间，数据工厂会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound` 和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名和值范围，并将其发送到 Teradata。 <br>例如，如果为分区列“ID”设置了下限 1、上限 80，并将并行复制设置为 4，则数据工厂会按 4 个分区检索数据。 其 ID 分别介于 [1, 20]、[21, 40]、[41, 60] 和 [61, 80] 之间。 |

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
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |不支持。 在源查询中应用显式强制转换。 |
| Integer |Int32 |
| Interval Day |不支持。 在源查询中应用显式强制转换。 |
| Interval Day To Hour |不支持。 在源查询中应用显式强制转换。 |
| Interval Day To Minute |不支持。 在源查询中应用显式强制转换。 |
| Interval Day To Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Hour |不支持。 在源查询中应用显式强制转换。 |
| Interval Hour To Minute |不支持。 在源查询中应用显式强制转换。 |
| Interval Hour To Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Minute |不支持。 在源查询中应用显式强制转换。 |
| Interval Minute To Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Month |不支持。 在源查询中应用显式强制转换。 |
| Interval Second |不支持。 在源查询中应用显式强制转换。 |
| Interval Year |不支持。 在源查询中应用显式强制转换。 |
| Interval Year To Month |不支持。 在源查询中应用显式强制转换。 |
| Number |Double |
| 期间（日期） |不支持。 在源查询中应用显式强制转换。 |
| 期间（时间） |不支持。 在源查询中应用显式强制转换。 |
| 期间（带时区的时间） |不支持。 在源查询中应用显式强制转换。 |
| 期间（时间戳） |不支持。 在源查询中应用显式强制转换。 |
| 期间（带时区的时间戳） |不支持。 在源查询中应用显式强制转换。 |
| SmallInt |Int16 |
| 时间 |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不支持。 在源查询中应用显式强制转换。 |
| Xml |不支持。 在源查询中应用显式强制转换。 |


## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
