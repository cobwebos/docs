---
title: "使用 Azure 数据工厂从 Sybase 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 Sybase 数据库移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: dd8a68029449ad013c4df9a46c558efaefd20e96
ms.openlocfilehash: b4b60c9134fd491184b02593c32659d2dcdfed8a


---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>使用 Azure 数据工厂从 Sybase 移动数据
本文概述了如何使用 Azure 数据工厂中的复制活动将数据从 Sybase 移到另一个数据存储。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

数据工厂服务支持通过数据管理网关连接到本地 Sybase 源。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。

> [!NOTE]
> 即使 Sybase 数据库托管在 Azure IaaS VM 中，仍需要网关。 可在与数据存储相同的 IaaS VM 上或不同的 VM 上安装网关，只要网关能连接数据库即可。
>
>

数据工厂当前仅支持将数据从 Sybase 移动到其他数据存储，不支持将数据从其他存储移动到 Sybase。

## <a name="supported-versions-and-installation"></a>支持的版本和安装
为使数据管理网关连接到 Sybase 数据库，需要在数据管理网关所在的系统上安装 16 版本或更高版本的 [用于 Sybase iAnywhere.Data.SQLAnywhere 的数据提供程序](http://go.microsoft.com/fwlink/?linkid=324846)。 支持 16 版本和更高版本的 Sybase。

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
>
>

## <a name="copy-data-wizard"></a>复制数据向导
若要创建将数据从 Sybase 数据库复制到任何支持的接收数据存储的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据从 Sybase 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。   

## <a name="sample-copy-data-from-sybase-to-azure-blob"></a>示例：将数据从 Sybase 表复制到 Azure Blob
此示例演示如何将数据从 Sybase 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

1. [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例将数据从 Sybase 数据库中的查询结果复制到 blob，每小时进行一次。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

**Sybase 链接服务：**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob 存储链接服务：**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Sybase 输入数据集：**

该示例假定已在 Sybase 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestamp”的列。

“external”: “true” 设置将告知数据工厂服务：此数据集在数据工厂外部，且不由数据工厂中的活动生成。 请注意，链接服务的**类型**设置为：**RelationalTable**。

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**包含复制活动的管道：**

管道包含配置为使用输入和输出数据集，且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为**查询**属性指定的 SQL 查询从数据库中 DBA.Orders 表中选择数据。

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="sybase-linked-service-properties"></a>Sybase 链接服务属性
下表提供了有关 Sybase 链接服务专属 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |“type”属性必须设置为：**OnPremisesSybase** |是 |
| server |Sybase 服务器的名称。 |是 |
| database |Sybase 数据库的名称。 |是 |
| schema |数据库中架构的名称。 |否 |
| authenticationType |用于连接 Sybase 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 Sybase 数据库。 |是 |

请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)，了解有关设置本地 Sybase 数据源凭据的详细信息。

## <a name="sybase-dataset-type-properties"></a>Sybase 数据集类型属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 结构、可用性和数据集 JSON 的策略等部分与所有数据集类型（Azure SQL、Azure blob、Azure 表等）类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable**类型数据集（包括 Sybase 数据集）的 **typeProperties** 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务指代的 Sybase 数据库实例中表的名称。 |否（如果指定了 **RelationalSource** 的**query**） |

## <a name="sybase-copy-activity-type-properties"></a>Sybase 复制活动类型属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

当源属于 **RelationalSource** 类型（包括 Sybase）时，以下属性可在 **typeProperties** 部分中使用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如，select * from MyTable。 |否（如果指定了**数据集**的 **tableName**） |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-sybase"></a>Sybase 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动使用以下 2 个步骤执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

Sybase 支持 T-SQL 和 T-SQL 类型。 有关 SQL 类型到 .NET 类型的映射表，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md)一文。

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。



<!--HONumber=Jan17_HO4-->


