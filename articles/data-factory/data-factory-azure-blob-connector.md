---
title: "将数据复制到 Azure Blob 存储/从 Azure Blob 存储复制数据 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中复制 blob 数据。 使用我们的示例：如何将数据复制到 Azure Blob 存储和 Azure SQL 数据库，以及如何从中复制数据。"
keywords: "blob 数据, Azure Blob 复制"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 3ec7a230a4051d23e741360f3f714cccadf85902
ms.openlocfilehash: 511021e43e7d5b3247744592cbeab67769758375
ms.lasthandoff: 02/24/2017


---
# <a name="move-data-to-and-from-azure-blob-using-azure-data-factory"></a>使用 Azure 数据工厂将数据移入和移出 Azure Blob
本文介绍如何在 Azure 数据工厂中使用复制活动，以通过从其他数据存储获取 blob 数据将数据移入和移出 Azure Blob。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

## <a name="supported-sources-and-sinks"></a>受支持的源和接收器
有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 可将数据从任一支持的源数据存储移动到 Azure Blob 存储，或从 Azure Blob 存储移动到任一支持的接收器数据存储。

复制活动支持从常规用途的 Azure 存储帐户和冷/热 Blob 存储中复制数据，以及将数据复制到常规用途的 Azure 存储帐户和冷/热 Blob 存储。 活动支持从块 blob、追加 blob 或页 blob 进行读取，但仅支持对块 blob 写入。 请注意，Azure 高级存储不支持在其页 blob 下提供为接收器。

## <a name="create-pipeline"></a>创建管道
可以使用不同的工具/API 创建包含复制活动的管道，此管道将数据移入/移出 Azure Blob 存储。  

* 复制向导
* Azure 门户
* Visual Studio
* Azure PowerShell
* .NET API
* REST API

有关使用复制活动以不同方式创建管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

## <a name="copy-data-wizard"></a>复制数据向导
若要创建将数据复制到 Azure Blob 存储以及从中存储复制数据的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 这些示例演示了如何将数据复制到 Azure Blob 存储和 Azure SQL 数据库，以及如何从中复制数据。 但是，可以从任何源将数据**直接**复制到任何受支持的接收器。 有关详细信息，请参阅[使用复制活动移动数据](data-factory-data-movement-activities.md)中的“支持的数据存储和格式”部分。

## <a name="sample-copy-data-from-azure-blob-to-azure-sql"></a>示例：将数据从 Azure Blob 复制到 Azure SQL
以下示例显示：

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 类型的链接服务。
2. [AzureStorage](#azure-storage-linked-service-properties) 类型的链接服务。
3. [AzureBlob](#azure-blob-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的一个[管道](data-factory-create-pipelines.md)，该复制活动使用 [BlobSource](#azure-blob-copy-activity-type-properties) 和 [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)。

此示例按小时将时序数据从 Azure blob 复制到 Azure SQL 表。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure SQL 链接服务：**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure 存储链接服务：**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure 数据工厂支持两种类型的 Azure 存储链接服务：**AzureStorage** 和 **AzureStorageSas**。 对于前者，请指定包括帐户密钥的连接字符串，对于后者，请指定共享访问签名 (SAS) URI。 有关详细信息，请参阅[链接服务](#linked-services)部分。  

**Azure Blob 输入数据集：**

从新 blob 获取数据，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，将对 blob 的文件夹路径和文件名进行动态计算。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 “external”: “true” 设置将告知数据工厂：表在数据工厂外部，且不由数据工厂中的活动生成。

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL 输出数据集：**

此示例将数据复制到 Azure SQL 数据库中名为“MyTable”的表。 在 Azure SQL 数据库中创建表，其列数与 Blob CSV 文件要包含的列数相同。 每小时向该表添加新行。

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**包含复制活动的管道：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **SqlSink**。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
## <a name="sample-copy-data-from-azure-sql-to-azure-blob"></a>示例：将数据从 Azure SQL 复制到 Azure Blob
以下示例显示：

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 类型的链接服务。
2. [AzureStorage](#azure-storage-linked-service-properties) 类型的链接服务。
3. [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的一个[管道](data-factory-create-pipelines.md)，该复制活动使用 [WebSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) 和 [BlobSink](#azure-blob-copy-activity-type-properties)。

此示例按每小时将时序数据从 Azure SQL 表复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure SQL 链接服务：**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure 存储链接服务：**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure 数据工厂支持两种类型的 Azure 存储链接服务：**AzureStorage** 和 **AzureStorageSas**。 对于前者，请指定包括帐户密钥的连接字符串，对于后者，请指定共享访问签名 (SAS) URI。 有关详细信息，请参阅[链接服务](#linked-services)部分。  

**Azure SQL 输入数据集：**

该示例假定已在 Azure SQL 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

“external”: “true” 设置将告知数据工厂服务：表在数据工厂外部，且不由数据工厂中的活动生成。

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**包含复制活动的管道：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **BlobSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```
## <a name="linked-services"></a>链接服务
在示例中，已使用 **AzureStorage** 类型的链接服务将 Azure 存储帐户链接到数据工厂。 下表提供 Azure 存储链接服务专属 JSON 元素的描述。

有两种类型的链接服务，可用于将 Azure Blob 存储链接到 Azure 数据工厂。 它们是：**AzureStorage** 链接服务和 **AzureStorageSas** 链接服务。 Azure 存储链接服务为数据工厂提供 Azure 存储的全局访问权限。 而 Azure 存储 SAS（共享访问签名）链接服务为数据工厂提供 Azure 存储的受限/有时限的访问。 这两种链接服务之间没有其他区别。 请选择适合你需求的链接服务。 以下各部分提供了有关这两种链接服务的详细信息。

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-blob-dataset-type-properties"></a>Azure Blob 数据集类型属性
在示例中，已使用了类型为 **AzureBlob** 的数据集来表示 Azure Blob 存储中的 blob 容器和文件夹。

有关可用于定义数据集的 JSON 部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **typeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置、格式等信息。 **AzureBlob** 类型的数据集的 typeProperties 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |到 Blob 存储中的容器和文件夹的路径。 示例：myblobcontainer\myblobfolder\ |是 |
| fileName |blob 的名称。 fileName 可选，并且区分大小写。<br/><br/>如果指定文件名，则活动（包括复制）将对特定 Blob 起作用。<br/><br/>如果未指定 fileName，则复制将包括输入数据集的 folderPath 中所有的 Blob。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称将采用以下格式：Data.<Guid>.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 是一个可选属性。 它可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 请参阅[使用 partitionedBy 属性](#using-partitionedBy-property)部分，了解详细信息和示例。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](#specifying-textformat)、[Json 格式](#specifying-jsonformat)、[Avro 格式](#specifying-avroformat)、[Orc 格式](#specifying-orcformat)和 [Parquet 格式](#specifying-parquetformat)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**；支持的级别为：**Optimal** 和 **Fastest**。 有关详细信息，请参阅[指定压缩](#specifying-compression)部分。 |否 |

### <a name="using-partitionedby-property"></a>使用 partitionedBy 属性
如前面部分中所提到的，可以通过 **partitionedBy** 部分、数据工厂宏和系统变量（SliceStart 和 SliceEnd，它们指示给定数据切片的开始和结束时间）指定时序数据的动态 folderPath 和文件名。

有关时序数据集、计划和切片的详细信息，请参阅文章[创建数据集](data-factory-create-datasets.md)和[计划和执行](data-factory-scheduling-and-execution.md)。

#### <a name="sample-1"></a>示例 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值，格式为指定的指定的格式 (YYYYMMDDHH)。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 例如：wikidatagateway/wikisampledataout/2014100103 or wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>示例 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

在本示例中，SliceStart 的年、月、日和时间已提取到 folderPath 和 fileName 属性使用的各个变量。

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-blob-copy-activity-type-properties"></a>Azure Blob 复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出数据集等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异

若要从 Azure Blob 存储移动数据，请在复制活动中将源类型设置为 **BlobSource**。 同样，若要将数据移动到 Azure Blob 存储，请在复制活动中将接收器类型设置为 **BlobSink**。 本部分提供 BlobSource 和 BlobSink 支持的属性列表。

**BlobSource** 支持 **typeProperties** 部分的以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True（默认值）、False |否 |

**BlobSink** 支持以下 **typeProperties** 属性部分：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |源为 BlobSource 或 FileSystem 时，请定义复制行为。 |**PreserveHierarchy：**保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><br/>**FlattenHierarchy：**源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><br/>**MergeFiles：（默认值）**将源文件夹中的所有文件合并到一个文件中。 如果指定文件/Blob 名称，则合并的文件名称将为指定的名称；否则，将会自动生成文件名。 |否 |

**BlobSource** 还支持向后兼容性的这两种属性。

* **treatEmptyAsNull**：指定是否将 Null 或空字符串视为 Null 值。
* **skipHeaderLineCount** - 指定需要跳过的行数。 仅适用于输入数据集使用 TextFormat 的情况。

同样，**BlobSink** 支持以下向后兼容性的属性。

* **blobWriterAddHeader**：指定在写入到输出数据集时，是否添加列定义的标头。

数据集目前支持实现相同功能的以下属性：**treatEmptyAsNull**、**skipLineCount** 和 **firstRowAsHeader**。

下表提供了如何使用新数据集属性替代这些 blob 源/接收器属性的指南。

| 复制活动属性 | 数据集属性 |
|:--- |:--- |
| BlobSource 上的 skipHeaderLineCount |skipLineCount 和 firstRowAsHeader。 首先跳过行，然后读取第一行作为标头。 |
| BlobSource 上的 treatEmptyAsNull |输入数据集上的 treatEmptyAsNull |
| BlobSink 上的 blobWriterAddHeader |输出数据集上的 firstRowAsHeader |

有关这些属性的详细信息，请参阅[指定 TextFormat](#specifying-textformat)。    

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例
本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 产生的行为 |
| --- | --- | --- |
| 是 |preserveHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用与源相同的结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| 是 |flattenHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| 是 |mergeFiles |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名 |
| false |preserveHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy |对于具有以下结构的源文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles |对于具有以下结构的源文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

