<properties
   pageTitle="Data Lake Store 入门 | Azure"
   description="使用 Azure PowerShell 创建 Data Lake Store 帐户并执行基本操作"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# 通过 Azure PowerShell 实现 Azure Data Lake Store 入门

> [AZURE.SELECTOR]
- [门户](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure PowerShell 来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上载和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Azure Data Lake Store 概述](data-lake-store-overview.md)。

## 先决条件

在开始阅读本教程前，你必须具有：

- **一个 Azure 订阅**。请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。


##安装 Azure PowerShell 1.0 或更高版本

请参阅[将 Azure PowerShell 与 Azur Resource Manager 配合使用](../powershell-azure-resource-manager.md#prerequisites)的“先决条件”部分。

## 创建 Azure Data Lake Store 帐户

1. 在桌面上，打开一个新的 Windows PowerShell 窗口，输入以下代码段登录到 Azure 帐户，然后设置订阅，并注册 Data Lake Store 提供程序。当系统提示输入登录信息时，请确保以订阅管理员/所有者身份登录：

        # Log in to your Azure account
		Login-AzureRmAccount

		# List all the subscriptions associated to your account
		Get-AzureRmSubscription

		# Select a subscription
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Azure Data Lake Store 帐户与 Azure 资源组是相关联的。首先创建 Azure 资源组。

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![创建 Azure 资源组](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "创建 Azure 资源组")

2. 创建 Azure Data Lake Store 帐户。指定名称必须仅包含小写字母与数字。

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![创建 Azure Data Lake Store 帐户](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "创建 Azure Data Lake Store 帐户")

3. 验证是否已成功创建帐户。

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	此输出应为 **True**。

## 在 Azure Data Lake Store 中创建目录结构

可以在 Azure Data Lake Store 帐户下创建目录，用于管理和存储数据。

1. 指定根目录。

		$myrootdir = "/"

2. 在指定根下，创建一个名为 **mynewdirectory** 的新目录。

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. 验证是否已成功创建新目录。

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	应该会显示如下输出：

	![验证目录](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "验证目录")


## 将数据上载到 Azure Data Lake Store

可以直接将数据上载到 Data Lake Store 的根级别，也可以上载到在帐户中创建的目录。下面的代码段演示了如何将一些示例数据上载到上一节中创建的目录 (**mynewdirectory**)。

如果正在查找一些示例数据进行上载，可以从 [Azure Data Lake Git 存储库](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)获取 **Ambulance Data** 文件夹。下载文件，并将其存储在计算机的本地目录，如 C:\\sampledata。

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## 重命名、下载并删除 Data Lake Store 中的数据

使用以下命令，重命名文件：

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

使用以下命令，下载文件：

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

使用以下命令，删除文件：

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

出现提示时，输入 **Y** 以删除项目。如果有多个要删除的文件，可以用逗号分隔提供所有路径。

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## 删除你的 Azure Data Lake Store 帐户

使用以下命令删除 Data Lake Store 帐户。

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

出现提示时，输入 **Y** 删除帐户。


## 后续步骤

- [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
- [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0921_2016-->