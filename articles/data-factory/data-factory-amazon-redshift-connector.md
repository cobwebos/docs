---
title: "使用数据工厂从 Amazon Redshift 移动数据 |Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 Amazon Redshift 移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: e3079b55036a514b31249e5930f40ab4346f437a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017


---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>使用 Azure 数据工厂从 Amazon Redshift 移动数据
本文介绍如何使用 Azure 数据工厂中的复制活动从 Amazon Redshift 移动数据。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。 

可以将数据从 Amazon Redshift 复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 数据工厂当前支持将数据从 Amazon Redshift 移至其他数据存储，但不支持将数据从其他数据存储移至 Amazon Redshift。

## <a name="prerequisites"></a>先决条件
* 如果要将数据移到本地数据存储，请在本地计算机上安装[数据管理网关](data-factory-data-management-gateway.md)。 然后，请向数据管理网关（使用计算机的 IP 地址）授予对 Amazon Redshift 群集的访问权限。 有关说明，请参阅[授予对群集的访问权限](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)。
* 如果正在将数据移动到 Azure 数据存储，请参阅 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)，了解 Azure 数据中心使用的计算 IP 地址和 SQL 范围。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从 Amazon Redshift 源移动数据。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，将自动为你创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从 Amazon Redshift 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 Amazon Redshift 复制到 Azure Blob](#json-example-copy-data-from-amazon-redshift-to-azure-blob) 部分。 

对于特定于 Amazon Redshift 的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息： 

## <a name="linked-service-properties"></a>链接服务属性
下表描述了专属于 Amazon Redshift 链接服务的 JSON 元素。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性必须设置为：**AmazonRedshift**。 |是 |
| server |Amazon Redshift 服务器的 IP 地址或主机名。 |是 |
| 端口 |Amazon Redshift 服务器用于侦听客户端连接的 TCP 端口数。 |否，默认值：5439 |
| database |Amazon Redshift 数据库名称。 |是 |
| username |有权访问数据库的用户的名称。 |是 |
| password |用户帐户密码。 |是 |

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 所有数据集类型（Azure SQL、Azure blob、Azure 表等）的结构、可用性和策略等部分类似。

每个数据集类型的 **typeProperties** 节都不同。 它提供数据存储中数据位置的相关信息。 **RelationalTable** 类型数据集（包括 Amazon Redshift 数据集）的 typeProperties 部分具有以下属性

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |Amazon Redshift 数据库中链接服务引用的表的名称。 |否（如果指定了 **RelationalSource** 的**query**） |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

复制活动的源是类型 **RelationalSource**（包括 Amazon Redshift）时，以下属性可用于 typeProperties 部分：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如，select * from MyTable。 |否（如果指定了**数据集**的 **tableName**） |

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>JSON 示例：将数据从 Amazon Redshift 复制到 Azure Blob
此示例演示如何将数据从 Amazon Redshift 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

* [AmazonRedshift](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [RelationalTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties)。

此示例每隔一小时将数据从 Amazon Redshift 中的查询结果复制到 blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Amazon Redshift 链接服务：**

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

**Azure 存储链接服务：**

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
**Amazon Redshift 输入数据集：**

设置 `"external": true` 告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。 对于不是由管道中的活动生成的输入数据集，将此属性设置为 true。

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

**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

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

**管道中使用 Azure Redshift 源 (RelationalSource) 和 Blob 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询选择复制过去一小时的数据。

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
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
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
如[数据移动活动](data-factory-data-movement-activities.md)一文所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移至 Amazon Redshift 时，使用从 Amazon Redshift 类型到 .NET 类型的以下映射。

| Amazon Redshift 类型 | 基于.NET 的类型 |
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

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，了解创建包含复制活动的管道的分步说明。

