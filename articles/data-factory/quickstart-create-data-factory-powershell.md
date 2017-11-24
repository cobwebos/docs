---
title: "使用 Azure 数据工厂复制 Blob 存储中的数据 | Microsoft Docs"
description: "创建 Azure 数据工厂，将 Azure Blob 存储的一个文件夹到另一个文件夹中的数据复制到同一 Blob 存储中的另一个位置。"
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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>使用 PowerShell 创建 Azure 数据工厂 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [版本 2 - 预览版](quickstart-create-data-factory-powershell.md)

此快速入门介绍了如何使用 PowerShell 创建 Azure 数据工厂。 在此数据工厂中创建的管道会将数据从 Azure Blob 存储中的一个文件夹**复制**到另一个文件夹。 有关如何使用 Azure 数据工厂**转换**数据的教程，请参阅[教程：使用 Spark 转换数据](transform-data-using-spark.md)。 

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 入门](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
>
> 本文不提供数据工厂服务的详细介绍。 有关 Azure 数据工厂服务的介绍，请参阅 [Azure 数据工厂简介](introduction.md)。

## <a name="prerequisites"></a>先决条件

### <a name="azure-subscription"></a>Azure 订阅
如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

### <a name="azure-roles"></a>Azure 角色
若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于**参与者**或**所有者**角色，或者是 Azure 订阅的**管理员**。 在 Azure 门户中，单击右上角的**用户名**，然后选择“权限”查看你在订阅中拥有的权限。 如果可以访问多个订阅，请选择相应的订阅。 有关将用户添加到角色的示例说明，请参阅[添加角色](../billing/billing-add-change-azure-subscription-administrator.md)一文。

### <a name="azure-storage-account"></a>Azure 存储帐户
在本快速入门中，使用通用的 Azure 存储帐户（具体说来就是 Blob 存储）作为**源**和**目标**数据存储。 如果没有通用的 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)创建一个。 

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
此部分在 Azure Blob 存储中创建名为 **adftutorial** 的 Blob 容器。 然后，在容器中创建名为 **input** 的文件夹，再将示例文件上传到 input 文件夹。 

1. 在“存储帐户”页中切换到“概览”，然后单击“Blob”。 

    ![选择“Blob”选项](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. 在“Blob 服务”页中，单击工具栏上的“+ 容器”。 

    ![“添加容器”按钮](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. 在“新建容器”对话框中，输入 **adftutorial** 作为名称，然后单击“确定”。 

    ![输入容器名称](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. 在容器列表中单击“adftutorial”。 

    ![选择容器](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. 在“容器”页中，单击工具栏上的“上传”。  

    ![“上传”按钮](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. 在“上传 Blob”页中，单击“高级”。

    ![单击“高级”链接](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. 启动**记事本**，创建包含以下内容的名为 **emp.txt** 的文件：将其保存在 **c:\ADFv2QuickStartPSH** 文件夹中（如果 **ADFv2QuickStartPSH** 文件夹不存在，请创建）。
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. 在 Azure 门户的“上传 Blob”页上，浏览并选择 **emp.txt** 文件作为“文件”字段的值。 
9. 输入 **input** 作为“上传到文件夹”字段的值。 

    ![上传 Blob 设置](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. 确认文件夹是 **input**、文件是 **emp.txt**，然后单击“上传”。
11. 应该会在列表中看到 **emp.txt** 文件和上传状态。 
12. 通过单击边角处的“X”关闭“上传 Blob”页。 

    ![关闭“上传 Blob”页](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. 使“容器”页保持打开状态。 在本快速入门结束时可以使用它来验证输出。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>安装 Azure PowerShell
安装最新的 Azure PowerShell（如果未在计算机上安装）。 

1. 在 Web 浏览器中导航到 [Azure SDK 下载和 SDK](https://azure.microsoft.com/downloads/)页。 
2. 在“命令行工具” -> “PowerShell”部分单击“Windows 安装”。 
3. 若要安装 Azure PowerShell，请运行 **MSI** 文件。 

有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-azure-powershell"></a>登录到 Azure PowerShell

1. 在计算机上启动 **PowerShell**。 在完成本快速入门之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行这些命令。

    ![启动 PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. 运行以下命令，并输入用于登录 Azure 门户的同一 Azure 用户名和密码：
       
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
1. 为资源组名称定义一个变量，稍后会在 PowerShell 命令中使用该变量。 将以下命令文本复制到 PowerShell，在双引号中指定 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称，然后运行命令。 例如：`"adfrg"`。
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. 定义一个用于数据工厂名称的变量。 

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
    如果该资源组已存在，请勿覆盖它。 为 `$resourceGroupName` 变量分配另一个值，然后再次运行命令。 
5. 若要创建数据工厂，请运行以下 **Set-AzureRmDataFactoryV2** cmdlet： 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于**参与者**或**所有者**角色，或者是 Azure 订阅的**管理员**。
* 目前，数据工厂版本 2 仅允许在“美国东部”、“美国东部 2”和“西欧”区域创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

## <a name="create-a-linked-service"></a>创建链接服务

在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本快速入门中，请创建一个 Azure 存储链接服务，用作源存储和接收器存储。 链接服务包含的连接信息可供数据工厂服务用来在运行时连接到它。

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
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
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
在此步骤中，请定义一个数据集，表示从源复制到接收器的数据。 数据集为 **AzureBlob** 类型。 它引用在上一步创建的 **Azure 存储链接服务**。 它采用一个参数来构造 **folderPath** 属性。 对于输入数据集，管道中的复制活动将输入路径作为此参数的值来传递。 同样，对于输出数据集，复制活动将输出路径作为此参数的值来传递。 

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
  
在此快速入门中，所创建管道包含的一个活动采用两个参数 - 输入 Blob 路径和输出 Blob 路径。 这些参数的值是在触发/运行管道时设置的。 复制活动使用在上一步中创建的同一 blob 数据集作为输入和输出。 当该数据集用作输入数据集时，即指定了输入路径。 并且，当该数据集用作输出数据集时，即指定了输出路径。 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. 运行 **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet 来创建一个管道运行并传入参数值。 此 cmdlet 返回管道运行 ID，用于将来的监视。

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 运行以下 PowerShell 脚本，持续检查管道运行状态，直到完成数据复制为止。 在 PowerShell 窗口中复制/粘贴以下脚本，然后按 ENTER。 

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

        Start-Sleep -Seconds 10
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

    如果看到此错误：
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    请执行以下步骤： 
    1. 在 AzureStorageLinkedService.json 中，确认 Azure 存储帐户的名称和密钥是否正确。 
    2. 验证连接字符串的格式是否正确。 例如，属性 AccountName 和 AccountKey 通过分号 (`;`) 字符隔开。 
    3. 如果帐户名称和帐户密钥带尖括号，请删除尖括号。 
    4. 下面是一个连接字符串示例： 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. 按[创建链接服务](#create-a-linked-service)部分的步骤重新创建链接服务。 
    6. 按[创建管道运行](#create-a-pipeline-run)部分的步骤重新运行管道。 
    7. 再次运行当前的监视命令，监视新的管道运行。 
1. 运行以下脚本来检索复制活动运行详细信息，例如，读取/写入的数据的大小。

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
该管道自动在 adftutorial Blob 容器中创建 output 文件夹， 然后将 emp.txt 文件从 input 文件夹复制到 output 文件夹。 

1. 在 Azure 门户的“adftutorial”容器页中单击“刷新”，查看输出文件夹。 
    
    ![刷新](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. 单击文件夹列表中的“output”。 
2. 确认 **emp.txt** 已复制到 output 文件夹。 

    ![刷新](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>清理资源
可以通过两种方式清理在快速入门中创建的资源。 可以删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，其中包括资源组中的所有资源。 若要使其他资源保持原封不动，请仅删除在此教程中创建的数据工厂。

删除资源组时会删除所有资源，包括其中的数据工厂。 运行以下命令可以删除整个资源组： 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

如果只需删除数据工厂，不需删除整个资源组，请运行以下命令： 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 完成相关[教程](tutorial-copy-data-dot-net.md)来了解如何在更多方案中使用数据工厂。 
