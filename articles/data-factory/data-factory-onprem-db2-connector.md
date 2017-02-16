---
title: "使用 Azure 数据工厂从 DB2 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 DB2 数据库移动数据"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: dd8a68029449ad013c4df9a46c558efaefd20e96
ms.openlocfilehash: 78adfcb8431305a94982b11586fce5d183fe35ec


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>使用 Azure 数据工厂从 DB2 移动数据
本文概述了如何使用 Azure 数据工厂中的复制活动在 DB2 和另一个数据存储之间移动数据。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

数据工厂支持通过数据管理网关连接到本地 DB2 源。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。

> [!NOTE]
> 使用网关连接到 DB2，即使它托管在 Azure IaaS VM 中也是如此。 若要尝试连接到托管在云中的 DB2 实例，还可在 IaaS VM 中安装网关实例。
>
>

数据工厂当前仅支持将数据从 DB2 移动到其他数据存储，不支持将数据从其他存储移动到 DB2。

## <a name="supported-versions"></a>支持的版本
此 DB2 连接器支持以下 IBM DB2 平台和版本，以及分布式关系数据库结构 (DRDA) SQL 访问管理器 (SQLAM) 版本 9、版本 10 和版本 11：

* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

数据管理网关提供了内置 DB2 驱动程序，因此从 DB2 复制数据时，无需手动安装任何驱动程序。

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
>
>

## <a name="copy-data-wizard"></a>复制数据向导
若要创建从 DB2 数据库复制数据的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 这些示例演示了如何从 DB2 数据库和 Azure Blob 存储复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>示例：将数据从 DB2 复制到 Azure Blob
此示例演示如何将数据从本地 DB2 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

1. [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例每小时会将数据从 DB2 数据库的查询结果复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步：安装和配置数据管理网关。 [在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文中提供了说明。

**DB2 链接服务：**

```JSON
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**DB2 输入数据集：**

该示例假定已在 DB2 中创建了表“MyTable”，并且它包含时间序列数据的名为“timestamp”的列。

“external”: “true” 设置将告知数据工厂服务：此数据集在数据工厂外部，且不由数据工厂中的活动生成。 请注意：**类型**设置为 **RelationalTable**。

```JSON
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**复制活动的管道：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为**查询**属性指定的 SQL 查询从 Orders 表中选择数据。

```JSON
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="db2-linked-service-properties"></a>DB2 链接服务的属性
下表提供 DB2 链接服务专属 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |“type”属性必须设置为：**OnPremisesDB2** |是 |
| server |DB2 服务器的名称。 |是 |
| database |DB2 数据库的名称。 |是 |
| schema |数据库中架构的名称。 架构名称区分大小写。 |否 |
| authenticationType |用于连接 DB2 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 DB2 数据库。 |是 |

请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)，了解有关设置本地 DB2 数据源凭据的详细信息。 

## <a name="db2-dataset-type-properties"></a>DB2 数据集类型属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 结构、可用性和数据集 JSON 的策略等部分与所有数据集类型（Azure SQL、Azure blob、Azure 表等）类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 RelationalTable 类型数据集（包括 DB2 数据集）的 typeProperties 部分具有以下属性。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 DB2 数据库实例中表的名称。 TableName 区分大小写。 |否（如果指定了 **RelationalSource** 的**query**） |

## <a name="db2-copy-activity-type-properties"></a>DB2 复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

在复制活动中，当源属于 **RelationalSource** 类型（包括 DB2）时，以下属性在 typeProperties 部分中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `"query": "select * from "MySchema"."MyTable""`。 |否（如果指定了**数据集**的 **tableName**） |

> [!NOTE]
> 架构和表名称区分大小写。 在查询中将名称括在 ""（双引号）中。  
>
>

**示例：**

```JSON
"query": "select * from "DB2ADMIN"."Customers""
```


[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>DB2 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移到 DB2 时，将从 DB2 类型到 .NET 类型使用以下映射。

| DB2 数据库类型 | .NET Framework 类型 |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| 小数 |小数 |
| DecimalFloat |小数 |
| 数字 |小数 |
| 日期 |Datetime |
| 时间 |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| 二进制 |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| 小数 |小数 |
| DecimalFloat |小数 |
| 数字 |小数 |
| 日期 |Datetime |
| 时间 |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。



<!--HONumber=Jan17_HO4-->


