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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 24a4255a23f0b9b9da5d8c3cefeefb8fe250f2f1
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-copy-data-between-on-premises-and-cloud"></a>教程：在本地与云之间复制数据
本教程使用 Azure PowerShell 创建一个数据工厂管道，用于将数据从本地 SQL Server 数据库复制到 Azure Blob 存储。 本教程创建并使用 Azure 数据工厂的自承载 Integration Runtime (IR)，以便将本地数据存储与云数据存储相集成。  若要了解如何使用其他工具/SDK 创建数据工厂，请参阅[快速入门](quickstart-create-data-factory-dot-net.md)。

本文不提供数据工厂服务的详细介绍。 有关 Azure 数据工厂服务的介绍，请参阅 [Azure 数据工厂简介](introduction.md)。 

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时。
> * 创建 SQL Server 和 Azure 存储链接服务。 
> * 创建 SQL Server 和 Azure Blob 数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道运行。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 或 2016。 
在本教程中，将本地 SQL Server 数据库用作**源**数据存储。 在 SQL Server 数据库中创建名为 **emp** 的表，并向表中插入几个示例条目。

1. 启动 **SQL Server Management Studio**。 如果使用 SQL Server 2016，可能需要从[下载中心](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)单独安装 SQL Server Management Studio。 
2. 使用凭据连接到 SQL Server。 
3. 创建示例数据库。 在树状视图中右键单击“数据库”，然后单击“新建数据库”。 在“新建数据库”对话框中输入数据库的**名称**，然后单击“确定”。 
4. 对数据库运行以下查询脚本，以便创建 **emp** 表。 在树状视图中右键单击所创建的**数据库**，然后单击“新建查询”。 

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
2. 对数据库运行以下命令，向表中插入一些示例数据：

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Azure 存储帐户
在本教程中，请使用通用的 Azure 存储帐户（具体说来就是 Blob 存储）作为 **destination/sink** 数据存储。 如果没有通用的 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)创建一个。

#### <a name="get-storage-account-name-and-account-key"></a>获取存储帐户名称和帐户密钥
在本快速入门中，请使用 Azure 存储帐户的名称和密钥。 以下过程提供的步骤用于获取存储帐户的名称和密钥。 

1. 启动 Web 浏览器并导航到 [Azure 门户](https://portal.azure.com)。 使用 Azure 用户名和密码登录。 
2. 单击左侧菜单中的“更多服务 >”，使用“存储”关键字进行筛选，然后选择“存储帐户”。

    ![搜索存储帐户](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. 在存储帐户列表中，通过筛选找出你的存储帐户（如果需要），然后选择**你的存储帐户**。 
4. 在“存储帐户”页的菜单上选择“访问密钥”。

    ![获取存储帐户名称和密钥](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. 将“存储帐户名称”和“key1”字段的值复制到剪贴板。 将这些值粘贴到记事本或任何其他编辑器中，然后进行保存。 在本教程中，请使用该存储帐户名称和密钥。 

#### <a name="create-the-adftutorial-container"></a>创建 adftutorial 容器 
此部分在 Azure Blob 存储中创建名为 adftutorial 的 Blob 容器。 

1. 安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)（如果尚未在计算机上安装）。 
2. 在计算机上启动 **Microsoft Azure 存储资源管理器**。   
3. 在“连接到 Azure 存储”窗口中选择“使用存储帐户名称和密钥”，然后单击“下一步”。 如果看不到“连接到 Azure 存储”窗口，请右键单击树状视图中的“存储帐户”，然后单击“连接到 Azure 存储”。 

    ![连接到 Azure 存储](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. 在“使用名称和密钥进行附加”窗口中，粘贴在上一步保存的“帐户名称”和“帐户密钥”。 然后单击“下一步”。 
5. 在“连接摘要”窗口中单击“连接”。
6. 确认你在树状视图的“(本地和附加)” -> “存储帐户”下看到了自己的存储帐户。 
7. 展开“Blob 容器”，确认名为“adftutorial”的 Blob 容器不存在。 如果该容器已存在，则跳过创建该容器的后续步骤。 
8. 右键单击“Blob 容器”，并选择“创建 Blob 容器”。

    ![创建 Blob 容器](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. 输入 **adftutorial** 作为名称，然后按 **ENTER**。 
10. 确认 **adftutorial** 容器在树状视图中已选中。 数据工厂自动在该容器中创建输出文件夹，因此不需要你来创建。 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>安装 Azure PowerShell
安装最新的 Azure PowerShell（如果尚未在计算机上安装）。 

1. 在 Web 浏览器中导航到 [Azure SDK 下载和 SDK](https://azure.microsoft.com/downloads/)页。 
2. 在“命令行工具” -> “PowerShell”部分单击“Windows 安装”。 
3. 若要安装 Azure PowerShell，请运行 **MSI** 文件。 

有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。 

#### <a name="log-in-to-azure-powershell"></a>登录到 Azure PowerShell
在计算机上启动 **PowerShell**。 在完成本快速入门之前，请将 Azure PowerShell 保持打开状态。 如果将它关闭再重新打开，则需要再次运行下述命令。

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

## <a name="create-a-self-hosted-ir"></a>创建自我托管的 IR

在本部分，可以创建一个自我托管的集成运行时，并将其与本地节点（计算机）相关联。

1. 创建一个适用于 Integration Runtime 名称的变量。 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. 创建自我托管的集成运行时。 如果存在同名的集成运行时，请使用唯一名称。

   ```powershell
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
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>安装 Integration Runtime
1. 将自承载 Integration Runtime [下载](https://www.microsoft.com/download/details.aspx?id=39717)到本地 Windows 计算机上，然后进行安装。 
2. 在“欢迎使用 Microsoft Integration Runtime 安装向导”页上，单击“下一步”。  
3. 在“最终用户许可协议”页上接受许可协议的条款，然后单击“下一步”。 
4. 在“目标文件夹”页上单击“下一步”。 
5. 在“准备安装 Microsoft Integration Runtime”页上单击“安装”。 
6. 如果看到一条警告消息，指出系统正将计算机配置为在不使用时进入睡眠或休眠模式，请单击“确定”。 
7. 在“完成 Microsoft Integration Runtime 安装向导”页上单击“完成”。
8. 在“注册 Integration Runtime (自承载)”页上粘贴在上一部分保存的密钥，然后单击“注册”。 

   ![注册集成运行时](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. 成功注册自我托管的集成运行时后，会看到以下消息：

   ![已成功注册](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. 在“新建 Integration Runtime (自承载)节点”页上，单击“下一步”。 

    ![“新建 Integration Runtime 节点”页](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. 在“Intranet 信道”页上，单击“跳过”。 可以选择 TLS/SSL 认证，确保多节点 Integration Runtime 环境中的节点内通信安全。 

    ![“Intranet 信道”页](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. 在“注册 Integration Runtime (自承载)”页上，单击“启动配置管理器”。 
6. 将节点连接到云服务后，会看到以下页：

   ![节点已连接](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>创建链接服务

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>创建 Azure 存储链接服务（目标/接收器）

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **AzureStorageLinkedService.json** 的 JSON 文件：如果文件夹 ADFv2Tutorial 不存在，请创建该文件夹。  

    > [!IMPORTANT]
    > 将 &lt;accountName&gt; 和 &lt;accountKey&gt; 分别替换为 Azure 存储帐户的名称和密钥，然后保存文件。

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

1. 在 **C:\ADFv2Tutorial** 文件夹中，创建包含以下内容的名为 **SqlServerLinkedService.json** 的 JSON 文件：将 **&lt;servername>**、**&lt;databasename>**、**&lt;username>**、**&lt;servername>** 和 **&lt;password>** 替换为 SQL Server 的值，然后保存文件。 

    > [!IMPORTANT]
    > 将 **&lt;integration** **runtime** **name>** 替换为集成运行时的名称。

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
2. 若要在本地的自承载 Integration Runtime 中加密 JSON 有效负载中的敏感数据，请运行 **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** 并传递上述 JSON 有效负载。 这种加密可确保使用数据保护应用程序编程接口 (DPAPI) 加密凭据。 加密的凭据存储在自承载 Integration Runtime 节点本地（本地计算机）。 可将输出的有效负载重定向到包含已加密凭据的另一个 JSON 文件（在本例中为“encryptedLinkedService.json”）。
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. 使用前一步骤中的 JSON 运行以下命令，创建 **SqlServerLinkedService**：

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>创建数据集
本步骤创建表示输入和输出数据的输入和输出数据集以进行复制操作（从本地 SQL Server 数据库复制到 Azure blob 存储）。

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
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>验证输出
该管道自动在 `adftutorial` Blob 容器中创建名为 `fromonprem` 的输出文件夹。 确认在输出文件夹中看到 **dbo.emp.txt** 文件。 使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)验证是否已创建输出。 

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 你已了解如何：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建自我托管的集成运行时。
> * 创建 SQL Server 和 Azure 存储链接服务。 
> * 创建 SQL Server 和 Azure Blob 数据集。
> * 创建包含复制活动的管道，用于移动数据。
> * 启动管道运行。
> * 监视管道运行。

有关 Azure 数据工厂支持的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

继续学习以下教程，了解如何将数据从源批量复制到目标：

> [!div class="nextstepaction"]
>[批量复制数据](tutorial-bulk-copy.md)
