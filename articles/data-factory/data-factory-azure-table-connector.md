---
title: "将数据移入/移出 Azure 表 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据移入和移出 Azure 表存储。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d688b5c6f918542b73d95c795f5dbb82070b17c8
ms.lasthandoff: 03/27/2017


---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>使用 Azure 数据工厂将数据移入和移出 Azure 表
本文介绍如何使用 Azure 数据工厂中的复制活动将数据移入/移出 Azure 表存储。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。 

可将数据从任一支持的源数据存储复制到 Azure 表存储，或从 Azure 表存储复制到任一支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以将数据移入/移出 Azure 表存储。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，将自动为你创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从 Azure 表存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples)部分。 

对于特定于 Azure 表存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息： 

## <a name="linked-service-properties"></a>链接服务属性
有两种类型的链接服务，可用于将 Azure Blob 存储链接到 Azure 数据工厂。 它们是：**AzureStorage** 链接服务和 **AzureStorageSas** 链接服务。 Azure 存储链接服务为数据工厂提供 Azure 存储的全局访问权限。 而 Azure 存储 SAS（共享访问签名）链接服务为数据工厂提供 Azure 存储的受限/有时限的访问。 这两种链接服务之间没有其他区别。 请选择适合你需求的链接服务。 以下各部分提供了有关这两种链接服务的详细信息。

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 结构、可用性和数据集 JSON 的策略等部分与所有数据集类型（Azure SQL、Azure blob、Azure 表等）类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **AzureTable** 数据集类型的 **typeProperties** 部分具有以下属性。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务指代的 Azure 表数据库实例中表的名称。 |是的。 指定了 tableName 但未指定 azureTableSourceQuery 时，表中的所有记录都将复制到目标中。 如果还指定了 azureTableSourceQuery，则会将满足查询的表中的记录复制到目标中。 |

### <a name="schema-by-data-factory"></a>数据工厂的构架
对于无架构的数据存储（如 Azure 表），数据工厂服务将使用下列方式之一推断架构：

1. 如果使用数据集定义中的**结构**属性指定数据的结构，数据工厂服务会将此结构作为架构。 在这种情况下，如果行不包含列的值，则会为其提供 null 值。
2. 如果不使用数据集定义中的**结构**属性指定数据结构，数据工厂将通过使用数据中的第一行来推断架构。 在这种情况下，如果第一行不包含完整架构，则复制操作的结果中会丢失部分列。

因此，对于无架构的数据源，最佳做法是使用**结构**属性指定数据的结构。

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出数据集等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

**AzureTableSource** 支持 typeProperties 部分的以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| azureTableSourceQuery |使用自定义查询读取数据。 |Azure 表查询字符串。 请参阅下一节中的示例。 |不会。 指定了 tableName 但未指定 azureTableSourceQuery 时，表中的所有记录都将复制到目标中。 如果还指定了 azureTableSourceQuery，则会将满足查询的表中的记录复制到目标中。 |
| azureTableSourceIgnoreTableNotFound |指示是否存在忽略表异常。 |TRUE<br/>FALSE |否 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 示例
如果 Azure 表列为 string 类型：

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

如果 Azure 表列为 datetime 类型：

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** 支持 typeProperties 部分的以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |接收器可以使用的默认分区键值。 |字符串值。 |否 |
| azureTablePartitionKeyName |指定列名称，使用列值作为分区键。 如果未指定，则使用 AzureTableDefaultPartitionKeyValue 作为分区键。 |列名称。 |否 |
| azureTableRowKeyName |指定列名称，使用列值作为行键。 如果未指定，对每一行使用 GUID。 |列名称。 |否 |
| azureTableInsertType |将数据插入 Azure 表的模式。<br/><br/>此属性控制输出表中具有匹配的分区键和行键的现有行是否替换或合并其值。 <br/><br/>若要了解这些设置（合并和替换）的工作原理，请参阅 [Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx)（插入或合并实体）和 [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx)（插入或替换实体）主题。 <br/><br> 此设置在行级别而不是表级别进行应用，并且两个选项都不会删除输入中不存在的输出表中的行。 |合并（默认值）<br/>replace |否 |
| writeBatchSize |writeBatchSize 或 writeBatchTimeout 命中时，将数据插入 Azure 表。 |整数（行数） |否（默认值：10000） |
| writeBatchTimeout |writeBatchSize 或 writeBatchTimeout 命中时，将数据插入 Azure 表 |timespan<br/><br/>示例：“00:20:00”（20 分钟） |否（默认为存储客户端默认超时值 90 秒） |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
使用转换器 JSON 属性将源列映射到目标列后，才能将目标列用作azureTablePartitionKeyName。

在以下示例中，源列 DivisionID 映射到目标列 DivisionID。  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID 被指定为分区键。

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON 示例
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据复制到 Azure 表存储和 Azure Blob 数据库，以及如何从 Azure 表存储和 Azure Blob 数据库复制数据。 但是，可以从任何源将数据**直接**复制到任何受支持的接收器。 有关详细信息，请参阅[使用复制活动移动数据](data-factory-data-movement-activities.md)中的“支持的数据存储和格式”部分。

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>示例：将数据从 Azure 表复制到 Azure Blob
以下示例显示：

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务（用于表和 blob）。
2. 类型 [AzureTable](#dataset-properties) 的输入[数据集](data-factory-create-datasets.md)。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
4. [管道](data-factory-create-pipelines.md)，具有使用 [AzureTableSource](#activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 的复制活动。

此示例按小时将属于 Azure 表中默认分区的数据复制到 blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure 存储链接服务：**

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
Azure 数据工厂支持两种类型的 Azure 存储链接服务：**AzureStorage** 和 **AzureStorageSas**。 对于前者，请指定包括帐户密钥的连接字符串，对于后者，请指定共享访问签名 (SAS) URI。 有关详细信息，请参阅[链接服务](#linked-service-properties)部分。  

**Azure 表输入数据集：**

该示例假定你在 Azure 表中创建了表“MyTable”。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**管道中使用 AzureTableSource 和 BlobSink 的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将**源**类型设置为 **AzureTableSource**，将**接收器**类型设置为 **BlobSink**。 使用 **AzureTableSourceQuery** 属性指定的 SQL 查询会每小时从默认分区中选择要复制的数据。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                      {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                      {
                            "name": "AzureBlobOutput"
                      }
                ],
                "typeProperties": {
                      "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>示例：将数据从 Azure Blob 复制到 Azure 表
以下示例显示：

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务（用于表和 blob）
2. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
3. [AzureTable](#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
4. [管道](data-factory-create-pipelines.md)，具有使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [AzureTableSink](#copy-activity-properties) 的复制活动。

此示例按小时将时序数据从 Azure blob 复制到 Azure 表。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure 存储（适用于 Azure 表和 Blob）链接服务：**

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

Azure 数据工厂支持两种类型的 Azure 存储链接服务：**AzureStorage** 和 **AzureStorageSas**。 对于前者，请指定包括帐户密钥的连接字符串，对于后者，请指定共享访问签名 (SAS) URI。 有关详细信息，请参阅[链接服务](#linked-service-properties)部分。

**Azure Blob 输入数据集：**

从新 blob 获取数据，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，将对 blob 的文件夹路径和文件名进行动态计算。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Azure 表输出数据集：**

此示例将数据复制到 Azure 表中名为“MyTable”的表。 创建 Azure 表，其列数与 Blob CSV 文件要包含的列数相同。 每隔一小时会向表添加新行。

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**管道中使用 BlobSource 和 AzureTableSink 的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **AzureTableSink**。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
### <a name="type-mapping-for-azure-table"></a>Azure 表的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换。

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移入和移出 Azure 表时，[Azure 表服务定义的以下映射](https://msdn.microsoft.com/library/azure/dd179338.aspx)将从 Azure 表 OData 类型转换为 .NET 类型，反之亦然。

| Odata 数据类型 | .NET 类型 | 详细信息 |
| --- | --- | --- |
| Edm.Binary |byte[] |一个字节数组，最大 64 KB。 |
| Edm.Boolean |bool |布尔值。 |
| Edm.DateTime |DateTime |一个 64 位值，用协调世界时 (UTC) 表示。 支持的 DateTime 范围从 (UTC) 公元 (C.E.) 1601 年 1 月 1 日 午夜 12:00 开始。 到 9999 年 12 月 31 日结束。 |
| Edm.Double |double |64 位浮点值。 |
| Edm.Guid |Guid |128 位全局唯一标识符。 |
| Edm.Int32 |Int32 或 int |32 位整数。 |
| Edm.Int64 |Int64 或 long |64 位整数。 |
| Edm.String |String |UTF-16 编码值。 字符串值最大可以为 64 KB。 |

### <a name="type-conversion-sample"></a>类型转换示例
以下示例适用于使用类型转换将数据从 Azure Blob 复制到 Azure 表。

假设 Blob 数据集采用 CSV 格式且包含 3 列。 其中一列是采用自定义日期时间格式（星期日期的法语名称已缩写）的日期时间列。

将按如下所示定义 Blob 源数据集并指定列的类型定义。

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
         "structure":
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
对于上述从 Azure 表 OData 类型到 .NET 类型的类型映射，可以使用以下架构来定义 Azure 表中的表。

**Azure 表架构：**

| 列名称 | 类型 |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogindate |Edm.DateTime |

接下来，按如下所示定义 Azure 表数据集。 不需要指定包含类型信息的“结构”部分，因为类型信息已在基础数据存储中指定。

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

在此情况下，将数据从 Blob 移到 Azure 表时，数据工厂将自动执行类型转换，包括对使用自定义日期时间格式（使用 fr-fr 区域性）的 Datetime 字段的类型转换。

> [!NOTE]
> 若要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

