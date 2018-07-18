---
title: 使用 Azure 数据工厂从 SAP Business Warehouse 移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从 SAP Business Warehouse 移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2104f2690e681c53649b9a37c6c764380aa94568
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051487"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP Business Warehouse 移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-sap-business-warehouse-connector.md)
> * [版本 2（当前版本）](../connector-sap-business-warehouse.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅 [V2 中的 SAP Business Warehouse 连接器](../connector-sap-business-warehouse.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动从本地 SAP Business Warehouse (BW) 移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从本地 SAP Business Warehouse 数据存储复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 SAP Business Warehouse 移至其他数据存储，而不支持将数据从其他数据存储移至 SAP Business Warehouse。 

## <a name="supported-versions-and-installation"></a>支持的版本和安装
此连接器支持 SAP Business Warehouse 版本 7.x。 它支持使用 MDX 查询从 InfoCubes 和 QueryCubes（包括 BEx 查询）复制数据。

若要启用与 SAP BW 实例的连接，请安装以下组件：
- **数据管理网关**：数据工厂服务支持使用称为“数据管理网关”的组件连接到本地数据存储（包括 SAP Business Warehouse）。 若要了解数据管理网关和设置网关的分步说明，请参阅[在本地数据存储与云数据存储之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。 即使 SAP Business Warehouse 托管在 Azure IaaS 虚拟机 (VM) 中，也需要网关。 只要网关能连接数据库，即可在与数据存储相同的 VM 上或不同的 VM 上安装网关。
- 网关计算机上的 **SAP NetWeaver 库**。 可以从 SAP 管理员处或直接从 [SAP 软件下载中心](https://support.sap.com/swdc)获取 SAP Netweaver 库。 搜索“SAP Note #1025361”获取最新版本的下载位置。 确保 SAP NetWeaver 库（32 位或 64 位）的体系结构与安装的网关匹配。 然后，按照 SAP 说明安装 SAP NetWeaver RFC SDK 中包含的所有文件。 SAP NetWeaver 库也包括在 SAP 客户端工具安装中。

> [!TIP]
> 将从 NetWeaver RFC SDK 中提取的 dll 放入 system32 文件夹。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从本地 Cassandra 数据存储移动数据。 

- 创建管道的最简单方法是使用复制向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。 
- 也可以使用以下工具创建管道：Azure 门户、Visual Studio、Azure PowerShell、Azure 资源管理器模板、.NET API 和 REST API。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建链接服务可将输入和输出数据存储链接到数据工厂。
2. 创建数据集以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的管道，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从本地 SAP Business Warehouse 复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 SAP Business Warehouse 复制到 Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) 部分。 

对于特定于 SAP BW 数据存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供了 SAP Business Warehouse (BW) 链接服务专属 JSON 元素的说明。

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
server | SAP BW 实例所驻留的服务器的名称。 | 字符串 | 是
systemNumber | SAP BW 系统的系统编号。 | 用字符串表示的两位十进制数。 | 是
clientId | SAP W 系统中的客户端的客户端 ID。 | 用字符串表示的三位十进制数。 | 是
username | 有权访问 SAP 服务器的用户名 | 字符串 | 是
password | 用户密码。 | 字符串 | 是
gatewayName | 网关的名称，数据工厂服务应使用此网关连接到本地 SAP BW 实例。 | 字符串 | 是
encryptedCredential | 加密的凭据字符串。 | 字符串 | 否

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 typeProperties 部分有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型的 SAP BW 数据集不支持任何类型特定的属性。 


## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称和描述等属性、输入和输出表格以及策略可用于所有类型的活动。

但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

在复制活动中，当源属于 **RelationalSource** 类型（包括 SAP BW）时，以下属性在 typeProperties 节中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query | 指定要从 SAP BW 实例读取数据的 MDX 查询。 | MDX 查询。 | 是 |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON 示例：将数据从 SAP Business Warehouse 复制到 Azure Blob
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 此示例演示如何将数据从本地 SAP Business Warehouse 复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

> [!IMPORTANT]
> 此示例提供 JSON 代码段。 它不包括创建数据工厂的分步说明。 请参阅文章[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)以获取分步说明。

此示例具有以下数据工厂实体：

1. [SapBw](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [RelationalTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 SAP Business Warehouse 实例复制到 Azure blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse 链接服务
此链接服务将 SAP BW 实例链接到数据工厂。 type 属性设置为 **SapBw**。 typeProperties 节提供 SAP BW 实例的连接信息。 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>SAP BW 输入数据集
此数据集定义 SAP Business Warehouse 数据集。 将数据工厂数据集的类型设置为 **RelationalTable**。 目前，未为 SAP BW 数据集指定任何类型特定的属性。 “复制活动”定义中的查询指定要从 SAP BW 实例读取的数据。 

将 external 属性设为 true 可告知数据工厂服务：表位于数据工厂外且不由数据工厂中的活动生成。

使用 Frequency 和 interval 属性定义计划。 在此示例中，每小时从 SAP BW 实例读取数据。 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
此数据集定义输出 Azure Blob 数据集。 type 属性设置为 AzureBlob。 typeProperties 节提供从 SAP BW 实例复制的数据的存储位置。 数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**（适用于 SAP BW 源），将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的查询选择复制过去一小时的数据。

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>SAP BW 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

从 SAP BW 移动数据时，将使用以下从 SAP BW 类型到 .NET 类型的映射。

ABAP 字典中的数据类型 | .Net 数据类型
-------------------------------- | --------------
ACCP |  int
CHAR | String
CLNT | String
CURR | 小数
CUKY | String
DEC | 小数
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | 小数
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
单位 | String
DATS | String
NUMC | String
TIMS | String

> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。


## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。
