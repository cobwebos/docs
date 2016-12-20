---
title: "使用数据工厂从 Amazon 简单存储服务移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 Amazon 简单存储服务 (S3) 移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: c2350ae447ccebf1a6b85a563e7fa1d7c12b16d7
ms.openlocfilehash: 05a9466ba2a2d4a495d2e9a4f3ca4c9d08ddcadb


---
# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon 简单存储服务移动数据
本文概述如何使用 Azure 数据工厂中的复制活动将数据从 Amazon 简单存储服务 (S3) 移至其他数据存储。 本文基于对数据移动做出一般概述的[数据移动活动](data-factory-data-movement-activities.md)一文和复制活动支持的源/接收器数据存储列表。  

数据工厂当前仅支持将数据从 Amazon S3 移至其他数据存储，但不支持将数据从其他数据存储移至 Amazon S3。

## <a name="required-permissions"></a>所需的权限
若要从 Amazon S3 复制数据，请确保已具有以下权限：

* Amazon S3 对象操作的 **s3:GetObject** 和 **s3:GetObjectVersion**
* Amazon S3 存储桶操作的 **s3:ListBucket** 和 **s3:ListAllMyBuckets**（仅用于复制向导）

可以从 [Specifying Permissions in a Policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)（在策略中指定权限）中找到 Amazon S3 权限的完整列表（包含详细信息）。

## <a name="copy-data-wizard"></a>复制数据向导
若要创建从 Amazon S3 复制数据的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它演示如何将数据从 Amazon S3 复制到 Azure Blob 存储。 但是，可以将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>示例：将数据从 Amazon S3 复制到 Azure Blob
此示例演示如何将数据从 Amazon S3 复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

* [AwsAccessKey](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
* [AmazonS3](#dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](#copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例中每隔一小时会将数据从 Amazon S3 复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Amazon S3 链接服务**

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

**Azure 存储链接服务**

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

**Amazon S3 输入数据集**

设置 **"external": true** 将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。 对于不是由管道中的活动生成的输入数据集，将此属性设置为 true。

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


**Azure Blob 输出数据集**

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


**包含复制活动的管道**

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


## <a name="linked-service-properties"></a>链接服务属性
下表提供 Amazon S3 (**AwsAccessKey**) 链接服务专属 JSON 元素的说明。

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| accessKeyID |机密访问键 ID。 |字符串 |是 |
| secretAccessKey |机密访问键本身。 |加密的机密字符串 |是 |

## <a name="dataset-type-properties"></a>数据集类型属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 所有数据集类型（Azure SQL、Azure blob、Azure 表等）的结构、可用性和策略部分类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **AmazonS3** 类型数据集（包括 Amazon S3 数据集）的 typeProperties 部分具有以下属性

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| bucketName |S3 存储桶的名称。 |String |是 |
| key |S3 对象键。 |String |否 |
| 前缀 |S3 对象键的前缀。 已选中其键以该前缀开头的对象。 仅当键为空时应用。 |String |否 |
| 版本 |启用 S3 版本控制时 S3 对象的版本。 |String |否 |
| 格式 |支持以下格式类型：**TextFormat**、**AvroFormat**、**JsonFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat) 和[指定 ParquetFormat](#specifying-parquetformat) 部分。 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 | |
| compression |指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate** 和 **BZip2**，支持的级别为：**Optimal** 和 **Fastest**。 目前不支持 **AvroFormat** 或 **OrcFormat** 数据的压缩设置。 有关详细信息，请参阅[压缩支持](#compression-support)部分。 |否 | |

> [!NOTE]
> bucketName + key 指定S3 对象的位置，其中存储桶是 S3 对象的根容器，key 是 S3 对象的完整路径。
>
>

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
### <a name="sample-data-set-with-version"></a>示例数据集（包含版本）

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
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
在示例中，对于 Amazon S3 数据集中的 key 和 bucketName 属性，使用固定的值。

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

可以通过使用 SliceStart 等系统变量，让数据工厂在运行时动态计算 key 和 bucketName。

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

也可对 Amazon S3 数据集的前缀属性执行该操作。 有关支持的函数和变量的列表，请参阅[数据工厂的函数和系统变量](data-factory-functions-variables.md)。

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-type-properties"></a>复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

复制活动中源的类型为 **FileSystemSource**（包括 Amazon S3）时，可以在 typeProperties 部分中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指定是否以递归方式列出目录下的 S3 对象。 |true/false |否 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，了解创建包含复制活动的管道的分步说明。



<!--HONumber=Nov16_HO3-->


