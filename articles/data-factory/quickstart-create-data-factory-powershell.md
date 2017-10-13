---
title: "使用 PowerShell 创建 Azure 数据工厂 | Microsoft Docs"
description: "创建 Azure 数据工厂来将数据从 Azure Blob 存储中的一个位置复制到同一 Blob 存储中的另一位置。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 1e9109581a1943a77e91e7fa034873dc2a15a5e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>使用 PowerShell 创建数据工厂和管道
Azure 数据工厂是基于云的数据集成服务，用于在云中创建数据驱动型工作流，以便协调和自动完成数据移动和数据转换。 使用 Azure 数据工厂，可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据，通过各种计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据，将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。 

此快速入门介绍了如何使用 PowerShell 创建 Azure 数据工厂。 此数据工厂中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

* **Azure 存储帐户**。 可以将 blob 存储同时用作**源**和**接收器**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)创建一个。 
* 在 Blob 存储中创建一个 **blob 容器**，在该容器中创建一个输入**文件夹**，并向该文件夹上传一些文件。 可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具连接到 Azure Blob 存储、创建 Blob 容器、上传输入文件，以及验证输出文件。
* **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **PowerShell**。 在完成本快速入门之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。

    运行以下命令并输入用于登录 Azure 门户的用户名和密码：
        
    ```powershell
    Login-AzureRmAccount
    ```        
    运行以下命令查看此帐户的所有订阅：

    ```powershell
    Get-AzureRmSubscription
    ```
    运行以下命令选择要使用的订阅。 请将 **SubscriptionId** 替换为自己的 Azure 订阅的 ID：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. 运行 **Set-AzureRmDataFactoryV2** cmdlet 创建数据工厂。 在执行命令前，请将占位符替换为你自己的值。 将**占位符**替换为你自己的值。 

    为资源组名称定义一个变量，稍后可在 PowerShell 命令中使用该变量。 
    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    ```

    为数据工厂名称定义一个变量，稍后可在 PowerShell 命令中使用该变量。 

    ```powershell
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    ```

    运行以下命令创建数据工厂。 
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

    请注意以下几点：

    * Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * 只有 Azure 订阅的参与者或管理员才可以创建数据工厂实例。
    * 目前，数据工厂 V2 仅允许在美国东部区域中创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

## <a name="create-a-linked-service"></a>创建链接服务

在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在此快速入门中，只需要创建一个将同时用作源和接收器存储的 Azure 存储链接服务，在此示例中名为“AzureStorageLinkedService”。

1. 在 **C:\ADFv2QuickStartPSH** 文件夹中（如果 ADFv2QuickStartPSH 文件夹尚不存在，则创建它）创建一个名为 **AzureStorageLinkedService.json** 的 JSON 文件，使其包含以下内容。 在保存此文件之前，请将 &lt;accountName&gt; 和 &lt;accountKey&gt; 替换为你的 Azure 存储帐户的名称和密钥。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. 在 **Azure PowerShell** 中，切换到 **ADFv2QuickStartPSH** 文件夹。

3. 运行 **Set-AzureRmDataFactoryV2LinkedService** cmdlet 创建链接服务：**AzureStorageLinkedService**。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    下面是示例输出：

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>创建数据集

定义一个数据集来表示要从源复制到接收器的数据。 在本示例中，此 Blob 数据集引用在上一步中创建的 Azure 存储链接服务。 此数据集采用一个参数，其值在使用此数据集的活动中设置。 该参数用来构造“folderPath”，该路径指向数据的存储位置。

1. 在 **C:\ADFv2QuickStartPSH** 文件夹中创建一个名为 **BlobDataset.json** 的 JSON 文件，使其包含以下内容：

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. 若要创建数据集 **BlobDataset**，请运行 **Set-AzureRmDataFactoryV2Dataset** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    下面是示例输出：

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>创建管道

在此示例中，此管道包含一个活动并采用两个参数 - 输入 blob 路径和输出 blob 路径。 这些参数的值是在触发/运行管道时设置的。 复制活动引用在上一步中创建的同一 blob 数据集作为输入和输出。 当该数据集用作输入数据集时，即指定了输入路径。 并且，当该数据集用作输出数据集时，即指定了输出路径。 

1. 在 **C:\ADFv2QuickStartPSH** 文件夹中创建一个名为 **Adfv2QuickStartPipeline.json** 的 JSON 文件，使其包含以下内容：

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                        "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. 若要创建管道 **Adfv2QuickStartPipeline**，请运行 **Set-AzureRmDataFactoryV2Pipeline** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    下面是示例输出：

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>创建管道运行

在此步骤中，将为管道参数设置值：将 **inputPath** 和 **outputPath** 的值替换为源和接收器 blob 路径的实际值。 然后，使用这些参数创建管道运行。 

1. 在 **C:\ADFv2QuickStartPSH** 文件夹中创建一个名为 **PipelineParameters.json** 的 JSON 文件，使其包含以下内容：

    在保存该文件之前，请将“inputPath”和“outputPath”的值替换为要从中复制数据和要将数据复制到的源和接收器 blob 路径。

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. 运行 **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet 来创建一个管道运行并传入参数值。 它还捕获管道运行 ID 以用于将来的监视。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>监视管道运行

1. 运行以下脚本来持续检查管道运行状态，直到它完成数据复制。

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    下面是管道运行的示例输出：

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. 运行以下脚本来检索复制活动运行详细信息，例如，读取/写入的数据的大小。

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. 确认你看到了与活动运行结果的以下示例输出类似的输出：

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>验证输出
使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具检查 inputBlobPath 中的 blob 是否已复制到 outputBlobPath。

## <a name="clean-up-resources"></a>清理资源
可以通过两种方式清理在快速入门中创建的资源。 可以删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，其中包括资源组中的所有资源。 若要使其他资源保持原封不动，请仅删除在此教程中创建的数据工厂。

运行以下命令可以删除整个资源组： 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

运行以下命令可以仅删除数据工厂： 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 完成相关[教程](tutorial-copy-data-dot-net.md)来了解如何在更多方案中使用数据工厂。 