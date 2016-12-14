---
title: "将数据移入和移出 SQL Server | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂将数据移入/移出本地或 Azure VM 中的 SQL Server 数据库。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 1b2514e1e6f39bb3ce9d8a46f4af01835284cdcc
ms.openlocfilehash: b473a9e5bc70b9f2b100032b45a0114f8745ab46


---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>使用 Azure 数据工厂将数据移入和移出本地或 IaaS (Azure VM) 中的 SQL Server
本文概述如何使用复制活动在 SQL Server 和另一个数据存储之间移动数据。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中概述了数据移动和支持的作为源和接收器的数据存储。

## <a name="supported-versions"></a>支持的版本
此 SQL Server 连接器支持同时使用 SQL 身份验证和 Windows 身份验证，从/向本地或 Azure IaaS 中托管的以下版本的实例复制数据：

* SQL Server 2016
* SQL Server 2014
* SQL Server 2012
* SQL Server 2008R2
* SQL Server 2008
* SQL Server 2005

## <a name="create-pipeline"></a>创建管道
可创建包含复制活动的管道，该活动使用不同的工具/API 将数据移入/移出本地 SQL Server 数据库。  

* 复制向导
* Azure 门户
* Visual Studio
* Azure PowerShell
* .NET API
* REST API

有关使用复制活动以不同方式创建管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

## <a name="enabling-connectivity"></a>启用连接
连接本地托管的 SQL Server 和连接 Azure IaaS（服务架构）VM 中托管的 SQL Server 所需的概念和步骤相同。 这两种情况下，都需使用数据管理网关进行连接。

请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。 与 SQL Server 连接的先决条件之一是设置网关实例。

虽然可在同一本地计算机或云 VM 实例上安装网关和 SQL Server 以提高性能，但建议将二者安装在不同的计算机上。 在不同的计算机上安装网关和 SQL Server 可减少资源争用。

## <a name="copy-data-wizard"></a>复制数据向导
若要创建将数据从 SQL Server 数据库复制到任何支持的接收数据存储的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 以下示例演示如何将数据复制到 SQL Server 和 Azure Blob 存储，以及如何从 SQL Server 和 Azure Blob 存储复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据**直接**从任何源复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。     

## <a name="sample-copy-data-from-sql-server-to-azure-blob"></a>示例：将数据从 SQL Server 复制到 Azure Blob
以下示例显示：

1. [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该活动使用 [WebSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例每隔一小时将时序数据从 SQL Server 表复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

**SQL Server 链接服务**

    {
      "Name": "SqlServerLinkedService",
      "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
          "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
          "gatewayName": "<gatewayname>"
        }
      }
    }

**Azure Blob 存储链接服务**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**SQL Server 输入数据集**

该示例假定已在 SQL Server 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。 可在使用单个数据集的同一数据库中对多个表进行查询，但必须对数据集的 tableName typeProperty 使用单个表。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

    {
      "name": "SqlServerInput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
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

**Azure Blob 输出数据集**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

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

**包含复制活动的管道**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **BlobSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " SqlServerInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


在本例中，为 SqlSource 指定了 **sqlReaderQuery**。 复制活动针对 SQL Server 数据库源运行此查询以获取数据。 此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。 SqlReaderQuery 可以引用输入数据集所引用的数据库中的多个表。 这不仅局限于设置为数据集的 tableName typeProperty 的表。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在结构部分中定义的列来生成针对 SQL Server 数据库运行的 select 查询。 如果数据集定义不具备该结构，则从表中选择所有列。

有关 SqlSource 和 BlobSink 支持的属性列表，请参阅 [Sql Source](#sqlsource) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 部分。

## <a name="sample-copy-data-from-azure-blob-to-sql-server"></a>示例：将数据从 Azure Blob 复制到 SQL Server
以下示例显示：

1. [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，该活动使用 [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 和 [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties)。

此示例每隔一小时将时序数据从 Azure blob 复制到 SQL Server 表。 示例后续部分描述了这些示例中使用的 JSON 属性。

**SQL Server 链接服务**

    {
      "Name": "SqlServerLinkedService",
      "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
          "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
          "gatewayName": "<gatewayname>"
        }
      }
    }

**Azure Blob 存储链接服务**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure Blob 输入数据集**

每小时从新的 blob 获取数据一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态评估 blob 的文件夹路径和文件名。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

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

**SQL Server 输出数据集**

此示例将数据复制到 SQL Server 中名为“MyTable”的表。 在 SQL Server 中创建表，其列数与 Blob CSV 文件应包含的列数相同。 每小时向该表添加新行。

    {
      "name": "SqlServerOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**包含复制活动的管道**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **SqlSink**。

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": " SqlServerOutput "
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

## <a name="sql-server-linked-service-properties"></a>SQL Server 链接服务属性
在示例中，已使用 **OnPremisesSqlServer** 类型的链接服务将 SQL Server 数据库链接到数据工厂。 下表提供了有关特定于本地 SQL Server 链接服务的 JSON 元素的描述。

下表提供了有关特定于 SQL Server 链接服务的 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为：**OnPremisesSqlServer**。 |是 |
| connectionString |指定使用 SQL 身份验证或 Windows 身份验证连接到本地 SQL Server 数据库时所需的 connectionString 信息。 |是 |
| gatewayName |网关的名称 - 数据工厂服务应使用此网关连接到本地 SQL Server 数据库。 |是 |
| username |如果使用的是 Windows 身份验证，请指定用户名。 示例：**域名\\用户名**。 |否 |
| password |指定为用户名指定的用户帐户的密码。 |否 |

可使用 **New-AzureRmDataFactoryEncryptValue** cmdlet 加密凭据，并在连接字符串中使用凭据，如下例所示（**EncryptedCredential** 属性）：  

    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### <a name="samples"></a>示例
**用于实现 SQL 身份验证使用的 JSON**

    {
        "name": "MyOnPremisesSQLDB",
        "properties":
        {
            "type": "OnPremisesSqlLinkedService",
            "typeProperties": {
                "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
                "gatewayName": "<gateway name>"
            }
        }
    }

**用于实现 Windows 身份验证使用的 JSON**

如果指定了用户名和密码，网关会使用用户名和密码模拟指定的用户帐户来连接到本地 SQL Server 数据库。 否则，网关使用网关（其启动帐户）的安全上下文直接连接到 SQL Server。

    {
         "Name": " MyOnPremisesSQLDB",
         "Properties":
         {
             "type": "OnPremisesSqlLinkedService",
             "typeProperties": {
                 "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
                 "username": "<domain\\username>",
                 "password": "<password>",
                 "gatewayName": "<gateway name>"
            }
         }
    }


请参阅[设置凭据和安全](data-factory-data-management-gateway.md#encrypting-credentials)，了解有关设置 SQL Server 数据源的凭据的详细信息。

## <a name="sql-server-dataset-type-properties"></a>SQL Server 数据集类型属性
在示例中，已使用 **SqlServerTable** 类型的数据集来表示 SQL Server 数据库中的表。  

有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（SQL Server、Azure blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分类似。

每种数据集的 typeProperties 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **SqlServerTable** 类型的数据集的 **typeProperties** 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| tableName |链接服务所引用的 SQL Server 数据库实例中的表或视图的名称。 |是 |

## <a name="sql-server-copy-activity-type-properties"></a>SqlServerTable 复制活动类型属性
若要从 SQL Server 数据库移动数据，请在复制活动中将源类型设置为 **SqlSource**。 同样，若要将数据移动到 SQL Server 数据库，请在复制活动中将接收器类型设置为 **SqlSink**。 本部分提供 SqlSource 和 SqlSink 支持的属性列表。

有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

> [!NOTE]
> 复制活动只使用一个输入，只生成一个输出。
>
>

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

### <a name="sqlsource"></a>SqlSource
复制活动中源的类型为 **SqlSource**时，则可在 **typeProperties** 节中使用以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自定义查询读取数据。 |SQL 查询字符串。 例如，select * from MyTable。 可引用输入数据集所引用的数据库中的多个表。 如果未指定，执行的 SQL 语句为：select from MyTable。 |否 |
| sqlReaderStoredProcedureName |从源表读取数据的存储过程名。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |

如果为 SqlSource 指定 **sqlReaderQuery**，则复制活动针对 SQL Server 数据库源运行此查询以获取数据。

此外，也可以通过指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 来指定存储过程（如果存储过程使用参数）。

如果不指定 sqlReaderQuery 或 sqlReaderStoredProcedureName，则使用在结构部分中定义的列来生成针对 SQL Server 数据库运行的 select 查询。 如果数据集定义不具备该结构，则从表中选择所有列。

> [!NOTE]
> 使用 **sqlReaderStoredProcedureName** 时，仍需指定数据集 JSON 中 **tableName** 属性的值。 但不会对此表执行任何验证。
>
>

### <a name="sqlsink"></a>SqlSink
**SqlSink** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。 |timespan<br/><br/> 示例：“00:30:00”（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时将数据插入 SQL 表。 |整数（行数） |否（默认值：10000） |
| sqlWriterCleanupScript |指定复制活动要执行的查询，以便清除特定切片的数据。 有关详细信息，请参阅[重复性](#repeatability-during-copy)部分。 |查询语句。 |否 |
| sliceIdentifierColumnName |指定复制活动要使用自动生成的切片标识符进行填充的列名，该标识符用于重新运行时清除特定切片的数据。 有关详细信息，请参阅[重复性](#repeatability-during-copy)部分。 |数据类型为 binary(32) 的列的列名。 |否 |
| sqlWriterStoredProcedureName |在目标表中更新/插入数据的存储过程的名称。 |存储过程的名称。 |否 |
| storedProcedureParameters |存储过程的参数。 |名称/值对。 参数的名称和大小写必须与存储过程参数的名称和大小写匹配。 |否 |
| sqlWriterTableType |指定要在存储过程中使用的表类型名称。 通过复制活动，使移动数据在具备此表类型的临时表中可用。 然后，存储过程代码可合并复制数据和现有数据。 |表类型名称。 |否 |

## <a name="troubleshooting-connection-issues"></a>连接问题故障排除
1. 将 SQL Server 配置为接受远程连接。 启动 **SQL Server Management Studio**，右键单击“服务器”，然后单击“属性”。 从列表选择“连接”，然后选中“允许远程连接到此服务器”。

    ![启用远程连接](.\\media\\data-factory-sqlserver-connector\\AllowRemoteConnections.png)

    有关详细步骤，请参阅[配置远程访问服务器配置选项](https://msdn.microsoft.com/library/ms191464.aspx)。
2. 启动 **SQL Server 配置管理器**。 针对所需实例展开“SQL Server 网络配置”，然后选择“MSSQLSERVER 的协议”。 应会在右侧窗格中看到协议。 右键单击“TCP/IP”，然后单击“启用”，可启用 TCP/IP。

    ![启用 TCP/IP](.\\media\\data-factory-sqlserver-connector\\EnableTCPProptocol.png)

    有关详细信息和启用 TCP/IP 协议的其他方法，请参阅[启用或禁用服务器网络协议](https://msdn.microsoft.com/library/ms191294.aspx)。
3. 在同一窗口中，双击“TCP/IP”以启动“TCP/IP 属性”窗口。
4. 切换到“IP 地址”选项卡。 向下滚动以查看 **IPAll** 部分。 记录下 **TCP 端口**（默认值是 **1433**）。
5. 在计算机上创建 **Windows 防火墙规则**，以便允许通过此端口传入流量。  
6. **验证连接**：若要使用完全限定名称连接到 SQL Server，请从另一台计算机使用 SQL Server Management Studio。 例如：“<machine>.<domain>.corp.<company>.com,1433”。

   > [!IMPORTANT]

   > 有关详细信息，请参阅[使用数据管理网关在本地源与云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)。
   >
   > 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
   >
   >


## <a name="identity-columns-in-the-target-database"></a>目标数据库中的标识列
本部分提供一个示例，该示例演示将数据从没有标识列的源表复制到具有标识列的目标表。

**源表：**

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**目标表：**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


请注意，目标表包含标识列。

**源数据集 JSON 定义**

    {
        "name": "SampleSource",
        "properties": {
            "published": false,
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**数据集 JSON 定义**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }
    }


请注意，源表和目标表具有不同架构（目标表具有一个额外标识列）。 在本方案中，需在不包含标识列的目标数据集定义中指定 **structure** 属性。

然后，将源数据集中的列映射到目标数据集中的列。 有关示例，请参阅[列映射示例](#column-mapping-samples)部分。

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[!INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server-azure-sql"></a>SQL Server 和 Azure SQL 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文中所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

将数据移入和移出 Azure SQL、SQL Server、Sybase 时，使用以下映射实现从 SQL 类型到 .NET 类型的转换，反之亦然。

此映射与用于 ADO.NET 的 SQL Server 数据类型映射相同。

| SQL Server 数据库引擎类型 | .NET Framework 类型 |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| 日期 |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM 特性 (varbinary(max)) |Byte[] |
| Float |Double |
| 图像 |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |小数 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
| sql_variant |对象 * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。



<!--HONumber=Nov16_HO3-->


