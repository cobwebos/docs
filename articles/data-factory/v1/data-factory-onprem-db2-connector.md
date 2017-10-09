---
title: "使用 Azure 数据工厂从 DB2 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂复制活动从本地 DB2 数据库移动数据"
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
ms.date: 07/19/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eacecb9f084ead76633cf802751d3a603cb2f0d8
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>使用 Azure 数据工厂复制活动从 DB2 移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](data-factory-onprem-db2-connector.md)
> * [版本 2 - 预览版](../connector-db2.md)

> [!NOTE]
> 本文适用于数据工厂版本 1，即正式版 (GA)。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 DB2 连接器](../connector-db2.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动将数据从本地 DB2 数据库复制到数据存储。 可将数据复制到[数据工厂数据移动活动](data-factory-data-movement-activities.md#supported-data-stores-and-formats)一文中列为受支持接收器的任何存储。 本主题基于“数据工厂”一文，其中概述了如何使用复制活动移动数据，并列出了受支持的数据存储组合。 

数据工厂目前仅支持将 DB2 数据库中的数据移至[支持的接收器数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 不支持将其他数据存储中的数据移至 DB2 数据库。

## <a name="prerequisites"></a>先决条件
数据工厂支持使用[数据管理网关](data-factory-data-management-gateway.md)连接到本地 DB2 数据库。 有关设置用于移动数据的网关数据管道的分步说明，请参阅[将数据从本地移到云](data-factory-move-data-between-onprem-and-cloud.md)一文。

即使 DB2 托管在 Azure IaaS VM 中，也需要网关。 可在与数据存储相同的 IaaS VM 上安装网关。 如果网关可连接到数据库，则可在不同的 VM 上安装网关。

数据管理网关提供内置的 DB2 驱动程序，因此，无需手动安装驱动程序即可从 DB2 复制数据。

> [!NOTE]
> 有关排查连接和网关问题的提示，请参阅[排查网关问题](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)一文。


## <a name="supported-versions"></a>支持的版本
数据工厂 DB2 连接器支持以下 IBM DB2 平台和版本，以及分布式关系型数据库结构 (DRDA) SQL 访问管理器版本 9、10 和 11：

* IBM DB2 for z/OS 版本 11.1
* IBM DB2 for z/OS 版本 10.1
* IBM DB2 for i (AS400) 版本 7.2
* IBM DB2 for i (AS400) 版本 7.1
* IBM DB2 for Linux、UNIX 和 Windows (LUW) 版本 11
* IBM DB2 for LUW 版本 10.5
* IBM DB2 for LUW 版本 10.1

> [!TIP]
> 如果看到错误消息“找不到与 SQL 语句执行请求对应的包。 SQLSTATE=51002 SQLCODE=-805”，这是因为没有为 OS 上的普通用户创建所需的包。 若要解决此问题，请根据自己的 DB2 服务器类型遵照以下说明：
> - DB2 for i (AS400)：让超级用户在运行复制活动之前，先为普通用户创建集合。 若要创建集合，请使用命令：`create collection <username>`
> - DB2 for z/OS 或 LUW：使用高特权帐户（拥有包权限和 BIND、BINDADD、GRANT EXECUTE TO PUBLIC 权限的超级用户或管理员）运行复制一次。 复制期间会自动创建所需的包。 之后，可以切换回普通用户，继续运行后续复制。

## <a name="getting-started"></a>入门
可以使用不同的工具和 API 创建包含复制活动的管道，以便从本地 DB2 数据存储移动数据： 

- 创建管道的最简单方法是使用 Azure 数据工厂复制向导。 有关使用复制数据向导创建管道的快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。 
- 也可以使用工具创建管道，这些工具包括 Azure 门户、Visual Studio、Azure PowerShell、Azure 资源管理器模板、.NET API 和 REST API。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建链接服务可将输入和输出数据存储链接到数据工厂。
2. 创建数据集用于表示复制操作的输入和输出数据。 
3. 创建包含复制活动的管道，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用复制向导时，会自动创建数据工厂链接服务、数据集和管道实体的 JSON 定义。 使用工具或 API（.NET API 除外）时，可使用 JSON 格式定义数据工厂实体。 [JSON 示例：将数据从 DB2 复制到 Azure Blob 存储](#json-example-copy-data-from-db2-to-azure-blob)中演示了用于从本地 DB2 数据存储复制数据的数据工厂实体的 JSON 定义。

对于特定于 DB2 数据存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息。

## <a name="db2-linked-service-properties"></a>DB2 链接服务的属性
下表列出了特定于 DB2 链接服务的 JSON 属性。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| **类型** |必须将此属性设置为 **OnPremisesDB2**。 |是 |
| **server** |DB2 服务器的名称。 |是 |
| **database** |DB2 数据库的名称。 |是 |
| **schema** |DB2 数据库中架构的名称。 此属性区分大小写。 |否 |
| **authenticationType** |用于连接 DB2 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| **username** |如果使用 Basic 或 Windows 身份验证，则表示用户帐户的名称。 |否 |
| **password** |用户帐户的密码。 |否 |
| **gatewayName** |应该由数据工厂服务用来连接到本地 DB2 数据库的网关的名称。 |是 |

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的各节和属性的列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 所有数据集类型（Azure SQL、Azure Blob 存储、Azure 表存储等）的数据集 JSON 的 **structure**、**availability** 和 **policy** 等节类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型数据集（包括 DB2 数据集）的 **typeProperties** 节具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| **tableName** |链接服务引用的 DB2 数据库实例中表的名称。 此属性区分大小写。 |否（如果指定了 **RelationalSource** 类型复制活动的 **query** 属性） |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义复制活动的各节和属性的列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 **name**、**description**、**inputs** 表、**outputs** 表和 **policy** 等复制活动属性可用于所有类型的活动。 可在活动的 typeProperties 节中使用的属性因每个活动的类型而异。 对于复制活动，其属性因数据源和接收器的类型而异。

对于复制活动，当源的类型为 **RelationalSource**（包括 DB2）时，以下属性在 **typeProperties** 节中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| **query** |使用自定义查询读取数据。 |SQL 查询字符串。 例如： `"query": "select * from "MySchema"."MyTable""` |否（如果指定了数据集的 **tableName** 属性） |

> [!NOTE]
> 架构和表名称区分大小写。 在查询语句中，应使用 ""（双引号）括住属性名称。 例如：
>
> ```sql
> "query": "select * from "DB2ADMIN"."Customers""
> ```

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON 示例：将数据从 DB2 复制到 Azure Blob 存储
本示例提供示例 JSON 定义，可使用这些定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 本示例演示了如何将数据从 DB2 数据库复制到 Azure Blob 存储。 但是，可以使用 Azure 数据工厂复制活动将数据复制到[任何受支持的数据存储接收器类型](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。

此示例具有以下数据工厂实体：

- [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties) 类型的 DB2 链接服务
- [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的 Azure Blob 存储链接服务
- [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)
- 包含复制活动的[管道](data-factory-create-pipelines.md)，该活动使用 [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 属性

此示例每小时会将数据从 DB2 数据库的查询结果复制到 Azure blob。 实体定义后面的部分描述了本示例中使用的 JSON 属性。

首先请安装并配置数据网关。 [在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文中提供了说明。

**DB2 链接服务**

```json
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

**Azure Blob 存储链接服务**

```json
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

**DB2 输入数据集**

本示例假设已在 DB2 中创建了名为“MyTable”的表，其中包含标签为“timestamp”的列来表示时序数据。

**external** 属性设置为“true”。 此设置告知数据工厂服务，数据集在数据工厂外部且不由数据工厂中的活动生成。 请注意 **type** 属性设置为 **RelationalTable**。


```json
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

**Azure Blob 输出数据集**

将 **frequency** 属性设置为“Hour”并将 **interval** 属性设置为 1，以便每隔一小时将数据写入新 Blob。 根据正在处理的切片的开始时间，动态评估 Blob 的 **folderPath** 属性。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
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

**复制活动的管道**

管道包含配置为使用指定的输入和输出数据集、且计划每小时运行一次的复制活动。 在管道的 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询从“Orders”表中选择数据。

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
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

## <a name="type-mapping-for-db2"></a>DB2 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动使用以下两步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

复制活动将数据从 DB2 类型转换为 .NET 类型时使用以下映射：

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
| 日期 |DateTime |
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
| 日期 |DateTime |
| 时间 |TimeSpan |
| Timestamp |DateTime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
若要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-reads-from-relational-sources"></a>从关系源进行可重复读取
从关系型数据存储复制数据时，请注意可重复性，以免出现意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试**策略**属性，以便在出现故障时重新运行切片。 无论要重新运行切片几次，或者以哪种方式重新运行切片，都需要确保读取相同的数据。 有关详细信息，请参阅[从关系型数据源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="performance-and-tuning"></a>性能和优化
在[复制活动性能和优化指南](data-factory-copy-activity-performance.md)中了解影响复制活动性能的关键因素以及各种性能优化方法。

