---
title: 使用 Azure 数据工厂向/从 Azure Database for PostgreSQL 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，向/从 Azure Database for PostgreSQL 复制数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: 7443ab3abb12451619e77d925912b4bfc118cfaa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681228"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure Database for PostgreSQL

本文介绍如何使用 Azure 数据工厂中的复制活动功能从 Azure Database for PostgreSQL 复制数据。 它构建于[Azure 数据工厂中的复制活动](copy-activity-overview.md)一文，其中提供了复制活动的一般概述。

此连接器专用于[Azure Database for PostgreSQL 服务](../postgresql/overview.md)。 若要从位于本地或云中的一般 PostgreSQL 数据库复制数据，请使用[PostgreSQL 连接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Database for PostgreSQL 连接器：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可将数据从 Azure Database for PostgreSQL 复制到任何支持的接收器数据存储。 或者，可将数据从任何支持的源数据存储复制到 Azure Database for PostgreSQL。 有关复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置驱动程序以启用连接。 因此，无需手动安装任何驱动程序即可使用此连接器。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各节提供了有关用于定义特定于 Azure Database for PostgreSQL 连接器的数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Database for PostgreSQL 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为： **AzurePostgreSql**。 | 是 |
| connectionString | 用于连接到 Azure Database for PostgreSQL 的 ODBC 连接字符串。<br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 你还可以将密码放在 Azure Key Vault，并将 `password` 配置从连接字符串中提取出来。 有关更多详细信息，请参阅以下示例并[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 此属性表示要用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 以下是每种情况下可以设置的更多属性：

| 属性 | 说明 | 选项 | 必选 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驱动程序用于加密在驱动程序和数据库服务器之间发送的数据的方法。 例如 `EncryptionMethod=<0/1/6>;`| 0 (No Encryption) **(Default)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 确定在启用 SSL 加密时，驱动程序是否验证数据库服务器发送的证书（加密方法 = 1）。 例如 `ValidateServerCertificate=<0/1>;`| 0 (Disabled) **(Default)** / 1 (Enabled) | 否 |

**示例**：

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**示例**：

***在 Azure Key Vault 中存储密码***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各节和属性的完整列表，请参阅[Azure 数据工厂中的数据集](concepts-datasets-linked-services.md)。 本部分提供在数据集内 Azure Database for PostgreSQL 支持的属性列表。

要从 Azure Database for PostgreSQL 复制数据，请将数据集的 type 属性设置为 **AzurePostgreSqlTable**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为**AzurePostgreSqlTable** | 是 |
| tableName | 表的名称 | 否（如果指定了活动源中的“query”） |

**示例**：

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各节和属性的完整列表，请参阅[Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)。 本部分提供 Azure Database for PostgreSQL 源支持的属性列表。

### <a name="azure-database-for-postgresql-as-source"></a>用于 PostgreSql 的 Azure 数据库作为源

要从 Azure Database for PostgreSQL 复制数据，请将复制活动中的源类型设置为 **AzurePostgreSqlSource**。 复制活动**source**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为**AzurePostgreSqlSource** | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如： `"SELECT * FROM MyTable"` | 否（如果指定了数据集中的 tableName 属性） |

**示例**：

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL 作为接收器

将数据复制到 Azure Database for PostgreSQL 时，复制活动的 **sink** 节支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为**AzurePostgreSQLSink**。 | 是 |
| preCopyScript | 指定要在每次运行中将数据写入 Azure Database for PostgreSQL 之前要执行的复制活动的 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |
| writeBatchSize | 当缓冲区大小达到 writeBatchSize 时，会将数据插入 Azure Database for PostgreSQL 表。<br>允许的值是表示行数的整数。 | 否（默认值为 10,000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br>允许的值为 Timespan 字符串。 示例为 00:30:00（30 分钟）。 | 否（默认值为 00:00:30） |

**示例**：

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

有关属性的详细信息，请参阅[Azure 数据工厂中的查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
