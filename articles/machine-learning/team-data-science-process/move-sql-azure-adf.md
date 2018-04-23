---
title: 使用 Azure 数据工厂将数据从本地 SQL Server 移到 SQL Azure | Microsoft Docs
description: 设置一个 ADF 管道，它由两个数据迁移活动组成，这两个活动每天共同在本地和云中的数据库之间移动数据。
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 05884fd39db284e268f31987e5ad7a47b9f87ebf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从本地 SQL 服务器移到 SQL Azure
本主题演示如何使用 Azure 数据工厂 (ADF)，通过 Azure Blob 存储将数据从本地 SQL Server 数据库移到 SQL Azure 数据库。

有关汇总了用于将数据移到 Azure SQL 数据库的各种选项的表格，请参阅[将数据移到 Azure SQL 数据库进行 Azure 机器学习](move-sql-azure.md)。

## <a name="intro"></a>简介：什么是 ADF 以及它应何时用于迁移数据？
Azure 数据工厂是一项完全托管、基于云的数据集成服务，可安排并自动化处理数据的移动和转换。 ADF 模型中的关键概念是管道。 管道是活动的逻辑分组，其中每个活动定义对包含在数据集中的数据所执行的操作。 链接服务用于定义数据工厂连接到数据资源所需的信息。

凭借 ADF，可将现有的数据处理服务整合到数据管道中，该数据管道具有高可用性且托管在云中。 可安排这些数据管道用于引入、准备、转换、分析和发布数据，ADF 可管理并安排复杂数据和处理依赖关系。 可在云中快速生成并部署解决方案，连接越来越多的本地和云数据源。

在以下情况中，请考虑使用 ADF：

* 在同时访问本地和云资源的混合方案中需要不断迁移数据时
* 对数据进行事务处理或需要进行修改，或者在数据迁移过程中对其添加业务逻辑时。

ADF 允许使用简单的 JSON 脚本计划和监视作业，JSON 脚本可定期管理数据移动。 ADF 还具有其他功能，例如支持复杂操作。 有关 ADF 的详细信息，请参阅 [Azure 数据工厂 (ADF)](https://azure.microsoft.com/services/data-factory/) 中的文档。

## <a name="scenario"></a>方案
我们设置了一个由两个数据迁移活动组成的 ADF 管道。 它们可每天共同在本地 SQL 数据库和云中的 Azure SQL 数据库之间移动数据。 这两个活动是：

* 从本地 SQL Server 数据库将数据复制到 Azure Blob 存储帐户
* 从 Azure Blob 存储帐户将数据复制到 Azure SQL 数据库。

> [!NOTE]
> 此处介绍的步骤源于 ADF 团队所提供的更详细的教程：[使用数据管理网关在本地源和云之间移动数据](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) 在适当的时候，将提供该主题相关部分的引用。
>
>

## <a name="prereqs"></a>先决条件
本教程假设你拥有：

* 一个 **Azure 订阅**。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个 **Azure 存储帐户**。 在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)一文。 创建存储帐户后，需要获取用于访问存储的帐户密钥。 请参阅[管理存储访问密钥](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys)。
* 访问 **Azure SQL 数据库**。 如果必须设置 Azure SQL 数据库，可在主题 [Microsoft Azure SQL 数据库入门](../../sql-database/sql-database-get-started.md)中找到相关信息，了解如何预配 Azure SQL 数据库的新实例。
* 已在本地安装和配置 **Azure PowerShell**。 有关说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

> [!NOTE]
> 此过程将使用 [Azure 门户](https://portal.azure.com/)。
>
>

## <a name="upload-data"></a>将数据上传到本地 SQL Server
我们使用 [NYC 出租车数据集](http://chriswhong.com/open-data/foil_nyc_taxi/)来演示迁移过程。 该文章所述的 NYC 出租车数据集在 Azure Blob 存储 [NYC 出租车数据](http://www.andresmh.com/nyctaxitrips/)上可用。 该数据具有两个文件，trip_data.csv 文件（包含行程详情）和 trip_far.csv 文件（包含每次行程的费用详情）。 [NYC 出租车行程数据集说明](sql-walkthrough.md#dataset)中介绍了这些文件的示例和说明。

可将此处提供的流程调整为自己的一组数据，或者使用 NYC 出租车数据集遵循所述的步骤进行操作。 若要将 NYC 出租车数据集上传到本地 SQL Server 数据库，请按照[将数据批量导入 SQL Server 数据库](sql-walkthrough.md#dbload)中概述的过程进行操作。 这些说明适用于 Azure 虚拟机上的 SQL Server，但是上传到本地 SQL Server 的过程是相同的。

## <a name="create-adf"></a>创建 Azure 数据工厂
[创建 Azure 数据工厂](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-a-data-factory)中提供了在 [Azure 门户](https://portal.azure.com/)中创建新的 Azure 数据工厂和资源组的相关说明。 将新的 ADF 实例命名为 *adfdsp*，将创建的资源组命名为 *adfdsprg*。

## <a name="install-and-configure-up-the-data-management-gateway"></a>安装和配置数据管理网关
若要使 Azure 数据工厂中的管道适用于本地 SQL Server，请将其作为链接服务添加到数据工厂。 若要创建适用于本地 SQL Server 的链接服务，必须执行以下操作：

* 下载 Microsoft 数据管理网关，并安装到本地计算机。
* 为本地数据源配置链接服务以使用网关。

数据管理网关在托管其的计算机上对源和接收器数据进行序列化和反序列化。

有关设置说明和数据管理网关的详细信息，请参阅[使用数据管理网关在本地源和云之间移动数据](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>创建链接服务以连接到数据资源
链接服务定义 Azure 数据工厂连接到数据资源所需的信息。 在此方案中，我们有三个需要链接服务的资源：

1. 本地 SQL Server
2. Azure Blob 存储
3. Azure SQL 数据库

[创建链接服务](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services)中提供了创建链接服务的分步过程。


## <a name="adf-tables"></a>定义和创建表以指定访问数据集的方式
使用以下基于脚本的过程，创建指定数据集的结构、位置和可用性的表。 可使用 JSON 文件定义表。 若要深入了解这些文件的结构，请参阅[数据集](../../data-factory/v1/data-factory-create-datasets.md)。

> [!NOTE]
> 在执行 [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet 之前，应先执行 `Add-AzureAccount` cmdlet，以确认是否为命令执行选择了正确的 Azure 订阅。 有关此 cmdlet 的文档，请参阅 [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0)。
>
>

表中基于 JSON 的定义使用以下名称：

* 本地 SQL server 中的“表名”为 nyctaxi_data
* Azure Blob 存储帐户中的**容器名**为 containername  

此 ADF 管道所需的表定义有 3 个：

1. [SQL 本地表](#adf-table-onprem-sql)
2. [Blob 表](#adf-table-blob-store)
3. [SQL Azure 表](#adf-table-azure-sql)

> [!NOTE]
> 这些过程使用 Azure PowerShell 来定义和创建 ADF 活动。 但是，也可使用 Azure 门户来完成这些任务。 有关详细信息，请参阅[创建数据集](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets)。
>
>

### <a name="adf-table-onprem-sql"></a>SQL 本地表
本地 SQL Server 的表定义指定在以下的 JSON 文件中：

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

此处不包括列名称。 可通过将列名称包含在此处以对其进行子选择（有关详细信息，请参阅 [ADF 文档](../../data-factory/v1/data-factory-data-movement-activities.md)主题）。

将表的 JSON 定义复制到名为 onpremtabledef.json 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\onpremtabledef.json*）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建表：

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Blob 表
以下是适用于输出 blob 位置的表的定义（这将引入的数据从本地映射到 Azure blob）：

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

将表的 JSON 定义复制到名为 bloboutputtabledef.json 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\bloboutputtabledef.json*）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建表：

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql"></a>SQL Azure 表
以下是适用于 SQL Azure 输出的表的定义（此架构将映射来自 blob 的数据）：

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

将表的 JSON 定义复制到名为 AzureSqlTable.json 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\AzureSqlTable.json*）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建表：

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>定义和创建管道
使用以下基于脚本的过程，指定属于管道的活动并创建管道。 可使用 JSON 文件定义管道属性。

* 该脚本假设管道名称是 AMLDSProcessPipeline。
* 另请注意：我们将管道的周期设置为每天执行，并且为作业使用默认的执行时间（UTC 的凌晨 12 点）。

> [!NOTE]
> 以下过程使用 Azure PowerShell 来定义和创建 ADF 管道。 但是，也可使用 Azure 门户来完成此任务。 有关详细信息，请参阅[创建管道](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline)。
>
>

使用前面提供的表定义，按以下步骤指定适用于 ADF 的管道定义：

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premises SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",                
                            }            
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

将管道的 JSON 定义复制到名为 *pipelinedef.json* 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\pipelinedef.json*）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建管道：

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>启动管道
现在可使用以下命令来运行管道：

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

需要将 *startdate* 和 *enddate* 参数值替换为想要在此期间运行管道的实际日期。

在管道执行后，应能看到数据显示在为 blob 选择的容器中，每天一个文件。

请注意：我们并未使用 ADF 提供的功能以用管道增量输送数据。 若要深入了解如何执行此操作以及 ADF 提供的其他功能，请参阅 [ADF 文档](https://azure.microsoft.com/services/data-factory/)。
