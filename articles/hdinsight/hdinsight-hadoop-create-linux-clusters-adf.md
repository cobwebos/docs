---
title: "使用数据工厂创建按需 Hadoop 群集 - Azure HDInsight | Microsoft Docs"
description: "了解如何在 HDInsight 中使用 Azure 数据工厂创建按需 Hadoop 群集。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: b9b73f6691af957e42236ef9a223411a0296f96f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>在 HDInsight 中使用 Azure 数据工厂创建按需 Hadoop 群集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure 数据工厂](../data-factory/introduction.md)是一项基于云的数据集成服务，可对数据的转移进行调整并自动实现数据的转换过程。 它可以实时创建 HDInsight Hadoop 群集来处理输入数据切片，并在处理完成后删除群集。 使用按需 HDInsight Hadoop 群集的部分优势包括：

- 用户只需为作业在 HDInsight Hadoop 群集上运行那段时间（以及一段简短的可配置空闲时间）付费。 HDInsight 群集是基于分钟按比例收费，而不管用户是否正在使用它们。 在数据工厂中使用按需 HDInsight 链接服务时，群集是按需创建的。 并在作业完成时，自动删除这些群集。 因此，用户只需为作业运行时间和简短的空闲时间（生存时间设置）付费。
- 用户可以使用数据工厂管道创建工作流。 例如，用户可以通过管道将数据从本地 SQL Server 复制到 Azure Blob 存储，在按需 HDInsight Hadoop 群集上运行 Hive 脚本和 Pig 脚本来处理数据， 然后将结果数据复制到 Azure SQL 数据仓库供 BI 应用程序使用。
- 可以对工作流进行计划，使之定期（每小时、每日、每周、每月等）运行。

在 Azure 数据工厂中，一个数据工厂可以有一个或多个数据管道。 一个数据管道有一个或多个活动。 有两种类型的活动：[数据移动活动](../data-factory/copy-activity-overview.md)和[数据转换活动](../data-factory/transform-data.md)。 可以使用数据移动活动（目前只能使用复制活动）将数据从源数据存储移动到目标数据存储。 可以使用数据转换活动来转换/处理数据。 HDInsight Hive 活动是数据工厂支持的转换活动之一。 用户在本教程中使用 Hive 转换活动。

可以将 Hive 活动配置为使用用户自己的 HDInsight Hadoop 群集或按需 HDInsight Hadoop 群集。 在本教程中，数据工厂管道中的 Hive 活动配置为使用按需 HDInsight 群集。 因此，当运行活动来处理数据切片时，会发生下面这样的情况：

1. 系统自动为用户实时创建 HDInsight Hadoop 群集，以便处理该切片。  
2. 通过在群集上运行 HiveQL 脚本处理输入数据。
3. HDInsight Hadoop 群集在处理完成后删除，群集空闲时间为配置的时间（timeToLive 设置）。 如果在这段 timeToLive 空闲时间内可以处理下一数据切片，则会使用同一群集处理该切片。  

在本教程中，与 Hive 活动关联的 HiveQL 脚本执行以下操作：

1. 创建一个外部表，以便引用存储在 Azure Blob 存储中的原始 Web 日志数据。
2. 按年和月将原始数据分区。
3. 在 Azure Blob 存储中存储分区的数据。

在本教程中，与 Hive 活动关联的 HiveQL 脚本创建一个外部表，以便引用存储在 Azure Blob 存储中的原始 Web 日志数据。 以下是针对输入文件中的每个月的示例行。

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

HiveQL 脚本按年和月将原始数据分区。 该脚本基于先前的输入创建三个输出文件夹。 每个文件夹均包含一个文件，其中包含每个月的条目。

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

有关 Hive 活动以外的数据工厂数据转换活动的列表，请参阅[使用 Azure 数据工厂转换和分析](../data-factory/transform-data.md)。

> [!NOTE]
> 目前只能从 Azure 数据工厂创建 HDInsight 群集 3.2。

## <a name="prerequisites"></a>先决条件
在开始按照本文中的说明操作之前，必须具有以下各项：

* [Azure 订阅](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell。

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>准备存储帐户
在此方案中，最多可以使用三个存储帐户：

- HDInsight 群集的默认存储帐户
- 输入数据的存储帐户
- 输出数据的存储帐户

为了简化本教程，使用一个存储帐户来实现 3 个目的。 本部分提供的 Azure PowerShell 示例脚本执行以下任务：

1. 登录 Azure。
2. 创建 Azure 资源组。
3. 创建 Azure 存储帐户。
4. 在存储帐户中创建 Blob 容器
5. 将以下两个文件复制到 Blob 容器：

   * 输入数据文件：[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * HiveQL 脚本：[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     这两个文件将存储在公共 Blob 容器中。


**使用 Azure PowerShell 准备存储并复制文件：**
> [!IMPORTANT]
> 指定将通过脚本创建的 Azure 资源组和 Azure 存储帐户的名称。
> 记下脚本所输出的“资源组名称”、“存储帐户名称”和“存储帐户密钥”。 下一部分需要它们。

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

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

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

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
```

如果需要关于此 PowerShell 脚本的帮助，请参阅[将 Azure PowerShell 用于 Azure 存储](../storage/common/storage-powershell-guide-full.md)。 若要改用 Azure CLI，请参阅[附录](#appendix)部分的 Azure CLI 脚本。

**检查存储帐户和内容**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击左侧窗格中的“资源组”。
3. 双击使用 PowerShell 脚本创建的资源组名称。 如果列出了太多资源组，请使用筛选器。
4. 除非你与其他项目共享资源组，否则在“资源”磁贴中，应列出一个资源。 该资源就是在前面你为其指定名称的存储帐户。 单击存储帐户名称。
5. 单击“Blob”磁贴。
6. 单击“adfgetstarted”容器。 可看到两个文件夹：**inputdata** 和 **script**。
7. 打开文件夹，并检查文件夹中的文件。 inputdata 包含 input.log 文件（其中有输入数据），script 文件夹包含 HiveQL 脚本文件。

## <a name="create-a-data-factory-using-resource-manager-template"></a>使用 Resource Manager 模板创建数据工厂
准备好存储帐户、输入数据和 HiveQL 脚本后，便可以创建 Azure 数据工厂了。 有几种方法可以创建数据工厂。 本教程使用 Azure 门户部署 Azure 资源管理器模板，从而创建数据工厂。 还可以使用 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 和 [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template) 部署 Resource Manager 模板。 有关其他数据工厂创建方法，请参阅[教程：构建第一个数据工厂](../data-factory/quickstart-create-data-factory-dot-net.md)。

1. 单击以下映像以登录到 Azure，并在 Azure 门户中打开 Resource Manager 模板。 该模板位于 https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json。 请参阅[模板中的数据工厂实体](#data-factory-entities-in-the-template)部分，以便详细了解模板中定义的实体。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 针对“资源组”设置选择“使用现有项”选项，并选择在上一步创建（使用 PowerShell 脚本）的资源组的名称。
3. 输入数据工厂的名称（**数据工厂名称**）。 该名称必须全局唯一。
4. 输入在前面的步骤中记下的**存储帐户名称**和**存储帐户密钥**。
5. 阅读完“条款和条件”后，选择“我同意上述条款和条件”。
6. 选择“固定到仪表板”选项。
6. 单击“购买/创建”。 在仪表板上，可看到一个名为“部署模板部署”的磁贴。 等待资源组的“资源组”边栏选项卡打开。 也可单击标题为资源组名称的磁贴，打开资源组边栏选项卡。
6. 如果资源组边栏选项卡尚未打开，请单击该磁贴打开资源组。 现在，应看到除了存储帐户资源外，多列出了一个数据工厂资源。
7. 单击数据工厂的名称（为 **Data Factory Name** 参数指定的值）。
8. 在“数据工厂”边栏选项卡中，单击“图表”磁贴。 图表显示一个包含输入数据集和输出数据集的活动：

    ![Azure 数据工厂 HDInsight 按需 Hive 活动管道图](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Resource Manager 模板中定义了名称。
9. 双击“AzureBlobOutput”。
10. 在“最近更新的切片”中，可看到一个切片。 如果状态为“正在进行”，请等到状态变为“就绪”。 创建一个 HDInsight 群集通常需要大约 **20 分钟**的时间。

### <a name="check-the-data-factory-output"></a>检查数据工厂输出

1. 使用上一课程中的相同过程检查 adfgetstarted 容器包含的内容。 除了 **adfgetsarted** 外，还有两个新容器：

   * 一个其名称遵循 `adf<yourdatafactoryname>-linkedservicename-datetimestamp` 模式的容器。 此容器是 HDInsight 群集的默认容器。
   * adfjobs：此容器是 ADF 作业日志的容器。

     根据 Resource Manager 模板中的配置，数据工厂输出存储在 **afgetstarted** 中。
2. 单击“adfgetstarted”。
3. 双击“partitioneddata”。 此时可看到 **year=2014** 文件夹，因为所有 Web 日志都注有日期 2014 年。

    ![Azure 数据工厂 HDInsight 按需 Hive 活动管道输出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    如果向下钻取列表，可看到 3 个文件夹，分别为“1月”、“2 月”和“3 月”。 并且每个月均有日志。

    ![Azure 数据工厂 HDInsight 按需 Hive 活动管道输出](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>模板中的数据工厂实体
下面是数据工厂的顶级 Resource Manager 模板的情况：

```json
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
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>定义数据工厂
如以下示例中所示，在 Resource Manager 模板中定义数据工厂：  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
dataFactoryName 是在部署模板时指定的数据工厂的名称。 目前，仅美国东部、美国西部和北欧区域支持数据工厂。

### <a name="defining-entities-within-the-data-factory"></a>定义数据工厂中的实体
JSON 模板中定义了以下数据工厂实体：

* [Azure 存储链接服务](#azure-storage-linked-service)
* [HDInsight 按需链接服务](#hdinsight-on-demand-linked-service)
* [Azure Blob 输入数据集](#azure-blob-input-dataset)
* [Azure Blob 输出数据集](#azure-blob-output-dataset)
* [包含复制活动的数据管道](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
Azure 存储链接服务将 Azure 存储帐户链接到数据工厂。 在本教程中，同一存储帐户既用作默认的 HDInsight 存储帐户，又用作输入数据存储和输出数据存储。 因此，只定义一个 Azure 存储链接服务。 在链接服务定义中，用户指定 Azure 存储帐户的名称和密钥。 有关用于定义 Azure 存储链接服务的 JSON 属性的详细信息。请参阅 [Azure Storage linked service](../data-factory/connector-azure-blob-storage.md)（Azure 存储链接服务）。

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** 使用 storageAccountName 和 storageAccountKey 参数。 在部署模板时指定这些参数的值。  

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight 按需链接服务
在按需 HDInsight 链接服务定义中，用户指定配置参数的值，这些配置参数由数据工厂服务用来在运行时创建 HDInsight Hadoop 群集。 有关用于定义 HDInsight 按需链接服务的 JSON 属性的详细信息。请参阅 [Compute linked services](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service)（计算链接服务）。  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
注意以下几点：

* 数据工厂为用户创建**基于 Linux 的** HDInsight 群集。
* HDInsight Hadoop 群集在创建时与存储帐户位于同一区域。
* 请注意 *timeToLive* 设置。 数据工厂会在群集空闲 30 分钟后自动删除该群集。
* HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。

有关详细信息，请参阅 [On-demand HDInsight Linked Service](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。

> [!IMPORTANT]
> 随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。 使用 [Microsoft 存储资源管理器](http://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。

#### <a name="azure-blob-input-dataset"></a>Azure Blob 输入数据集
在输入数据集定义中，用户指定包含输入数据的 Blob 容器、文件夹和文件的名称。 有关用于定义 Azure Blob 数据集的 JSON 属性的详细信息，请参阅 [Azure Blob dataset properties](../data-factory/connector-azure-blob-storage.md)（Azure Blob 数据集属性）。

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

请注意 JSON 定义中的以下特定设置：

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Azure Blob 输出数据集
在输出数据集定义中，用户指定用于保存输出数据的 Blob 容器和文件夹的名称。 有关用于定义 Azure Blob 数据集的 JSON 属性的详细信息，请参阅 [Azure Blob dataset properties](../data-factory/connector-azure-blob-storage.md)（Azure Blob 数据集属性）。  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

folderPath 指定保留输出数据的文件夹的路径：

```json
"folderPath": "adfgetstarted/partitioneddata",
```

[数据集可用性](../data-factory/concepts-datasets-linked-services.md)设置如下所示：

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

在 Azure 数据工厂中，输出数据集可用性将驱动管道。 在此示例中，会在每月的最后一天 (EndOfInterval) 生成切片。 

#### <a name="data-pipeline"></a>数据管道
定义一个管道，用于在按需 Azure HDInsight 群集上通过运行 Hive 脚本来转换数据。 有关用于定义本示例中所述管道的 JSON 元素的说明，请参阅 [Pipeline JSON](../data-factory/concepts-pipelines-activities.md)（管道 JSON）。

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

该管道包含一个活动（HDInsightHive 活动）。 由于开始和结束日期均在 2016 年 1 月，只处理一个月的数据（切片）。 活动的*开始* 和*结束* 日期均为过去的日期，因此数据工厂会立即处理该月的数据。 如果结束是将来的日期，则数据工厂会在该时间到来时再创建一个切片。 有关详细信息，请参阅[数据工厂计划和执行](../data-factory/v1/data-factory-scheduling-and-execution.md)。

## <a name="clean-up-the-tutorial"></a>结束本教程

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>删除由按需 HDInsight 群集创建的 Blob 容器
使用按需 HDInsight 链接服务时，除非有现有的实时群集 (timeToLive)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。 对于每个群集，Azure 数据工厂都会在用作群集的默认存储帐户的 Azure Blob 存储中创建 Blob 容器。 即使删除 HDInsight 群集，也不会删除默认 blob 存储容器和关联的存储帐户。 这是设计的行为。 随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循 `adfyourdatafactoryname-linkedservicename-datetimestamp` 模式。

删除 **adfjobs** 和 **adfyourdatafactoryname-linkedservicename-datetimestamp** 文件夹。 adfjobs 容器包含来自 Azure 数据工厂的作业日志。

### <a name="delete-the-resource-group"></a>删除资源组
[Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)用于将解决方案作为组来部署、管理和监视。  删除资源组会删除组内的所有组件。  

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击左侧窗格中的“资源组”。
3. 单击使用 PowerShell 脚本创建的资源组名称。 如果列出了太多资源组，请使用筛选器。 此时在新的边栏选项卡中打开资源组。
4. 除非你与其他项目共享资源组，否则在“资源”磁贴中，应列出默认存储帐户和数据工厂。
5. 在边栏选项卡顶部，单击“删除”。 这样做会删除存储帐户和存储帐户中存储的数据。
6. 输入资源组名称对删除进行确认，并单击“删除”。

如果不想在删除资源组时删除存储帐户，则可以通过将业务数据与默认存储帐户分离，实现以下体系结构。 在这种情况下，将为包含业务数据的存储帐户创建一个资源组，为 HDInsight 链接服务的默认存储帐户和数据工厂创建另一个资源组。 删除第二个资源组时，不会影响业务数据存储帐户。 为此，请执行以下操作：

* 将以下代码添加到 Resource Manager 模板中的顶层资源组以及 Microsoft.DataFactory/datafactories 资源。 它创建存储帐户：

    ```json
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
    ```
* 将新的链接服务点添加到新的存储帐户：

    ```json
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
    ```
* 使用附加的 dependsOn 和 additionalLinkedServiceNames 配置 HDInsight 按需链接服务：

    ```json
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
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>后续步骤
在本文中，学习了如何使用 Azure 数据工厂创建按需 HDInsight 群集以处理 Hive 作业。 若要了解更多信息，请参考以下文章：

* [Hadoop 教程：在 HDInsight 中使用基于 Linux 的 Hadoop 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight 文档](https://azure.microsoft.com/documentation/services/hdinsight/)
* [数据工厂文档](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>附录

### <a name="azure-cli-script"></a>Azure CLI 脚本
可以使用 Azure CLI 而非 Azure PowerShell 完成本教程。 若要使用 Azure CLI，请先按以下说明安装 Azure CLI：

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>使用 Azure CLI 准备存储并复制文件

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

容器名称为 *adfgetstarted*。 请保留该名称。 否则，需要更新 Resource Manager 模板。 如果需要关于此 CLI 脚本的帮助，请参阅[将 Azure CLI 用于 Azure 存储](../storage/common/storage-azure-cli.md)。
