---
title: 使用 Azure 数据工厂从 Amazon Redshift 移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂复制活动从 Amazon Redshift 移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ece34809734478ddb52c12d5dbd92291231f439
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045681"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon Redshift 移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-amazon-redshift-connector.md)
> * [版本 2（当前版本）](../connector-amazon-redshift.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅 [V2 中的 Amazon Redshift 连接器](../connector-amazon-redshift.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动从 Amazon Redshift 移动数据。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。 

数据工厂目前仅支持将 Amazon Redshift 中的数据移至[支持的接收器数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 不支持将其他数据存储中的数据移至 Amazon Redshift。

> [!TIP]
> 若要在从 Amazon Redshift 复制大量数据时获得最佳性能，请考虑通过 Amazon 简单存储服务 (Amazon S3) 使用内置的 Redshift **UNLOAD** 命令。 有关详细信息，请参阅[使用 UNLOAD 从Amazon Redshift 复制数据](#use-unload-to-copy-data-from-amazon-redshift)。

## <a name="prerequisites"></a>先决条件
* 如果要将数据移到本地数据存储，请在本地计算机上安装[数据管理网关](data-factory-data-management-gateway.md)。 使用本地计算机 IP 地址授予网关对 Amazon Redshift 群集的访问权限。 有关说明，请参阅[授予对群集的访问权限](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)。
* 若要将数据移动到 Azure 数据存储，请参阅[计算 Microsoft Azure 数据中心使用的 IP 地址和 SQL 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

## <a name="getting-started"></a>入门
可以使用不同的工具和 API 创建包含复制活动的管道，以从 Amazon Redshift 源移动数据。

创建管道的最简单方法是使用 Azure 数据工厂复制向导。 有关使用复制数据向导创建管道的快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

也可以使用 Azure 门户、Visual Studio、Azure PowerShell 或其他工具创建管道。 也可以使用 Azure 资源管理器模板、.NET API 或 REST API 创建管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建链接服务可将输入和输出数据存储链接到数据工厂。
2. 创建数据集用于表示复制操作的输入和输出数据。 
3. 创建包含复制活动的管道，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用复制向导时，会自动创建这些数据工厂实体的 JSON 定义。 使用工具或 API（.NET API 除外）时，可使用 JSON 格式定义数据工厂实体。 [JSON 示例：将数据从 Amazon Redshift 复制到 Azure Blob 存储](#json-example-copy-data-from-amazon-redshift-to-azure-blob)中演示了用于从 Amazon Redshift 数据存储复制数据的数据工厂实体的 JSON 定义。

以下部分介绍了用于定义 Amazon Redshift 的数据工厂实体的的 JSON 属性。

## <a name="linked-service-properties"></a>链接服务属性

下表提供了特定于 Amazon Redshift 链接服务的 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |该属性必须设置为 **AmazonRedshift**。 |是 |
| **server** |Amazon Redshift 服务器的 IP 地址或主机名。 |是 |
| **port** |Amazon Redshift 服务器用于侦听客户端连接的 TCP 端口数。 |否（默认值为 5439） |
| **database** |Amazon Redshift 数据库的名称。 |是 |
| **username** |有权访问数据库的用户的名称。 |是 |
| **password** |用户帐户的密码。 |是 |

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各节和属性的列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 **结构**、**可用性**和**策略**部分对于所有数据集类型都是类似的。 数据集类型的示例包括 Azure SQL、Azure Blob 存储和 Azure 表存储。

每种数据集的 **typeProperties** 部分有所不同，该部分提供有关数据在存储区中的位置信息。 **RelationalTable** 类型数据集（包括 Amazon Redshift 数据集）的 **typeProperties** 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| **tableName** |Amazon Redshift 数据库中链接服务引用的表的名称。 |否（如果指定了 **RelationalSource** 类型复制活动的 **query** 属性） |

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 **name**、**description**、**inputs** 表、**outputs** 表和 **policy** 属性可用于所有类型的活动。 typeProperties 节中可用的属性因每个活动的类型而异。 对于复制活动，其属性因数据源和接收器的类型而异。

对于复制活动，当源的类型为 **AmazonRedshiftSource** 时，则可在 **typeProperties** 部分中使用以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| **query** | 使用自定义查询读取数据。 |否（如果指定了数据集的 **tableName** 属性） |
| **redshiftUnloadSettings** | 使用 Redshift **UNLOAD** 命令时包含属性组。 | 否 |
| **s3LinkedServiceName** | 要用作临时存储的 Amazon S3。 使用 **AwsAccessKey** 的 Azure 数据工厂的名称类型指定链接服务。 | 使用 **redshiftUnloadSettings** 属性时需要该服务 |
| **bucketName** | 指示存储临时数据所使用的 Amazon S3 存储桶。 如果未提供该属性，复制活动会自动生成存储桶。 | 使用 **redshiftUnloadSettings** 属性时需要该服务 |

或者，也可将类型 **RelationalSource**（包括 Amazon Redshift）与 **typeProperties** 节中的以下属性配合使用。 请注意，此源类型不支持 Redshift **UNLOAD** 命令。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| **query** |使用自定义查询读取数据。 | 否（如果指定了数据集的 **tableName** 属性） |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>使用 UNLOAD 从Amazon Redshift 复制数据

Amazon Redshift [**UNLOAD**](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) 命令将查询结果卸载到 Amazon S3 上的一个或多个文件。 Amazon 推荐使用此命令从 Redshift 复制大型数据集。

**示例：将数据从 Amazon Redshift 复制到 Azure SQL 数据仓库**

此示例将数据从 Amazon Redshift 复制到 Azure SQL 数据仓库。 该示例使用 Redshift **UNLOAD** 命令、临时复制数据和 Microsoft PolyBase。

对于该示例用例，复制活动首先将数据从 Amazon Redshift 卸载到 Amazon S3（如 **redshiftUnloadSettings** 选项中所配置）。 接下来，数据从 Amazon S3 复制到 Azure Blob 存储（如 **stagingSettings** 选项中所指定）。 最后，PolyBase 将数据加载到 SQL 数据仓库。 所有临时格式均由复制活动处理。

![将工作流从 Amazon Redshift 复制到 SQL 数据仓库](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON 示例：将数据从 Amazon Redshift 复制到 Azure Blob 存储
此示例演示如何将数据从 Amazon Redshift 数据库复制到 Azure Blob 存储。 可以使用复制活动将数据直接复制到任何[受支持的接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。  

此示例具有以下数据工厂实体：

* [AmazonRedshift](#linked-service-properties) 类型的链接服务
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [RelationalTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)
* 包含复制活动的[管道](data-factory-create-pipelines.md)，该活动使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) 属性

此示例每隔一小时将数据从 Amazon Redshift 中的查询结果复制到 Azure Blob。 实体定义后面的部分描述了本示例中使用的 JSON 属性。

**Amazon Redshift 链接服务**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Blob 存储链接服务**

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
**Amazon Redshift 输入数据集**

将 **external** 属性设置为“true”以告知数据工厂服务：数据集在数据工厂外部。 此属性设置指示该数据集不是由数据工厂中的活动生成的。 对于不是由管道中的活动生成的输入数据集，将其属性设置为 true。

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
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

将 **frequency** 属性设置为“Hour”并将 **interval** 属性设置为 1，以便每隔一小时将数据写入新 Blob。 动态计算 Blob 的 **folderPath** 属性。 属性值基于正被处理的切片的开始时间。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**管道中使用 Azure Redshift 源（RelationalSource 类型）和 Azure Blob 接收器的复制活动**

管道包含配置为使用输入和输出数据集的复制活动。 该管道计划每小时运行一次。 在管道的 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询从过去一小时选择要复制的数据。

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动执行从源类型到接收器类型的自动类型转换。 使用以下两步方法转换该类型：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

复制活动将数据从 Amazon Redshift 类型转换为 .NET 类型时使用以下映射：

| Amazon Redshift 类型 | .NET 类型 |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |小数 |
| REAL |Single |
| 双精度 |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
若要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-reads-from-relational-sources"></a>从关系源进行可重复读取
从关系型数据存储复制数据时，请注意可重复性，以免出现意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试**策略**，以便在出现故障时重新运行切片。 无论要重新运行切片几次，都需要确保读取相同的数据。 此外，还需确保无论以哪种方式重新运行切片，都会读取相同的数据。 有关详细信息，请参阅[从关系型数据源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="performance-and-tuning"></a>性能和优化
在[复制活动性能和优化指南](data-factory-copy-activity-performance.md)中了解影响复制活动性能的关键因素以及各种性能优化方法。 

## <a name="next-steps"></a>后续步骤
有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
