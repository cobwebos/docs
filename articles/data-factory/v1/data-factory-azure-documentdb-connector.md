---
title: "将数据移入/移出 Azure Cosmos DB | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据移入/移出 Azure Cosmos DB 集合"
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 89b860bb4174a06c17da1db2bce2eaa11832b0b2
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>使用 Azure 数据工厂将数据移入和移出 Azure Cosmos DB
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-azure-documentdb-connector.md)
> * [版本 2 - 预览版](../connector-azure-cosmos-db.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 Azure Cosmos DB 连接器](../connector-azure-cosmos-db.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动将数据移入/移出 Azure Cosmos DB (SQL API)。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。 

可将数据从任一支持的源数据存储复制到 Azure Cosmos DB，或从 Azure Cosmos DB 复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 

> [!IMPORTANT]
> Azure Cosmos DB 连接器仅支持 SQL API。

若要向/从 JSON 文件或另一 Azure Cosmos DB 集合原样复制数据，请参阅[导入/导出 JSON 文档](#importexport-json-documents)。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以将数据移入/移出 Azure Cosmos DB。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从 Cosmos DB 复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples)部分。 

对于特定于 Cosmos DB 的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息： 

## <a name="linked-service-properties"></a>链接服务属性
下表描述特定于 Azure Cosmos DB 链接服务的 JSON 元素。

| **属性** | **说明** | **必需** |
| --- | --- | --- |
| type |类型属性必须设置为：**DocumentDb** |是 |
| connectionString |指定连接 Azure Cosmos DB 数据库所需的信息。 |是 |

示例：

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 对于所有数据集类型（Azure SQL、Azure blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **DocumentDbCollection** 类型的数据集的 typeProperties 部分具有以下属性。

| **属性** | **说明** | **必需** |
| --- | --- | --- |
| collectionName |Cosmos DB 文档集合的名称。 |是 |

示例：

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>数据工厂的构架
对于无架构的数据存储（如 Azure Cosmos DB），数据工厂服务使用下列方式之一推断架构：  

1. 如果使用数据集定义中的**结构**属性指定数据的结构，数据工厂服务会将此结构作为架构。 在这种情况下，如果行不包含列的值，则为其提供 null 值。
2. 如果不使用数据集定义中的**结构**属性指定数据结构，数据工厂服务将通过使用数据中的第一行来推断架构。 在这种情况下，如果第一行不包含完整架构，则复制操作的结果中将丢失部分列。

因此，对于无架构的数据源，最佳做法是使用**结构**属性指定数据的结构。

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的部分和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

> [!NOTE]
> 复制活动只使用一个输入，只生成一个输出。

另一方面，在活动的 typeProperties 部分中可用的属性因每种活动类型而异；在复制活动中，这些属性则因源和接收器的类型而异。

在复制活动中，如果源的类型为 **DocumentDbCollectionSource**，则可以在 **typeProperties** 部分中使用以下属性：

| **属性** | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| query |指定要读取数据的查询。 |Azure Cosmos DB 支持的查询字符串。 <br/><br/>示例：`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，则执行的 SQL 语句为：`select <columns defined in structure> from mycollection` |
| nestingSeparator |指示嵌套文档的特殊字符 |任意字符。 <br/><br/>Azure Cosmos DB 是 JSON 文档的 NoSQL 存储，其中允许存在嵌套结构。 Azure 数据工厂允许用户通过 nestingSeparator 来表示层次结构，即 上述示例中的“.”。 通过该分隔符，复制活动会根据表定义中的“Name.First”、“Name.Middle”和“Name.Last”生成包含三个子元素（First、Middle 和 Last）的“Name”对象。 |否 |

**DocumentDbCollectionSink** 支持以下属性：

| **属性** | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| nestingSeparator |源列名称中的特殊字符，指示需要嵌套的文档。 <br/><br/>在上述示例中：输出表中的 `Name.First` 在 Cosmos DB 文档中生成以下 JSON 结构：<br/><br/>"Name": {<br/>    "First":"John"<br/>}, |用于分隔嵌套级别的字符。<br/><br/>默认值为 `.`（点）。 |用于分隔嵌套级别的字符。 <br/><br/>默认值为 `.`（点）。 |
| writeBatchSize |向 Azure Cosmos DB 服务发送创建文档的并行请求数。<br/><br/>向/从 Cosmos DB 复制数据时，可使用此属性对性能进行微调。 当增加 writeBatchSize 时，由于会向 Cosmos DB 发送更多的并行请求，因此可以获得更好的性能。 但是，需要避免可能会引发“请求速率大”的错误消息的限制。<br/><br/>限制由多种因素决定，包括文档大小、文档中的术语数、目标集合的索引策略等。对于复制操作，可以使用更好的集合（例如 S3）以实现最大的吞吐量（2,500 请求单位/秒）。 |Integer |否（默认值：5） |
| writeBatchTimeout |超时之前等待操作完成的时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |

## <a name="importexport-json-documents"></a>导入/导出 JSON 文档
使用此 Cosmos DB 连接器，可以轻松地

* 将各种源中的 JSON 文档导入 Cosmos DB，包括 Azure Blob、Azure Data Lake、本地文件系统或 Azure 数据工厂所支持的其他基于文件的存储。
* 将 JSON 文档从 Cosmos DB 集合导出到各种基于文件的存储。
* 在两个 Cosmos DB 集合之间按原样迁移数据。

若要实现此类“架构不可知”复制： 
* 使用复制向导时，选中“原样导出到 JSON 文件或 Cosmos DB 集合”选项。
* 使用 JSON 编辑时，请勿指定 Cosmos DB 数据集中的“structure”节，也不要指定复制活动中 Cosmos DB 源/接收器的“nestingSeparator”属性。 若要导出到 JSON 文件或从此文件中导入，请在文件存储数据集中指定格式类型为“JsonFormat”，配置“filePattern”并跳过剩余格式设置，详情请参阅 [JSON 格式](data-factory-supported-file-and-compression-formats.md#json-format)部分。

## <a name="json-examples"></a>JSON 示例
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示了如何向/从 Azure Cosmos DB 和 Azure Blob 存储复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据**直接**从任何源复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>示例：将数据从 Azure Cosmos DB 复制到 Azure Blob
以下示例显示：

1. [DocumentDb](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [DocumentDbCollection](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的一个[管道](data-factory-create-pipelines.md)，该复制活动使用 [DocumentDbCollectionSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例将 Azure Cosmos DB 中的数据复制到 Azure Blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**Azure Cosmos DB 链接服务：**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob 存储链接服务：**

```JSON
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
**Azure Document DB 输入数据集：**

此示例假定在 Azure Cosmos DB 数据库中有一个名为 **Person** 的集合。

“external”: ”true” 设置和指定 externalData 策略将告知 Azure 数据工厂服务：表在数据工厂外部，且不由数据工厂中的活动生成。

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob 输出数据集：**

每隔一小时通过反应以小时为粒度的特定日期/时间的 blob 的路径，将数据复制到新的 blob 中。

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Cosmos DB 数据库中 Person 集合中的示例 JSON 文档：

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB 支持使用类似于 SQL 的语法在分层的 JSON 文档上查询文档。

示例： 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

以下管道将数据从 Azure Cosmos DB 数据库中的 Person 集合复制到 Azure Blob。 已将输入和输出数据集指定为复制活动的一部分。  

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>示例：将数据从 Azure Blob 复制到 Azure Cosmos DB 
以下示例显示：

1. [DocumentDb](#azure-documentdb-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)类型的输入[数据集](data-factory-create-datasets.md)。
4. [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 类型的一个输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的一个[管道](data-factory-create-pipelines.md)，该复制活动使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties)。

此示例将数据从 Azure Blob 复制到 Azure Cosmos DB。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**Azure Blob 存储链接服务：**

```JSON
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
**Azure Cosmos DB 链接服务：**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob 输入数据集：**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB 输出数据集：**

此示例将数据复制到一个名为“Person”的集合。

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
以下管道将数据从 Azure Blob 复制到 Cosmos DB 中的 Person 集合。 已将输入和输出数据集指定为复制活动的一部分。

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          }
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
如果示例 blob 输入为

```
1,John,,Doe
```
则 Cosmos DB 中的输出 JSON 将为：

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB 是 JSON 文档的 NoSQL 存储，其中允许存在嵌套结构。 Azure 数据工厂允许用户通过 **nestingSeparator** 来表示层次结构，即“.” 来实现。 通过该分隔符，复制活动会根据表定义中的“Name.First”、“Name.Middle”和“Name.Last”生成包含三个子元素（First、Middle 和 Last）的“Name”对象。

## <a name="appendix"></a>附录
1. **问题：**复制活动是否支持现有记录的更新？

    **答案：**否。
2. **问题：**复制到 Azure Cosmos DB 的重试操作如何处理已复制的记录？

    **答案：**如果记录具有一个“ID”字段，并且复制操作尝试插入具有相同 ID 的记录，则复制操作将引发错误。  
3. **问题：**数据工厂是否支持[按范围分区或按基于哈希的数据分区](../../cosmos-db/sql-api-partition-data.md)？

    **答案：**否。
4. **问题：**是否可以为一个表指定多个 Azure Cosmos DB 集合？

    **答案：**否。 目前仅可以指定一个集合。

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法。
