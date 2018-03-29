---
title: 使用 Azure 数据工厂从 PostgreSQL 移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从 PostgreSQL 移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ee466c85b68ebc72dbd55849db84a473d584ffb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>使用 Azure 数据工厂从 PostgreSQL 移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-onprem-postgresql-connector.md)
> * [版本 2 - 预览版](../connector-postgresql.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 PostgreSQL 连接器](../connector-postgresql.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动从本地 PostgreSQL 数据库移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从本地 PostgreSQL 数据存储复制到任何支持的接收器数据存储。 有关复制活动支持用作接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 数据工厂当前支持将数据从 PostgreSQL 数据库移至其他数据存储，而不支持将数据从其他数据存储移至 PostgreSQL 数据库。 

## <a name="prerequisites"></a>先决条件

数据工厂服务支持使用数据管理网关连接到本地 PostgreSQL 源。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。

即使 PostgreSQL 数据库托管在 Azure IaaS VM 中，仍需要网关。 可在与数据存储相同的 IaaS VM 上或不同的 VM 上安装网关，只要网关能连接数据库即可。

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。

## <a name="supported-versions-and-installation"></a>支持的版本和安装
为使数据管理网关连接到 PostgreSQL 数据库，请在数据管理网关所在的同一系统上安装 2.0.12 版到 3.1.9 版之间的[用于 PostgreSQL 的 Ngpsql 数据提供程序](http://go.microsoft.com/fwlink/?linkid=282716)。 支持 7.4 版本和更高版本的 PostgreSQL。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从本地 PostgreSQL 数据存储移动数据。 

- 创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。 
- 还可以使用以下工具来创建管道： 
    - Azure 门户
    - Visual Studio
    - Azure PowerShell
    - Azure 资源管理器模板
    - .NET API
    - REST API

     有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从本地 PostgreSQL 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 PostgreSQL 复制到 Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) 部分。 

对于特定于 PostgreSQL 数据存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供 PostgreSQL 链接服务专属 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |type 属性必须设置为：**OnPremisesPostgreSql** |是 |
| server |PostgreSQL 服务器的名称。 |是 |
| database |PostgreSQL 数据库的名称。 |是 |
| schema |数据库中架构的名称。 架构名称区分大小写。 |否 |
| authenticationType |用于连接到 PostgreSQL 数据库的身份验证类型。 可能的值为：Anonymous、Basic 和 Windows。 |是 |
| username |如果使用基本或 Windows 身份验证，请指定用户名。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 PostgreSQL 数据库。 |是 |

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型的数据集的 typeProperties 部分（包括 PostgreSQL 数据集）具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 PostgreSQL 数据库实例中表的名称。 TableName 区分大小写。 |否（如果指定了 **RelationalSource** 的**query**） |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

而可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

当源属于 **RelationalSource** 类型（包括 PostgreSQL）时，以下属性可在 typeProperties 节中使用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如：`"query": "select * from \"MySchema\".\"MyTable\""`。 |否（如果指定了**数据集**的 **tableName**） |

> [!NOTE]
> 架构和表名称区分大小写。 在查询中将名称括在 `""`（双引号）中。  

**示例：**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON 示例：将数据从 PostgreSQL 复制到 Azure Blob
此示例提供示例 JSON 定义，可使用这些定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据从 PostgreSQL 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。   

> [!IMPORTANT]
> 此示例提供 JSON 代码段。 它不包括创建数据工厂的分步说明。 请参阅文章[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)以获取分步说明。

此示例具有以下数据工厂实体：

1. [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 PostgreSQL 数据库中的查询结果复制到 blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

**PostgreSQL 链接服务：**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
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

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
    }
    }
}
```
**PostgreSQL 输入数据集：**

该示例假定已在 PostgreSQL 中创建表“MyTable”，并且它包含用于时间序列数据的“timestamp”列。

设置 `"external": true` 将告知数据工厂服务：数据集在数据工厂外部且不是由数据工厂中的活动生成的。

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态评估 blob 的文件夹路径和文件名。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

管道包含配置为使用输入和输出数据集，且计划每小时运行一次的复制活动。 在管道 JSON 定义中， **源** 类型设置为 **RelationalSource****接收器** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询从 PostgreSQL 数据库的 public.usstates 表中选择数据。

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
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
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>PostgreSQL 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动通过以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移到 PostgreSQL 时，使用以下映射实现从 PostgreSQL 类型到 .NET 类型的转换。

| PostgreSQL 数据库类型 | PostgresSQL 别名 | .NET Framework 类型 |
| --- | --- | --- |
| abstime | |Datetime | &nbsp;
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte[], String | &nbsp;
| bit varying [ (n) ] |varbit |Byte[], String |
| 布尔值 |bool |布尔 |
| box | |Byte[], String |&nbsp;
| bytea | |Byte[], String |&nbsp;
| character [(n)] |char [(n)] |String |
| character varying [(n)] |varchar [(n)] |String |
| cid | |String |&nbsp;
| cidr | |String |&nbsp;
| circle | |Byte[], String |&nbsp;
| 日期 | |Datetime |&nbsp;
| daterange | |String |&nbsp;
| 双精度 |float8 |Double |
| inet | |Byte[], String |&nbsp;
| intarry | |String |&nbsp;
| int4range | |String |&nbsp;
| int8range | |String |&nbsp;
| integer |int, int4 |Int32 |
| interval [fields] [(p)] | |Timespan |&nbsp;
| json | |String |&nbsp;
| jsonb | |Byte[] |&nbsp;
| 折线图 | |Byte[], String |&nbsp;
| lseg | |Byte[], String |&nbsp;
| macaddr | |Byte[], String |&nbsp;
| money | |小数 |&nbsp;
| numeric [(p, s)] |decimal [(p, s)] |小数 |
| numrange | |String |&nbsp;
| oid | |Int32 |&nbsp;
| 路径 | |Byte[], String |&nbsp;
| pg_lsn | |Int64 |&nbsp;
| point | |Byte[], String |&nbsp;
| polygon | |Byte[], String |&nbsp;
| real |float4 |Single |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| serial |serial4 |Int32 |
| text | |String |&nbsp;

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法。
