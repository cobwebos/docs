---
title: "使用数据工厂将数据移出/移入 Oracle | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据移入/移出本地 Oracle 数据库。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: c3161d1b870936ff51bbe092f1ca8ff320cf956e


---
# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>使用 Azure 数据工厂将数据移入/移出本地 Oracle
本文概述如何使用数据工厂复制活动在 Oracle 和另一个数据存储之间移动数据。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

## <a name="supported-versions"></a>支持的版本
通过安装在数据管理网关计算机上的 Oracle 数据提供程序 .NET 12.1，可使用此 Oracle 连接器从/向以下版本的 Oracle 实例复制数据。 有关如何设置的详细信息，请参阅[安装](#installation)部分。

* Oracle 12c
* Oracle 11g
* Oracle 10g 版本 2

## <a name="installation"></a>安装
若要使 Azure 数据工厂能够连接到本地 Oracle 数据库，则必须安装以下组件：

* 在托管数据库的同一计算机上安装数据管理网关，或安装在不同计算机上，以避免与数据库争用资源。 数据管理网关是一个客户端代理，以安全和托管的方式将本地数据源连接到云服务。 有关数据管理网关的详细信息，请参阅[在本地与云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。
* 适用于 .NET 的 Oracle 数据提供程序。 该组件包含在[适用于 Windows 的 Oracle 数据访问组件](http://www.oracle.com/technetwork/topics/dotnet/downloads/)中。 在安装网关的主机计算机上安装适合的版本（32/64 位）。 [Oracle 数据提供程序 .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) 可访问 Oracle 数据库 10g 版本 2 或更高版本。

    如果选择“XCopy 安装”，请遵循 readme.htm 中的步骤。 推荐选择带 UI 的安装程序（非 XCopy）。

    提供程序安装后，使用服务小程序（或）数据管理网关配置管理器在计算机上重新启动数据管理网关主机服务。  

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
>
>

## <a name="copy-data-wizard"></a>复制数据向导
若要创建向 Oracle 数据库复制数据或将数据从 Oracle 数据库复制到任何支持的接收数据存储的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们介绍如何在 Oracle 数据库和 Azure Blob 存储之间复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>示例：将数据从 Oracle 复制到 Azure Blob
此示例演示如何将数据从本地 Oracle 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

1. [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该活动将 [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) 用作源，并将 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 用作接收器。

该实例每隔一小时会将数据从本地 Oracle 数据库中的表复制到 blob。 有关此实例中使用的各类属性的详细信息，请参阅实例之后的部分中的文档。

**Oracle 链接服务：**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob 存储链接服务：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Oracle 输入数据集：**

该示例假定已在 Oracle 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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


**Azure Blob 输出数据集：**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态评估 blob 的文件夹路径和文件名。 文件夹路径使用开始时间的年、月、日和小时部分。

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**包含复制活动的管道：**

管道包含配置为使用输入和输出数据集，且计划每小时运行一次的复制活动。 在管道 JSON 定义中，“源”类型设置为 **OracleSource**，“接收器”类型设置为 **BlobSink**。  为 **oracleReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
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


需要根据 Oracle 数据库中日期的配置方式来调整查询字符串。 如果出现下面的错误消息：

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

可能需要更改查询，如下面的示例所示（使用 to_date 函数）：

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>示例：将数据从 Azure Blob 复制到 Oracle
该实例演示如何将数据从 Azure Blob 存储复制到本地 Oracle 数据库。 但可使用 Azure 数据工厂中的复制活动**直接**从[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何源复制数据。  

此示例具有以下数据工厂实体：

1. [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该活动将 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 用作源，并将[OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) 用作接收器。

该实例每隔一小时会将数据从 blob 复制到本地 Oracle 数据库中的表。 有关此实例中使用的各类属性的详细信息，请参阅实例之后的部分中的文档。

**Oracle 链接服务：**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob 存储链接服务：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Azure Blob 输入数据集**

每小时从新的 blob 获取数据一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态评估 blob 的文件夹路径和文件名。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 “external”: ”true”设置将告知数据工厂服务：表在数据工厂外部且不由数据工厂中的活动生成。

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
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

**Oracle 输出数据集：**

该示例假定已在 Oracle 表中创建了表“MyTable”。 在 Oracle 中创建列数与 Blob CSV 文件应包含的列数相同的表。 每小时向该表添加新行。

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**包含复制活动的管道：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **OracleSink**。  

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Oracle 链接服务属性
下表提供了有关特定于 Oracle 链接服务的 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性必须设置为：**OnPremisesOracle** |是 |
| connectionString |为 connectionString 属性指定连接到 Oracle 数据库实例所需的信息。 |是 |
| gatewayName |用于连接到本地 Oracle 服务器的网关的名称 |是 |

请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)，了解有关设置本地 Oracle 数据源凭据的详细信息。

## <a name="oracle-dataset-type-properties"></a>Oracle 数据集类型属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Oracle、Azure blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 OracleTable 类型的数据集的 typeProperties 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务引用的 Oracle 数据库中表的名称。 |否（如果指定了 **OracleSource** 的 **oracleReaderQuery**） |

## <a name="oracle-copy-activity-type-properties"></a>Oracle 复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

> [!NOTE]
> 复制活动只使用一个输入，只生成一个输出。
>
>

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

### <a name="oraclesource"></a>OracleSource
在复制活动中，如果源的类型为 **OracleSource**，则可以在 **typeProperties** 部分中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| oracleReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 例如：如未指定，则为 select * from MyTable <br/><br/>，则执行的 SQL 语句是：select * from MyTable |否（如果指定了**数据集**的 **tableName**） |

### <a name="oraclesink"></a>OracleSink
**OracleSink** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：00:30:00（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时将数据插入到 SQL 表。 |整数（行数） |否（默认值：100） |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 |查询语句。 |否 |
| sliceIdentifierColumnName |指定复制活动要使用自动生成的切片标识符进行填充的列名，该标识符用于重新运行时清除特定切片的数据。 |数据类型为 binary(32) 的列的列名。 |否 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Oracle 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动通过以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

从 Oracle 移动数据时，会使用以下从 Oracle 数据类型到 .NET 类型的映射，反之亦然。

| Oracle 数据类型 | .NET framework 数据类型 |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |小数 |
| INTEGER |小数 |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |TimeSpan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |小数 |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

## <a name="troubleshooting-tips"></a>故障排除提示
**问题：**见到以下**错误消息**：复制活动遇到无效参数：“UnknownParameterName”，详细消息：无法找到请求的 .Net Framework 数据提供程序。 该程序可能未安装”。  

**可能的原因：**

1. 未安装有适用于 Oracle 的 .NET Framework 数据提供程序。
2. 适用于 Oracle 的 .NET Framework 数据提供程序安装到 .NET Framework 2.0，且未在 .NET Framework 4.0 文件夹中找到。

**解决方法：**

1. 如果未安装适用于 Oracle 的 .NET 提供程序，请先[安装](http://www.oracle.com/technetwork/topics/dotnet/downloads/)该程序，然后再重试此方案。
2. 如果安装此提供程序后仍出现此错误消息，请执行以下步骤：
   1. 从文件夹 <system disk>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config 中打开 .NET 2.0 的计算机配置。
   2. 搜索**用于 .Net 的 Oracle 数据提供程序**，应会找到如下面的示例 un 中的示例 unwn 中所示的项der **system.data** -> **DbProviderFactories**:
           “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. 将此项复制到下面的 v4.0 文件夹 <system disk>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config 中的 machine.config 文件，然后将版本更改为 4.xxx.x.x。
4. 运行 `gacutil /i [provider path]`，将“<ODP.NET Installed Path>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll”安装到全局程序集缓存 (GAC)。

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。



<!--HONumber=Nov16_HO3-->


