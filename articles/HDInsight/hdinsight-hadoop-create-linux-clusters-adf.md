<!-- not suitable for Mooncake -->

<properties
   pageTitle="在 HDInsight 中使用 Azure 数据工厂创建基于 Linux 的按需 Hadoop 群集 | Azure"
   	description="了解如何使用 Azure 数据工厂创建按需 HDInsight 群集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="03/16/2016"
	wacn.date=""/>

# 在 HDInsight 中使用 Azure 数据工厂创建基于 Linux 的按需 Hadoop 群集

[AZURE.INCLUDE [选择器](../includes/hdinsight-selector-create-clusters.md)]

[Azure 数据工厂](/documentation/articles/data-factory-introduction)是一项基于云的数据集成服务，可对数据移动和转换进行安排并使其实现自动化。在本文中，你将了解如何使用 Azure 数据工厂来创建 [Azure HDInsight 按需链接服务](/documentation/articles/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)，并使用群集运行 Hive 作业。下面是概要流程：

1. 按需创建 HDInsight 群集。
2. 运行 Hive 作业以从源 blob 存储帐户中读取原始 Web 日志数据、转换数据，并将输出写入到目标 blob 存储帐户。 
3. 基于生存时间设置删除群集。

数据工厂管道中定义的 Hive 活动调用预定义的 HiveQL 脚本。该脚本创建外部表，以引用存储在 Azure Blob 存储中的原始 Web 日志数据，然后按年和月对原始数据进行分区。

以下是针对输入文件中的每个月的示例行。

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

该脚本基于先前的输入创建三个输出文件夹。每个文件夹均包含一个文件，其中包含每个月的条目。

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

有关 Hive 活动以外的数据工厂数据转换活动的列表，请参阅[使用 Azure 数据工厂转换和分析](/documentation/articles/data-factory-data-transformation-activities)。

将 HDInsight 与数据工厂配合使用有很多好处：

- 无论你是否使用 HDInsight 群集，HDInsight 群集都按小时计费。如果使用数据工厂，群集将按需创建。并在作业完成时，自动删除这些群集。因此你只需为作业运行时间和简短的空闲时间（生存时间）付费。
- 你可以使用数据工厂管道创建工作流。
- 你可以计划递归作业。  

##先决条件：

在开始按照本文中的说明操作之前，你必须具有以下内容：

- [Azure 订阅](/pricing/1rmb-trial/)。
- [Azure CLI](/documentation/articles/xplat-cli-install) 或 [Azure PowerShell](/documentation/articles/hdinsight-administer-use-powershell#install-azure-powershell-10-and-greater)。 

##准备存储帐户

在此方案中，你最多可以使用三个存储帐户：

- HDInsight 群集的默认存储帐户
- 输入数据的存储帐户
- 输出数据的存储帐户

为了简化本教程，你将使用一个存储帐户来实现 3 个目的。本部分中提供的 Azure CLI 和 Azure PowerShell 示例脚本执行以下操作：

1. 登录到 Azure。
2. 创建 Azure 资源组。
3. 创建 Azure 存储帐户。
4. 在存储帐户中创建 Blob 容器
5. 将以下两个文件复制到 Blob 容器：

    - 输入数据文件：[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - HiveQL 脚本：[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    这两个文件将存储在公共 Blob 容器中。

>[AZURE.IMPORTANT] 记下你的脚本中使用的资源组名称、存储帐户名称和存储帐户密钥。你将在下一部分中需要它们。

**准备存储并使用 Azure CLI 复制文件**

    azure login

    azure group create --name "<Azure Resource Group Name>" --location "China East 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "China East 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

容器名称是 *adfgetstarted*。请按原样保留它。否则，将需要更新 ARM 模板。

如果你需要使用此 CLI 脚本的帮助，请参阅[将 Azure CLI 用于 Azure 存储空间](/documentation/articles/storage-azure-cli)。

**准备存储并使用 Azure PowerShell 复制文件**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "China East 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName |  %{ $_.Key1 }

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

如果你需要使用此 PowerShell 脚本的帮助，请参阅[将 Azure PowerShell 用于 Azure 存储空间](/documentation/articles/storage-powershell-guide-full)。

**检查存储帐户和内容**

1. 登录到 [Azure 管理门户](https://manage.windowsazure.cn)。
2. 单击左侧窗格中的“资源组”。
3. 双击你使用 CLI 或 PowerShell 脚本创建的资源组名称。如果列出了太多资源组，请使用筛选器。 
4. 除非你与其他项目共享资源组，否则在“资源”磁贴中，应列出一个资源。这就是具有你在前面指定的名称的存储帐户。单击存储帐户名称。
5. 单击“Blob”磁贴。
6. 单击“adfgetstarted”容器。你将看到两个文件夹：“输入数据”和“脚本”。
7. 打开文件夹，并检查文件夹中的文件。
 
## 创建数据工厂

准备好存储帐户、输入数据和 HiveQL 脚本后，你便可以创建 Azure 数据工厂了。有几种方法可以创建数据工厂。在本教程中，你将使用 Azure 管理门户调用自定义 ARM 模板。你还可以从 [Azure CLI](/documentation/articles/resource-group-template-deploy#deploy-with-azure-cli-for-mac-linux-and-windows) 和 [Azure PowerShell](/documentation/articles/resource-group-template-deploy#deploy-with-powershell) 调用 ARM 模板。有关其他数据工厂创建方法，请参阅[教程：生成第一个数据工厂](/documentation/articles/data-factory-build-your-first-pipeline)。

顶级 ARM 模板包含：

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "chinanorth",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

它包含一个名为 *hdinsight-hive-on-demand* 的数据工厂资源（屏幕截图中未显示该名称）。目前仅中国北部区域支持数据工厂。

*hdinsight-hive-on-demand* 资源包含 4 个资源：

- 存储帐户的链接服务，该存储帐户将用作默认 HDInsight 存储帐户、输入数据存储和输出数据存储。
- 要创建的 HDInsight 群集的链接服务：

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    即使未指定，也将在存储帐户所在的同一区域中创建群集。
    
    请注意 *timeToLive* 设置。数据工厂会在群集空闲 30 分钟后自动删除该群集。
- 输入数据的数据集。在此处定义文件名和文件夹名称：

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- 输出数据的数据集，然后定义用于数据处理的管道。在此处定义输出路径：
        
        "folderPath": "adfgetstarted/partitioneddata",

    [数据集可用性](/documentation/articles/data-factory-create-datasets#Availability)设置如下所示：
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    在 Azure 数据工厂中，输出数据集可用性将驱动管道。这意味着，将在每月的最后一天生成切片。有关详细信息，请参阅[数据工厂计划和执行](/documentation/articles/data-factory-scheduling-and-execution)。

    管道定义如下所示：
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    它包含一个活动。该活动的*开始*和*结束*都具有过去的日期，这意味着将只有一个切片。如果结束是将来的日期，则数据工厂将在该时间到时再创建一个切片。有关详细信息，请参阅[数据工厂计划和执行](/documentation/articles/data-factory-scheduling-and-execution)。

    以下是活动定义：
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.chinacloudapi.cn/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.chinacloudapi.cn/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    定义了输入、输出和脚本路径。
    
**创建数据工厂**

1. 单击下面的图像可登录到 Azure，并在 Azure 管理门户中打开 ARM 模板。该模板位于 https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json。 

    <a href="https://manage.windowsazure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-v1/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 为在上一部分中创建的帐户输入“DATAFACTORYNAME”、“STORAGEACCOUNTNAME”和“STORAGEACCOUNTKEY”，然后单击“确定”。数据工厂名称必须全局唯一。
3. 在“资源组”中，选择上一部分中使用的同一资源组。
4. 单击“法律条款”，然后单击“创建”。
5. 单击“创建”。你将在仪表板上看到一个名为“部署模板部署”的磁贴。等到该磁贴文本更改为资源组名称。创建一个 HDInsight 群集通常需要 20 分钟时间。
6. 单击该磁贴以打开资源组。现在，你应看到除了存储帐户资源外，多列出了一个数据工厂资源。
7. 单击 **hdinsight-hive-on-demand**。
8. 单击“图表”磁贴。图表显示一个包含输入数据集和输出数据集的活动：

    ![Azure 数据工厂 HDInsight 按需 hive 活动管道图表](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    名称在 ARM 模板中定义。
9. 双击“AzureBlobOutput”。
10. 在“最近更新的切片”中，你应看到一个切片。如果状态为“正在进行”，请等到它更改为“就绪”。

**检查数据工厂输出**

1. 使用上一课程中的相同过程检查 adfgetstarted 容器包含的内容。除了 **adfgetsarted** 外，还有两个新容器：

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx：这是 HDInsight 群集的默认容器。默认容器名称遵循模式：“adf>yourdatafactoryname>-linkedservicename-datetimestamp”。 
    - adfjobs：这是 ADF 作业日志的容器。
    
    根据 ARM 模板中的配置，数据工厂输出存储在 afgetstarted 中。 
2. 单击 **adfgetstarted**。
3. 双击 **partitioneddata**。你将看到 **year=2014** 文件夹，因为所有 Web 日志都注有日期 2014 年。 

    ![Azure 数据工厂 HDInsight 按需 hive 活动管道输出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    如果向下钻取列表，你应看到 3 个文件夹，分别为“1月”、“2 月”和“3 月”。并且每个月均有日志。

    ![Azure 数据工厂 HDInsight 按需 hive 活动管道输出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##结束本教程

使用按需 HDInsight 链接服务时，除非有现有的实时群集 (timeToLive)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。对于每个群集，Azure 数据工厂将创建 Azure blob 存储用作群集的默认文件系统。即使删除 HDInsight 群集，也不会删除默认 blob 存储容器和关联的存储帐户。这是设计使然。随着越来越多的切片进行处理，你将在 Azure blob 存储中看到大量容器。如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。这些容器的名称遵循模式：“adfyourdatafactoryname-linkedservicename-datetimestamp”。

[Azure 资源管理器](/documentation/articles/resource-group-overview)用于作为组部署、管理和监视解决方案。删除资源组将删除组内的所有组件。

**删除资源组**

1. 登录到 [Azure 管理门户](https://manage.windowsazure.cn)。
2. 单击左侧窗格中的“资源组”。
3. 双击你使用 CLI 或 PowerShell 脚本创建的资源组名称。如果列出了太多资源组，请使用筛选器。它将在新的边栏选项卡中打开资源组。
4. 除非你与其他项目共享资源组，否则在“资源”磁贴中，应列出默认存储帐户和数据工厂。
5. 单击边栏选项卡顶部的“删除”。这样做，将同时删除存储帐户和存储帐户中存储的数据。
6. 输入资源组名称，然后单击“删除”。

如果不想在删除资源组时删除存储帐户，则可以通过将业务数据与默认存储帐户分离，实现以下体系结构设计。在这种情况下，你将为包含业务数据的存储帐户创建一个资源组，为默认存储帐户和数据工厂创建另一个资源组。删除第二个资源组时，将不会影响业务数据存储帐户。为此，请执行以下操作：

- 将以下内容添加到 ARM 模板中的顶级资源组以及 Microsoft.DataFactory/datafactories 资源。这将创建新的存储帐户：

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- 将新的链接服务点添加到新的存储帐户：

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- 使用附加的 dependsOn 和 additionalLinkedServiceNames 配置 HDInsight 按需链接服务：

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##后续步骤
在本文中，你学习了如何使用 Azure 数据工厂创建按需 HDInsight 群集以处理 Hive 作业。若要了解更多信息，请参考以下文章：

- [Hadoop 教程：在 HDInsight 中使用基于 Linux 的 Hadoop 入门](/documentation/articles/hdinsight-hadoop-linux-tutorial-get-started)
- [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](/documentation/articles/hdinsight-provision-clusters-v1)
- [HDInsight 文档](/documentation/services/hdinsight/)
- [数据工厂文档](/documentation/services/data-factory/)


<!---HONumber=Mooncake_0405_2016-->