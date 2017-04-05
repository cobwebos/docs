---
title: "使用 Azure 数据工厂从 SAP HANA 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 SAP HANA 移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 37594924f9474d225421d2b2d783a19d9295fcf8
ms.lasthandoff: 03/30/2017


---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP HANA 移动数据
本文介绍如何使用 Azure 数据工厂中的复制活动从本地 SAP HANA 移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从本地 SAP HANA 数据存储复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 SAP HANA 移至其他数据存储，而不支持将数据从其他数据存储移至 SAP HANA。

## <a name="supported-versions-and-installation"></a>支持的版本和安装
此连接器支持任何版本的 SAP HANA 数据库。 它支持使用 SQL 查询从 HANA 信息模型（如分析和计算视图）和行/列表中复制数据。

若要启用与 SAP HANA 实例的连接，请安装以下组件：
- **数据管理网关**：数据工厂服务支持使用称为“数据管理网关”的组件连接到本地数据存储（包括 SAP HANA）。 若要了解数据管理网关和设置网关的分步说明，请参阅[在本地数据存储与云数据存储之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。 即使 SAP HANA 托管在 Azure IaaS 虚拟机 (VM) 中，也需要网关。 只要网关能连接数据库，即可在与数据存储相同的 VM 上或不同的 VM 上安装网关。
- 网关计算机上的 **SAP HANA ODBC 驱动程序**。 可以从 [SAP 软件下载中心](https://support.sap.com/swdc)下载 SAP HANA ODBC 驱动程序。 使用关键字 **SAP HANA CLIENT for Windows** 进行搜索。 

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从本地 Cassandra 数据存储移动数据。 

- 创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。 
- 也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，将自动为你创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从本地 SAP HANA 复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 SAP HANA 复制到 Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) 部分。 

对于特定于 SAP HANA 数据存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供了 SAP HANA 链接服务专属 JSON 元素的说明。

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
server | SAP HANA 实例所驻留的服务器的名称。 如果服务器使用的是自定义端口，则指定 `server:port`。 | 字符串 | 是
authenticationType | 身份验证的类型。 | 字符串。 “基本”或“Windows” | 是 
username | 有权访问 SAP 服务器的用户名 | 字符串 | 是
password | 用户密码。 | 字符串 | 是
gatewayName | 网关的名称，数据工厂服务应使用此网关连接到本地 SAP HANA 实例。 | 字符串 | 是
encryptedCredential | 加密的凭据字符串。 | 字符串 | 否

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型的 SAP HANA 数据集不支持任何类型特定的属性。 


## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称和描述等属性、输入和输出表格以及策略可用于所有类型的活动。

但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

在复制活动中，当源属于 **RelationalSource** 类型（包括 SAP HANA）时，以下属性在 typeProperties 节中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query | 指定要从 SAP HANA 实例读取数据的 SQL 查询。 | SQL 查询。 | 是 |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON 示例：将数据从 SAP HANA 复制到 Azure Blob
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 此示例演示如何将数据从本地 SAP HANA 复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)列出的任何接收器。  

> [!IMPORTANT]
> 此示例提供 JSON 代码段。 它不包括创建数据工厂的分步说明。 请参阅文章[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)以获取分步说明。

此示例具有以下数据工厂实体：

1. [SapHana](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [RelationalTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 SAP HANA 实例复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

### <a name="sap-hana-linked-service"></a>SAP HANA 链接服务
此链接服务将 SAP HANA 实例链接到数据工厂。 type 属性设置为 **SapHana**。 typeProperties 节提供 SAP HANA 实例的连接信息。

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
此链接服务将 Azure 存储帐户链接到数据工厂。 type 属性设置为 **AzureStorage**。 typeProperties 节提供 Azure 存储帐户的连接信息。

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

### <a name="sap-hana-input-dataset"></a>SAP HANA 输入数据集

此数据集定义 SAP HANA 数据集。 将数据工厂数据集的类型设置为 **RelationalTable**。 目前，未为 SAP HANA 数据集指定任何类型特定的属性。 “复制活动”定义中的查询指定要从 SAP HANA 实例读取的数据。 

将 external 属性设为 true 可告知数据工厂服务：表位于数据工厂外且不由数据工厂中的活动生成。

使用 Frequency 和 interval 属性定义计划。 在此示例中，每小时从 SAP HANA 实例读取数据。 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob 输出数据集
此数据集定义输出 Azure Blob 数据集。 type 属性设置为 AzureBlob。 typeProperties 节提供从 SAP HANA 实例复制的数据的存储位置。 数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>包含复制活动的管道

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**（适用于 SAP HANA 源），将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询选择复制过去一小时的数据。

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>SAP HANA 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

从 SAP HANA 移动数据时，将使用以下从 SAP HANA 类型到 .NET 类型的映射。

SAP HANA 类型 | 基于.NET 的类型
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Single
DOUBLE | Single
DECIMAL | 小数
BOOLEAN | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TIME | TimeSpan
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>已知限制
从 SAP HANA 复制数据时，有以下几个已知限制：

- NVARCHAR 字符串截断为最大长度为 4000 的 Unicode 字符
- 不支持 SMALLDECIMAL
- 不支持 VARBINARY
- 有效日期在 1899/12/30 和 9999/12/31 之间

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
若要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

