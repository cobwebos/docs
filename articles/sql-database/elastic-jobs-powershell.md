---
title: 使用 PowerShell 创建 Azure SQL 数据库弹性作业代理 | Microsoft Docs
description: 了解如何使用 PowerShell 创建弹性作业代理。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 53e10636535c553ac5fa17b5f4aac1000cd138bc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445376"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>使用 PowerShell 创建弹性作业代理

使用[弹性作业](sql-database-job-automation-overview.md#elastic-database-jobs)，可以跨多个数据库并行运行一个或多个 Transact-SQL (T-SQL) 脚本。

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

安装 **Az.Sql** 1.1.1-preview 模块以获得最新弹性作业 cmdlet。 以管理员访问权限在 PowerShell 中运行以下命令。

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

- 除了 **Az.Sql** 1.1.1-preview 模块之外，本教程还需要 *sqlserver* PowerShell 模块。 有关详细信息，请参阅[安装 SQL Server PowerShell 模块](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module)。


## <a name="create-required-resources"></a>创建所需资源

创建弹性作业代理需要一个用作[作业数据库](sql-database-job-automation-overview.md#job-database)的数据库（S0 或更高级别）。 

 下面的脚本创建新的资源组、服务器以及可用作作业数据库的数据库。下面的脚本还创建了另外一个服务器，其中包含两个用于对其执行作业的空数据库。*

弹性作业没有特定的命名要求，因此可以使用所需的任何命名约定，只要其符合 [Azure 要求](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)即可。

```powershell
# Sign in to your Azure account
Connect-AzAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>启用适合订阅的弹性作业预览版

若要使用弹性作业，请运行以下命令，在 Azure 订阅中注册此功能。 针对要在其中预配弹性作业代理的订阅运行此命令一次。 如果订阅只包含属于作业目标的数据库，则不需注册此类订阅。

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>创建弹性作业代理

弹性作业代理是用于创建、运行和管理作业的 Azure 资源。 此代理执行的作业是按计划的，或者是一次性的。

**New-AzSqlElasticJobAgent** cmdlet 要求存在 Azure SQL 数据库，因此 *ResourceGroupName*、*ServerName* 和 *DatabaseName* 参数必须都指向现有的资源。

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>创建作业凭据，使作业可以在其目标上执行脚本

作业在执行时使用数据库范围的凭据连接到目标组指定的目标数据库。 也可使用这些数据库范围的凭据连接到 master 数据库，以便枚举服务器或弹性池中的所有数据库，前提是将这其中的一个用作目标组成员类型。

必须在作业数据库中创建数据库范围的凭据。  
所有目标数据库必须有一个具有足够权限的登录名，否则作业无法成功完成。

![弹性作业凭据](media/elastic-jobs-overview/job-credentials.png)

请注意，除了图像中的凭据，还在以下脚本中添加了 *GRANT* 命令。 这些权限是为此示例作业选择的脚本所需要的。 由于此示例会在目标数据库中创建新表，因此每个目标DB 需要适当的权限才能成功运行。

若要（在作业数据库中）创建所需的作业凭据，请运行以下脚本：

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>定义需对其运行作业的目标数据库

[目标组](sql-database-job-automation-overview.md#target-group)定义可以在其上执行作业步骤的数据库集（包含一个或多个数据库）。 

以下代码片段将创建两个目标组：*ServerGroup* 和 *ServerGroupExcludingDb2*。 *ServerGroup* 的目标是执行时在服务器上存在的所有数据库，*ServerGroupExcludingDb2* 的目标是服务器上的所有数据库，*TargetDb2* 除外：

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>创建作业

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>创建作业步骤

此示例为要运行的作业定义两个作业步骤。 第一个作业步骤 (*step1*) 在目标组 *ServerGroup* 的每个数据库中创建新表 (*Step1Table*)。 第二个作业步骤 (*step2*) 在除 *TargetDb2* 之外的每个数据库中创建新表 (*Step2Table*)，因为[以前定义的](#define-the-target-databases-you-want-to-run-the-job-against)目标组已指定将其排除。

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>运行作业

若要立即启动作业，请运行以下命令：

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzSqlElasticJob
$JobExecution
```

成功完成以后，应该会在 TargetDb1 中看到两个新表，在 TargetDb2 中只看到一个新表：


   ![SSMS 中的新表验证](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>监视作业执行操作的状态

以下代码片段获取作业执行详细信息：

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

### <a name="job-execution-states"></a>作业执行状态

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

## <a name="schedule-the-job-to-run-later"></a>计划要在以后运行的作业

若要计划在特定时间运行的作业，请运行以下命令：

```powershell
# Run every hour starting from now
$Job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来删除本教程中创建的 Azure 资源。

> [!TIP]
> 如果打算继续使用这些作业，请勿清除本文中创建的资源。 如果不打算继续学习，请通过以下步骤删除本文中创建的所有资源。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>后续步骤

本教程介绍了如何对一组数据库运行 Transact-SQL 脚本。  此外还介绍了如何执行以下任务：

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
>[使用 Transact-SQL 管理弹性作业](elastic-jobs-tsql.md)
