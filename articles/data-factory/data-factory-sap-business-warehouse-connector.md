---
title: "使用 Azure 数据工厂从 SAP Business Warehouse 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 SAP Business Warehouse 移动数据。"
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
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6a14e5e0cab25b26782ebd45b52aa7542b7e24d5
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP Business Warehouse 移动数据
本文概述如何使用 Azure 数据工厂管道中的复制活动在 SAP Business Warehouse 和另一个数据存储之间移动数据。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。 数据工厂当前仅支持将数据从 SAP Business Warehouse 移至其他数据存储，但不支持将数据从其他数据存储移至 SAP Business Warehouse。


## <a name="supported-versions-and-installation"></a>支持的版本和安装
此连接器支持 SAP Business Warehouse 版本 7.x。 它支持使用 MDX 查询从 InfoCubes 和 QueryCubes（包括 BEx 查询）复制数据。

若要启用与 SAP BW 实例的连接，请安装以下组件：
- **数据管理网关**：数据工厂服务支持使用称为“数据管理网关”的组件连接到本地数据存储（包括 SAP Business Warehouse）。 若要了解数据管理网关和设置网关的分步说明，请参阅[在本地数据存储与云数据存储之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。 即使 SAP Business Warehouse 托管在 Azure IaaS 虚拟机 (VM) 中，也需要网关。 只要网关能连接数据库，即可在与数据存储相同的 VM 上或不同的 VM 上安装网关。
- 网关计算机上的 **SAP NetWeaver 库**。 可以从 SAP 管理员处或直接从 [SAP 软件下载中心](https://support.sap.com/swdc)获取 SAP Netweaver 库。 搜索“SAP Note #1025361”获取最新版本的下载位置。 确保 SAP NetWeaver 库（32 位或 64 位）的体系结构与安装的网关匹配。 然后，按照 SAP 说明安装 SAP NetWeaver RFC SDK 中包含的所有文件。 SAP NetWeaver 库也包括在 SAP 客户端工具安装中。

## <a name="supported-sinks"></a>支持的接收器
有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 可以将数据从 SAP Business Warehouse 移到任何支持的接收器数据存储。 

## <a name="copy-data-wizard"></a>复制数据向导
若要创建将数据从 SAP Business Warehouse 复制到任何支持的接收器数据存储的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 此示例演示如何将数据从本地 SAP Business Warehouse 复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

> [!IMPORTANT]
> 此示例提供 JSON 代码段。 它不包括创建数据工厂的分步说明。 请参阅文章[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)以获取分步说明。

## <a name="sample-copy-data-from-sap-business-warehouse-to-azure-blob"></a>示例：将数据从 SAP Business Warehouse 复制到 Azure Blob
此示例具有以下数据工厂实体：

1. [SapBw](#sap-bw-linked-service) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [RelationalTable](#sap-bw-dataset) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](#sap-bw-source-in-copy-activity) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例每小时将数据从 SAP Business Warehouse 实例复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

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


## <a name="sap-bw-linked-service"></a>SAP BW 链接服务
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

## <a name="sap-bw-dataset"></a>SAP BW 数据集
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型的 SAP BW 数据集不支持任何类型特定的属性。 


## <a name="sap-bw-source-in-copy-activity"></a>复制活动中的 SAP BW 源
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称和描述等属性、输入和输出表格以及策略可用于所有类型的活动。

但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

在复制活动中，当源属于 **RelationalSource** 类型（包括 SAP BW）时，以下属性在 typeProperties 节中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query | 指定要从 SAP BW 实例读取数据的 MDX 查询。 | MDX 查询。 | 是 |

### <a name="type-mapping-for-sap-bw"></a>SAP BW 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

从 SAP BW 移动数据时，将使用以下从 SAP BW 类型到 .NET 类型的映射。

ABAP 字典中的数据类型 | .Net 数据类型
-------------------------------- | --------------
ACCP |    int
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

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

