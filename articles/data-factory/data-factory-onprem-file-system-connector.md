---
title: "将数据移入和移出文件系统 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据移入和移出本地文件系统。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: e715588edb60dbe19449474e89da841e8b375878


---
# <a name="move-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>使用 Azure 数据工厂将数据移入和移出本地文件系统
本文概述如何使用 Azure 数据工厂复制活动将数据移入和移出本地文件系统。 有关可用作本地文件系统的源或接收器的数据存储列表，请参阅[支持的源和接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

数据工厂支持通过数据管理网关连接到本地文件系统和从本地文件系统进行连接。 请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。

> [!NOTE]
> 除了数据管理网关之外，要与本地文件系统进行通信，不需要安装其他二进制文件。
>
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
>
>

## <a name="linux-file-share"></a>Linux 文件共享
执行以下两个步骤将 Linux 文件共享与文件服务器链接服务配合使用：

* 在 Linux 服务器上安装 [Samba](https://www.samba.org/)。
* 在 Windows Server 上安装和配置数据管理网关。 不支持在 Linux 服务器上安装数据管理网关。

## <a name="copy-wizard"></a>复制向导
若要创建将数据复制到本地文件系统和从本地文件系统复制数据的管道，最简单的方法是使用复制向导。 有关快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据复制到本地文件系统和 Azure Blob 存储，以及如何从本地文件系统和 Azure Blob 存储复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据直接从任意源复制到[受支持的源和接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)中列出的任意接收器。

## <a name="sample-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>示例：将数据从本地文件系统复制到 Azure Blob 存储
此示例演示如何将数据从本地文件系统复制到 Azure Blob 存储。

此示例具有以下数据工厂实体：

* [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#on-premises-file-server-linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
* [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

以下示例每小时将时间序列数据从本地文件系统复制到 Azure Blob 存储。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步，按照[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)中的说明设置数据管理网关。

**本地文件服务器的链接服务：**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

建议使用 **encryptedCredential** 属性，而不是 **userid** 和 **password** 属性。 有关此链接服务的详细信息，请参阅[文件服务器的链接服务](#onpremisesfileserver-linked-service-properties)。

**Azure 存储链接服务：**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**本地文件系统输入数据集：**

每小时从新文件中获取数据。 基于切片的开始时间确定 folderPath 和 fileName 属性。  

设置 `"external": "true"` 将告知数据工厂：数据集在数据工厂外部且不由数据工厂中的活动生成。

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
          ]
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

**Azure Blob 存储输出数据集：**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **FileSystemSource**，将 **sink** 类型设置为 **BlobSink**。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "FileSystemSource"
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

## <a name="sample-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>示例：将数据从 Azure SQL 数据库复制到本地文件系统
以下示例显示：

* AzureSqlDatabase 类型的链接服务。
* OnPremisesFileServer 类型的链接服务。
* AzureSqlTable 类型的输入数据集。
* FileShare 类型的输出数据集。
* 包含复制活动的管道，该复制活动使用 SqlSource 和 FileSystemSink。

该示例每小时将时间序列数据从 Azure SQL 表复制到本地文件系统。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure SQL 链接服务：**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**本地文件服务器的链接服务：**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

建议使用 **encryptedCredential** 属性，而不是 **userid** 和 **password** 属性。 有关此链接服务的详细信息，请参阅[文件系统的链接服务](#onpremisesfileserver-linked-service-properties)。

**Azure SQL 输入数据集：**

该示例假定已在 Azure SQL 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

设置 ``“external”: ”true”`` 将告知数据工厂：数据集在数据工厂外部且不由数据工厂中的活动生成。

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**本地文件系统输出数据集：**

每小时将数据复制到新文件。 blob 的 folderPath 和 fileName 基于切片的开始时间确定。

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
          ]
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

**包含复制活动的管道：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **FileSystemSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="on-premises-file-server-linked-service-properties"></a>本地文件服务器的链接服务属性
可使用本地文件服务器链接服务将本地文件系统链接到 Azure 数据工厂。 下表提供本地文件服务器链接服务特定的 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |确保类型属性设置为 **OnPremisesFileServer**。 |是 |
| 主机 |指定要复制的文件夹的根路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。 |是 |
| userid |指定有权访问服务器的用户的 ID。 |否（如果选择 encryptedCredential） |
| password |设置用户的密码 (userid)。 |否（如果选择 encryptedCredential） |
| encryptedCredential |指定可通过运行 New-AzureRmDataFactoryEncryptValue cmdlet 获取的加密凭据。 |否（如果选择在纯文本中指定 userid 和密码） |
| gatewayName |指定网关的名称，数据工厂应将其用于连接到本地文件服务器。 |是 |

请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)，了解有关设置本地文件系统数据源凭据的详细信息。

### <a name="sample-linked-service-and-dataset-definitions"></a>链接服务和数据集定义示例
| 方案 | 链接服务定义中的主机 | 数据集定义中的 folderPath |
| --- | --- | --- |
| 数据管理网关计算机上的本地文件夹： <br/><br/>示例：D:\\\* 或 D:\folder\subfolder\\* |D:\\\\（适用于数据管理网关 2.0 以及更高版本） <br/><br/> localhost（适用于数据管理网关 2.0 之前的版本） |.\\\\ 或 folder\\\\subfolder（适用于数据管理网关 2.0 以及更高版本） <br/><br/>D:\\\\ 或 D:\\\\folder\\\\subfolder（适用于低于 2.0 的网关版本） |
| 远程共享文件夹： <br/><br/>示例：\\\\myserver\\share\\\* 或 \\\\myserver\\share\\folder\\subfolder\\* |\\\\\\\\myserver\\\\share |.\\\\ 或 folder\\\\subfolder |

**查找网关的版本：**

1. 在计算机上启动[数据管理网关配置管理器](data-factory-data-management-gateway.md#configuration-manager)。
2. 切换到“帮助”选项卡。

> [!NOTE]
> 建议[将网关升级到数据管理网关 2.0 或更高版本](data-factory-data-management-gateway.md#update)，以充分利用最新功能和修补程序。
>
>

**示例：使用纯文本格式的用户名和密码**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

**示例：使用 encryptedcredential**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "D:\\",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## <a name="on-premises-file-system-dataset-type-properties"></a>本地文件系统数据集类型属性
有关可用于定义数据集的各部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。

每个数据集类型的 typeProperties 部分不同。 它提供诸如数据存储中数据的位置和格式等信息。 **FileShare** 类型的数据集的 typeProperties 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |指定文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成文件的名称将采用以下格式： <br/><br/>`Data.<Guid>.txt`（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| partitionedBy |可使用 partitionedBy 指定时序数据的动态 folderPath/fileName。 例如，针对每小时数据参数化的 folderPath。 |否 |
| 格式 |支持以下格式类型：**TextFormat**、**AvroFormat**、**JsonFormat**、**OrcFormat** 和 **ParquetFormat**。 请将“格式”下的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[指定 TextFormat](#specifying-textformat)、[指定 AvroFormat](#specifying-avroformat)、[指定 JsonFormat](#specifying-jsonformat)、[指定 OrcFormat](#specifying-orcformat) 和[指定 ParquetFormat](#specifying-parquetformat)。 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。 <br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1："fileFilter": "*.log"<br/>示例 2："fileFilter": 2014-1-?.txt"<br/><br/>注意：fileFilter 适用于 FileShare 输入数据集。 |否 |
| compression |指定数据的压缩类型和级别。 支持的类型为 **GZip**、**Deflate** 和 **BZip2**。 支持的级别为“最佳”和“最快”。 目前不支持 **AvroFormat** 或 **OrcFormat** 数据的压缩设置。 有关详细信息，请参阅[压缩支持](#compression-support)部分。 |否 |

> [!NOTE]
> 不能同时使用 fileName 和 fileFilter。
>
>

### <a name="using-partitionedby-property"></a>使用 partitionedBy 属性
如上一部分所述，可以使用 partitionedBy 指定时序数据的动态 folderPath 和 fileName。 为此，可以使用数据工厂宏和系统变量 SliceStart 与 SliceEnd（表示给定数据切片的逻辑时间段）。

若要了解有关时序数据集、计划和切片的信息，请参阅[创建数据集](data-factory-create-datasets.md)、[计划和执行](data-factory-scheduling-and-execution.md)以及[创建管道](data-factory-create-pipelines.md)。

#### <a name="sample-1"></a>示例 1：
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值（使用的格式为 YYYYMMDDHH）。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>示例 2：
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

在本示例中，SliceStart 的年、月、日和时间已提取到 folderPath 和 fileName 属性使用的各个变量。

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="file-share-copy-activity-type-properties"></a>文件共享复制活动类型属性
**FileSystemSource** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

**FileSystemSink** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |源为 BlobSource 或 FileSystem 时，请定义复制行为。 |**PreserveHierarchy：**将文件层次结构保留到目标文件夹中。 也就是说，指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><br/>**FlattenHierarchy：**源文件夹中的所有文件在目标文件夹的第一个级别中创建。 创建目标文件时，自动生成名称。<br/><br/>**MergeFiles：**将源文件夹的所有文件合并到一个文件中。 如果指定了文件名/blob 名称，则合并的文件名是指定的名称。 否则，它是自动生成的文件名。 |否 |

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例
本节介绍了 recursive 和 copyBehavior 属性值的不同组合的复制操作产生的行为。

| recursive 值 | copyBehavior 值 | 产生的行为 |
| --- | --- | --- |
| 是 |preserveHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| 是 |flattenHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| 是 |mergeFiles |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>性能和优化
 若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。



<!--HONumber=Nov16_HO3-->


