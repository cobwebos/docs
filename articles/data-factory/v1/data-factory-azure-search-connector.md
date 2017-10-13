---
title: "使用数据工厂将数据推送到搜索索引 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据推送到 Azure 搜索索引。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3d3ffa343a91aaad632705f24bcb357b7b20386d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据推送到 Azure 搜索索引
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](data-factory-azure-search-connector.md)
> * [版本 2 - 预览版](../connector-azure-search.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 Azure 搜索连接器](../connector-azure-search.md)。

本文介绍如何使用“复制活动”将数据从支持的源数据存储推送到 Azure 搜索索引。 [支持的源和接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表的“源”列中列出了支持的源数据存储。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

## <a name="enabling-connectivity"></a>启用连接
若要允许数据工厂服务连接到本地数据存储，需在本地环境中安装数据管理网关。 可在托管源数据存储的同一计算机上安装网关，或者在不同计算机上安装以避免与数据存储争用资源。

数据管理网关以安全和托管的方式将本地数据源连接到云服务。 有关数据管理网关的详细信息，请参阅[在本地与云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从源数据存储将数据推送到 Azure 搜索索引。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于将数据复制到 Azure 搜索索引的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从本地 SQL Server 复制到 Azure 搜索索引](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index)部分。 

对于特定于 Azure 搜索索引的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性

下表提供 Azure 搜索链接服务特定的 JSON 元素说明。

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| type | type 属性必须设置为：**AzureSearch**。 | 是 |
| url | Azure 搜索服务的 URL。 | 是 |
| key | Azure 搜索服务的管理密钥。 | 是 |

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各节和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。 每个数据集类型的 **typeProperties** 节都不同。 **AzureSearchIndex** 数据集类型的 typeProperties 节具有以下属性：

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| type | type 属性必须设置为 **AzureSearchIndex**。| 是 |
| indexName | Azure 搜索索引的名称。 数据工厂不创建索引。 索引必须存在于 Azure 搜索中。 | 是 |


## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表等属性和各种策略可用于所有类型的活动。 但是，可用于 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

对于复制活动，如果接收器类型为 **AzureSearchIndexSink**，则可在 typeProperties 节中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | 指定索引中已存在文档时要合并还是替换该文档。 请参阅 [WriteBehavior 属性](#writebehavior-property)。| 合并（默认值）<br/>上传| 否 |
| WriteBatchSize | 缓冲区大小达到 writeBatchSize 时会数据上传到 Azure 搜索索引。 有关详细信息，请参阅 [WriteBatchSize 属性](#writebatchsize-property)。 | 1 到 1,000。 默认值为 1000。 | 否 |

### <a name="writebehavior-property"></a>WriteBehavior 属性
AzureSearchSink 在写入数据时执行 upsert 操作。 换言之，编写文档时，如果 Azure 搜索索引中已存在文档键，则 Azure 搜索会更新现有文档，而不引发冲突异常。

AzureSearchSink（通过使用 AzureSearch SDK）提供以下两种 upsert 行为：

- **合并**：合并新文档和现有文档中的所有列。 对于新文档中具有 null 值的列，会在现有文档列中保留该值。
- **上传**：新文档替换现有文档。 对于未在新文档中指定的列，无论现有文档中是否存在非 null 值，均将该值设置为 null。

默认行为是**合并**。

### <a name="writebatchsize-property"></a>WriteBatchSize 属性
Azure 搜索服务支持成批编写文档。 每批次可包含 1 到 1,000 个操作。 每个操作处理一个文档以执行上传/合并操作。

### <a name="data-type-support"></a>数据类型支持
下表指定是否支持某个 Azure 搜索数据类型。

| Azure 搜索数据类型 | 在 Azure 搜索接收器中受到支持 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| 布尔 | Y |
| DataTimeOffset | Y |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON 示例：将数据从本地 SQL Server 复制到 Azure 搜索索引

以下示例显示：

1.  [AzureSearch](#linked-service-properties) 类型的链接服务。
2.  [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties) 类型的链接服务。
3.  [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4.  [AzureSearchIndex](#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
4.  包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) 和 [AzureSearchIndexSink](#copy-activity-properties)。

本示例每小时将时间序列数据从本地 SQL Server 数据库复制到 Azure 搜索索引。 此示例中使用的 JSON 属性会在示例后的各部分进行说明。

第一步，在本地计算机上设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

**Azure 搜索链接服务：**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server 链接服务**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server 输入数据集**

该示例假定已在 SQL Server 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。 可在使用单个数据集的同一数据库中对多个表进行查询，但必须对数据集的 tableName typeProperty 使用单个表。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Azure 搜索输出数据集：**

此示例将数据复制到名为**产品**的 Azure 搜索索引中。 数据工厂不创建索引。 若要测试示例，请创建具有此名称的索引。 创建列数与输入数据集列数相同的 Azure 搜索索引。 每隔一小时会将新条目添加到 Azure 搜索索引。

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**管道中使用 SQL 源和 Azure 搜索索引接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **AzureSearchIndexSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

如果要将数据从云数据存储复制到 Azure 搜索中，则 `executionLocation` 属性是必需的。 以下 JSON 片段显示复制活动 `typeProperties`（作为示例）下所需的更改。 查看[在云数据存储之间复制数据](data-factory-data-movement-activities.md#global)部分以了解支持的值和详细信息。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>从云源复制
如果要将数据从云数据存储复制到 Azure 搜索中，则 `executionLocation` 属性是必需的。 以下 JSON 片段显示复制活动 `typeProperties`（作为示例）下所需的更改。 查看[在云数据存储之间复制数据](data-factory-data-movement-activities.md#global)部分以了解支持的值和详细信息。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

还可以在复制活动定义中将源数据集中的列映射到接收器数据集中的列。 有关详细信息，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化  
若要了解影响数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，了解创建包含复制活动的管道的分步说明。
