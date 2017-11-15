---
title: "使用 Azure 数据工厂向/从 Oracle 复制数据 | Microsoft Docs"
description: "了解如何使用数据工厂将数据从支持的源存储复制到 Oracle 数据库，或从 Oracle 复制到支持的接收器存储。"
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
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: 2266bf17dd769102e70c20728ededb304f73beb4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Oracle 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-onprem-oracle-connector.md)
> * [版本 2 - 预览版](connector-oracle.md)

本文概述了如何在 Azure 数据工厂中使用复制活动从/向 Oracle 数据库复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 数据工厂。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅 [V1 中的 Oracle 连接器](v1/data-factory-onprem-oracle-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 Oracle 数据库复制到任意受支持的接收器数据存储，或将数据从任意受支持的源数据存储复制到 Oracle 数据库。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Oracle 连接器支持以下版本的 Oracle 数据库，并且支持 Basic 或 OID 身份验证。

    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>先决条件

要从/向不可公开访问的 Oracle 数据库复制数据，需要设置自我托管集成运行时。 有关集成运行时的详细信息，请参阅[自我托管集成运行时](create-self-hosted-integration-runtime.md)一文。 集成运行时提供内置 Oracle 驱动程序，因此从/向 Oracle 复制数据时，无需手动安装任何驱动程序。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 Oracle 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Oracle 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：Oracle | 是 |
| connectionString | 指定连接到 Oracle 数据库实例所需的信息。 将此字段标记为 SecureString。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Oracle 数据集支持的属性列表。

要从/向 OData 复制数据，请将数据集的 type 属性设置为“OracleTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：OracleTable | 是 |
| tableName |链接服务引用的 Oracle 数据库中表的名称。 | 是 |

**示例：**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Oracle 源和接收器支持的属性列表。

### <a name="oracle-as-source"></a>Oracle 作为源

要从 Oracle 复制数据，请将复制活动中的源类型设置为“OracleSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：OracleSource | 是 |
| oracleReaderQuery | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否 |

如果未指定“oracleReaderQuery”，则使用在数据集的“结构”部分定义的列，构建针对 Oracle 数据库运行的查询 (`select column1, column2 from mytable`)。 如果数据集定义不具备该“结构”，则从表中选择所有列。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle 作为接收器

要向 Oracle 复制数据，请将复制活动中的接收器类型设置为“OracleSink”。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：OracleSink | 是 |
| writeBatchSize | 缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。<br/>允许的值为：整数（行数）。 |否（默认值为 10000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br/>允许的值为：Timespan。 示例：00:30:00（30 分钟）。 | 否 |
| preCopyScript | 每次运行时，将数据写入到 Oracle 之前，指定复制活动要执行的 SQL 查询。 此属性可用于清理预先加载的数据。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Oracle 的数据类型映射

从/向 Oracle 复制数据时，以下映射用于从 Oracle 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Oracle 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |十进制、字符串（如果精度 > 28） |
| INTEGER |十进制、字符串（如果精度 > 28） |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |十进制、字符串（如果精度 > 28） |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 不支持数据类型 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。