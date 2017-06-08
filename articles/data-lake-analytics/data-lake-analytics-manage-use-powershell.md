---
title: "使用 Azure PowerShell 管理 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何管理 Data Lake Analytics 作业、数据源、用户。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 4dd1ba30101d364fa52738a4e1c3e07874c5ed1f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/27/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure PowerShell 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。 若要查看使用其他工具的管理主题，请单击上述选项卡选项。

**先决条件**

在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell**。 请参阅 [将 Azure PowerShell 与 Azur Resource Manager 配合使用](../powershell-azure-resource-manager.md)的“先决条件”部分。

## <a name="running-the-snippets"></a>运行代码片段

本教程中的 PowerShell 代码片段使用上述变量来存储该信息

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="manage-accounts"></a>管理帐户

### <a name="create-a-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户

```
New-AzureRmResourceGroup -Name  $rg -Location $location
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="create-a-data-lake-analytics-account-using-a-template"></a>使用模板创建 Data Lake Analytics 帐户

还可使用 Azure 资源组模板。 用于创建 Data Lake Analytics 帐户和从属 Data Lake Store 帐户的模板位于[附录 A](#appendix-a)。将模板保存为具有 .json 模板的文件，然后使用以下 PowerShell 脚本对其进行调用：

    $AzureSubscriptionID = "<Your Azure Subscription ID>"

    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"

    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID

    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 


### <a name="list-accounts"></a>列出帐户

列出当前订阅中的 Data Lake Analytics 帐户

    Get-AzureRmDataLakeAnalyticsAccount

列出特定资源组中的 Data Lake Analytics 帐户

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

获取特定 Data Lake Analytics 帐户的详细信息

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

测试是否存在特定的 Data Lake Analytics 帐户

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

该 cmdlet 将返回 **True** 或 **False**。

### <a name="delete-data-lake-analytics-accounts"></a>删除 Data Lake Analytics 帐户
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

删除 Data Lake Analytics 帐户不会删除从属 Data Lake 存储帐户。 以下示例将删除 Data Lake Analytics 帐户和默认 Data Lake Store 帐户

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>管理帐户数据源
Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/storage-introduction.md)

创建 Analytics 帐户时，必须指定一个 Azure Data Lake 存储帐户作为默认存储帐户。 默认 Data Lake Store 帐户用于存储作业元数据和作业审核日志。 创建 Analytics 帐户后，可添加其他 Data Lake 存储帐户和/或 Azure 存储帐户。 

### <a name="find-the-default-data-lake-store-account"></a>查找默认 Data Lake Store 帐户
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>添加其他 Azure Blob 存储帐户
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>添加其他 Data Lake Store 帐户
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>列出数据源：
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts



<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>管理作业
必须拥有 Data Lake Analytics 帐户才可创建作业。  有关详细信息，请参阅[管理 Data Lake Analytics 帐户](#manage-data-lake-analytics-accounts)。

### <a name="list-jobs"></a>列出作业
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"    

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>获取作业详细信息
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>

### <a name="submit-jobs"></a>提交作业
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [!NOTE]
> 作业的默认优先级为 1000，默认并行度为 1。
> 
> 

### <a name="cancel-jobs"></a>取消作业
    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>管理目录项
可以使用 -SQL 目录组织数据和代码，以便通过 U-SQL 脚本共享数据和代码。 目录启用 Azure Data Lake 中数据的最高可能性能。 有关详细信息，请参阅 [使用 U-SQL 目录](data-lake-analytics-use-u-sql-catalog.md)。

### <a name="list-catalog-items"></a>列出目录项
    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database



    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

### <a name="get-catalog-item-details"></a>获取目录项详细信息
    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

### <a name="test-existence-of--catalog-item"></a>测试是否存在目录项
    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

## <a name="use-azure-resource-manager-groups"></a>使用 Azure Resource Manager 组
应用程序通常由许多组件构成，例如 Web 应用、数据库、数据库服务器、存储和第三方服务。 你可以使用 Azure 资源管理器 (ARM) 以组（称为 Azure 资源组）的方式处理应用程序中的资源。 你可以通过一个协调的操作为应用程序部署、更新、监视或删除所有资源。 你可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 你可以通过查看整个组的累积费用，明确了解组织的帐单开支。 有关详细信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服务可包括以下组件：

* Azure Data Lake Analytics 帐户
* 所需的默认 Azure Data Lake 存储帐户
* 其他 Azure Data Lake 存储帐户
* 其他 Azure 存储帐户

可在一个 ARM 组下创建所有这些组件，使其更易于管理。

![Azure Data Lake Analytics 帐户和存储](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帐户和从属存储帐户必须位于同一个 Azure 数据中心。
但 ARM 组可以位于不同的数据中心。  

## <a name="see-also"></a>另请参阅
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="appendix-a---data-lake-analytics-arm-template"></a>附录 A - Data Lake Analytics ARM 模板
以下 ARM 模板可用于部署 Data Lake Analytics 帐户及其从属 Data Lake Store 帐户。  将其保存为 json 文件，然后使用 PowerShell 脚本调用此模板。 有关详细信息，请参阅[使用 Azure Resource Manager 模板部署应用程序](../azure-resource-manager/resource-group-template-deploy.md)和[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }


