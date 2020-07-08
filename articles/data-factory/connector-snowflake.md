---
title: 将数据从和复制到雪花
description: 了解如何使用 Azure 数据工厂从和向雪花复制数据。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 347f37fb999656a1c4951f01a75a392887b5b882
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045665"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Snowflake 复制数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到雪花。 有关数据工厂的详细信息，请参阅[简介文章](introduction.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Snowflake 连接器：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)表
- [Lookup 活动](control-flow-lookup-activity.md)

对于复制活动，此雪花型连接器支持以下功能：

- 将使用雪花型副本的雪花中的数据复制[到 [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html)命令，以获得最佳性能。
- 将数据复制到雪花，该雪花利用雪花的[复制到 [表]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html)命令来获得最佳性能。 它支持 Azure 上的 Snowflake。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于雪花型连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

雪花链接服务支持以下属性。

| 属性         | 说明                                                  | 必须 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | type 属性必须设置为 **Snowflake**。              | 是      |
| connectionString | 配置[完整帐户名称](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name)（包括标识区域和云平台的其他段）、用户名、密码、数据库和仓库。 指定用于连接到 Snowflake 实例的 JDBC 连接字符串。 还可以将密码置于 Azure Key Vault 中。 有关更多详细信息，请参阅表下面的示例以及[Azure Key Vault 文章中的存储凭据](store-credentials-in-key-vault.md)。| 是      |
| connectVia       | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认的 Azure 集成运行时。 | 否       |

**示例：**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>(optional)"
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
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>(optional)",
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

雪花数据集支持以下属性。

| 属性  | 说明                                                  | 必须                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 数据集的 type 属性必须设置为 SnowflakeTable。 | 是                         |
| 架构 | 架构的名称。 |对于源为否，对于接收器为 yes  |
| 表 | 表/视图的名称。 |对于源为否，对于接收器为 yes  |

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

雪花型连接器利用雪花型的[复制到 [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html)命令来获得最佳性能。

如果 "雪花复制" 命令本身支持接收器数据存储和格式，则可以使用复制活动从雪花直接复制到接收器。 有关详细信息，请参阅[从 Snowflake 进行的直接复制](#direct-copy-from-snowflake)。 否则，请使用内置的[从 Snowflake 进行的暂存复制](#staged-copy-from-snowflake)。

若要从雪花复制数据，复制活动**源**部分支持以下属性。

| 属性                     | 说明                                                  | 必须 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 复制活动源的 type 属性必须设置为**SnowflakeSource**。 | 是      |
| 查询          | 指定要从 Snowflake 读取数据的 SQL 查询。<br>不支持执行存储过程。 | 否       |
| exportSettings | 用于从 Snowflake 检索数据的高级设置。 你可以将 "复制到" 命令支持的配置文件配置为在调用语句时数据工厂传递到的命令。 | 否       |
| 在 `exportSettings` 下： |  |  |
| type | 导出命令的类型，设置为 **SnowflakeExportCopyCommand**。 | 是 |
| additionalCopyOptions | 其他复制选项，作为键值对的字典提供。 示例:MAX_FILE_SIZE、OVERWRITE。 有关详细信息，请参阅[雪花复制选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供用于将命令作为键值对字典进行复制的其他文件格式选项。 示例:DATE_FORMAT、TIME_FORMAT、TIMESTAMP_FORMAT。 有关详细信息，请参阅[雪花格式类型选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-from-snowflake"></a>从 Snowflake 进行的直接复制

如果接收器数据存储和格式符合此部分中所述的条件，则可以使用复制活动从雪花直接复制到接收器。 如果未满足以下条件，数据工厂将检查设置并失败复制活动运行：

- **接收器链接服务**是使用**共享访问签名**身份验证的[**Azure Blob 存储**](connector-azure-blob-storage.md)。

- 连接器数据格式为“Parquet”或“带分隔符的文本”，其配置如下：

   - 对于“Parquet”格式，压缩编解码器为“无”、“Snappy”或或“Lzo”。
   - 对于“带分隔符的文本”格式：
     - `rowDelimiter` 为 **\r\n** 或任何单个字符。
     - `compression` 可为“无压缩”、 **gzip**、**bzip2** 或 **deflate**。
     - `encodingName` 保留为默认值或设置为 **utf-8**。
     - `quoteChar`为**双引号**、**单引号**或**空字符串**（无引号字符）。
- 在复制活动源中， `additionalColumns` 未指定。
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
                "sqlReaderQuery": "SELECT * FROM MyTable",
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

如果接收器数据存储或格式与雪花复制命令不在本机上兼容（如上一部分中所述），请使用临时 Azure Blob 存储实例启用内置暂存复制。 暂存复制功能也能提供更高的吞吐量。 数据工厂将数据从雪花型导出到过渡存储，然后将数据复制到 sink，最后从临时存储中清除临时数据。 有关使用过渡复制数据的详细信息，请参阅[暂存复制](copy-activity-performance-features.md#staged-copy)。

若要使用此功能，请创建一个[Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)，该服务将 azure 存储帐户引用为临时暂存。 然后指定 `enableStaging` `stagingSettings` 复制活动中的和属性。

> [!NOTE]
> 根据雪花复制命令的要求，暂存 Azure Blob 存储链接服务必须使用共享访问签名身份验证。 

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

雪花型连接器利用雪花的 "[复制到 [表]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) " 命令来获得最佳性能。 它支持将数据写入 Azure 上的 Snowflake。

如果雪花复制命令以本机方式支持源数据存储和格式，则可以使用复制活动直接从源复制到雪花。 有关详细信息，请参阅[直接复制到 Snowflake](#direct-copy-to-snowflake)。 否则，请使用内置的[暂存复制到 Snowflake](#staged-copy-to-snowflake)。

若要将数据复制到雪花，请在复制活动**接收器**部分中支持以下属性。

| Property          | 描述                                                  | 必需                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| 类型              | 复制活动接收器的 type 属性，设置为**SnowflakeSink**。 | 是                                           |
| preCopyScript     | 指定要在每次运行中将数据写入到雪花之前运行的复制活动的 SQL 查询。 使用此属性清理预加载的数据。 | 否                                            |
| importSettings | 用于将数据写入到雪花的高级设置。 你可以将 "复制到" 命令支持的配置文件配置为在调用语句时数据工厂传递到的命令。 | 否 |
| 在 `importSettings` 下： |                                                              |  |
| type | 导入命令的类型，设置为 **SnowflakeImportCopyCommand**。 | 是 |
| additionalCopyOptions | 其他复制选项，作为键值对的字典提供。 示例:ON_ERROR、FORCE、LOAD_UNCERTAIN_FILES。 有关详细信息，请参阅[雪花复制选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供给 COPY 命令的其他文件格式选项，作为键值对的字典提供。 示例:DATE_FORMAT、TIME_FORMAT、TIMESTAMP_FORMAT。 有关详细信息，请参阅[雪花格式类型选项](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-to-snowflake"></a>直接复制到 Snowflake

如果源数据存储和格式符合此部分中所述的条件，则可以使用复制活动直接从源复制到雪花。 如果未满足以下条件，Azure 数据工厂将检查设置并将复制活动运行失败：

- **源链接服务**是使用**共享访问签名**身份验证的[**Azure Blob 存储**](connector-azure-blob-storage.md)。

- **源数据格式**为**Parquet**或**带分隔符的文本**，具有以下配置：

   - 对于**Parquet**格式，压缩编解码器为**None**或**Snappy**。

   - 对于“带分隔符的文本”格式：
     - `rowDelimiter` 为 **\r\n** 或任何单个字符。 如果行分隔符不是“\r\n”，则需将 `firstRowAsHeader` 设置为 **false**，且不指定 `skipLineCount`。
     - `compression` 可为“无压缩”、 **gzip**、**bzip2** 或 **deflate**。
     - `encodingName` 保留为默认值或设置为“UTF-8”、“UTF-16”、“UTF-16BE”、“UTF-32”、“UTF-32BE”、“BIG5”、“EUC-JP”、“EUC-KR”、“GB18030”、“ISO-2022-JP”、“ISO-2022-KR”、“ISO-8859-1”、“ISO-8859-2”、“ISO-8859-5”、“ISO-8859-6”、“ISO-8859-7”、“ISO-8859-8”、“ISO-8859-9”、“WINDOWS-1250”、“WINDOWS-1251”、“WINDOWS-1252”、“WINDOWS-1253”、“WINDOWS-1254”、“WINDOWS-1255”。
     - `quoteChar`为**双引号**、**单引号**或**空字符串**（无引号字符）。

- 在复制活动源中： 

   -  未指定 `additionalColumns`。
   - 如果源是文件夹， `recursive` 则将设置为 true。
   - 未指定 `prefix`、`modifiedDateTimeStart`、`modifiedDateTimeEnd`。

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

如果接收器数据存储或格式与雪花复制命令不在本机上兼容（如上一部分中所述），请使用临时 Azure Blob 存储实例启用内置暂存复制。 暂存复制功能也能提供更高的吞吐量。 数据工厂会自动转换数据以满足雪花的数据格式要求。 然后，它调用 "复制" 命令将数据加载到雪花。 最后，它会从 Blob 存储中清理临时数据。 有关使用过渡复制数据的详细信息，请参阅[暂存复制](copy-activity-performance-features.md#staged-copy)。

若要使用此功能，请创建一个[Azure Blob 存储链接服务](connector-azure-blob-storage.md#linked-service-properties)，该服务将 azure 存储帐户引用为临时暂存。 然后指定 `enableStaging` `stagingSettings` 复制活动中的和属性。

> [!NOTE]
> 暂存 Azure Blob 存储链接服务需要根据雪花复制命令的要求使用共享访问签名身份验证。

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


## <a name="lookup-activity-properties"></a>查找活动属性

有关属性的详细信息，请参阅[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关数据工厂中的复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
