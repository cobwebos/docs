---
title: "向/从 Azure SQL 数据库复制数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂向/从 Azure SQL 数据库复制数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: a64d13fa7dc5f50c259b98774be80b603dce400a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# 使用 Azure 数据工厂向 Azure SQL 数据库以及从 Azure SQL 数据库复制数据
<a id="copy-data-to-and-from-azure-sql-database-using-azure-data-factory" class="xliff"></a>
本文介绍如何使用 Azure 数据工厂中的复制活动将数据移入/移出 Azure SQL 数据库。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。  

## 支持的方案
<a id="supported-scenarios" class="xliff"></a>
可以将数据**从 Azure SQL 数据库**复制到以下数据存储：

[!INCLUDE [data-factory-supported-sinks](../../includes/data-factory-supported-sinks.md)]

可以将数据从以下数据存储复制**到 Azure SQL 数据库**：

[!INCLUDE [data-factory-supported-sources](../../includes/data-factory-supported-sources.md)]

## 支持的身份验证类型
<a id="supported-authentication-type" class="xliff"></a>
Azure SQL 数据库连接器支持基本身份验证。

## 入门
<a id="getting-started" class="xliff"></a>
可以使用不同的工具/API 创建包含复制活动的管道，以将数据移入/移出 Azure SQL 数据库。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建**数据工厂**。 数据工厂可以包含一个或多个管道。 
2. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。 例如，如果要将数据从 Azure Blob 存储复制到 Azure SQL 数据库，可创建两个链接服务，将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂。 有关特定于 Azure SQL 数据库的链接服务属性，请参阅[链接服务属性](#linked-service-properties)部分。 
3. 创建**数据集**以表示复制操作的输入和输出数据。 在上一个步骤所述的示例中，创建了一个数据集来指定 Blob 容器和包含输入数据的文件夹。 创建了另一个数据集来指定 Azure SQL 数据库中用于保存从 Blob 存储复制的数据的 SQL 表。 有关特定于 Azure Data Lake Store 的数据集属性，请参阅[数据集属性](#dataset-properties)部分。
4. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 在前面所述的示例中，在复制活动中使用 BlobSource 作为源，SqlSink 作为接收器。 同样，如果从 Azure SQL 数据库复制到 Azure Blob 存储，则在复制活动中使用 SqlSource 和 BlobSink。 有关特定于 Azure SQL 数据库的复制活动属性，请参阅[复制活动属性](#copy-activity-properties)部分。 有关如何将数据存储用作源或接收器的详细信息，请单击前面章节中的相应数据存储链接。

使用向导时，将自动为你创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从 Azure SQL 数据库复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples-for-copying-data-to-and-from-sql-database)部分。 

对于特定于 Azure SQL 数据库的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息： 

## 链接服务属性
<a id="linked-service-properties" class="xliff"></a>
Azure SQL 链接服务可将 Azure SQL 数据库链接到数据工厂。 下表描述了 Azure SQL 链接服务的专属 JSON 元素。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性必须设置为：**AzureSqlDatabase** |是 |
| connectionString |为 connectionString 属性指定连接到 Azure SQL 数据库实例所需的信息。 仅支持基本身份验证。 |是 |

> [!IMPORTANT]
> 配置 [Azure SQL 数据库防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)数据库服务器以[允许 Azure 服务访问该服务器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果将数据从 Azure 外部（包括配有数据工厂网关的本地数据源）复制到 Azure SQL 数据库，请针对将数据发送到 Azure SQL 数据库的计算机配置适当 IP 地址范围。

## 数据集属性
<a id="dataset-properties" class="xliff"></a>
若要指定数据集来表示 Azure SQL 数据库中的输入或输出数据，可以将数据集的类型属性设置为：**AzureSqlTable**。 将数据集的 **linkedServiceName** 属性设置为 Azure SQL 链接服务的名称。  

有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 结构、可用性和数据集 JSON 的策略等部分与所有数据集类型（Azure SQL、Azure blob、Azure 表等）类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **AzureSqlTable** 类型的数据集的 **typeProperties** 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 Azure SQL 数据库实例中的表名称或视图名称。 |是 |

## 复制活动属性
<a id="copy-activity-properties" class="xliff"></a>
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

> [!NOTE]
> 复制活动只使用一个输入，只生成一个输出。

但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

若要从 Azure SQL 数据库移动数据，请在复制活动中将源类型设置为 **SqlSource**。 同样，若要将数据移动到 Azure SQL 数据库，请在复制活动中将接收器类型设置为 **SqlSink**。 本部分提供 SqlSource 和 SqlSink 支持的属性列表。

### SqlSource
<a id="sqlsource" class="xliff"></a>
在复制活动中，如果源类型为 **SqlSource**，则可在 **typeProperties** 部分中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 示例：`select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程的名称。 |存储过程的名称。 存储过程中的最后一条 SQL 语句必须是 SELECT 语句。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

如果为 SqlSource 指定 **sqlReaderQuery**，则复制活动针对 Azure SQL 数据库源运行此查询可获取数据。 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在数据集 JSON 的结构部分定义的列来生成针对 Azure SQL 数据库运行的查询 (`select column1, column2 from mytable`)。 如果数据集定义不具备该结构，则从表中选择所有列。

> [!NOTE]
> 使用 **sqlReaderStoredProcedureName** 时，仍需指定数据集 JSON 中 **tableName** 属性的值。 但不会对此表执行任何验证。
>
>

### SqlSource 示例
<a id="sqlsource-example" class="xliff"></a>

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**存储过程定义：**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### SqlSink
<a id="sqlsink" class="xliff"></a>
**SqlSink** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时将数据插入 SQL 表。 |整数（行数） |否（默认值：10000） |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 有关详细信息，请参阅[可重复复制](#repeatable-copy)。 |查询语句。 |否 |
| sliceIdentifierColumnName |指定复制活动要填充自动生成切片标识符的列名，以便在重新运行时清除特定切片的数据。 有关详细信息，请参阅[可重复复制](#repeatable-copy)。 |数据类型为 binary(32) 的列的列名。 |否 |
| sqlWriterStoredProcedureName |在目标表中更新/插入数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |表类型名称。 |否 |

#### SqlSink 示例
<a id="sqlsink-example" class="xliff"></a>

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## 向/从 SQL 数据库复制数据的 JSON 示例
<a id="json-examples-for-copying-data-to-and-from-sql-database" class="xliff"></a>
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何在 Azure SQL 数据库和 Azure Blob 存储中复制和粘贴数据。 但是，可使用 Azure 数据工厂中的复制活动将数据**直接**从任何源复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

### 示例：将数据从 Azure SQL 数据库复制到 Azure Blob
<a id="example-copy-data-from-azure-sql-database-to-azure-blob" class="xliff"></a>
此示例定义以下数据工厂实体：

1. [AzureSqlDatabase](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [AzureSqlTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [SqlSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

示例每隔一小时将（每小时、每天等）时间序列数据从 Azure SQL 数据库中的表复制到 blob。 示例后续部分描述了这些示例中使用的 JSON 属性。  

**Azure SQL 数据库链接服务：**

```JSON
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
有关此链接服务支持的属性列表，请参阅 [Azure SQL 链接服务](#linked-service)部分。

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
有关此链接服务支持的属性列表，请参阅 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) 一文。


**Azure SQL 输入数据集：**

该示例假定已在 Azure SQL 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

设置“external”: ”true”将告知 Azure 数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
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

有关此数据集类型支持的属性列表，请参阅 [Azure SQL 数据集类型属性](#dataset)部分。  

**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
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
有关此数据集类型支持的属性列表，请参阅 [Azure Blob 数据集类型属性](data-factory-azure-blob-connector.md#dataset-properties)部分。  

**管道中使用 SQL 源和 Blob 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **BlobSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

```JSON
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
在本例中，为 SqlSource 指定了 **sqlReaderQuery**。 复制活动针对 Azure SQL 数据库源运行此查询以获取数据。 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在数据集 JSON 的结构部分定义的列来生成针对 Azure SQL 数据库运行的查询。 例如： `select column1, column2 from mytable`。 如果数据集定义不具备该结构，则从表中选择所有列。

有关 SqlSource 和 BlobSink 支持的属性列表，请参阅 [Sql Source](#sqlsource) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 部分。

### 示例：将数据从 Azure Blob 复制到 Azure SQL 数据库
<a id="example-copy-data-from-azure-blob-to-azure-sql-database" class="xliff"></a>
此示例定义以下数据工厂实体：  

1. [AzureSqlDatabase](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureSqlTable](#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [SqlSink](#copy-activity-properties)。

示例每隔一小时将（每小时、每天等）时间序列数据从 Azure Blob 复制到 Azure SQL 数据库中的表。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure SQL 链接服务：**

```JSON
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
有关此链接服务支持的属性列表，请参阅 [Azure SQL 链接服务](#linked-service)部分。

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
有关此链接服务支持的属性列表，请参阅 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) 一文。


**Azure Blob 输入数据集：**

从新 blob 获取数据，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，将对 blob 的文件夹路径和文件名进行动态计算。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 “external”: ”true”设置将告知数据工厂服务：表在数据工厂外部且不由数据工厂中的活动生成。

```JSON
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
有关此数据集类型支持的属性列表，请参阅 [Azure Blob 数据集类型属性](data-factory-azure-blob-connector.md#dataset-properties)部分。

**Azure SQL 数据库输出数据集：**

此示例将数据复制到 Azure SQL 中名为“MyTable”的表。 在 Azure SQL 中创建表，其列数与 Blob CSV 文件应包含的列数相同。 每小时向该表添加新行。

```JSON
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
有关此数据集类型支持的属性列表，请参阅 [Azure SQL 数据集类型属性](#dataset)部分。

**管道中使用 Blob 源和 SQL 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **SqlSink**。

```JSON
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
            "type": "BlobSource",
            "blobColumnSeparators": ","
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
有关 SqlSink 和 BlobSource 支持的属性列表，请参阅 [Sql Sink](#sqlsink) 和 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 部分。

## 目标数据库中的标识列
<a id="identity-columns-in-the-target-database" class="xliff"></a>
本部分举例说明了如何将数据从不含标识列的源表复制到含标识列的目标表。

**源表：**

```SQL
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**目标表：**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
请注意，目标表包含标识列。

**源数据集 JSON 定义**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**数据集 JSON 定义**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }    
}
```

请注意，源表和目标表具有不同架构（目标表具有一个额外标识列）。 在本方案中，需在不包含标识列的目标数据集定义中指定 **structure** 属性。

## 调用 SQL 接收器的存储过程
<a id="invoke-stored-procedure-from-sql-sink" class="xliff"></a>
有关在管道的复制活动中调用 SQL 接收器的存储过程的示例，请参阅[在复制活动中调用 SQL 接收器的存储过程](data-factory-invoke-stored-procedure-from-copy-activity.md)一文。 

## Azure SQL 数据库的类型映射
<a id="type-mapping-for-azure-sql-database" class="xliff"></a>
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动通过以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移入和移出 Azure SQL 数据库时，会使用以下从 SQL 类型到 .NET 类型的映射以及从 .NET 类型到 SQL 类型的映射。 此映射与用于 ADO.NET 的 SQL Server 数据类型映射相同。

| SQL Server 数据库引擎类型 | .NET Framework 类型 |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| 日期 |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM 特性 (varbinary(max)) |Byte[] |
| Float |Double |
| 图像 |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |小数 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
| sql_variant |对象 * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

## 将源映射到接收器列
<a id="map-source-to-sink-columns" class="xliff"></a>
若要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## 可重复复制
<a id="repeatable-copy" class="xliff"></a>
将数据复制到 SQL Server 数据库时，默认情况下复制活动将数据追加到接收器表后面。 若要改为执行 UPSERT，请参阅[可重复写入 SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) 一文。 

从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## 性能和优化
<a id="performance-and-tuning" class="xliff"></a>
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

