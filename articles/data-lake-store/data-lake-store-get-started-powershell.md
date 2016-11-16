---
title: "Data Lake Store 入门 | Microsoft 文档"
description: "使用 Azure PowerShell 创建 Data Lake Store 帐户并执行基本操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c70eff85416b6b41eb455edc430a66b588dec41


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>通过 Azure PowerShell 实现 Azure Data Lake Store 入门
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

了解如何使用 Azure PowerShell 来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上载和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Data Lake Store 概述](data-lake-store-overview.md)。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

## <a name="authentication"></a>身份验证
本文对 Data Lake Store 使用一种较为简单的身份验证方法，其中会提示输入 Azure 帐户凭据。 系统会根据登录用户的访问级别监管对 Data Lake Store 帐户和文件系统的访问权限。 不过，也可以使用其他方法在 Data Lake Store 中进行身份验证，即**最终用户身份验证**或**服务到服务身份验证**。 有关如何进行身份验证的说明和详细信息，请参阅[使用 Azure Active Directory 进行 Data Lake Store 身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="create-an-azure-data-lake-store-account"></a>创建 Azure Data Lake Store 帐户
1. 在桌面上，打开一个新的 Windows PowerShell 窗口，输入以下代码段登录到 Azure 帐户，然后设置订阅，并注册 Data Lake Store 提供程序。 当系统提示输入登录信息时，请确保以订阅管理员/所有者身份登录：
   
        # Log in to your Azure account
        Login-AzureRmAccount
   
        # List all the subscriptions associated to your account
        Get-AzureRmSubscription
   
        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>
   
        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Azure Data Lake Store 帐户与 Azure 资源组是相关联的。 首先创建 Azure 资源组。
   
        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"
   
    ![创建 Azure 资源组](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. 创建 Azure Data Lake Store 帐户。 指定名称必须仅包含小写字母与数字。
   
        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"
   
    ![创建 Azure Data Lake Store 帐户](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")
4. 验证是否已成功创建帐户。
   
        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName
   
    此输出应为 **True**。

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>在 Azure Data Lake Store 中创建目录结构
可以在 Azure Data Lake Store 帐户下创建目录，用于管理和存储数据。

1. 指定根目录。
   
        $myrootdir = "/"
2. 在指定根下，创建一个名为 **mynewdirectory** 的新目录。
   
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 验证是否已成功创建新目录。
   
        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir
   
    应该会显示如下输出：
   
    ![验证目录](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

## <a name="upload-data-to-your-azure-data-lake-store"></a>将数据上载到 Azure Data Lake Store
可以直接将数据上载到 Data Lake Store 的根级别，也可以上载到在帐户中创建的目录。 下面的代码段演示了如何将一些示例数据上载到上一节中创建的目录 (**mynewdirectory**)。

如果正在查找一些示例数据进行上载，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。 下载文件，并将其存储在计算机的本地目录，如 C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>重命名、下载并删除 Data Lake Store 中的数据
使用以下命令，重命名文件：

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

使用以下命令，下载文件：

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

使用以下命令，删除文件：

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

出现提示时，输入 **Y** 以删除项目。 如果有多个要删除的文件，可以用逗号分隔提供所有路径。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>删除你的 Azure Data Lake Store 帐户
使用以下命令删除 Data Lake Store 帐户。

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

出现提示时，输入 **Y** 删除帐户。

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO2-->


