---
title: 通过使用 Azure PowerShell 实现 Azure Data Lake Analytics 入门 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 创建 Data Lake Store 帐户，使用 U-SQL 创建 Data Lake Analytics 作业，并提交该作业。
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2016
ms.author: edmaca

---
# 教程：通过使用 Azure PowerShell 实现 Azure Data Lake Analytics 入门
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure PowerShell 来创建 Azure Data Lake Analytics 帐户，在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定义 Data Lake Analytics 作业，并将作业提交至 Data Lake Analytics 帐户。有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

在本教程中，用户将开发一项作业，它能读取制表符分隔值 (TSV) 文件，并将其转换为逗号分隔值 (CSV) 文件。若要通过其他支持的工具来完成此教程，请单击本节顶部的选项卡。

## 先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **配备 Azure PowerShell 的工作站**。请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

## 创建 Data Lake Analytics 帐户
在运行任何作业之前，首先必须拥有 Data Lake Analytics 帐户。若要创建 Data Lake Analytics 帐户，必须指定以下各项：

* **Azure 资源组**：必须在 Azure 资源组中创建一个 Data Lake 分析帐户。那么，你可以使用 [Azure 资源管理器](../resource-group-overview.md)以组的方式处理应用程序中的资源。你可以通过一个协调的操作为应用程序部署、更新或删除所有资源。
  
    枚举订阅中的资源组：
  
        Get-AzureRmResourceGroup
  
    若要创建新的资源组：
  
        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"
* **Data Lake Analytics 帐户名**
* **位置**：支持 Data Lake Analytics 的 Azure 数据中心之一。
* **默认 Data Lake 帐户**：每个 Data Lake Analytics 帐户都有一个默认 Data Lake 帐户。
  
    创建新的 Data Lake 帐户：
  
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"
  
  > [!NOTE]
  > Data Lake 帐户名必须仅包含小写字母和数字。
  > 
  > 

**创建 Data Lake Analytics 帐户**

1. 从 Windows 工作站中打开 PowerShell ISE。
2. 运行以下脚本：
   
        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
   
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
   
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
   
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
   
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

## 将数据上载到 Data Lake
在本教程中，将处理一些搜索日志。搜索日志可以存储在 Data Lake Store 或 Azure Blob 存储中。

已将示例搜索日志文件复制到公共 Azure Blob 容器中。使用以下 PowerShell 脚本来将文件下载到你的工作站，然后将文件上传到 Data Lake Analytics 帐户的默认 Data Lake Store 帐户。

    $dataLakeStoreName = "<The default Data Lake Store account name>"

    $localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location    
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

下面的 PowerShell 脚本演示如何为 Data Lake Analytics 帐户获取默认的 Data Lake Store 名称：

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

> [!NOTE]
> Azure 门户提供了一个用户界面，供你将示例数据文件复制到默认的 Data Lake Store 帐户。有关说明，请参阅[通过 Azure 门户实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account)。
> 
> 

Data Lake Analytics 还可以访问 Azure Blob 存储。有关将数据上传到 Azure Blob 存储的详细信息，请参阅[对 Azure 存储空间使用 Azure PowerShell](../storage/storage-powershell-guide-full.md)。

## 提交 Data Lake Analytics 作业
Data Lake Analytics 作业使用 U-SQL 语言编写而成。若要了解有关 U-SQL 的详细信息，请参阅 [U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**创建 Data Lake Analytics 作业脚本**

* 使用下列 U-SQL 脚本创建文本文件，并将其保存至工作站：
  
        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
  
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();
  
    此 U-SQL 脚本通过 **Extractors.Tsv()** 读取源数据文件，然后通过 **Outputters.Csv()** 创建 csv 文件。
  
    除非将源文件复制到其他位置，否则不要修改这两条路径。如果输出文件夹不存在，Data Lake Analytics 将创建一个。
  
    对于存储在默认 Data Lake 帐户中的文件而言，使用相对路径更为简单。也可使用绝对路径。例如
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    必须使用绝对路径来访问链接的存储帐户中的文件。链接的 Azure 存储帐户中所储存文件的语法是：
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > 当前不支持对含公共 blob 的 Azure Blob 容器或公共容器的访问权限。
  > 
  > 

**提交作业**

1. 从 Windows 工作站中打开 PowerShell ISE。
2. 运行以下脚本：
   
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
   
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
   
        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId
   
        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId
   
    在脚本中，U-SQL 脚本文件存储在 c:\\tutorials\\data-lake-analytics\\copyFile.usql。相应地更新文件路径。

作业完成后，可以使用以下 cmdlet 列出该文件，并下载它：

    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"

    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## 另请参阅
* 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
* 若要查看更复杂的查询，请参阅[使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要着手开发 U-SQL 应用程序，请参阅[使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅[使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

<!---HONumber=AcomDC_0921_2016-->