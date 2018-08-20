---
title: 使用 Azure 弹性数据库作业清理 SSISDB 日志 | Microsoft Docs
description: 本文介绍如何使用 Azure 弹性数据库作业来触发现存的用于此目的的存储过程，以便清理 SSISDB 日志
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 5bd300a318008b34415f8bc9625cad3c38dec873
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "40106974"
---
# <a name="clean-up-ssisdb-logs-with-azure-elastic-database-jobs"></a>使用 Azure 弹性数据库作业清理 SSISDB 日志

本文介绍如何使用 Azure 弹性数据库作业来触发存储过程，以便清理 SQL Server Integration Services 目录数据库 `SSISDB` 的日志。

弹性数据库作业是一项 Azure 服务，便于针对一个或一组数据库自动执行和运行作业。 可以使用 Azure 门户、Transact-SQL、PowerShell 或 REST API 来计划、运行和监视这些作业。 使用弹性数据库作业来触发适用于一次性或计划性日志清理的存储过程。 可以根据 SSISDB 资源使用情况来选择计划时间间隔，避免数据库负载过重。

有关详细信息，请参阅[通过弹性数据库作业管理数据库组](../sql-database/elastic-jobs-overview.md)。

以下部分介绍如何触发存储过程 `[internal].[cleanup_server_retention_window_exclusive]`，以便删除不在管理员设置的保留期限内的 SSISDB 日志。

## <a name="clean-up-logs-with-power-shell"></a>使用 Power Shell 清理日志

以下示例 PowerShell 脚本创建一个新的弹性作业，用于触发适用于 SSISDB 日志清理的存储过程。 有关详细信息，请参阅[使用 PowerShell 创建弹性作业代理](../sql-database/elastic-jobs-powershell.md)。

### <a name="create-parameters"></a>创建参数

``` powershell
# Parameters needed to create the Job Database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL server(for example, yhxserver) to hold the SSISDBLogCleanup job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for the Job Database to be created in the given SQL Server"),
# The Job Database should be a clean,empty,S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create the Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your new Elastic Job agent"),

# Parameters needed to create the job credential in the Job Databse to connect to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for SSISDBLogCleanup job user to connect to SSISDB database for log cleanup"),
# Parameters needed to create a login and a user in the SSISDB of the target server
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of the target Azure SQL server which contains SSISDB you need to cleanup, for example, myserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set job scheduling to trigger execution of cleanup stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run the job to cleanup SSISDB logs outside the log retention window immediately(Y/N). Make sure the retention window is set appropriately before running the following powershell scripts. Those removed SSISDB logs cannot be recoverd"),
$IntervalType = $(Read-Host "Please enter the interval type for the execution schedule of SSISDB log cleanup stored procedure. For the interval type, Year, Month, Day, Hour, Minute, Second can be supported."),
$IntervalCount = $(Read-Host "Please enter the detailed interval value in the given interval type for the execution schedule of SSISDB log cleanup stored procedure"),
# StartTime of the execution schedule is set as the current time as default. 
$StartTime = (Get-Date)
```

### <a name="trigger-the-cleanup-stored-procedure"></a>触发清理存储过程

```powershell
# Install the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force
# You may need to restart the powershell session
# Install the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Place AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create a Job Database which is used for defining jobs of triggering SSISDB log cleanup stored procedure and tracking cleanup history of jobs
Write-Output "Creating a blank SQL database to be used as the SSISDBLogCleanup  Job Database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable the Elastic Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create the Elastic Job agent
Write-Output "Creating the Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create the job credential in the Job Database to connect to SSISDB database in the target server for log cleanup
Write-Output "Creating job credential to connect to SSISDB database..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# In the master database of the target SQL server which contains SSISDB to cleanup 
# - Create the job user login
Write-Output "Grant permissions on the master database of the target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# For SSISDB database of the target SQL server
# - Create the SSISDBLogCleanup user from the SSISDBlogCleanup user login
# - Grant permissions for the execution of SSISDB log cleanup stored procedure 
Write-Output "Grant appropriate permissions on SSISDB database..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser"

$TargetDatabase | % {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create a target group which includes SSISDB database needed to cleanup
Write-Output "Creating the target group including only SSISDB databse needed to cleanup ..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create the job to trigger execution of SSISDB log cleanup stored procedure
Write-Output "Creating a new job to trigger execution of the stored procedure for SSISDB log cleanup"
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add the job step to execute internal.cleanup_server_retention_window_exclusive
Write-Output "Adding the job step for the cleanup stored procedure execution"
$SqlText = "EXEC internal.cleanup_server_retention_window_exclusive"
$Job | Add-AzureRmSqlElasticJobStep -Name "step to execute cleanup stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run the job to immediately start cleanup stored procedure execution for once
IF(($RunJobOrNot = "Y") -Or ($RunJobOrNot = "y"))
{
Write-Output "Start a new execution of the stored procedure for SSISDB log cleanup immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule the job running to trigger stored procedure execution on schedule for removing SSISDB logs outside the retention window
Write-Output "Start the execution schedule of the stored procedure for SSISDB log cleanup..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

## <a name="clean-up-logs-with-transact-sql"></a>使用 Transact-SQL 清理日志

以下示例 Transact-SQL 脚本创建一个新的弹性作业，用于触发适用于 SSISDB 日志清理的存储过程。 有关详细信息，请参阅[使用 Transact-SQL (T-SQL) 创建和管理弹性数据库作业](../sql-database/elastic-jobs-tsql.md)。

1. 创建或标识将要成为 SSISDB 清理作业数据库的空的 S0 或更高级别的 Azure SQL 数据库。 然后在 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent)中创建弹性作业代理。

2. 在作业数据库中，为 SSISDB 日志清理作业创建凭据。 此凭据用于连接到 SSISDB 数据库，以便清理日志。

    ```sql
    -- Connect to the job database specified when creating the job agent
    -- Create a database master key if one does not already exist, using your own password.  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';  

    -- Create a credential for SSISDB log cleanup.  
    CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
    ```

3. 定义目标组，其中包含需为其运行清理存储过程的 SSISDB 数据库。

    ```sql
    -- Connect to the job database 
    -- Add a target group 
    EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

    -- Add SSISDB database into the target group
    EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
    @target_type = 'SqlDatabase',
    @server_name = '<EnterSSISDBTargetServerName>',
    @database_name = '<EnterSSISDBName>'

    --View the recently created target group and target group members
    SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
    SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
    ```
4. 为 SSISDB 数据库授予适当的权限。 SSISDB 目录必须有适当的权限，否则存储过程无法成功运行 SSISDB 日志清理操作。 如需详细指导，请参阅[管理登录名](../sql-database/sql-database-manage-logins.md)。

    ```sql
    -- Connect to the master database in the target server including SSISDB 
    CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

    -- Connect to SSISDB database in the target server to cleanup logs
    CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
    GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser
    ```
5. 创建作业并添加一个作业步骤，以便触发适用于 SSISDB 日志清理的存储过程的执行。

    ```sql
    --Connect to the job database 
    --Add the job for the execution of SSISDB log cleanup stored procedure.
    EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs which are outside the retention window'

    --Add a job step to execute internal.cleanup_server_retention_window_exclusive
    EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
    @command=N'EXEC internal.cleanup_server_retention_window_exclusive',
    @credential_name='SSISDBLogCleanupCred',
    @target_group_name='SSISDBTargetGroup'
    ```
6. 在继续操作之前，请确保已正确设置保留期限。 超出期限的 SSISDB 日志会被删除，不能恢复。

   然后即可立即运行作业，开始 SSISDB 日志清理。

    ```sql
    --Connect to the job database 
    --Run the job immediately to execute the stored procedure for SSISDB log cleanup
    declare @je uniqueidentifier
    exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

    --Watch the execution results for SSISDB log cleanup 
    select @je
    select * from jobs.job_executions where job_execution_id = @je
    ```
7. 可以选择对作业的执行进行计划，以便按计划删除超出保留期限的 SSISDB 日志。 使用类似的语句更新作业参数。

    ```sql
    --Connect to the job database 
    EXEC jobs.sp_update_job
    @job_name='CleanupSSISDBLog',
    @enabled=1,
    @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
    @schedule_interval_count='<EnterDetailedIntervalValue>',
    @schedule_start_time='<EnterProperStartTimeForSchedule>',
    @schedule_end_time='<EnterProperEndTimeForSchedule>'
    ```

## <a name="monitor-the-cleanup-job-in-the-azure-portal"></a>在 Azure 门户中监视清理作业

可以在 Azure 门户中监视清理作业的执行。 每次执行都可以查看作业的状态、开始时间和结束时间。

![在 Azure 门户中监视清理作业](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

## <a name="monitor-the-cleanup-job-with-transact-sql"></a>使用 Transact-SQL 监视清理作业

也可使用 Transact-SQL 查看清理作业的执行历史记录。

```sql
--Connect to the job database 
--View all execution statuses for the job to cleanup SSISDB logs
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>后续步骤

对于与 Azure-SSIS Integration Runtime 相关的管理和监视任务，请参阅以下文章。 Azure-SSIS IR 是存储在 Azure SQL 数据库的 SSISDB 中的 SSIS 包的运行时引擎。

-   [重新配置 Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)

-   [监视 Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
