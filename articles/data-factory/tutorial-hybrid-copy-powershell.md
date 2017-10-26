---
title: "使用 Azure 数据工厂将本地数据复制到云中 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中使用自我托管的集成运行时将数据从本地数据存储复制到 Azure 云。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2017
ms.author: jingwang
ms.openlocfilehash: 9aac9c9bcc609a91415438279419d4cc8e237fcb
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>在本地与云之间复制数据
Azure 数据工厂是基于云的数据集成服务，用于在云中创建数据驱动型工作流，以便协调和自动完成数据移动和数据转换。 使用 Azure 数据工厂，可以创建和计划数据驱动型工作流（称为管道），以便从不同的数据存储引入数据，通过各种计算服务（例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure 机器学习）处理/转换数据，将输出数据发布到数据存储（例如 Azure SQL 数据仓库），供商业智能 (BI) 应用程序使用。

本教程使用 Azure PowerShell 创建一个数据工厂管道，用于将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。 本教程将创建并使用 Azure 数据工厂的自我托管集成运行时 (IR)，以便将本地数据存储与云数据存储相集成。  若要了解如何使用其他工具/SDK 创建数据工厂，请参阅[快速入门](quickstart-create-data-factory-dot-net.md)。

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时。
> * 在自我托管的集成运行时中创建并加密本地 SQL Server 链接服务。
> * 创建 Azure 存储链接服务。
> * 创建 SQL Server 和 Azure 存储数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道和活动运行。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

* **SQL Server**。 在本教程中，将本地 SQL Server 数据库用作**源**数据存储。
* **Azure 存储帐户**。 本教程使用 Azure Blob 存储作为**目标/接收器**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
* **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **PowerShell**。 在本教程结束之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。

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
2. 运行 **Set-AzureRmDataFactoryV2** cmdlet 创建数据工厂。 执行该命令之前，请将占位符替换为自己的值。

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    请注意以下几点：

    * Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * 只有 Azure 订阅的参与者或管理员才可以创建数据工厂实例。
    * 目前，数据工厂仅允许在美国东部和美国东部 2 区域中创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

## <a name="create-a-self-hosted-ir"></a>创建自我托管的 IR

在本部分，可以创建一个自我托管的集成运行时，并将其与本地节点（计算机）相关联。

1. 创建自我托管的集成运行时。 如果存在同名的集成运行时，请使用唯一名称。

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   下面是示例输出：

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 运行以下命令检索所创建的集成运行时的状态。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   下面是示例输出：

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 运行以下命令，检索用于将自我托管的集成运行时注册到云中数据工厂服务的身份验证密钥。 复制用于注册自我托管集成运行时的密钥之一。

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   下面是示例输出：

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. 在本地 Windows 计算机上[下载](https://www.microsoft.com/download/details.aspx?id=39717)自我托管的集成运行时，并使用在上一步骤中获取的身份验证密钥手动注册该自我托管的集成运行时。

   ![注册集成运行时](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   成功注册自我托管的集成运行时后，会看到以下消息：

   ![已成功注册](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   将节点连接到云服务后，会看到以下页：

   ![节点已连接](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>创建链接服务

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>创建 Azure 存储链接服务（目标/接收器）

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **AzureStorageLinkedService.json** 的 JSON 文件。 创建 ADFv2Tutorial 文件夹（如果不存在）。  将 &lt;accountname&gt; 和 &lt;accountkey&gt; 分别替换为 Azure 存储帐户的名称和密钥。

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. 在 **Azure PowerShell** 中，切换到 **ADFv2Tutorial** 文件夹。

   运行 **Set-AzureRmDataFactoryV2LinkedService** cmdlet 创建链接服务：**AzureStorageLinkedService**。 本教程中的 cmdlet 使用 **ResourceGroupName** 和 **DataFactoryName** 参数的值。 或者，可以传递 Set-AzureRmDataFactoryV2 cmdlet 返回的 **DataFactory** 对象，而无需在每次运行 cmdlet 时键入 ResourceGroupName 和 DataFactoryName。

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   下面是示例输出：

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>创建并加密 SQL Server 链接服务（源）

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **SqlServerLinkedService.json** 的 JSON 文件：将 **&lt;servername>**、**&lt;databasename>**、**&lt;username>**、**&lt;servername>** 和 **&lt;password>** 替换为 SQL Server 的值，然后保存文件。 将 **&lt;integration** **runtime** **name>** 替换为集成运行时的名称。

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. 若要在本地自我托管的集成运行时中加密 JSON 有效负载中的敏感数据，可以运行 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** 并传递上述 JSON 有效负载。 这种加密可确保使用数据保护应用程序编程接口 (DPAPI) 加密凭据，并将其存储在自我托管的集成运行时节点本地。 可将输出的有效负载重定向到包含已加密凭据的另一个 JSON 文件（在本例中为“encryptedLinkedService.json”）。

    在运行该命令之前，请将 **&lt;integration runtime name&gt;** 替换为集成运行时的名称。

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 使用前一步骤中的 JSON 运行以下命令，创建 **SqlServerLinkedService**：

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>创建数据集

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>为教程准备本地 SQL Server

本步骤创建表示输入和输出数据的输入和输出数据集以进行复制操作（从本地 SQL Server 数据库复制到 Azure blob 存储）。 在创建数据集之前，执行以下步骤（详细步骤见以下列表）：

- 在作为链接服务添加到数据工厂的 SQL Server 数据库中，创建名为 **emp** 的表，并在该表中插入几个示例条目。
- 在作为链接服务添加到数据工厂的 Azure blob 存储帐户中，创建一个名为 **adftutorial** 的 blob 容器。


1. 在为本地 SQL Server 链接服务 (**SqlServerLinkedService**) 指定的数据库中，使用以下 SQL 脚本在数据库中创建 **emp** 表。

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

2. 在该表中插入几个示例：

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>为源 SQL 数据库创建数据集

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **SqlServerDataset.json** 的 JSON 文件：  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
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
    ```

2. 若要创建数据集 **SqlServerDataset**，请运行 **Set-AzureRmDataFactoryV2Dataset** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    下面是示例输出：

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>为接收器 Azure Blob 存储创建数据集

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **AzureBlobDataset.json** 的 JSON 文件：

    > [!IMPORTANT]
    > 此示例代码假设 Azure Blob 存储中有一个名为 **adftutorial** 的容器。

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
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
    ```

2. 若要创建数据集 **AzureBlobDataset**，请运行 **Set-AzureRmDataFactoryV2Dataset** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    下面是示例输出：

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>创建管道

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>创建管道“SqlServerToBlobPipeline”

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **SqlServerToBlobPipeline.json** 的 JSON 文件：

    ```json
    {
       "name": "SQLServerToBlobPipeline",
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
    ```

2. 若要创建管道 **SQLServerToBlobPipeline**，请运行 **Set-AzureRmDataFactoryV2Pipeline** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    下面是示例输出：

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>启动并监视管道运行

1. 针对“SQLServerToBlobPipeline”管道启动管道运行，并捕获管道运行 ID，以便将来进行监视。  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. 运行以下脚本可持续检查管道“**SQLServerToBlobPipeline**”的运行状态，并列显最终结果。

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    下面是示例运行的输出：

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. 可以获取管道“**SQLServerToBlobPipeline**”的运行 ID，并检查详细的活动运行结果，如下所示。

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    下面是示例运行的输出：

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. 连接到接收器 Azure Blob 存储，并确认是否已正确地从 Azure SQL 数据库复制数据。

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 你已了解如何：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时
> * 在自我托管的集成运行时中创建并加密本地 SQL Server 链接服务
> * 创建 Azure 存储链接服务。
> * 创建 SQL Server 和 Azure 存储数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道和活动运行。

有关 Azure 数据工厂支持用作源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

继续学习以下教程，了解如何将数据从源批量复制到目标：

> [!div class="nextstepaction"]
>[批量复制数据](tutorial-bulk-copy.md)
