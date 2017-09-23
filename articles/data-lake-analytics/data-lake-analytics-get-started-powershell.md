---
title: "通过 Azure PowerShell 开始使用 Azure Data Lake Analytics | Microsoft Docs"
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
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: faf17bcac66a70fc78bb171e172886fd2dcadca8
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017

---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>通过 Azure PowerShell 开始使用 Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure PowerShell 创建 Azure Data Lake Analytics 帐户，然后提交并运行 U-SQL 作业。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>先决条件

开始学习本教程之前，必须做好以下准备：

* **一个 Azure Data Lake Analytics 帐户**。 请参阅 [Data Lake Analytics 入门](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-get-started-portal)。
* **配备 Azure PowerShell 的工作站**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="log-in-to-azure"></a>登录 Azure

本教程假定观看者已熟悉如何使用 Azure PowerShell。 具体而言，观看者需要知道如何登录到 Azure。 如需帮助，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps)。

使用订阅名称登录：

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

除订阅名称外，还可使用订阅 ID 登录：

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

如果成功，此命令的输出将类似于以下文本：

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>准备教程

本教程中的 PowerShell 代码片段使用上述变量来存储该信息：

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>获取有关 Data Lake Analytics 帐户的信息

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>提交 U-SQL 作业

创建一个 PowerShell 变量以保存 U-SQL 脚本。

```
$script = @"
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

"@
```

提交该脚本。

```
$job = Submit-AdlJob -AccountName $adla –Script $script
```

或者，可将脚本保存为文件，然后使用以下命令提交：

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -AccountName $adla –ScriptPath $filename
```


获取特定作业的状态。 继续使用此 cmdlet，直至作业完成。

```
$job = Get-AdlJob -AccountName $adla -JobId $job.JobId
```

可以使用 Wait-AdlJob cmdlet，而不必反复调用 Get-AdlAnalyticsJob 直至作业完成。

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

下载输出文件。

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>另请参阅
* 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。

