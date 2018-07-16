---
title: 使用 Azure PowerShell 管理 Azure Data Lake Analytics
description: 本文介绍如何使用 Azure PowerShell 管理 Data Lake Analytics 帐户、数据源、用户和作业。
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 94cd8de875baac31ee6cd450707be57a3d1dfdd0
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341855"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

本文介绍如何使用 Azure PowerShell 管理 Azure Data Lake Analytics 帐户、数据源、用户和作业。

## <a name="prerequisites"></a>先决条件

若要将 PowerShell 与 Data Lake Analytics 配合使用，请收集以下信息： 

* **订阅 ID**：Data Lake Analytics 帐户所在的 Azure 订阅的 ID。
* **资源组**：包含 Data Lake Analytics 帐户的 Azure 资源组的名称。
* **Data Lake Analytics 帐户名称**：Data Lake Analytics 帐户的名称。
* **默认的 Data Lake Store 帐户名称**：每个 Data Lake Analytics 帐户都有一个默认的 Data Lake Store 帐户。
* **位置**：Data Lake Analytics 帐户的位置，如“美国东部 2”或其他支持的位置。

本教程中的 PowerShell 代码片段使用上述变量来存储该信息

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>登录 Azure

### <a name="log-in-using-interactive-user-authentication"></a>使用交互式用户身份验证登录

使用订阅 ID 或订阅名称登录

```powershell
# Using subscription id
Connect-AzureRmAccount -SubscriptionId $subId

# Using subscription name
Connect-AzureRmAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>保存身份验证上下文

`Connect-AzureRmAccount` cmdlet 始终提示输入凭据。 可以使用以下 cmdlet 避免出现提示：

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>使用服务主体标识 (SPI) 登录

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzureRmAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>管理帐户


### <a name="list-accounts"></a>列出帐户

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>创建帐户

每个 Data Lake Analytics 帐户都需要一个默认的 Data Lake Store 帐户，用于存储日志。 可以重复使用现有的帐户，也可以创建帐户。 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>获取帐户信息

获取有关帐户的详细信息。

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>检查帐户是否存在

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>管理数据源
Azure Data Lake Analytics 当前支持以下数据源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 存储](../storage/common/storage-introduction.md)

每个 Data Lake Analytics 帐户都有一个默认的 Data Lake Store 帐户。 默认 Data Lake Store 帐户用于存储作业元数据和作业审核日志。 

### <a name="find-the-default-data-lake-store-account"></a>查找默认 Data Lake Store 帐户

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

可使用 `IsDefault` 属性，通过筛选数据源列表来查找默认的 Data Lake Store 帐户：

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>添加数据源

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>列出数据源

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>提交 U-SQL 作业

### <a name="submit-a-string-as-a-u-sql-job"></a>以 U-SQL 作业形式提交字符串

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>以 U-SQL 作业形式提交文件

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>列出作业

输出包括当前运行的作业以及最近完成的那些作业。

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>列出前 N 个作业

默认情况下，作业的列表按提交时间进行排序。 因此，最近提交的作业第一个出现。 默认情况下，ADLA 帐户会在 180 天内记住作业，但是 Get-AdlJob cmdlet 在默认情况下只返回前 500 个作业。 使用 -Top 参数可列出特定数量的作业。

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>按作业状态列出作业

使用 `-State` 参数。 可合并下列任意值：

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>按作业结果列出作业

使用 `-Result` 参数检测结束作业是否成功完成。 它具有下列值：

* 已取消
* 已失败
* 无
* 已成功

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>按作业提交者列出作业

`-Submitter` 参数用于标识作业的提交者。

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>按提交时间列出作业

按时间范围筛选时，可使用 `-SubmittedAfter`。


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>获取作业状态

获取特定作业的状态。

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>取消作业

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>等待作业完成

可以使用 `Wait-AdlJob` cmdlet 等待作业结束，而不是重复 `Get-AdlAnalyticsJob` 直到作业完成。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>分析作业历史记录

使用 Azure PowerShell 分析 Data Lake 分析中运行的作业的历史记录是一项强大的技术。 通过此方法可深入了解使用情况和成本。 可参阅[作业历史分析示例存储库](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)，了解详细信息  

## <a name="list-job-pipelines-and-recurrences"></a>列出作业管道和重复周期

使用 `Get-AdlJobPipeline` cmdlet 查看先前所提交作业的管道信息。

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

使用 `Get-AdlJobRecurrence` cmdlet 查看先前所提交作业的重复周期信息。

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>管理计算策略

### <a name="list-existing-compute-policies"></a>列出现有计算策略

`Get-AdlAnalyticsComputePolicy` cmdlet 可检索 Data Lake Analytics 帐户计算策略的相关信息。

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>创建计算策略

`New-AdlAnalyticsComputePolicy` cmdlet 可为 Data Lake Analytics 帐户创建新的计算策略。 此示例将指定用户可用的最大 AU 设置为 50，最小作业优先级设置为 250。

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>管理文件

### <a name="check-for-the-existence-of-a-file"></a>检查文件是否存在。

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>上传和下载

上传文件。

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

以递归方式上传整个文件夹。

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

下载文件。

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

以递归方式下载整个文件夹。

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> 如果上载或下载过程中断，则可以通过再次运行带 ``-Resume`` 标志的 cmdlet 来尝试恢复该过程。

## <a name="manage-the-u-sql-catalog"></a>管理 U-SQL 目录

可以使用 -SQL 目录组织数据和代码，以便通过 U-SQL 脚本共享数据和代码。 目录启用 Azure Data Lake 中数据的最高可能性能。 有关详细信息，请参阅 [使用 U-SQL 目录](data-lake-analytics-use-u-sql-catalog.md)。

### <a name="list-items-in-the-u-sql-catalog"></a>列出 U-SQL 目录中的项

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>列出 U-SQL 目录中的所有程序集

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>获取有关目录项的详细信息

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>在目录中存储凭据

在 U-SQL 数据库中，为 Azure 中托管的数据库创建凭据对象。 当前，U-SQL 凭据是可以通过 PowerShell 创建的唯一目录项类型。

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>管理防火墙规则

### <a name="list-firewall-rules"></a>列出防火墙规则

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>添加防火墙规则

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>修改防火墙规则

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>移除防火墙规则

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>允许 Azure IP 地址

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>使用 Azure

### <a name="get-details-of-azurerm-errors"></a>获取 AzureRm 错误的详细信息

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>验证是否正在 Windows 计算机上以管理员身份运行

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>查找 TenantID

通过订阅名称：

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

通过订阅 ID：

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

通过域地址，如“contoso.com”

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>列出所有订阅和租户 ID

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>使用模板创建 Data Lake Analytics 帐户

还可以使用以下示例使用 Azure 资源组模板：[使用模板创建 Data Lake Analytics 帐户](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* 通过 [Azure portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md) 使用 Data Lake Analytics 入门
* 使用 [Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 管理 Azure Data Lake Analytics 
