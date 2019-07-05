---
title: 使用 Azure 数据工厂从 Teradata 复制数据 | Microsoft Docs
description: 了解数据工厂服务的 Teradata 连接器，可通过它将数据从 Teradata 数据库复制到数据工厂支持作为接收器的数据存储中。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561431"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>使用 Azure 数据工厂从 Teradata 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
>
> * [版本 1](v1/data-factory-onprem-teradata-connector.md)
> * [当前版本](connector-teradata.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 Teradata 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Teradata 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Teradata 连接器支持：

- Teradata**版本 14.10、 15.0、 15.10、 16.0、 16.10 和 16.20**。
- 使用**基本**或 **Windows** 身份验证复制数据。
- Teradata 源中的并行副本。 请参阅[并行从 Teradata 复制](#parallel-copy-from-teradata)部分的详细信息。

> [!NOTE]
>
> Azure 数据工厂自自承载 Integration Runtime v3.18，它提供支持，内置的 ODBC 驱动程序，并提供灵活的连接选项，以及全新的并行复制来提高性能升级 Teradata 连接器。 适用于 Teradata 仍受支持为，则使用以前的 Teradata 连接器的任何现有工作负荷由.NET 数据提供程序提供支持的是，而建议使用新今后的一个。 请注意新路径需要一组不同的链接服务/数据集/复制源。 在配置详细信息，请参阅各自的部分。

## <a name="prerequisites"></a>必备组件

如果你 Teradata 不可公开访问，你需要设置自承载集成运行时。 有关集成运行时的详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)。 集成运行时提供内置的 Teradata 驱动程序从版本 3.18 开始，因此你无需手动安装任何驱动程序。 驱动程序需要"VisualC++可再发行组件 2012 Update 4"的自承载 IR 计算机上，从下载[此处](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)如果还没有安装它。

对于自承载 IR 版本低于 3.18，需要安装[Teradata 的.NET 数据提供程序](https://go.microsoft.com/fwlink/?LinkId=278886)版本 14 或更高版本的集成运行时计算机上。 

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Teradata 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Teradata 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Teradata** | 是 |
| connectionString | 指定连接到 Teradata 数据库实例所需的信息。 请参阅以下示例。<br/>还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 请参阅[在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)文章更多详细信息。 | 是 |
| username | 指定用于连接到 Teradata 数据库的用户名。 使用 Windows 身份验证时适用。 | 否 |
| password | 指定为用户名指定的用户帐户的密码。 您还可以选择[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 <br>适用于使用 Windows 身份验证时，或引用密钥保管库中进行基本身份验证的密码。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例： 使用基本身份验证**

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
> 如果在使用的 Teradata 链接的服务提供支持的.NET Data Provider for teradata 一起使用与以下的有效负载，仍然支持为的是，而建议使用新今后的一个。

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

有关可用于定义数据集的各部分和属性的完整列表，请参阅数据集一文。 本部分提供 Teradata 数据集支持的属性列表。

若要从 Teradata 复制数据，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**TeradataTable** | 是 |
| database | Teradata 数据库的名称。 | 否（如果指定了活动源中的“query”） |
| table | Teradata 数据库中的表名。 | 否（如果指定了活动源中的“query”） |

**示例：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> 如果你已使用"RelationalTable"类型的数据集，如下所示，仍然支持为的是，而建议使用新今后的一个。

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Teradata 源支持的属性列表。

### <a name="teradata-as-source"></a>以 Teradata 作为源

> [!TIP]
>
> 了解详细信息[并行从 Teradata 复制](#parallel-copy-from-teradata)部分，了解如何将数据从 Teradata 数据分区使用高效地加载。

若要从 Teradata 复制数据，支持以下属性将复制活动中**源**部分：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**TeradataSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。<br>启用分区的负载，需要在查询中挂钩相应内置分区个参数。 请参阅中的示例[并行从 Teradata 复制](#parallel-copy-from-teradata)部分。 | 否 （如果指定了表中数据集） |
| partitionOptions | 指定分区选项用于将数据从 Teradata 加载的数据。 <br>允许的值为：**无**（默认值），**哈希**并**DynamicRange**。<br>当启用分区选项 (不 None)，请配置 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 设置复制活动，例如，4，用于确定并行度，同时从 Teradata 加载数据数据库。 | 否 |
| partitionSettings | 指定的数据分区的设置的组。 <br>当分区选项不位于应用`None`。 | 否 |
| partitionColumnName | 指定源列的名称**整数类型中**，将使用的并行复制分区范围。 如果未指定，表的主键将自动检测到并用作分区列。 <br>将应用分区选项时`Hash`或`DynamicRange`。 如果使用查询来检索源数据，挂钩`?AdfHashPartitionCondition`或`?AdfRangePartitionColumnName`WHERE 子句中。 中的示例，请参阅[并行从 Teradata 复制](#parallel-copy-from-teradata)部分。 | 否 |
| partitionUpperBound | 要将数据复制的分区列的最大值。 <br>将应用分区选项时`DynamicRange`。 如果使用查询来检索源数据，挂钩`?AdfRangePartitionUpbound`WHERE 子句中。 中的示例，请参阅[并行从 Teradata 复制](#parallel-copy-from-teradata)部分。 | 否 |
| PartitionLowerBound | 要将数据复制的分区列的最小值。 <br>将应用分区选项时`DynamicRange`。 如果使用查询来检索源数据，挂钩`?AdfRangePartitionLowbound`WHERE 子句中。 中的示例，请参阅[并行从 Teradata 复制](#parallel-copy-from-teradata)部分。 | 否 |

> [!NOTE]
>
> 如果你已使用"RelationalSource"类型的复制源，仍然支持为的但不支持从 Teradata （分区选项） 的新内置并行负载。 建议使用今后这个新绑定。

**使用没有分区的基本查询的示例： 复制数据**

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

## <a name="parallel-copy-from-teradata"></a>从 Teradata 并行复制

数据工厂的 Teradata 连接器提供了内置的数据分区，以在提供最佳性能的同时从 Teradata 复制数据。 您可以找到复制活动的数据分区选项-> Teradata 源：

![分区选项](./media/connector-teradata/connector-teradata-partition-options.png)

启用分区的副本时，数据工厂对 Teradata 源将数据加载分区运行并行查询。 配置并通过控制并行度 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 上复制活动设置。 例如，如果您设置`parallelCopies`为 4，数据工厂同时生成，并根据指定的分区选项和设置，数据从 Teradata 数据库的每个检索部分运行四个查询。

建议以启用并行复制数据分区，尤其是当您加载大量数据从 Teradata 数据库时使用。 以下是针对不同方案的建议的配置：

| 场景                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从较大的表的完全加载                                   | **分区选项**:哈希值。 <br><br/>在执行期间，数据工厂自动检测 PK 列中，将应用对其的哈希并通过分区将数据复制。 |
| 加载大量数据使用自定义查询                 | **分区选项**:哈希值。<br>**查询**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**分区列**:指定用于应用哈希的分区的列。 如果未指定，ADF 将自动检测 Teradata 数据集内指定的表的 PK 列。<br><br>在执行期间，数据工厂替换`?AdfHashPartitionCondition`与哈希分区逻辑和发送到 Teradata。 |
| 加载大量数据使用具有范围分区的平均分布的值的整数列的自定义查询 | **分区选项**:动态范围分区。<br>**查询**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**:指定用于对数据进行分区的列。 可以针对列分区使用整数数据类型。<br>**分区上界**并**分区下限**:指定你想要对分区列，以便只检索范围下限和上限之间的数据进行筛选。<br><br>在执行期间，数据工厂替换`?AdfRangePartitionColumnName`， `?AdfRangePartitionUpbound`，和`?AdfRangePartitionLowbound`使用实际的列名称和值范围为每个分区，并将发送到 Teradata。 <br>例如，如果分区列"ID"设置为 1，上限为 80，为 4，并行复制设置的下限 ADF 检索数据的 4 个分区之间 [1，20]，id 为 [21，40] [41，60] 和 [61，80]。 |

**示例： 查询具有哈希分区**

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

**示例： 查询与动态范围分区**

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

从 Teradata 复制数据时，以下映射用于从 Teradata 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

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
| Graphic |不支持。 应用源查询中的显式强制转换。 |
| Integer |Int32 |
| Interval Day |不支持。 应用源查询中的显式强制转换。 |
| Interval Day To Hour |不支持。 应用源查询中的显式强制转换。 |
| Interval Day To Minute |不支持。 应用源查询中的显式强制转换。 |
| Interval Day To Second |不支持。 应用源查询中的显式强制转换。 |
| Interval Hour |不支持。 应用源查询中的显式强制转换。 |
| Interval Hour To Minute |不支持。 应用源查询中的显式强制转换。 |
| Interval Hour To Second |不支持。 应用源查询中的显式强制转换。 |
| Interval Minute |不支持。 应用源查询中的显式强制转换。 |
| Interval Minute To Second |不支持。 应用源查询中的显式强制转换。 |
| Interval Month |不支持。 应用源查询中的显式强制转换。 |
| Interval Second |不支持。 应用源查询中的显式强制转换。 |
| Interval Year |不支持。 应用源查询中的显式强制转换。 |
| Interval Year To Month |不支持。 应用源查询中的显式强制转换。 |
| Number |Double |
| 期间 （日期） |不支持。 应用源查询中的显式强制转换。 |
| 时间 （时间） |不支持。 应用源查询中的显式强制转换。 |
| 段 （与时区的时间） |不支持。 应用源查询中的显式强制转换。 |
| 时间 （时间戳） |不支持。 应用源查询中的显式强制转换。 |
| 段 （与时区的时间戳） |不支持。 应用源查询中的显式强制转换。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不支持。 应用源查询中的显式强制转换。 |
| Xml |不支持。 应用源查询中的显式强制转换。 |


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
