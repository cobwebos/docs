---
title: 使用数据工厂从 Amazon 简单存储服务移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从 Amazon 简单存储服务 (S3) 移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d895dcdf9eefac01790aab6dc3f36a3feb0a8b12
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051144"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon 简单存储服务移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-amazon-simple-storage-service-connector.md)
> * [版本 2（当前版本）](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅 [V2 中的 Amazon S3 连接器](../connector-amazon-simple-storage-service.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动从 Amazon Simple Storage Service (S3) 移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，该文章总体概述了如何使用复制活动移动数据。

可将数据从 Amazon S3 复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 Amazon S3 移至其他数据存储，但不支持将数据从其他数据存储移至 Amazon S3。

## <a name="required-permissions"></a>所需的权限
若要从 Amazon S3 复制数据，请确保已具有以下权限：

* 对 Amazon S3 对象操作的 `s3:GetObject` 和 `s3:GetObjectVersion`。
* 对 Amazon S3 存储桶操作的 `s3:ListBucket`。 如果使用数据工厂复制向导，则还需要 `s3:ListAllMyBuckets`。

可以从[在策略中指定权限](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)中找到 Amazon S3 权限的完整列表（包含详细信息）。

## <a name="getting-started"></a>入门
可以使用不同的工具或 API 创建包含复制活动的管道，以便从 Amazon S3 源移动数据。

创建管道的最简单方法是使用复制向导。 有关快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

也可以使用以下工具创建管道：Azure 门户、Visual Studio、Azure PowerShell、Azure 资源管理器模板、.NET API 和 REST API。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建链接服务可将输入和输出数据存储链接到数据工厂。
2. 创建数据集以表示复制操作的输入和输出数据。
3. 创建包含复制活动的管道，该活动将一个数据集作为输入，将一个数据集作为输出。

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具或 API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。 有关用于从 Amazon S3 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 Amazon S3 复制到 Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) 部分。

> [!NOTE]
> 有关复制活动支持的文件和压缩格式的详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)。

对于特定于 Amazon S3 的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性
链接服务可将数据存储链接到数据工厂。 创建 **AwsAccessKey** 类型的链接服务，以便将 Amazon S3 数据存储链接到数据工厂。 下表提供 Amazon S3 (AwsAccessKey) 链接服务专属 JSON 元素的说明。

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| accessKeyID |机密访问键 ID。 |字符串 |是 |
| secretAccessKey |机密访问键本身。 |加密的机密字符串 |是 |

>[!NOTE]
>此连接器需要 IAM 帐户的访问密钥才能从 Amazon S3 复制数据。 不支持[临时安全凭据](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)。
>

下面是一个示例：

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性
要指定数据集来表示 Azure Blob 存储中的输入数据，可以将数据集的类型属性设置为：**AmazonS3**。 将数据集的 **linkedServiceName** 属性设置为 Amazon S3 链接服务的名称。 有关可用于定义数据集的各个部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)。 

所有数据集类型（例 SQL 数据库、Azure Blob 和 Azure 表）的结构、可用性和策略等部分类似。 每种数据集的 **typeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **AmazonS3** 类型数据集（包括 Amazon S3 数据集）的 **typeProperties** 节具有以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| bucketName |S3 存储桶的名称。 |String |是 |
| key |S3 对象键。 |String |否 |
| 前缀 |S3 对象键的前缀。 已选中其键以该前缀开头的对象。 仅当键为空时应用。 |String |否 |
| 版本 |启用 S3 版本控制时 S3 对象的版本。 |String |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[JSON 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 | |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 | |


> [!NOTE]
> **bucketName + key** 指定S3 对象的位置，其中存储桶是 S3 对象的根容器，key 是 S3 对象的完整路径。

### <a name="sample-dataset-with-prefix"></a>带前缀的示例数据集

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>示例数据集（包含版本）

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>S3 的动态路径
前面的示例对 Amazon S3 数据集中的 **key** 和 **bucketName** 属性使用固定的值。

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

可以通过使用 SliceStart 等系统变量，让数据工厂在运行时动态计算这些属性。

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

也可对 Amazon S3 数据集的 **prefix** 属性执行该操作。 有关支持的函数和变量列表，请参阅[数据工厂的函数和系统变量](data-factory-functions-variables.md)。

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各个部分和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。 可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，属性因源和接收器类型而异。 复制活动中源的类型为 **FileSystemSource**（包括 Amazon S3）时，可以在 **typeProperties** 节中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指定是否以递归方式列出目录下的 S3 对象。 |true/false |否 |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON 示例：将数据从 Amazon S3 复制到 Azure Blob 存储
此示例演示如何将数据从 Amazon S3 复制到 Azure Blob 存储。 但是，可以使用数据工厂中的复制活动，将数据直接复制到[支持的任何接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。

此示例提供了以下数据工厂实体的 JSON 定义。 可以通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 使用这些定义创建管道，以便将数据从 Amazon S3 复制到 Blob 存储。   

* [AwsAccessKey](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [AmazonS3](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例中每隔一小时会将数据从 Amazon S3 复制到 Azure blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

### <a name="amazon-s3-linked-service"></a>Amazon S3 链接服务

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 存储链接服务

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="amazon-s3-input-dataset"></a>Amazon S3 输入数据集

设置 **"external": true** 会告知数据工厂服务：数据集在数据工厂外部。 对于不是由管道中的活动生成的输入数据集，将此属性设置为 true。

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Azure Blob 输出数据集

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>管道中使用 Amazon S3 源和 Blob 接收器的复制活动

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **FileSystemSource**，将 **sink** 类型设置为 **BlobSink**。

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* 若要了解影响数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

* 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
