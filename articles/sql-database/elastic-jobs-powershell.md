---
title: 使用 PowerShell 创建弹性作业代理
description: 了解如何使用 PowerShell 创建弹性作业代理。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 74a72df9d8c0bc8a578fea57ab81fb496f8e6add
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420367"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>使用 PowerShell 创建弹性作业代理

使用[弹性作业](sql-database-job-automation-overview.md#elastic-database-jobs-preview)，可以跨多个数据库并行运行一个或多个 Transact-SQL (T-SQL) 脚本。

本教程介绍跨多个数据库运行查询所需的步骤：

> [!div class="checklist"]
> * 创建弹性作业代理
> * 创建作业凭据，使作业可以在其目标上执行脚本
> * 定义要对其运行作业的目标（服务器、弹性池、数据库、分片映射）
> * 在目标数据库中创建数据库范围的凭据，以便代理在连接后执行作业
> * 创建作业
> * 向作业添加作业步骤
> * 开始执行作业
> * 监视作业

## <a name="prerequisites"></a>先决条件

弹性数据库作业的升级版本有一组新的 PowerShell cmdlet，可在迁移期间使用。 这些新的 cmdlet 将所有现有的作业凭据、目标（包括数据库、服务器、自定义集合）、作业触发器、作业计划、作业内容和作业转移到新的弹性作业代理。

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>安装最新的弹性作业 cmdlet

如果还没有 Azure 订阅，请在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

安装 **Az.Sql** 模块以获取最新弹性作业 cmdlet。 以管理访问权限在 PowerShell 中运行以下命令。

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

除 **Az.Sql** 模块外，本教程还需要 *SqlServer* PowerShell 模块。 有关详细信息，请参阅[安装 SQL Server PowerShell 模块](/sql/powershell/download-sql-server-ps-module)。

## <a name="create-required-resources"></a>创建所需资源

创建弹性作业代理需要一个用作[作业数据库](sql-database-job-automation-overview.md#job-database)的数据库（S0 或更高级别）。

下面的脚本创建新的资源组、服务器以及可用作作业数据库的数据库。 第二个脚本创建另一个服务器，其中包含两个用于对其执行作业的空数据库。

弹性作业没有特定的命名要求，因此可以使用所需的任何命名约定，只要其符合 [Azure 要求](/azure/architecture/best-practices/resource-naming)即可。

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>使用弹性作业

若要使用弹性作业，请运行以下命令，在 Azure 订阅中注册此功能。 针对要在其中预配弹性作业代理的订阅运行此命令一次。 如果订阅只包含属于作业目标的数据库，则不需注册此类订阅。

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>创建弹性作业代理

弹性作业代理是用于创建、运行和管理作业的 Azure 资源。 此代理执行的作业是按计划的，或者是一次性的。

**New-AzSqlElasticJobAgent** cmdlet 要求存在 Azure SQL 数据库，因此，*resourceGroupName*、*serverName* 和 *databaseName* 参数必须都指向现有的资源。

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>创建作业凭据

作业在执行以及执行脚本时使用数据库范围的凭据连接到目标组指定的目标数据库。 也可使用这些数据库范围的凭据连接到 master 数据库，以便枚举服务器或弹性池中的所有数据库，前提是将这其中的一个用作目标组成员类型。

必须在作业数据库中创建数据库范围的凭据。 所有目标数据库必须有一个具有足够权限的登录名，否则作业无法成功完成。

![弹性作业凭据](media/elastic-jobs-overview/job-credentials.png)

请注意，除了图像中的凭据，还在以下脚本中添加了 **GRANT** 命令。 这些权限是为此示例作业选择的脚本所需要的。 由于此示例会在目标数据库中创建新表，每个目标 DB 需要适当的权限才能成功运行。

若要（在作业数据库中）创建所需的作业凭据，请运行以下脚本：

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>定义要对其运行作业的目标数据库

[目标组](sql-database-job-automation-overview.md#target-group)定义可以在其上执行作业步骤的数据库集（包含一个或多个数据库）。

以下代码片段创建两个目标组：*serverGroup* 和 *serverGroupExcludingDb2*。 *serverGroup* 的目标是执行时存在于服务器上的所有数据库，*serverGroupExcludingDb2* 的目标是服务器上的所有数据库，*targetDb2* 除外：

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>创建作业和步骤

此示例为要运行的作业定义作业和两个作业步骤。 第一个作业步骤 (*step1*) 在目标组 *ServerGroup* 的每个数据库中创建新表 (*Step1Table*)。 第二个作业步骤 (*step2*) 在除 *TargetDb2* 之外的每个数据库中创建新表 (*Step2Table*)，因为之前定义的目标组已指定将其排除。

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>运行作业

若要立即启动作业，请运行以下命令：

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

成功完成以后，应该会在 TargetDb1 中看到两个新表，在 TargetDb2 中只看到一个新表：

   ![SSMS 中的新表验证](media/elastic-jobs-overview/job-execution-verification.png)

还可以将作业计划为在以后运行。 若要计划在特定时间运行的作业，请运行以下命令：

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>监视作业执行操作的状态

以下代码片段获取作业执行详细信息：

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

下表列出了可能的作业执行状态：

|状态|说明|
|:---|:---|
|**创建时间** | 作业执行刚刚创建，还没有进行。|
|**InProgress** | 作业执行目前正在进行中。|
|**WaitingForRetry** | 作业执行无法完成其操作，正在等待重试。|
|成功  | 作业执行已成功完成。|
|**SucceededWithSkipped** | 作业执行已成功完成，但跳过了它的一些子项。|
|失败  | 作业执行失败，已用尽重试次数。|
|**TimedOut** | 作业执行已超时。|
|**已取消** | 作业执行已取消。|
|已跳过  | 已跳过作业执行，因为同一作业步骤的另一个执行已在同一目标上运行。|
|**WaitingForChildJobExecutions** | 作业执行正在等待其子执行完成。|

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来删除本教程中创建的 Azure 资源。

> [!TIP]
> 如果打算继续使用这些作业，请勿清理本文中创建的资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何对一组数据库运行 Transact-SQL 脚本。 此外还介绍了如何执行以下任务：

> [!div class="checklist"]
> * 创建弹性作业代理
> * 创建作业凭据，使作业可以在其目标上执行脚本
> * 定义要对其运行作业的目标（服务器、弹性池、数据库、分片映射）
> * 在目标数据库中创建数据库范围的凭据，以便代理在连接后执行作业
> * 创建作业
> * 将作业步骤添加到作业
> * 开始执行作业
> * 监视作业

> [!div class="nextstepaction"]
> [使用 Transact-SQL 管理弹性作业](elastic-jobs-tsql.md)
