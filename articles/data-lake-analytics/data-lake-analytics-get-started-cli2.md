---
title: "通过 Azure CLI 2.0 开始使用 Azure Data Lake Analytics | Microsoft Docs"
description: "了解如何使用 Azure 命令行界面 2.0 创建 Data Lake Analytics 帐户、如何使用 U-SQL 创建 Data Lake Analytics 作业，以及如何提交该作业。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 25278e621155e7c90b5bb870d08793d285f4edff
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>教程：Azure Data Lake Analytics 入门（使用 Azure CLI 2.0）
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure CLI 2.0 来创建 Azure Data Lake Analytics 帐户、如何在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定义 Data Lake Analytics 作业，以及如何将作业提交至 Data Lake Analytics 帐户。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

本教程将开发一个作业，它能读取制表符分隔值 (TSV) 文件，并将其转换为逗号分隔值 (CSV) 文件。 若要通过其他支持的工具来完成此教程，请使用本部分顶部的下拉列表。

## <a name="prerequisites"></a>先决条件
开始学习本教程之前，必须做好以下准备：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure CLI 2.0**。 请参阅 [安装和配置 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="log-in-to-azure"></a>登录 Azure

若要登录到 Azure 订阅，请执行以下命令：

```azurecli
az login
```

你需要浏览到 URL，然后输入身份验证代码。  然后按说明输入凭据。

登录以后，登录命令会列出你的订阅。

若要使用特定订阅，请执行以下命令：

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户
在运行任何作业之前，你需要一个 Data Lake Analytics 帐户。 若要创建 Data Lake Analytics 帐户，必须指定以下各项：

* **Azure 资源组**。 必须在 Azure 资源组中创建一个 Data Lake Analytics 帐户。 使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 能够以组的方式处理应用程序中的资源。 你可以通过一个协调的操作为应用程序部署、更新或删除所有资源。  
  
    若要列出订阅中的现有资源组，请执行以下命令：
  
    ```azurecli
    az group list 
    ```

    若要创建新的资源组：
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Data Lake Analytics 帐户名**。 每个 Data Lake Analytics 帐户都有一个名称。
* **位置**。 使用支持 Data Lake Analytics 的 Azure 数据中心之一。
* **默认的 Data Lake Store 帐户**：每个 Data Lake Analytics 帐户都有一个默认的 Data Lake Store 帐户。
  
    若要列出现有的 Data Lake Store 帐户，请执行以下命令：

    ```azurecli
    az dls account list
    ```
  
    若要创建新的 Data Lake Store 帐户，请执行以下命令：
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

请使用以下语法创建 Data Lake Analytics 帐户：

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

创建帐户以后，可以使用以下命令列出帐户并显示帐户详细信息：

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>将数据上传到 Data Lake Store
本教程将处理一些搜索日志。  搜索日志可以存储在 Data Lake Store 或 Azure Blob 存储中。 

Azure 门户提供一个用户界面，可将一些示例数据复制到默认 Data Lake Store 帐户，包括搜索日志文件。 要将数据上传至默认 Data Lake Store 帐户，请参阅 [准备源数据](data-lake-analytics-get-started-portal.md#prepare-source-data) 。

若要使用 CLI 2.0 上载文件，请使用以下命令：

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics 还可以访问 Azure Blob 存储。  有关将数据上传到 Azure Blob 存储的详细信息，请参阅 [将 Azure CLI 用于 Azure 存储](../storage/storage-azure-cli.md)。

## <a name="submit-data-lake-analytics-jobs"></a>提交 Data Lake Analytics 作业
Data Lake Analytics 作业使用 U-SQL 语言编写而成。 若要了解有关 U-SQL 的详细信息，请参阅 [U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**创建 Data Lake Analytics 作业脚本**

使用下列 U-SQL 脚本创建文本文件，并将其保存至工作站：
  
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

除非将源文件复制到其他位置，否则不要修改这两条路径。  如果输出文件夹不存在，Data Lake Analytics 将创建一个。

对于存储在默认 Data Lake Store 帐户中的文件而言，使用相对路径更为简单。 也可使用绝对路径。  例如：

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

必须使用绝对路径来访问链接的存储帐户中的文件。  链接的 Azure 存储帐户中所储存文件的语法是：

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > 当前不支持对含公共 Blob 的 Azure Blob 容器或公共容器的访问权限。      
  > 
  > 

**提交作业**

请使用以下语法提交作业。

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

例如：

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**列出作业并显示作业详细信息**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**取消作业**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>检索作业结果

作业完成后，可以使用以下命令列出输出文件，并下载这些文件：

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

例如：

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用其他工具完成本教程，请单击页面顶部的选项卡选择器。
* 若要查看 Data Lake Analytics CLI 2.0 参考文档，请参阅 [Data Lake Analytics - az dla](https://docs.microsoft.com/cli/azure/dla)。
* 若要查看 Data Lake Store CLI 2.0 参考文档，请参阅 [Data Lake Store - az dls](https://docs.microsoft.com/cli/azure/dls)。
* 若要查看更复杂的查询，请参阅 [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)（使用 Azure Data Lake Analytics 分析网站日志）。
* 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。


