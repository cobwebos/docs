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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 63e4c654409651f6655da1bed6ab2f544cf024dd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-azure-data-factory-and-pipeline-using-powershell"></a>使用 PowerShell 创建 Azure 数据工厂和管道
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [版本 2 - 预览版](quickstart-create-data-factory-powershell.md)

此快速入门介绍了如何使用 PowerShell 创建 Azure 数据工厂。 在此数据工厂中创建的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：使用 Spark 来转换数据](transform-data-using-spark.md)。 

本文不提供数据工厂服务的详细介绍。 有关 Azure 数据工厂服务的介绍，请参阅 [Azure 数据工厂简介](introduction.md)。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="prerequisites"></a>先决条件

### <a name="azure-subscription"></a>Azure 订阅
如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

### <a name="azure-storage-account"></a>Azure 存储帐户
在本快速入门中，请使用通用的 Azure 存储帐户（具体说来就是 Blob 存储）作为 **source** 和 **sink/destination** 数据存储。 如果没有通用的 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)创建一个。 

#### <a name="get-storage-account-name-and-account-key"></a>获取存储帐户名称和帐户密钥
在本快速入门中，请使用 Azure 存储帐户的名称和密钥。 以下过程提供的步骤用于获取存储帐户的名称和密钥。 

1. 启动 Web 浏览器并导航到 [Azure 门户](https://portal.azure.com)。 使用 Azure 用户名和密码登录。 
2. 单击左侧菜单中的“更多服务 >”，使用“存储”关键字进行筛选，然后选择“存储帐户”。

    ![搜索存储帐户](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. 在存储帐户列表中，通过筛选找出你的存储帐户（如果需要），然后选择**你的存储帐户**。 
4. 在“存储帐户”页的菜单上选择“访问密钥”。

    ![获取存储帐户名称和密钥](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. 将“存储帐户名称”和“key1”字段的值复制到剪贴板。 将这些值粘贴到记事本或任何其他编辑器中，然后进行保存。  

#### <a name="create-input-folder-and-files"></a>创建 input 文件夹和文件
此部分在 Azure Blob 存储中创建名为 adftutorial 的 Blob 容器。 然后，在容器中创建名为 input 的文件夹，再将示例文件上传到 input 文件夹。 

1. 安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)（如果尚未在计算机上安装）。 
2. 在计算机上启动 **Microsoft Azure 存储资源管理器**。   
3. 在“连接到 Azure 存储”窗口中选择“使用存储帐户名称和密钥”，然后单击“下一步”。 如果看不到“连接到 Azure 存储”窗口，请右键单击树状视图中的“存储帐户”，然后单击“连接到 Azure 存储”。 

    ![连接到 Azure 存储](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. 在“使用名称和密钥进行附加”窗口中，粘贴在上一步保存的“帐户名称”和“帐户密钥”。 然后单击“下一步”。 
5. 在“连接摘要”窗口中单击“连接”。
6. 确认你在树状视图的“(本地和附加)” -> “存储帐户”下看到了自己的存储帐户。 
7. 展开“Blob 容器”，确认 **adftutorial** Blob 容器不存在。 如果该容器已存在，则跳过创建该容器的后续步骤。 
8. 右键单击“Blob 容器”，并选择“创建 Blob 容器”。

    ![创建 Blob 容器](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. 输入 **adftutorial** 作为名称，然后按 **ENTER**。 
10. 确认 **adftutorial** 容器在树状视图中已选中。 
11. 在工具栏中单击“新建文件夹”。 

    ![创建文件夹按钮](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. 在“创建新的虚拟目录”窗口中，输入 **input** 作为**名称**，然后单击“确定”。 

    ![“创建目录”对话框](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. 启动**记事本**，创建包含以下内容的名为 **emp.txt** 的文件： 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    将其保存在 **c:\ADFv2QuickStartPSH** 文件夹中：如果 **ADFv2QuickStartPSH** 文件夹不存在，请创建。 
14. 单击工具栏中的“上传”按钮，选择“上传文件”。 

    ![“上传”按钮](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. 在“上传文件”窗口中，选择 `...` 作为“文件”。 
16. 在“选择要上传的文件夹”窗口中，导航到包含 **emp.txt** 的文件夹，然后选择该文件。 

    ![“上传文件”对话框](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. 在“上传文件”窗口中单击“上传”。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>安装 Azure PowerShell
安装最新的 Azure PowerShell（如果未在计算机上安装）。 

1. 在 Web 浏览器中导航到 [Azure SDK 下载和 SDK](https://azure.microsoft.com/downloads/)页。 
2. 在“命令行工具” -> “PowerShell”部分单击“Windows 安装”。 
3. 若要安装 Azure PowerShell，请运行 **MSI** 文件。 

有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-azure-powershell"></a>登录到 Azure PowerShell
在计算机上启动 **PowerShell**。 在完成本快速入门之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行这些命令。

1. 运行以下命令，并输入用于登录 Azure 门户的 Azure 用户名和密码：
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. 如果有多个 Azure 订阅，请运行以下命令，查看此帐户的所有订阅：

    ```powershell
    Get-AzureRmSubscription
    ```
3. 运行以下命令选择要使用的订阅。 请将 **SubscriptionId** 替换为自己的 Azure 订阅的 ID：

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>创建数据工厂
1. 为资源组名称定义一个变量，稍后会在 PowerShell 命令中使用该变量。 将以下命令文本复制到 PowerShell，在双引号中指定 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称，然后运行命令。 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. 为数据工厂名称定义一个变量，稍后可在 PowerShell 命令中使用该变量。 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. 定义一个用于数据工厂位置的变量： 

    ```powershell
    $location = "East US"
    ```
4. 若要创建 Azure 资源组，请运行以下命令： 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    如果该资源组已存在，请勿覆盖它。 为 `$resourceGroupName` 变量分配另一个值，然后重试。 若要与他人共享此资源组，请继续执行下一步。 
5. 若要创建数据工厂，请运行以下 **Set-AzureRmDataFactoryV2** cmdlet： 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* 只有 Azure 订阅的**参与者**或**管理员**才可以创建数据工厂实例。
* 目前，数据工厂版本 2 仅允许在“美国东部”、“美国东部 2”和“西欧”区域创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

## <a name="create-a-linked-service"></a>创建链接服务

在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在此快速入门中，只需要创建一个将同时用作源和接收器存储的 Azure 存储链接服务，在此示例中名为“AzureStorageLinkedService”。

1. 在 **C:\ADFv2QuickStartPSH** 文件夹中（如果 ADFv2QuickStartPSH 文件夹尚不存在，则创建它）创建一个名为 **AzureStorageLinkedService.json** 的 JSON 文件，使其包含以下内容。 

    > [!IMPORTANT]
    > 在保存此文件之前，请将 &lt;accountName&gt; 和 &lt;accountKey&gt; 替换为你的 Azure 存储帐户的名称和密钥。

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

定义一个数据集来表示要从源复制到接收器的数据。 在本示例中，此 Blob 数据集引用在上一步中创建的 Azure 存储链接服务。 此数据集采用一个参数，其值在使用此数据集的活动中设置。 该参数用来构造 **folderPath**，该项指向数据的驻留/存储位置。

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
  
在此示例中，此管道包含一个活动并采用两个参数 - 输入 blob 路径和输出 blob 路径。 这些参数的值是在触发/运行管道时设置的。 复制活动使用在上一步中创建的同一 blob 数据集作为输入和输出。 当该数据集用作输入数据集时，即指定了输入路径。 并且，当该数据集用作输出数据集时，即指定了输出路径。 

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

    如果使用不同的容器和文件夹，请将 **inputPath** 和 **outputPath** 的值替换为源和接收器 blob 路径。

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
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
该管道自动在 adftutorial Blob 容器中创建 output 文件夹， 然后将 emp.txt 文件从 input 文件夹复制到 output 文件夹。 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)检查 inputBlobPath 中的 blob 是否已复制到 outputBlobPath。 

## <a name="clean-up-resources"></a>清理资源
可以通过两种方式清理在快速入门中创建的资源。 可以删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，其中包括资源组中的所有资源。 若要使其他资源保持原封不动，请仅删除在此教程中创建的数据工厂。

运行以下命令可以删除整个资源组： 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

运行以下命令可以仅删除数据工厂： 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 完成相关[教程](tutorial-copy-data-dot-net.md)来了解如何在更多方案中使用数据工厂。 
