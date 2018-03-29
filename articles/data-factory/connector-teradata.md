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
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 287fc502a0a538660da86e028a81fba04bcac903
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>使用 Azure 数据工厂从 Teradata 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-onprem-teradata-connector.md)
> * [版本 2 - 预览版](connector-teradata.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 Teradata 数据库复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 Teradata 连接器](v1/data-factory-onprem-teradata-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Teradata 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Teradata 连接器支持：

- **12 版本和更高版本**的 Teradata。
- 使用**基本**或 **Windows** 身份验证复制数据。

## <a name="prerequisites"></a>先决条件

要使用此 Teradata 连接器，需要：

- 设置自承载集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。
- 在集成运行时计算机上安装 14 版本或更高版本的 [.NET Data Provider for Teradata](http://go.microsoft.com/fwlink/?LinkId=278886)（适用于 Teradata 的 .NET 数据提供程序）。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Teradata 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Teradata 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Teradata** | 是 |
| server | Teradata 服务器的名称。 | 是 |
| authenticationType | 用于连接 Teradata 数据库的身份验证类型。<br/>允许的值为：Basic 和 Windows。 | 是 |
| username | 指定用于连接到 Teradata 数据库的用户名。 | 是 |
| password | 指定为用户名指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例：**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Teradata 数据集支持的属性列表。

要从 Teradata 复制数据，请将数据集的 type 属性设置为“RelationalTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：RelationalTable | 是 |
| tableName | Teradata 数据库中的表名。 | 否（如果指定了活动源中的“query”） |

**示例：**

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

要从 Teradata 复制数据，请将复制活动中的源类型设置为“RelationalSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：RelationalSource | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

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
| 日期 |DateTime |
| 小数 |小数 |
| Double |Double |
| Graphic |String |
| Integer |Int32 |
| Interval Day |TimeSpan |
| Interval Day To Hour |TimeSpan |
| Interval Day To Minute |TimeSpan |
| Interval Day To Second |TimeSpan |
| Interval Hour |TimeSpan |
| Interval Hour To Minute |TimeSpan |
| Interval Hour To Second |TimeSpan |
| Interval Minute |TimeSpan |
| Interval Minute To Second |TimeSpan |
| Interval Month |String |
| Interval Second |TimeSpan |
| Interval Year |String |
| Interval Year To Month |String |
| Number |Double |
| Period(Date) |String |
| Period(Time) |String |
| Period(Time With Time Zone) |String |
| Period(Timestamp) |String |
| Period(Timestamp With Time Zone) |String |
| SmallInt |Int16 |
| 时间 |TimeSpan |
| Time With Time Zone |String |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTimeOffset |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |String |


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。