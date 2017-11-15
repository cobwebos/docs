---
title: "使用 Azure 数据工厂从 Amazon 简单存储服务复制数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据从 Amazon 简单存储服务 (S3) 复制到受支持的接收器数据存储。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 3c066aab8641377705047f6b0ebd0e18c2afbac8
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon 简单存储服务复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [版本 2 - 预览版](connector-amazon-simple-storage-service.md)

本文概述如何使用 Azure 数据工厂中的复制活动向/从 Azure Blob 存储复制数据。 本文基于说明复制活动总体概述的[复制活动概述](copy-activity-overview.md)一文构建。

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 的数据工厂。 如果使用正式版 (GA) 版本 1 的数据工厂服务，请参阅 [V1 中的 Amazon S3 连接器](v1/data-factory-amazon-simple-storage-service-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从任一支持的源数据存储复制到 Azure Data Lake Store，或从 Azure Data Lake Store 复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Amazon S3 连接器支持按原样复制文件，或者使用[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析文件。

## <a name="required-permissions"></a>所需的权限

若要从 Amazon S3 复制数据，请确保已具有以下权限：

- 对 Amazon S3 对象操作的 `s3:GetObject` 和 `s3:GetObjectVersion`。
- 对 Amazon S3 存储桶操作的 `s3:ListBucket`。 如果使用数据工厂复制向导，则还需要 `s3:ListAllMyBuckets`。

可以从[在策略中指定权限](http://docs.aws.amazon.com/amazons3/latest/dev/using-with-s3-actions.html)中找到 Amazon S3 权限的完整列表（包含详细信息）。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。 

对于特定于 Amazon S3 的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Amazon S3 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为“AmazonS3”。 | 是 |
| accessKeyID | 机密访问键 ID。 |是 |
| secretAccessKey | 机密访问键本身。 将此字段标记为 SecureString。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

>[!NOTE]
>此连接器需要 IAM 帐户的访问密钥才能从 Amazon S3 复制数据。 不支持[临时安全凭据](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)。
>

下面是一个示例：

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Amazon S3 数据集支持的属性列表。

要从 Amazon S3 复制数据，请将数据集的 type 属性设置为“AmazonS3Object”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**AmazonS3Object** |是 |
| bucketName | S3 存储桶的名称。 |是 |
| key | S3 对象键。 仅当未指定前缀时应用。 |否 |
| 前缀 | S3 对象键的前缀。 已选中其键以该前缀开头的对象。 仅当未指定键时应用。 |否 |
| 版本 | 启用 S3 版本控制时 S3 对象的版本。 |否 |
| 格式 | 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。<br/><br/>如果想要分析或生成具有特定格式的文件，则下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否（仅适用于二进制复制方案） |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：**最佳**和**最快**。 |否 |

> [!NOTE]
> **bucketName + key** 指定S3 对象的位置，其中存储桶是 S3 对象的根容器，key 是 S3 对象的完整路径。

**示例：使用前缀**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**示例：使用键和版本（可选）**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Azure Data Lake 源和接收器支持的属性列表。

### <a name="amazon-s3-as-source"></a>作为源的 Amazon S3

要从 Amazon S3 复制数据，请将复制活动中的源类型设置为“FileSystemSource”（这包含 Amazon S3）。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：FileSystemSource |是 |
| recursive | 指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。<br/>允许的值为：true（默认）、false | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。