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
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a86fd04a7ec0cffabe42d30132b97777c752bbde
ms.openlocfilehash: e37b698436c067faa20b0e589078927d5955934a


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>通过 Azure PowerShell 开始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 Azure PowerShell 来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上载和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Data Lake Store 概述](data-lake-store-overview.md)。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

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

    ![创建 Azure 资源组](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "创建 Azure 资源组")
3. 创建 Azure Data Lake Store 帐户。 指定名称必须仅包含小写字母与数字。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![创建 Azure Data Lake Store 帐户](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "创建 Azure Data Lake Store 帐户")
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

    ![验证目录](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "验证目录")

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

## <a name="performance-guidance-while-using-powershell"></a>使用 PowerShell 时的性能指南

使用 PowerShell 操作 Data Lake Store 时，调整下面这个最重要的设置可以获得最佳性能：

| 属性            | 默认 | 说明 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | 使用此参数可以选择用于上载或下载每个文件的并行线程数。 此数字表示可以针对每个文件分配的最大线程数，但根据具体的方案，获得的线程可能少于此数目（例如，如果只是上载 1KB 的文件，则即使请求了 20 个线程，也只能获得一个线程）。  |
| ConcurrentFileCount | 10      | 此参数专门为上载或下载文件夹。 此参数确定可上载或下载的并发文件数。 此数字表示一次性可上载或下载的最大并发文件数，但根据具体的方案，获得的并发性可能少于此数目（例如，如果只是上载两个文件，则即使请求 15 个并发文件上载进程，也只能获得两个）。 |

**示例**

此命令针对每个文件使用 20 个线程以及 100 个并发文件，将 Azure Data Lake Store 中的文件下载到用户的本地驱动器。

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>如何确定要为这些参数设置多大的值？

请参考下面的指导。

* **步骤 1：确定线程总数** - 首先，请计算要使用的线程总数。 一般指导原则是，应该为每个物理核心使用 6 个线程。

        Total thread count = total physical cores * 6

    **示例**

    假设在包含 16 个核心 D14 VM 中运行 PowerShell 命令

        Total thread count = 16 cores * 6 = 96 threads


* **步骤 2：计算 PerFileThreadCount** - 根据文件的大小计算 PerFileThreadCount。 对于小于 2.5GB 的文件，没有必要更改此参数，因为默认值 10 就已足够。 对于大于 2.5GB 的文件，应该为前 2.5GB 使用 10 个线程作为基础，文件大小每增加 256MB，就多使用 1 个线程。 如果要复制文件大小有很大变化的文件夹，请考虑根据类似的文件大小将这些文件分组。 文件大小有差异可能会导致性能不佳。 如果无法将类似大小的文件分组，应该根据最大文件大小设置 PerFileThreadCount。

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **示例**

    假设有 100 个文件，其大小范围为 1GB 到 10GB，可以在公式中使用 10GB 作为最大文件大小，如下所示。

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **步骤 3：计算 ConcurrentFilecount** - 根据以下公式，使用线程总数和 PerFileThreadCount 计算 ConcurrentFileCount。

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **示例**

    沿用前面使用的示例值

        96 = 40 * ConcurrentFileCount

    **ConcurrentFileCount** 为 **2.4**，舍入为 **2**。

### <a name="further-tuning"></a>进一步调整

由于要处理的文件大小有很大的差异，因此可能需要进一步调整。 如果所有或大多数文件都比较大，接近 10GB，则可以有效地利用上述计算公式。 但是，如果文件大小差异很大，许多文件都比较小，则可以减小 PerFileThreadCount。 减小 PerFileThreadCount 即可增大 ConcurrentFileCount。 假设大多数文件小于 5GB，我们可根据以下公式重新计算：

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

现在，**ConcurrentFileCount** 是 96/20，即 4.8，舍入为 **4**。

可以根据文件大小的分布，通过调大或调小 **PerFileThreadCount** 来继续调整这些设置。

### <a name="limitation"></a>限制

* **文件数小于 ConcurrentFileCount**：如果要上载的文件数小于计算得出的 **ConcurrentFileCount**，应该减小 **ConcurrentFileCount**，使其等于文件数。 可以使用所有剩余线程来增大 **PerFileThreadCount**。

* **线程过多**：如果在不增加群集大小的情况下大幅增加线程计数，将会面临性能下降的风险。 在 CPU 上执行上下文切换时，可能会出现资源争用的问题。

* **并发性不足**：如果并发性不足，可能表示群集太小。 可以在群集中增加节点数目，这样即可提高并发性。

* **限制错误**：并发性过高时，可能会出现限制错误。 如果看到限制错误，应该降低并发性，或者与我们联系。

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Dec16_HO2-->


