---
title: 在 Snowflake 中复制和转换数据
description: 了解如何使用数据工厂在 Snowflake 中复制和转换数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: 5bb5599c6ab6e630e0f26c6d4a13e9c9af8a15a7
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405167"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>使用 Azure 数据工厂在 Snowflake 中复制和转换数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何使用 Azure 数据工厂中的复制活动将数据从和复制到雪花，并使用数据流转换雪花中的数据。 有关数据工厂的详细信息，请参阅[介绍性文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Snowflake 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)表的[复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

对于复制活动，此 Snowflake 连接器支持以下功能：

- 从 Snowflake 复制数据：利用 Snowflake 的 [COPY into [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 命令实现最佳性能。
- 将数据复制到 Snowflake 中：利用 Snowflake 的 [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 命令实现最佳性能。 它支持 Azure 上的 Snowflake。 

使用 Azure Synapse Analytics 工作区时，不支持雪花作为接收器。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分详细介绍了用来定义特定于 Snowflake 连接器的数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

Snowflake 链接服务支持以下属性。

| 属性         | 说明                                                  | 必需 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | type 属性必须设置为 **Snowflake**。              | 是      |
| connectionString | 指定连接到 Snowflake 实例所需的信息。 可以选择将密码或整个连接字符串置于 Azure Key Vault。 如需更多详细信息，请参阅表下面的示例和[将凭据存储在 Azure Key Vault 中](store-credentials-in-key-vault.md)一文。<br><br>部分典型设置：<br>- **帐户名：** 雪花帐户的  [完整帐户名称](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (包括标识区域和云平台) 的其他段，如 xy12345-zh-cn。<br/>- 用户名：用于连接的用户登录名。<br>- 密码：用户的密码。<br>- 数据库：要在连接后使用的默认数据库。 它应为指定角色具有权限的现有数据库。<br>- 仓库：要在连接后使用的虚拟仓库。 它应为指定角色具有权限的现有仓库。<br>- 角色：要在 Snowflake 会话中使用的默认访问控制角色。 指定角色应为已分配给指定用户的现有角色。 默认角色为 PUBLIC。 | 是      |
| connectVia       | 用于连接到数据存储的 [Integration Runtime](concepts-integration-runtime.md)。 可使用 Azure Integration Runtime 或自承载集成运行时（如果数据存储位于专用网络）。 如果未指定，则使用默认 Azure Integration Runtime。 | 否       |

**示例：**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
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

**Azure 密钥保管库中的密码：**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 

Snowflake 数据集支持以下属性。

| 属性  | 说明                                                  | 必需                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 数据集的 type 属性必须设置为 SnowflakeTable。 | 是                         |
| 架构 | 架构的名称。 请注意，架构名称在 ADF 中区分大小写。 |对于源为“否”，对于接收器为“是”  |
| 表 | 表/视图的名称。 请注意，表名称在 ADF 中区分大小写。 |对于源为“否”，对于接收器为“是”  |

**示例：**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Snowflake 源和接收器支持的属性列表。

### <a name="snowflake-as-the-source"></a>以 Snowflake 作为源

Snowflake 连接器利用 Snowflake 的 [COPY into [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 命令实现最佳性能。

如果 Snowflake 的 COPY 命令以本机方式支持接收器数据存储和格式，则可使用复制活动将数据从 Snowflake 直接复制到接收器。 有关详细信息，请参阅[从 Snowflake 进行的直接复制](#direct-copy-from-snowflake)。 否则，请使用内置的[从 Snowflake 进行的暂存复制](#staged-copy-from-snowflake)。

从 Snowflake 复制数据时，复制活动的“源”部分支持以下属性。

| 属性                     | 说明                                                  | 必需 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 复制活动源的类型属性必须设置为 SnowflakeSource。 | 是      |
| 查询          | 指定要从 Snowflake 读取数据的 SQL 查询。 如果架构、表和列的名称包含小写字母，请在查询中引用对象标识符，例如 `select * from "schema"."myTable"`。<br>不支持执行存储过程。 | 否       |
| exportSettings | 用于从 Snowflake 检索数据的高级设置。 可以配置 COPY into 命令支持的此类设置。在调用相关语句时，数据工厂会传递此类设置。 | 否       |
| 在 `exportSettings` 下： |  |  |
| type | 导出命令的类型，设置为 **SnowflakeExportCopyCommand**。 | 是 |
| additionalCopyOptions | 其他复制选项，作为键值对的字典提供。 示例:MAX_FILE_SIZE、OVERWRITE。 有关详细信息，请参阅 [Snowflake 复制选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 作为键值对的字典提供给 COPY 命令的其他文件格式选项。 示例:DATE_FORMAT、TIME_FORMAT、TIMESTAMP_FORMAT。 有关详细信息，请参阅 [Snowflake 格式类型选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-from-snowflake"></a>从 Snowflake 进行的直接复制

如果接收器数据存储和格式符合此部分所述条件，则可使用复制活动将数据从 Snowflake 直接复制到接收器。 数据工厂将检查设置，如果不符合以下条件，复制活动运行将会失败：

- “接收器链接服务”是使用“共享访问签名”身份验证的 [Azure Blob 存储](connector-azure-blob-storage.md)  。

- 接收器数据格式为“Parquet”、“带分隔符的文本”或“JSON”，其配置如下   ：

    - 对于“Parquet”格式，压缩编解码器为“无”、“Snappy”或或“Lzo”。
    - 对于“带分隔符的文本”格式：
        - `rowDelimiter` 为 **\r\n** 或任何单个字符。
        - `compression` 可为“无压缩”、 **gzip**、**bzip2** 或 **deflate**。
        - `encodingName` 保留为默认值或设置为 **utf-8**。
        - `quoteChar` 为双引号、单引号或空字符串（无引号字符）  。
    - 对于“JSON”格式，直接复制只支持以下情况：源 Snowflake 表或查询结果仅有一列且该列的数据类型是“VARIANT”、“OBJECT”或“ARRAY”   。
        - `compression` 可为“无压缩”、 **gzip**、**bzip2** 或 **deflate**。
        - `encodingName` 保留为默认值或设置为 **utf-8**。
        - `filePattern` 在复制活动接收器中保留为默认值或设置为“setOfObjects”。

- 在复制活动源中，`additionalColumns` 未指定。
- 列映射未指定。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>从 Snowflake 进行暂存复制

如果接收器数据存储或格式与上一部分所述的 Snowflake COPY 命令并非以本机方式兼容，请通过临时的 Azure Blob 存储实例启用内置暂存复制。 暂存复制功能也能提供更高的吞吐量。 数据工厂将数据从 Snowflake 导出到临时存储，然后将数据复制到接收器，最后从临时存储中清除临时数据。 若要详细了解如何通过暂存方式复制数据，请参阅[暂存复制](copy-activity-performance-features.md#staged-copy)。

若要使用此功能，请创建一个引用 Azure 存储帐户作为临时暂存位置的 [Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)。 然后，在复制活动中指定 `enableStaging` 和 `stagingSettings` 属性。

> [!NOTE]
> 暂存 Azure Blob 存储链接服务必须使用 Snowflake 的 COPY 命令所需的共享访问签名身份验证。 

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>以 Snowflake 作为接收器

Snowflake 连接器利用 Snowflake 的 [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 命令实现最佳性能。 它支持将数据写入 Azure 上的 Snowflake。

如果 Snowflake 的 COPY 命令以本机方式支持源数据存储和格式，则可使用复制活动将数据从源直接复制到 Snowflake。 有关详细信息，请参阅[直接复制到 Snowflake](#direct-copy-to-snowflake)。 否则，请使用内置的[暂存复制到 Snowflake](#staged-copy-to-snowflake)。

若要将数据复制到 Snowflake，复制活动的“接收器”部分需要支持以下属性。

| 属性          | 说明                                                  | 必需                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 复制活动接收器的类型属性设置为 SnowflakeSink。 | 是                                           |
| preCopyScript     | 指定在每次运行中将数据写入到 Snowflake 之前要由复制活动运行的 SQL 查询。 使用此属性清理预加载的数据。 | 否                                            |
| importSettings | 用于将数据写入 Snowflake 的高级设置。 可以配置 COPY into 命令支持的此类设置。在调用相关语句时，数据工厂会传递此类设置。 | 否 |
| 在 `importSettings` 下： |                                                              |  |
| type | 导入命令的类型，设置为 **SnowflakeImportCopyCommand**。 | 是 |
| additionalCopyOptions | 其他复制选项，作为键值对的字典提供。 示例:ON_ERROR、FORCE、LOAD_UNCERTAIN_FILES。 有关详细信息，请参阅 [Snowflake 复制选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供给 COPY 命令的其他文件格式选项，作为键值对的字典提供。 示例:DATE_FORMAT、TIME_FORMAT、TIMESTAMP_FORMAT。 有关详细信息，请参阅 [Snowflake 格式类型选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-to-snowflake"></a>直接复制到 Snowflake

如果源数据存储和格式符合此部分所述条件，则可使用复制活动将数据从源直接复制到 Snowflake。 Azure 数据工厂将检查设置，如果不符合以下条件，复制活动运行将会失败：

- “源链接服务”是使用“共享访问签名”身份验证的 [Azure Blob 存储](connector-azure-blob-storage.md)  。

- “源数据格式”为“Parquet”、“带分隔符的文本”或“JSON”，其配置如下   ：

    - 对于“Parquet”格式，压缩编解码器为“无”或“Snappy”。

    - 对于“带分隔符的文本”格式：
        - `rowDelimiter` 为 **\r\n** 或任何单个字符。 如果行分隔符不是“\r\n”，则需将 `firstRowAsHeader` 设置为 **false**，且不指定 `skipLineCount`。
        - `compression` 可为“无压缩”、 **gzip**、**bzip2** 或 **deflate**。
        - `encodingName` 保留为默认值或设置为“UTF-8”、“UTF-16”、“UTF-16BE”、“UTF-32”、“UTF-32BE”、“BIG5”、“EUC-JP”、“EUC-KR”、“GB18030”、“ISO-2022-JP”、“ISO-2022-KR”、“ISO-8859-1”、“ISO-8859-2”、“ISO-8859-5”、“ISO-8859-6”、“ISO-8859-7”、“ISO-8859-8”、“ISO-8859-9”、“WINDOWS-1250”、“WINDOWS-1251”、“WINDOWS-1252”、“WINDOWS-1253”、“WINDOWS-1254”、“WINDOWS-1255”。
        - `quoteChar` 为双引号、单引号或空字符串（无引号字符）  。
    - 对于“JSON”格式，直接复制只支持以下情况：接收器 Snowflake 表仅有一列且该列的数据类型是“VARIANT”、“OBJECT”或“ARRAY”   。
        - `compression` 可为“无压缩”、 **gzip**、**bzip2** 或 **deflate**。
        - `encodingName` 保留为默认值或设置为 **utf-8**。
        - 列映射未指定。

- 在复制活动源中： 

   -  未指定 `additionalColumns`。
   - 如果源为文件夹，则将 `recursive` 设置为 true。
   - 未指定 `prefix`、`modifiedDateTimeStart`、`modifiedDateTimeEnd` 和 `enablePartitionDiscovery`。

**示例：**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>暂存复制到 Snowflake

如果源数据存储或格式与雪花复制命令不在本机上兼容（如上一部分中所述），请使用临时 Azure Blob 存储实例启用内置暂存复制。 暂存复制功能也能提供更高的吞吐量。 数据工厂会自动转换数据，以满足 Snowflake 的数据格式要求。 然后，它会调用 COPY 命令将数据载入 Snowflake。 最后，它会从 Blob 存储中清理临时数据。 若要详细了解如何通过暂存方式复制数据，请参阅[暂存复制](copy-activity-performance-features.md#staged-copy)。

若要使用此功能，请创建一个引用 Azure 存储帐户作为临时暂存位置的 [Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)。 然后，在复制活动中指定 `enableStaging` 和 `stagingSettings` 属性。

> [!NOTE]
> 暂存 Azure Blob 存储链接服务需要使用 Snowflake 的 COPY 命令所需的共享访问签名身份验证。

**示例：**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>映射数据流属性

在映射数据流中转换数据时，可以在雪花中对表进行读取和写入。 有关详细信息，请参阅映射数据流中的[源转换](data-flow-source.md)和[接收器转换](data-flow-sink.md)。 您可以选择将雪花型数据集或 [内联数据集](data-flow-source.md#inline-datasets) 用作源和接收器类型。

### <a name="source-transformation"></a>源转换

下表列出了雪花型源支持的属性。 可以在 " **源选项** " 选项卡中编辑这些属性。连接器利用雪花 [内部数据传输](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 表 | 如果选择 "表" 作为输入，则在使用内联数据集时，数据流将从雪花数据集或源选项中指定的表中获取所有数据。 | 否 | String | * 仅限内联数据集的 () *<br>tableName<br>schemaName |
| 查询 | 如果选择 "查询" 作为输入，请输入查询以从雪花中提取数据。 此设置将重写您在数据集中选择的任何表。<br>如果架构、表和列的名称包含小写字母，请在查询中引用对象标识符，例如 `select * from "schema"."myTable"`。 | 否 | String | query |

#### <a name="snowflake-source-script-examples"></a>雪花源脚本示例

使用雪花数据集作为源类型时，关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

如果使用内联数据集，则关联的数据流脚本为：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>接收器转换

下表列出了雪花接收器支持的属性。 可以在 " **设置** " 选项卡中编辑这些属性。使用内联数据集时，你将看到与 " [数据集属性](#dataset-properties) " 一节中所述的属性相同的其他设置。 连接器利用雪花 [内部数据传输](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定允许对雪花目标执行哪些操作。<br>若要更新、upsert 或删除行，需要 [更改行转换](data-flow-alter-row.md) 以标记这些操作的行。 | 是 | `true` 或 `false` | 删除 <br/>可插入 <br/>更新 <br/>upsertable |
| 键列 | 对于更新、更新插入和删除操作，必须设置一个或多个键列，以确定要更改的行。 | 否 | Array | 密钥 |
| 表操作 | 确定在写入之前是否在目标表中重新创建或删除所有行。<br>- **None**：不会对表执行任何操作。<br>- **重新创建**：该表将被删除并重新创建。 如果以动态方式创建表，则是必需的。<br>- **截断**：目标表中的所有行都将被删除。 | 否 | `true` 或 `false` | 重新创建<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>雪花接收器脚本示例

将雪花数据集用作接收器类型时，关联的数据流脚本为：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

如果使用内联数据集，则关联的数据流脚本为：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>查找活动属性

有关属性的详细信息，请参阅[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关数据工厂中复制活动支持用作源和接收器的数据存储的列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
