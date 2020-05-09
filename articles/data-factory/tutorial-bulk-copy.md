---
title: 批量复制数据
description: 了解如何使用 Azure 数据工厂和复制活动将源数据存储中的数据批量复制到目标数据存储。
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: 0f73095f72d07989cdfa309454a2b54efa8e5f95
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418745"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>使用 Azure 数据工厂批量复制多个表

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本教程演示如何**将 Azure SQL 数据库中的多个表复制到 Azure SQL 数据仓库**。 在其他复制方案中，也可以应用相同的模式。 例如，将 SQL Server/Oracle 中的表复制到 Azure SQL 数据库/数据仓库/Azure Blob，将 Blob 中的不同路径复制到 Azure SQL 数据库表。

从较高层面讲，本教程涉及以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建 Azure SQL 数据库、Azure SQL 数据仓库和 Azure 存储链接服务。
> * 创建 Azure SQL 数据库和 Azure SQL 数据仓库数据集。
> * 创建一个管道用于查找要复制的表，创建另一个管道用于执行实际复制操作。 
> * 启动管道运行。
> * 监视管道和活动运行。

本教程使用 Azure PowerShell。 若要了解如何使用其他工具/SDK 创建数据工厂，请参阅[快速入门](quickstart-create-data-factory-dot-net.md)。 

## <a name="end-to-end-workflow"></a>端到端工作流
在本方案中，Azure SQL 数据库中包含一些我们想要复制到 SQL 数据仓库的表。 下面是管道中发生的工作流中的逻辑步骤顺序：

![工作流](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* 第一个管道查找需要复制到接收器数据存储的表列表。  也可以维护一个元数据表用于列出要复制到接收器数据存储的所有表。 然后，该管道触发另一个管道，后者循环访问数据库中的每个表并执行数据复制操作。
* 第二个管道执行实际复制。 它使用表列表作为参数。 对于列表中的每个表，为获得最佳性能，会使用[通过 Blob 存储和 PolyBase 进行的分阶段复制](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)，将 Azure SQL 数据库中的特定表复制到 SQL 数据仓库中的相应表。 在本示例中，第一个管道传递表列表作为参数值。 

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-Az-ps) 中的说明。
* **Azure 存储帐户**。 Azure 存储帐户用作批量复制操作中的过渡 Blob 存储。 
* **Azure SQL 数据库**。 此数据库包含源数据。 
* **Azure SQL 数据仓库**。 此数据仓库包含从 SQL 数据库复制的数据。 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>准备 SQL 数据库和 SQL 数据仓库

**准备源 Azure SQL 数据库**：

遵循[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文，使用 Adventure Works LT 示例数据创建 Azure SQL 数据库。 本教程将此示例数据库中的所有表复制到 SQL 数据仓库。

**准备接收器 Azure SQL 数据仓库**：

1. 如果没有 Azure SQL 数据仓库，请参阅[创建 Azure SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)一文了解创建数据仓库的步骤。

2. 在 SQL 数据仓库中创建相应的表架构。 后面的步骤使用 Azure 数据工厂迁移/复制数据。

## <a name="azure-services-to-access-sql-server"></a>Azure 服务访问 SQL 服务器

对于 SQL 数据库和 SQL 数据仓库，请允许 Azure 服务访问 SQL 服务器。 确保针对 Azure SQL 服务器，将“允许访问 Azure 服务”设置切换为“打开”状态。   此设置允许数据工厂服务从 Azure SQL 数据库中读取数据，并将数据写入 Azure SQL 数据仓库。 若要验证并启用此设置，请执行以下步骤：

1. 单击左侧的“所有服务”  ，然后单击“SQL Server”  。
2. 选择服务器，并单击“设置”  下的“防火墙”  。
3. 在“防火墙设置”页中，单击“允许访问 Azure 服务”对应的“打开”。   

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **PowerShell**。 在本教程结束之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。

    运行以下命令并输入用于登录 Azure 门户的用户名和密码：
        
    ```powershell
    Connect-AzAccount
    ```
    运行以下命令查看此帐户的所有订阅：

    ```powershell
    Get-AzSubscription
    ```
    运行以下命令选择要使用的订阅。 请将 **SubscriptionId** 替换为自己的 Azure 订阅的 ID：

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. 运行 **Set-AzDataFactoryV2** cmdlet 创建数据工厂。 执行该命令之前，请将占位符替换为自己的值。 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    请注意以下几点：

    * Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * 只有 Azure 订阅的参与者或管理员才可以创建数据工厂实例。
    * 若要查看目前提供数据工厂的 Azure 区域的列表，请在以下页面上选择感兴趣的区域，然后展开“分析”  以找到“数据工厂”  ：[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/)。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

## <a name="create-linked-services"></a>创建链接服务

本教程分别为源、接收器和过渡 Blob 创建了三个链接服务，其中包括数据存储的连接：

### <a name="create-the-source-azure-sql-database-linked-service"></a>创建源 Azure SQL 数据库链接服务

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **AzureSqlDatabaseLinkedService.json** 的 JSON 文件：（如果 ADFv2TutorialBulkCopy 文件夹尚不存在，则创建该文件夹。）

    > [!IMPORTANT]
    > 保存文件之前，请将 &lt;servername&gt;、&lt;databasename&gt;、&lt;username&gt;@&lt;servername&gt; 和 &lt;password&gt; 替换为 Azure SQL 数据库的值。

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. 在 **Azure PowerShell** 中，切换到 **ADFv2TutorialBulkCopy** 文件夹。

3. 运行 **Set-AzDataFactoryV2LinkedService** cmdlet 来创建链接服务：**AzureSqlDatabaseLinkedService**。 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    下面是示例输出：

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>创建接收器 Azure SQL 数据仓库链接服务

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **AzureSqlDWLinkedService.json** 的 JSON 文件：

    > [!IMPORTANT]
    > 保存文件之前，请将 &lt;servername&gt;、&lt;databasename&gt;、&lt;username&gt;@&lt;servername&gt; 和 &lt;password&gt; 替换为 Azure SQL 数据库的值。

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. 若要创建链接服务 **AzureSqlDWLinkedService**，请运行 **Set-AzDataFactoryV2LinkedService** cmdlet。

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    下面是示例输出：

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>创建过渡 Azure 存储链接服务

本教程使用 Azure Blob 存储作为临时过渡区域，以利用 PolyBase 来实现更好的复制性能。

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **AzureStorageLinkedService.json** 的 JSON 文件：

    > [!IMPORTANT]
    > 将 &lt;accountName&gt; 和 &lt;accountKey&gt; 分别替换为 Azure 存储帐户的名称和密钥，然后保存文件。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```

2. 若要创建链接服务 **AzureStorageLinkedService**，请运行 **Set-AzDataFactoryV2LinkedService** cmdlet。

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    下面是示例输出：

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>创建数据集

在本教程中创建源和接收器数据集，用于指定数据的存储位置：

### <a name="create-a-dataset-for-source-sql-database"></a>为源 SQL 数据库创建数据集

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **AzureSqlDatabaseDataset.json** 的 JSON 文件。 “tableName”是一个虚构名称，因为稍后要在复制活动中使用 SQL 查询检索数据。

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. 若要创建数据集 **AzureSqlDatabaseDataset**，请运行 **Set-AzDataFactoryV2Dataset** cmdlet。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    下面是示例输出：

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>为接收器 SQL 数据仓库创建数据集

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **AzureSqlDWDataset.json** 的 JSON 文件：将“tableName”设置为参数，稍后引用此数据集的复制活动会将实际值传递给数据集。

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. 若要创建数据集 **AzureSqlDWDataset**，请运行 **Set-AzDataFactoryV2Dataset** cmdlet。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    下面是示例输出：

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>创建管道

在本教程中创建两个管道：

### <a name="create-the-pipeline-iterateandcopysqltables"></a>创建管道“IterateAndCopySQLTables”

此管道使用表列表作为参数。 对于列表中的每个表，此管道会使用分阶段复制和 PolyBase，将 Azure SQL 数据库中的表的数据复制到 Azure SQL 数据仓库。

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **IterateAndCopySQLTables.json** 的 JSON 文件：

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. 若要创建管道 **IterateAndCopySQLTables**，请运行 **Set-AzDataFactoryV2Pipeline** cmdlet。

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    下面是示例输出：

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>创建管道“GetTableListAndTriggerCopyData”

此管道执行两个步骤：

* 查找 Azure SQL 数据库系统表，以获取要复制的表列表。
* 触发管道“IterateAndCopySQLTables”来执行实际数据复制。

1. 在 **C:\ADFv2TutorialBulkCopy** 文件夹中，创建包含以下内容的名为 **GetTableListAndTriggerCopyData.json** 的 JSON 文件：

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. 若要创建管道 **GetTableListAndTriggerCopyData**，请运行 **Set-AzDataFactoryV2Pipeline** cmdlet。

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    下面是示例输出：

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>启动并监视管道运行

1. 针对“GetTableListAndTriggerCopyData”主管道启动管道运行，并捕获管道运行 ID，以便将来进行监视。 随后，此管道根据 ExecutePipeline 活动中的指定，触发管道“IterateAndCopySQLTables”的运行。

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  运行以下脚本可持续检查管道 **GetTableListAndTriggerCopyData** 的运行状态，并列显最终管道运行和活动运行的结果。

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    下面是示例运行的输出：

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 可以获取管道“**IterateAndCopySQLTables**”的运行 ID，并检查详细的活动运行结果，如下所示。

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    下面是示例运行的输出：

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. 连接到接收器 Azure SQL 数据仓库，并确认是否已正确地从 Azure SQL 数据库复制数据。

## <a name="next-steps"></a>后续步骤
已在本教程中执行了以下步骤： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建 Azure SQL 数据库、Azure SQL 数据仓库和 Azure 存储链接服务。
> * 创建 Azure SQL 数据库和 Azure SQL 数据仓库数据集。
> * 创建一个管道用于查找要复制的表，创建另一个管道用于执行实际复制操作。 
> * 启动管道运行。
> * 监视管道和活动运行。

转到以下教程，了解如何以增量方式将数据从源复制到目标：
> [!div class="nextstepaction"]
>[以增量方式复制数据](tutorial-incremental-copy-powershell.md)
