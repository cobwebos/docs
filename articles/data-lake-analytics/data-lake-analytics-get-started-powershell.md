---
title: "通过 Azure PowerShell 开始使用 Azure Data Lake Analytics | Microsoft 文档"
description: "使用 Azure PowerShell 创建 Data Lake Analytics 帐户，使用 U-SQL 创建 Data Lake Analytics 作业，并提交该作业。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>教程：通过使用 Azure PowerShell 实现 Azure Data Lake Analytics 入门
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure PowerShell 创建 Azure Data Lake Analytics 帐户，然后提交并运行 U-SQL 作业。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>先决条件
开始学习本教程之前，必须做好以下准备：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **配备 Azure PowerShell 的工作站**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="preparing-for-the-tutorial"></a>准备教程
若要创建 Data Lake Analytics 帐户，首先需定义：

* **Azure 资源组**：必须在 Azure 资源组中创建一个 Data Lake Analytics 帐户。
* **Data Lake Analytics 帐户名**：Data Lake 帐户名只能包含小写字母和数字。
* **位置**：支持 Data Lake Analytics 的 Azure 数据中心之一。
* **默认的 Data Lake Store 帐户**：每个 Data Lake Analytics 帐户都有一个默认的 Data Lake Store 帐户。 这些帐户必须位于同一位置。

本教程中的 PowerShell 代码片段使用上述变量来存储该信息

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>创建 Data Lake Analytics 帐户

如果还没有资源组，请创建一个，因为需要使用它。 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

每个 Data Lake Analytics 帐户都需要一个默认的 Data Lake Store 帐户，用于存储日志。 可以重复使用现有的帐户，也可以创建新帐户。 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

资源组和 Data Lake Store 帐户可用以后，请创建 Data Lake Analytics 帐户。

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>获取有关 Data Lake Analytics 帐户的信息

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>提交 U-SQL 作业

使用以下 U-SQL 脚本创建文本文件。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

提交该脚本。

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>监视 U-SQL 作业

列出帐户中的所有作业。 输出包括当前运行的作业以及最近完成的那些作业。

```
Get-AdlJob -Account $adla
```

获取特定作业的状态。

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

可以使用 Wait-AdlJob cmdlet，而不必反复调用 Get-AdlAnalyticsJob 直至作业完成。

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

作业完成后，可以列出文件夹中的文件，检查输出文件是否存在。

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

检查文件是否存在。

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>上传和下载文件

下载 U-SQL 脚本的输出。

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


上传一个文件，用作 U-SQL 脚本的输入。

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>另请参阅
* 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。

