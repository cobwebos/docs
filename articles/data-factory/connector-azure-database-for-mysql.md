---
title: 使用 Azure 数据工厂向/从 Azure Database for MySQL 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，向/从 Azure Database for MySQL 复制数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: jingwang
ms.openlocfilehash: dd450852931fdc57192bcc126b710d8cfb3e27ac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681235"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>使用 Azure 数据工厂向/从 Azure Database for MySQL 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Azure Database for MySQL 复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

此连接器专用于 [Azure Database for MySQL 服务](../mysql/overview.md)。 若要从位于本地或云中的通用 MySQL 数据库复制数据，请使用 [MySQL 连接器](connector-mysql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Database for MySQL 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可将数据从 Azure Database for MySQL 复制到任何支持的接收器数据存储。 或者，可将数据从任何支持的源数据存储复制到 Azure Database for MySQL。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Database for MySQL 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Database for MySQL 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureMySql** | 是 |
| connectionString | 指定连接到 Azure Database for MySQL 实例所需的连接信息。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[将凭据存储在 Azure 密钥保管库中](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`。 你可以根据自己的情况设置更多属性：

| 属性 | 说明 | 选项 | 必选 |
|:--- |:--- |:--- |:--- |
| SSLMode | 此选项指定驱动程序在连接到 MySQL 时是否使用 SSL 加密和验证。 例如 `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(Default)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | 否 |
| UseSystemTrustStore | 此选项指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 例如 `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(Default)** | 否 |

**示例：**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储密码**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Azure Database for MySQL 数据集支持的属性列表。

要从 Azure Database for MySQL 复制数据，请将数据集的 type 属性设置为 **AzureMySqlTable**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AzureMySqlTable** | 是 |
| tableName | MySQL 数据库中的表名。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Database for MySQL 源和接收器支持的属性列表。

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL 用作源

若要从 Azure Database for MySQL 复制数据，复制活动的 **source** 节需要支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**AzureMySqlSource** | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |
| queryCommandTimeout | 查询请求超时前的等待时间。默认值为120分钟（02:00:00） | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL 作为接收器

将数据复制到 Azure Database for MySQL 时，复制活动的 **sink** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为： **AzureMySqlSink** | 是 |
| preCopyScript | 每次运行时将数据写入 Azure Database for MySQL 之前，为要执行的复制活动指定 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |
| writeBatchSize | 当缓冲区大小达到 writeBatchSize 时，会将数据插入 Azure Database for MySQL 表。<br>允许的值为 integer（表示行数）。 | 否（默认值为 10,000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br>允许的值为 Timespan。 示例为 00:30:00（30 分钟）。 | 否（默认值为 00:00:30） |

**示例：**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Azure Database for MySQL 的数据类型映射

从 Azure Database for MySQL 复制数据时，以下映射用于从 MySQL 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Azure Database for MySQL 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
