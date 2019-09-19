---
title: 使用 Azure 数据工厂将数据复制到 Azure Database for PostgreSQL 和从中复制数据 |Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据复制到 Azure Database for PostgreSQL。
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
ms.openlocfilehash: 4cb6f420b6d084539dc98a09632d0760a1344012
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092174"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure Database for PostgreSQL

本文概述了如何使用 Azure 数据工厂中的复制活动从 Azure Database for PostgreSQL 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

此连接器专用于[Azure Database for PostgreSQL 服务](../postgresql/overview.md)。 若要从位于本地或云中的通用 PostgreSQL 数据库复制数据，请使用[PostgreSQL 连接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Azure Database for PostgreSQL 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可将数据从 Azure Database for PostgreSQL 复制到任何支持的接收器数据存储。 或者，你可以将数据从任何支持的源数据存储复制到 Azure Database for PostgreSQL。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Azure Database for PostgreSQL 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Azure Database for PostgreSQL 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzurePostgreSql** | 是 |
| connectionString | 用于连接到 Azure Database for PostgreSQL 的 ODBC 连接字符串。<br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[将凭据存储在 Azure 密钥保管库中](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure Integration Runtime 或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 你可以根据自己的情况设置更多属性：

| 属性 | 描述 | 选项 | 必填 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驱动程序用于加密在驱动程序和数据库服务器之间发送的数据的方法。 例如，`EncryptionMethod=<0/1/6>;`| 0 (No Encryption) **(Default)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 启用 SSL 加密后，确定驱动程序是否验证数据库服务器发送的证书（加密方法=1）。 例如，`ValidateServerCertificate=<0/1>;`| 0 (Disabled) **(Default)** / 1 (Enabled) | 否 |

**示例：**

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

**示例：在 Azure 密钥保管库中存储密码**

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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Azure Database for PostgreSQL 数据集支持的属性列表。

要从 Azure Database for PostgreSQL 复制数据，请将数据集的 type 属性设置为 **AzurePostgreSqlTable**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AzurePostgreSqlTable** | 是 |
| tableName | 表名称。 | 否（如果指定了活动源中的“query”） |

**示例**

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

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Database for PostgreSQL 数据源支持的属性列表。

### <a name="azure-database-for-postgresql-as-source"></a>用于 PostgreSql 的 Azure 数据库作为源

要从 Azure Database for PostgreSQL 复制数据，请将复制活动中的源类型设置为 **AzurePostgreSqlSource**。 复制活动**source**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**AzurePostgreSqlSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

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

### <a name="azure-database-for-postgresql-as-sink"></a>作为接收器 Azure Database for PostgreSQL

若要将数据复制到 Azure Database for PostgreSQL，请在复制活动**接收器**部分中支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**AzurePostgreSQLSink** | 是 |
| preCopyScript | 指定复制活动要执行的 SQL 查询，然后在每次运行中将数据写入 Azure Database for PostgreSQL。 可以使用此属性清除预加载的数据。 | 否 |
| writeBatchSize | 当缓冲区大小达到 writeBatchSize 时，将数据插入 Azure Database for PostgreSQL 表。<br>允许的值是表示行数的整数。 | 否（默认值为 10,000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br>允许的值为 Timespan。 示例为 00:30:00（30 分钟）。 | 否（默认值为00:00:30） |

**示例：**

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

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
