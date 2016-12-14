---
title: "从 PostgreSQL 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 PostgreSQL 移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 037f76cdd01376780dde8c435d81f8d072dca2f0


---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>使用 Azure 数据工厂从 PostgreSQL 移动数据
本文概述了如何使用 Azure 数据工厂中的复制活动将数据从 PostgreSQL 移到其他数据存储。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

数据工厂服务支持使用数据管理网关连接到本地 PostgreSQL 源。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。

> [!NOTE]
> 即使 PostgreSQL 数据库托管在 Azure IaaS VM 中，仍需要网关。 可在与数据存储相同的 IaaS VM 上或不同的 VM 上安装网关，只要网关能连接数据库即可。
>
>

数据工厂仅支持将数据从 PostgreSQL 移动到其他数据存储，不支持将数据从其他存储移动到 PostgreSQL。

## <a name="supported-versions-and-installation"></a>支持的版本和安装
为使数据管理网关连接到 PostgreSQL 数据库，需要在数据管理网关所在的同一系统上安装 2.0.12 版本或更高版本的[用于 PostgreSQL 的 Ngpsql 数据提供程序](http://go.microsoft.com/fwlink/?linkid=282716)。 支持 7.4 版本和更高版本的 PostgreSQL。

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
>
>

## <a name="copy-data-wizard"></a>复制数据向导
若要创建将数据从 PostgreSQL 数据库复制到任何支持的接收器数据存储的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据从 PostgreSQL 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。   

## <a name="sample-copy-data-from-postgresql-to-azure-blob"></a>示例：将数据从 PostgreSQL 复制到 Azure Blob
此示例演示如何将数据从 PostgreSQL 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

1. [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#postgresql-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [RelationalTable](data-factory-onprem-postgresql-connector.md#postgresql-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该复制活动使用 [RelationalSource](data-factory-onprem-postgresql-connector.md#postgresql-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例每小时将数据从 PostgreSQL 数据库中的查询结果复制到 blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

**PostgreSQL 链接服务：**

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

**Azure Blob 存储链接服务：**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
      }
    }

**PostgreSQL 输入数据集：**

该示例假定已在 PostgreSQL 中创建表“MyTable”，并且它包含用于时间序列数据的“timestamp”列。

设置“external”: true 将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

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


**Azure Blob 输出数据集：**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态评估 blob 的文件夹路径和文件名。 文件夹路径使用开始时间的年、月、日和小时部分。

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


**复制活动：**

管道包含配置为使用输入和输出数据集，且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询从 PostgreSQL 数据库的 public.usstates 表中选择数据。

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


## <a name="postgresql-linked-service-properties"></a>PostgreSQL 链接服务属性
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

请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)，了解有关设置本地 PostgreSQL 数据源凭据的详细信息。

## <a name="postgresql-dataset-type-properties"></a>PostgreSQL 数据集类型属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型的数据集的 typeProperties 部分（包括 PostgreSQL 数据集）具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 PostgreSQL 数据库实例中表的名称。 TableName 区分大小写。 |否（如果指定了 **RelationalSource** 的**query**） |

## <a name="postgresql-copy-activity-type-properties"></a>PostgreSQL 复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

源属于 **RelationalSource** 类型（包括 PostgreSQL）时，以下属性在 typeProperties 节可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如：“query”：“从 \"MySchema\".\"MyTable\" 选择 *”。 |否（如果指定了**数据集**的 **tableName**） |

> [!NOTE]
> 架构和表名称区分大小写，并且在查询中必须括在“”（双引号）内。  
>
>

**示例：**

 “query”：“从 \"MySchema\".\"MyTable\"选择 *”。

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-postgresql"></a>PostgreSQL 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动通过以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移到 PostgreSQL 时，使用以下映射实现从 PostgreSQL 类型到 .NET 类型的转换。

| PostgreSQL 数据库类型 | PostgresSQL 别名 | .NET Framework 类型 |
| --- | --- | --- |
| abstime | |Datetime |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [ (n) ] | |Byte[], String |
| bit varying [ (n) ] |varbit |Byte[], String |
| 布尔值 |bool |布尔 |
| box | |Byte[], String |
| bytea | |Byte[], String |
| character [ (n) ] |char [ (n) ] |String |
| character varying [ (n) ] |varchar [ (n) ] |String |
| cid | |String |
| cidr | |String |
| circle | |Byte[], String |
| 日期 | |Datetime |
| daterange | |String |
| 双精度 |float8 |Double |
| inet | |Byte[], String |
| intarry | |String |
| int4range | |String |
| int8range | |String |
| integer |int, int4 |Int32 |
| interval [ fields ] [ (p) ] | |Timespan |
| json | |String |
| jsonb | |Byte[] |
| 折线图 | |Byte[], String |
| lseg | |Byte[], String |
| macaddr | |Byte[], String |
| money | |小数 |
| numeric [ (p, s) ] |decimal [ (p, s) ] |小数 |
| numrange | |String |
| oid | |Int32 |
| 路径 | |Byte[], String |
| pg_lsn | |Int64 |
| point | |Byte[], String |
| polygon | |Byte[], String |
| real |float4 |Single |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| serial |serial4 |Int32 |
| text | |String |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。



<!--HONumber=Nov16_HO3-->


