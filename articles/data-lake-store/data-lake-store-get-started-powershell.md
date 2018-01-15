---
title: "通过 PowerShell 开始使用 Azure Data Lake Store | Microsoft Docs"
description: "使用 Azure PowerShell 创建 Data Lake Store 帐户并执行基本操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 31c98b930ccb8203316e3a3b13c3c0dc7d74dd9c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>通过 Azure PowerShell 开始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

了解如何使用 Azure PowerShell 来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Data Lake Store 概述](data-lake-store-overview.md)。

## <a name="prerequisites"></a>系统必备

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="authentication"></a>身份验证
本文对 Data Lake Store 使用一种较为简单的身份验证方法，其中会提示输入 Azure 帐户凭据。 系统会根据登录用户的访问级别监管对 Data Lake Store 帐户和文件系统的访问权限。 不过，也可以使用其他方法在 Data Lake Store 中进行身份验证，即**最终用户身份验证**或**服务到服务身份验证**。 有关如何进行身份验证的说明和详细信息，请参阅[最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)或[服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="create-an-azure-data-lake-store-account"></a>创建 Azure Data Lake Store 帐户
1. 从桌面打开新的 Windows PowerShell 窗口。 输入以下代码片段以登录到 Azure 帐户，设置订阅，并注册 Data Lake Store 提供程序。 当系统提示输入登录信息时，请确保以订阅管理员/所有者身份登录：

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

    ![创建 Azure 资源组](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "创建 Azure 资源组")
3. 创建 Azure Data Lake Store 帐户。 指定名称必须仅包含小写字母与数字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![创建 Azure Data Lake Store 帐户](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "创建 Azure Data Lake Store 帐户")
4. 验证是否已成功创建帐户。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    cmdlet 的输出应为 True。

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>在 Azure Data Lake Store 中创建目录结构
可以在 Azure Data Lake Store 帐户下创建目录，用于管理和存储数据。

1. 指定根目录。

        $myrootdir = "/"
2. 在指定根下，创建一个名为 **mynewdirectory** 的新目录。

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 验证是否已成功创建新目录。

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    显示的输出应该如以下屏幕截图所示：

    ![验证目录](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "验证目录")

## <a name="upload-data-to-your-azure-data-lake-store"></a>将数据上传到 Azure Data Lake Store
可以直接将数据上传到 Data Lake Store 的根级别，也可以上传到在帐户中创建的目录。 此节中的代码片段演示了如何将一些示例数据上传到上一节中创建的目录 (**mynewdirectory**)。

如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。 下载文件，并将其存储在计算机的本地目录中，如 C:\sampledata\。

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>重命名、下载以及删除 Data Lake Store 中的数据
使用以下命令，重命名文件：

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

使用以下命令，下载文件：

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

使用以下命令，删除文件：

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

出现提示时，输入 **Y** 以删除项目。 如果有多个要删除的文件，可以用逗号分隔提供所有路径。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>删除 Azure Data Lake Store 帐户
使用以下命令删除 Data Lake Store 帐户。

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

出现提示时，输入 **Y** 删除帐户。

## <a name="next-steps"></a>后续步骤
* [将 PowerShell 与 Azure Data Lake Store 配合使用的性能优化指南](data-lake-store-performance-tuning-powershell.md)
* [使用 Azure Data Lake Store 满足大数据要求](data-lake-store-data-scenarios.md) 
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)