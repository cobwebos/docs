---
title: PowerShell 脚本：使用数据工厂将数据从本地复制到 Azure | Microsoft Docs
description: 此 PowerShell 脚本将数据从本地 SQL Server 数据库复制到其他 Azure Blob 存储。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 1218408bee067d6edb274fdcbf4fae62b8245a01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160665"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>使用 PowerShell 创建用于将数据从本地复制到 Azure 的数据工厂管道

本 PowerShell 脚本示例在 Azure 数据工厂中创建管道，该管道将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>必备组件

- **SQL Server**。 在本示例中，将本地 SQL Server 数据库用作源数据存储。
- **Azure 存储帐户**。 本示例使用 Azure Blob 存储作为“目标/接收器”数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-quickstart-create-account.md)一文获取创建步骤。
- 自承载集成运行时。 从[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并运行 MSI 文件，在计算机上安装自承载集成运行时。  

### <a name="create-sample-database-in-sql-server"></a>在 SQL Server 中创建示例数据库
1. 在本地 SQL Server 数据库中，使用以下 SQL 脚本创建名为“emp”的表： 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. 在该表中插入一些示例数据：

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>示例脚本

> [!IMPORTANT]
> 此脚本在硬盘驱动器上的 c:\ 文件夹中创建 JSON 文件，用于定义数据工厂实体（链接服务、数据集和管道）。

```powershell
$resourceGroupName = "<Resource group name>"
$dataFactoryName = "<Data factory name>" # must be globally unique
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$sqlServerName = "<SQL server name>"
$sqlDatabaseName = "SQL Server database name"
$sqlTableName = "emp" # create the emp table if it does not already exist in your database with ID, FirstName, and LastName columns of type String. 
$sqlUserName = "<SQL Authentication - user name>"
$sqlPassword = "<SQL Authentication - user password>"
$blobFolderPath = "<Azure blob container name>/<Azure blob folder name>"
$integrationRuntimeName = "<Self-hosted integration runtime name"
$pipelineName = "SqlServerToBlobPipeline"
$dataFactoryRegion = "China East"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion

# create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Name $dataFactoryName -Location $dataFactoryRegion

# create a self-hosted integration runtime
Set-AzDataFactoryIntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName

# get the authorization key from the created integration runtime in the cloud
Get-AzDataFactoryIntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json

# IMPORTANT: Install self-hosted integration runtime on your machine and use one of the keys to register the IR installed on your machine with the cloud service

# create an Az.Storage linked service

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\AzureStorageLinkedService.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File c:\AzureStorageLinkedService.json

# create an on-premises SQL Server linked service

## JSON definition of the linked service. 
$sqlServerLinkedServiceDefinition = @"
{
   "properties": {
     "type": "SqlServer",
     "typeProperties": {
         "connectionString": {
             "type": "SecureString",
            "value": "Server=$sqlServerName;Database=$sqlDatabaseName;User ID=$sqlUserName;Password=$sqlPassword;Timeout=60"
         }
     },
     "connectVia": {
       "type": "integrationRuntimeReference",
       "referenceName": "$integrationRuntimeName"
     }
 },
 "name": "SqlServerLinkedService"
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$sqlServerLinkedServiceDefinition | Out-File c:\SqlServerLinkedService.json

## Encrypt SQL Server credentials 
New-AzDataFactoryLinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File "c:\SqlServerLinkedService.json" > c:\EncryptedSqlServerLinkedService.json

# Create a SQL Server linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName "EncryptedSqlServerLinkedService" -File "c:\EncryptedSqlServerLinkedService.json"


# Create a source dataset for source SQL Server Database

## JSON definition of the dataset
$sourceSqlServerDatasetDefiniton = @"
{
   "properties": {
        "type": "SqlServerTable",
        "typeProperties": {
            "tableName": "$sqlTableName"
        },
        "structure": [
             {
                "name": "ID",
                "type": "String"
            },
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
        ],
        "linkedServiceName": {
            "referenceName": "EncryptedSqlServerLinkedService",
            "type": "LinkedServiceReference"
        }
    },
    "name": "SqlServerDataset"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceSqlServerDatasetDefiniton | Out-File c:\SqlServerDataset.json

# Create an Azure Blob dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File "c:\SqlServerDataset.json"

# Create a dataset for sink Azure Blob Storage

## JSON definition of the dataset
$sinkBlobDatasetDefiniton = @"
{
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$blobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    },
    "name": "AzureBlobDataset"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkBlobDatasetDefiniton | Out-File c:\AzureBlobDataset.json

## Create the Azure Blob dataset
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File "c:\AzureBlobDataset.json"


# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
   "name": "$pipelineName",
    "properties": {
        "activities": [       
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type":"BlobSink"
                    }
                },
                "name": "CopySqlServerToAzureBlobActivity",
                "inputs": [
                    {
                        "referenceName": "SqlServerDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "AzureBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ]
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\SqlServerToBlobPipeline.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\SqlServerToBlobPipeline.json"


# start the pipeline run
$runId = Invoke-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName

# Check the pipeline run status until it finishes the copy operation
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline $pipelineName run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
        -PipelineRunId $runId `
        -RunStartedAfter (Get-Date).AddMinutes(-10) `
        -RunStartedBefore (Get-Date).AddMinutes(10) `
        -ErrorAction Stop

    $result

    if ($result.Status -eq "Succeeded") {`
        $result.Output -join "`r`n"`
    }`
    else {`
        $result.Error -join "`r`n"`
    }

# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```


## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可以使用以下命令删除资源组以及与其关联的所有资源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要从资源组中删除数据工厂，请运行以下命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 创建数据工厂。 |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | 在链接的服务中对凭据进行加密，并使用加密凭据生成新的链接服务定义。 
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 在数据工厂中创建链接服务。 链接服务可将数据存储或计算链接到数据工厂。 |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 在数据工厂中创建数据集。 数据集表示管道中活动的输入/输出。 | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 在数据工厂中创建管道。 一个管道包含一个或多个执行某项操作的活动。 在此管道中，复制活动在 Azure Blob 存储中将数据从一个位置复制到另一个位置。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 为管道创建运行。 换而言之，就是运行管道。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 获取管道中活动的运行（活动运行）的相关详细信息。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 数据工厂 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 数据工厂 PowerShell 脚本示例。
