---
title: "使用数据工厂从 MongoDB 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 MongoDB 数据库移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20df17ba01cfc18ce751491d154d7401001e706e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>使用 Azure 数据工厂从 MongoDB 移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-on-premises-mongodb-connector.md)
> * [版本 2 - 预览版](../connector-mongodb.md)

> [!NOTE]
> 本文适用于数据工厂版本 1，即正式版 (GA)。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 MongoDB 连接器](../connector-mongodb.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动从本地 MongoDB 数据库移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从本地 MongoDB 数据存储复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 MongoDB 数据存储移至其他数据存储，而不支持将数据从其他数据存储移至 MongoDB 数据存储。 

## <a name="prerequisites"></a>先决条件
若要使 Azure 数据工厂服务能够连接到本地 MongoDB 数据库，必须安装以下组件：

- 支持的 MongoDB 版本为：2.4、2.6、3.0 和 3.2。
- 在托管数据库的同一计算机上安装数据管理网关，或安装在不同计算机上，以避免与数据库争用资源。 数据管理网关是一种以安全和托管的方式将本地数据源连接到云服务的软件。 有关数据管理网关的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。 有关设置网关以便数据管道移动数据的分步说明，请参阅[将数据从本地移到云](data-factory-move-data-between-onprem-and-cloud.md)一文。

    安装网关时，会自动安装用于连接到 MongoDB的 Microsoft MongoDB ODBC 驱动程序。

    > [!NOTE]
    > 需使用网关连接到 MongoDB，即使它托管在 Azure IaaS VM 中也是如此。 若要尝试连接到托管在云中的 MongoDB 实例，还可在 IaaS VM 中安装网关实例。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从本地 MongoDB 数据存储移动数据。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储： 

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从本地 MongoDB 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 MongoDB 复制到 Azure Blob](#json-example-copy-data-from-mongodb-to-azure-blob) 部分。 

对于特定于 MongoDB 源的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供 **OnPremisesMongoDB** 链接服务专属 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |type 属性必须设置为：**OnPremisesMongoDb** |是 |
| server |MongoDB 服务器的 IP 地址或主机名。 |是 |
| 端口 |MongoDB 服务器用于侦听客户端连接的 TCP 端口。 |（可选）默认值：27017 |
| authenticationType |Basic 或 Anonymous。 |是 |
| username |用于访问 MongoDB 的用户帐户。 |是（如果使用基本身份验证）。 |
| password |用户密码。 |是（如果使用基本身份验证）。 |
| authSource |要用于检查身份验证凭据的 MongoDB 数据库名称。 |可选（如果使用基本身份验证）。 默认值：使用管理员帐户和使用 databaseName 属性指定的数据库。 |
| databaseName |要访问的 MongoDB 数据库名称。 |是 |
| gatewayName |用于访问数据存储的网关名称。 |是 |
| encryptedCredential |经过网关加密的凭据。 |可选 |

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **MongoDbCollection** 数据集类型的 typeProperties 节具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| collectionName |MongoDB 数据库中集合的名称。 |是 |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

源为 **MongoDbSource** 类型时，可在 typeProperties 节使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL-92 查询字符串。 例如，select * from MyTable。 |否（如果指定了**数据集**的 **collectionName**） |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON 示例：将数据从 MongoDB 复制到 Azure Blob
此示例提供示例 JSON 定义，可使用这些定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它演示如何将数据从本地 MongoDB 复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

此示例具有以下数据工厂实体：

1. [OnPremisesMongoDb](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [MongoDbCollection](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [MongoDbSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 MongoDB 数据库中的查询结果复制到 blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

首先，按照[数据管理网关](data-factory-data-management-gateway.md)一文中的说明设置数据管理网关。

**MongoDB 链接服务：**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**MongoDB 输入数据集：**设置“external”: ”true”将告知数据工厂服务：表在数据工厂外部且不由数据工厂中的活动生成。

```json
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
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

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**管道中使用 MongoDB 源和 Blob 接收器的复制活动：**

管道包含配置为使用上述输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **MongoDbSource**，**接收器**类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询选择复制过去一小时的数据。

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>数据工厂的构架
Azure 数据工厂服务通过使用 MongoDB 集合中最新的 100 个文档来推断该集合的架构。 如果这 100 个文档不包含完整架构，则在复制操作期间可能忽略某些列。

## <a name="type-mapping-for-mongodb"></a>MongoDB 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动通过以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移到 MongoDB 时，使用以下映射实现从 MongoDB 类型到 .NET 类型的转换。

| MongoDB 类型 | .NET Framework 类型 |
| --- | --- |
| 二进制 |Byte[] |
| 布尔 |布尔 |
| 日期 |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| 对象 |重新标准化为平展列，以“_”作为嵌套分隔符 |

> [!NOTE]
> 若要了解对使用虚拟表的数组的支持，请参阅以下[支持使用虚拟表的复杂类型](#support-for-complex-types-using-virtual-tables)一节。

目前，不支持以下 MongoDB 数据类型：DBPointer、JavaScript、Max/Min key、Regular Expression、Symbol、Timestamp、Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>支持使用虚拟表的复杂类型
Azure 数据工厂使用内置的 ODBC 驱动程序连接到 MongoDB 数据库，并从中复制数据。 对于数组或文档间不同类型的对象等复杂类型，该驱动程序会将数据重新标准化到相应虚拟表中。 具体而言，如果表中包含此类列，该驱动程序会生成以下虚拟表：

* **基表**，其中包含与实际表相同的数据（复杂类型列除外）。 基表使用与其所表示的实际表相同的名称。
* 对于每个复杂类型列生成一个**虚拟表**，这会扩展嵌套数据。 使用实际表名称、分隔符“_”和数组或对象的名称，对虚拟表命名。

虚拟表引用实际表中的数据，以使驱动程序能访问非规范化的数据。 有关详细信息，请参阅下方“示例”部分。 通过查询和联接虚拟表，可访问 MongoDB 数组的内容。

可利用[复制向导](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity)直观地查看 MongoDB 数据库中表（包括虚拟表）的列表，并预览表中的数据。 还可在复制向导中构造查询，并进行验证以查看结果。

### <a name="example"></a>示例
例如，以下“ExampleTable”为 MongoDB 表，其中“发票”列的每个单元格包含对象数组，“评级”列包含标量类型数组。

| _id | 客户名称 | 发票 | 服务级别 | 评级 |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, item:”toaster”, price:”456”, discount:”0.2”}, {invoice_id:”124”, item:”oven”, price: ”1235”, discount: ”0.2”}] |银牌服务 |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”, price: ”12543”, discount: ”0.0”}] |金牌服务 |[1,2] |

该驱动程序会生成多个虚拟表来表示此单个表。 第一个虚拟表是名为“ExampleTable”的基表，如下所示。 基表包含原始表中的所有数据，但已省略数组中的数据，这些数据会在虚拟表中展开。

| _id | 客户名称 | 服务级别 |
| --- | --- | --- |
| 1111 |ABC |银牌服务 |
| 2222 |XYZ |金牌服务 |

下表显示在示例中表示原始数组的虚拟表。 这些表包含以下项：

* 通过 _id 列返回到原始主键列的引用，该列与原始数组的行对应
* 原始数组中数据位置的指示
* 该数组中每个元素展开的数据

“ExampleTable_Invoices”表：

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | 折扣 |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |吐司炉 |456 |0.2 |
| 1111 |1 |124 |烤箱 |1235 |0.2 |
| 2222 |0 |135 |冰箱 |12543 |0.0 |

“ExampleTable_Ratings”表：

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |#N/A |

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>后续步骤
有关创建数据管道以将数据从本地数据存储移动到 Azure 数据存储的分布说明，请参阅[在本地和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。
