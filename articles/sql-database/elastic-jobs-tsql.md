---
title: 使用 Transact-SQL (T-SQL) 创建和管理 Azure SQL 弹性数据库作业 | Microsoft Docs
description: 使用 Transact-SQL (T-SQL) 通过弹性数据库作业跨多个数据库运行脚本。
services: sql-database
author: jaredmoo
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: jaredmoo
ms.openlocfilehash: fb6e4ebd635d8afa8e679ee5bb0f5646f28f887b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311401"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>使用 Transact-SQL (T-SQL) 创建和管理弹性数据库作业

本文通过许多示例方案说明了如何使用 T-SQL 来完成弹性作业的入门。

这些示例使用[作业数据库](elastic-jobs-overview.md#job-database)中提供的[存储过程](#job-stored-procedures)和[视图](#job-views)。

Transact-SQL (T-SQL) 用于创建、配置、执行和管理作业。 T-SQL 不支持创建弹性作业代理，因此必须先使用门户或 [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent) 创建弹性作业代理。


## <a name="create-a-credential-for-job-execution"></a>创建执行作业所需的凭据

此凭据用于连接到目标数据库，以便执行脚本。 此凭据需要在目标组指定的数据库上拥有适当的权限，否则无法成功地执行脚本。 使用服务器和/或池目标组成员时，强烈建议创建一个用于刷新此凭据的主凭据，然后再在执行作业时扩展服务器和/或池。 数据库范围的凭据在作业代理数据库中创建。 必须使用同一凭据来创建登录名，并创建基于登录名的用户，以便在目标数据库上授予登录数据库权限。


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>创建目标组（服务器）

以下示例演示如何针对服务器中的所有数据库执行作业。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-a-single-database"></a>排除单一数据库

以下示例演示如何针对服务器中的所有数据库执行作业，名为 *MappingDB* 的数据库除外。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group = N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Excude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>创建目标组（池）

以下示例演示如何以一个或多个弹性池中的所有数据库为目标。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>将新架构部署到多个数据库

以下示例演示如何将新架构部署到所有数据库。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>使用内置参数进行数据收集

在许多数据收集方案中，可以包括部分下述脚本变量，以便对作业结果进行后期处理。

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

例如，若要将同一作业执行操作的所有结果组合到一起，请使用 *$(job_execution_id)*，如以下命令所示：


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>监视数据库性能

以下示例创建一个新作业，以便从多个数据库收集性能数据。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name=’myjobcred’,
@output_server_name=’server1.database.windows.net',
@output_database_name=’<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name=’server1.database.windows.net',
@output_database_name=’resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>查看作业定义

以下示例演示了如何查看当前的作业定义。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>开始即席执行作业

以下示例演示如何立即启动作业。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>计划作业的执行

以下示例演示如何计划一项将来执行的作业。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>监视作业执行状态

以下示例演示如何查看所有作业的执行状态详细信息。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named ‘ResultsPoolJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named ‘ResultsPoolsJob’
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>取消作业

以下示例演示如何取消作业。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>删除旧的作业历史记录

以下示例演示如何删除特定日期之前的作业历史记录。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>删除作业及其所有历史记录

以下示例演示如何删除作业以及所有相关的作业历史记录。  
连接到[作业数据库](elastic-jobs-overview.md#job-database)，然后运行以下命令：

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>作业存储过程

以下存储过程位于[作业数据库](elastic-jobs-overview.md#job-database)中。



|存储过程  |说明  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     添加新的作业。    |
|[sp_update_job ](#spupdatejob)    |      更新现有的作业。   |
|[sp_delete_job](#spdeletejob)     |      删除现有的作业。   |
|[sp_add_jobstep](#spaddjobstep)    |    向作业添加步骤。     |
|[sp_update_jobstep](#spupdatejobstep)     |     更新作业步骤。    |
|[sp_delete_jobstep](#spdeletejobstep)     |     删除作业步骤。    |
|[sp_start_job](#spstartjob)    |  开始执行作业。       |
|[sp_stop_job](#spstopjob)     |     停止作业的执行。   |
|[sp_add_target_group](#spaddtargetgroup)    |     添加目标组。    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    删除目标组。     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    向目标组添加一个或一组数据库。     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     从目标组中删除目标组成员。    |
|[sp_purge_jobhistory ](#sppurgejobhistory)    |    删除作业的历史记录。     |





### <a name="spaddjob"></a>sp_add_job

添加新的作业。 
  
#### <a name="syntax"></a>语法  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>参数  

[ **@job_name =** ] 'job_name'  
作业的名称。 名称必须唯一，不能包含百分比 (%) 字符。 job_name 为 nvarchar(128)，没有默认值。

[ **@description =** ] 'description'  
作业的说明。 description 为 nvarchar(512)，默认值为 NULL。 如果省略 description，则使用空字符串。

[ **@enabled =** ] enabled  
作业的计划是否已启用。 enabled 为 bit，默认值为 0（禁用）。 如果为 0，则作业未启用，不会按计划运行，但可手动运行。 如果为 1，则作业会按计划运行，也可手动运行。

[ **@schedule_interval_type =**] schedule_interval_type  
其值指示何时会执行作业。 schedule_interval_type 为 nvarchar(50)，默认值为 Once，可以是下述值之一：
- 'Once'；
- 'Minutes'；
- 'Hours'；
- 'Days'；
- 'Weeks'；
- 'Months'

[ **@schedule_interval_count =** ] schedule_interval_count  
每次执行作业时，其间会出现的 schedule_interval_count 期间数。 schedule_interval_count 为 int，默认值为 1。 该值必须大于或等于 1。

[ **@schedule_start_time =** ] schedule_start_time  
可以开始执行作业的日期。 schedule_start_time 为 DATETIME2，默认值为 0001-01-01 00:00:00.0000000。

[ **@schedule_end_time =** ] schedule_end_time  
可以停止执行作业的日期。 schedule_end_time 为 DATETIME2，默认值为 9999-12-31 11:59:59.0000000。 

[ **@job_id =** ] job_id OUTPUT  
分配给已成功创建的作业的作业标识号。 job_id 是类型为 uniqueidentifier 的输出变量。

#### <a name="return-code-values"></a>返回代码值

0（成功）或 1（失败）

#### <a name="remarks"></a>备注
sp_add_job 必须从创建作业代理时指定的作业代理数据库运行。
通过执行 sp_add_job 来添加作业以后，即可使用 sp_add_jobstep 来添加那些为作业执行活动的步骤。 作业的初始版本号为 0，在添加第一个步骤后会递增到 1。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：

- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

### <a name="spupdatejob"></a>sp_update_job

更新现有的作业。

#### <a name="syntax"></a>语法

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>参数
[ **@job_name =** ] 'job_name'  
要更新的作业的名称。 job_name 为 nvarchar(128)。

[ **@new_name =** ] 'new_name'  
作业的新名称。 new_name 为 nvarchar(128)。

[ **@description =** ] 'description'  
作业的说明。 description 为 nvarchar(512)。

[ **@enabled =** ] enabled  
指定作业的计划是已启用 (1) 还是未启用 (0)。 enabled 为 bit。

[ **@schedule_interval_type=** ] schedule_interval_type  
其值指示何时会执行作业。 schedule_interval_type 为 nvarchar(50)，可以是下述值之一：

- 'Once'；
- 'Minutes'；
- 'Hours'；
- 'Days'；
- 'Weeks'；
- 'Months'

[ **@schedule_interval_count=** ] schedule_interval_count  
每次执行作业时，其间会出现的 schedule_interval_count 期间数。 schedule_interval_count 为 int，默认值为 1。 该值必须大于或等于 1。

[ **@schedule_start_time=** ] schedule_start_time  
可以开始执行作业的日期。 schedule_start_time 为 DATETIME2，默认值为 0001-01-01 00:00:00.0000000。

[ **@schedule_end_time=** ] schedule_end_time  
可以停止执行作业的日期。 schedule_end_time 为 DATETIME2，默认值为 9999-12-31 11:59:59.0000000。 

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
通过执行 sp_add_job 来添加作业以后，即可使用 sp_add_jobstep 来添加那些为作业执行活动的步骤。 作业的初始版本号为 0，在添加第一个步骤后会递增到 1。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。



### <a name="spdeletejob"></a>sp_delete_job

删除现有的作业。

#### <a name="syntax"></a>语法

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>参数
[ **@job_name =** ] 'job_name'  
要删除的作业的名称。 job_name 为 nvarchar(128)。

[ **@force =** ] force  
指定是在作业有正在进行的执行操作的情况下删除作业并取消所有正在进行的执行操作 (1)，还是在有正在进行的作业执行操作的情况下让作业失败 (0)。 force 为 bit。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
删除作业时，会自动删除作业历史记录。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。



### <a name="spaddjobstep"></a>sp_add_jobstep

向作业添加步骤。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>参数

[ **@job_name =** ] 'job_name'  
要向其添加步骤的作业的名称。 job_name 为 nvarchar(128)。

[ **@step_id =** ] step_id  
作业步骤的序列标识号。 步骤标识号从 1 开始连续递增。 如果某个现有的步骤已经有此 ID，则该步骤以及所有后续步骤的 ID 都会递增，于是这个新步骤就可以插入到序列中。 在未指定的情况下，step_id 会自动分配给步骤序列中的最后一个项。 step_id 为 int。

[ **@step_name =** ] step_name  
步骤的名称。 必须指定，除非是作业的第一个步骤，（为方便起见），该步骤的默认名称为“JobStep”。 step_name 为 nvarchar(128)。

[ **@command_type =** ] 'command_type'  
由此 jobstep 执行的命令的类型。 command_type 为 nvarchar(50)，默认值为 TSql，这意味着 @command_type 参数的值为 T-SQL 脚本。

如果指定，此值必须为 TSql。

[ **@command_source =** ] 'command_source'  
命令存储位置的类型。 command_source 为 nvarchar(50)，默认值为 Inline，这意味着 @command_source 参数的值为命令的文字文本。

如果指定，此值必须为 Inline。

[ **@command =** ] 'command'  
命令必须是有效的 T-SQL 脚本，然后才能通过此作业步骤执行该命令。 command 为 nvarchar(max)，默认值为 NULL。

[ **@credential_name =** ] ‘credential_name’  
存储在此作业控制数据库中的数据库范围的凭据的名称，此控制数据库用于在执行此步骤时连接到目标组中的每个目标数据库。 credential_name 为 nvarchar(128)。

[ **@target_group_name =** ] ‘target-group_name'  
包含目标数据库（作业步骤将在其上执行）的目标组的名称。 target_group_name 为 nvarchar(128)。

[ **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
第一次重试尝试之前的延迟，前提是作业步骤在初始执行尝试时失败。 initial_retry_interval_seconds 为 int，默认值为 1。

[ **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
重试尝试之间的最大延迟。 如果重试之间的延迟大于此值，则会将其削减到此值。 maximum_retry_interval_seconds 为 int，默认值为 120。

[ **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
将要应用到重试延迟的乘数，前提是多个作业步骤执行尝试失败。 例如，如果首次重试的延迟为 5 秒，而回退乘数为 2.0，则第二次重试的延迟为 10 秒，第三次重试的延迟为 20 秒。 retry_interval_backoff_multiplier 为 real，默认值为 2.0。

[ **@retry_attempts =** ] retry_attempts  
重试执行的次数，前提是初始尝试失败。 例如，如果 retry_attempts 值为 10，则初始尝试次数为 1，重试尝试次数为 10，总尝试次数为 11。 如果最后的重试尝试失败，则作业执行操作会终止，生命周期状态为“失败”。 retry_attempts 为 int，默认值为 10。

[ **@step_timeout_seconds =** ] step_timeout_seconds  
允许步骤执行的最长时间。 如果超出此时间，则作业执行操作会终止，生命周期状态为“超时”。 step_timeout_seconds 为 int，默认值为 43,200 秒（12 小时）。

[ **@output_type =** ] 'output_type'  
如果不为 null，则为将命令的首个结果集写入到其中的目标的类型。 output_type 为 nvarchar(50)，默认值为 NULL。

如果指定，此值必须为 SqlDatabase。

[ **@output_credential_name =** ] 'output_credential_name'  
如果不为 null，则为数据库范围的凭据的名称，该凭据用于连接到输出目标数据库。 如果 output_type 等于 SqlDatabase，则必须指定此项。 output_credential_name 为 nvarchar(128)，默认值为 NULL。

[ **@output_subscription_id =** ] 'output_subscription_id'  
需要描述。

[ **@output_resource_group_name =** ] 'output_resource_group_name'  
需要描述。

[ **@output_server_name =** ] 'output_server_name'  
如果不为 null，则为服务器的完全限定的 DNS 名称，该服务器包含输出目标数据库。 如果 output_type 等于 SqlDatabase，则必须指定此项。 output_server_name 为 nvarchar(256)，默认值为 NULL。

[ **@output_database_name =** ] 'output_database_name'  
如果不为 null，则为包含输出目标表的数据库的名称。 如果 output_type 等于 SqlDatabase，则必须指定此项。 output_database_name 为 nvarchar(128)，默认值为 NULL。

[ **@output_schema_name =** ] 'output_schema_name'  
如果不为 null，则为包含输出目标表的 SQL 架构的名称。 如果 output_type 等于 SqlDatabase，则默认值为 dbo。 output_schema_name 为 nvarchar(128)。

[ **@output_table_name =** ] 'output_table_name'  
如果不为 null，则为将命令的首个结果集写入到其中的表的名称。 如果此表不存在，则会根据返回结果集的架构来创建它。 如果 output_type 等于 SqlDatabase，则必须指定此项。 output_table_name 为 nvarchar(128)，默认值为 NULL。

[ **@job_version =** ] job_version OUTPUT  
一个输出参数，将会为其分配新作业版本号。 job_version 为 int。

[ **@max_parallelism =** ] max_parallelism OUTPUT  
每个弹性池的最大并行度级别。 如果设置此项，则作业步骤会受到限制，每个弹性池中可以在其上运行作业步骤的数据库会有一个最大数目。 这适用于每个特定的弹性池，该弹性池直接包括在目标组中，或者位于一个服务器中，而该服务器则包括在目标组中。 max_parallelism 为 int。


#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
当 sp_add_jobstep 成功时，作业的当前版本号会递增。 下次执行作业时，会使用新版本。 如果作业目前正在执行，该执行不会包含新步骤。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：  

- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。



### <a name="spupdatejobstep"></a>sp_update_jobstep

更新作业步骤。

#### <a name="syntax"></a>语法

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>参数
[ **@job_name =** ] 'job_name'  
步骤所属作业的名称。 job_name 为 nvarchar(128)。

[ **@step_id =** ] step_id  
要修改的作业步骤的标识号。 必须指定 step_id 或 step_name。 step_id 为 int。

[ **@step_name =** ] 'step_name'  
要修改的步骤的名称。 必须指定 step_id 或 step_name。 step_name 为 nvarchar(128)。

[ **@new_id =** ] new_id  
作业步骤的新序列标识号。 步骤标识号从 1 开始连续递增。 如果某个步骤重新标记顺序，则其他步骤也会自动重新编号。

[ **@new_name =** ] 'new_name'  
步骤的新名称。 new_name 为 nvarchar(128)。

[ **@command_type =** ] 'command_type'  
由此 jobstep 执行的命令的类型。 command_type 为 nvarchar(50)，默认值为 TSql，这意味着 @command_type 参数的值为 T-SQL 脚本。

如果指定，此值必须为 TSql。

[ **@command_source =** ] 'command_source'  
命令存储位置的类型。 command_source 为 nvarchar(50)，默认值为 Inline，这意味着 @command_source 参数的值为命令的文字文本。

如果指定，此值必须为 Inline。

[ **@command =** ] 'command'  
命令必须是有效的 T-SQL 脚本，然后才能通过此作业步骤执行该命令。 command 为 nvarchar(max)，默认值为 NULL。

[ **@credential_name =** ] ‘credential_name’  
存储在此作业控制数据库中的数据库范围的凭据的名称，此控制数据库用于在执行此步骤时连接到目标组中的每个目标数据库。 credential_name 为 nvarchar(128)。

[ **@target_group_name =** ] ‘target-group_name'  
包含目标数据库（作业步骤将在其上执行）的目标组的名称。 target_group_name 为 nvarchar(128)。

[ **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
第一次重试尝试之前的延迟，前提是作业步骤在初始执行尝试时失败。 initial_retry_interval_seconds 为 int，默认值为 1。

[ **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
重试尝试之间的最大延迟。 如果重试之间的延迟大于此值，则会将其削减到此值。 maximum_retry_interval_seconds 为 int，默认值为 120。

[ **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
将要应用到重试延迟的乘数，前提是多个作业步骤执行尝试失败。 例如，如果首次重试的延迟为 5 秒，而回退乘数为 2.0，则第二次重试的延迟为 10 秒，第三次重试的延迟为 20 秒。 retry_interval_backoff_multiplier 为 real，默认值为 2.0。

[ **@retry_attempts =** ] retry_attempts  
重试执行的次数，前提是初始尝试失败。 例如，如果 retry_attempts 值为 10，则初始尝试次数为 1，重试尝试次数为 10，总尝试次数为 11。 如果最后的重试尝试失败，则作业执行操作会终止，生命周期状态为“失败”。 retry_attempts 为 int，默认值为 10。

[ **@step_timeout_seconds =** ] step_timeout_seconds  
允许步骤执行的最长时间。 如果超出此时间，则作业执行操作会终止，生命周期状态为“超时”。 step_timeout_seconds 为 int，默认值为 43,200 秒（12 小时）。

[ **@output_type =** ] 'output_type'  
如果不为 null，则为将命令的首个结果集写入到其中的目标的类型。 若要将 output_type 的值重置为 NULL，请将此参数的值设置为 ''（空字符串）。 output_type 为 nvarchar(50)，默认值为 NULL。

如果指定，此值必须为 SqlDatabase。

[ **@output_credential_name =** ] 'output_credential_name'  
如果不为 null，则为数据库范围的凭据的名称，该凭据用于连接到输出目标数据库。 如果 output_type 等于 SqlDatabase，则必须指定此项。 若要将 output_credential_name 的值重置为 NULL，请将此参数的值设置为 ''（空字符串）。 output_credential_name 为 nvarchar(128)，默认值为 NULL。

[ **@output_server_name =** ] 'output_server_name'  
如果不为 null，则为服务器的完全限定的 DNS 名称，该服务器包含输出目标数据库。 如果 output_type 等于 SqlDatabase，则必须指定此项。 若要将 output_server_name 的值重置为 NULL，请将此参数的值设置为 ''（空字符串）。 output_server_name 为 nvarchar(256)，默认值为 NULL。

[ **@output_database_name =** ] 'output_database_name'  
如果不为 null，则为包含输出目标表的数据库的名称。 如果 output_type 等于 SqlDatabase，则必须指定此项。 若要将 output_database_name 的值重置为 NULL，请将此参数的值设置为 ''（空字符串）。 output_database_name 为 nvarchar(128)，默认值为 NULL。

[ **@output_schema_name =** ] 'output_schema_name'  
如果不为 null，则为包含输出目标表的 SQL 架构的名称。 如果 output_type 等于 SqlDatabase，则默认值为 dbo。 若要将 output_schema_name 的值重置为 NULL，请将此参数的值设置为 ''（空字符串）。 output_schema_name 为 nvarchar(128)。

[ **@output_table_name =** ] 'output_table_name'  
如果不为 null，则为将命令的首个结果集写入到其中的表的名称。 如果此表不存在，则会根据返回结果集的架构来创建它。 如果 output_type 等于 SqlDatabase，则必须指定此项。 若要将 output_server_name 的值重置为 NULL，请将此参数的值设置为 ''（空字符串）。 output_table_name 为 nvarchar(128)，默认值为 NULL。

[ **@job_version =** ] job_version OUTPUT  
一个输出参数，将会为其分配新作业版本号。 job_version 为 int。

[ **@max_parallelism =** ] max_parallelism OUTPUT  
每个弹性池的最大并行度级别。 如果设置此项，则作业步骤会受到限制，每个弹性池中可以在其上运行作业步骤的数据库会有一个最大数目。 这适用于每个特定的弹性池，该弹性池直接包括在目标组中，或者位于一个服务器中，而该服务器则包括在目标组中。 若要将 max_parallelism 的值重置为 null，请将此参数的值设置为 -1。 max_parallelism 为 int。


#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
不会影响作业的正在进行的执行操作。 当 sp_update_jobstep 成功时，作业的版本号会递增。 下次执行作业时，会使用新版本。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：

- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性




### <a name="spdeletejobstep"></a>sp_delete_jobstep

从作业中删除作业步骤。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>参数
[ **@job_name =** ] 'job_name'  
要从其中删除步骤的作业的名称。 job_name 为 nvarchar(128)，没有默认值。

[ **@step_id =** ] step_id  
要删除的作业步骤的标识号。 必须指定 step_id 或 step_name。 step_id 为 int。

[ **@step_name =** ] 'step_name'  
要删除的步骤的名称。 必须指定 step_id 或 step_name。 step_name 为 nvarchar(128)。

[ **@job_version =** ] job_version OUTPUT  
一个输出参数，将会为其分配新作业版本号。 job_version 为 int。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
不会影响作业的正在进行的执行操作。 当 sp_update_jobstep 成功时，作业的版本号会递增。 下次执行作业时，会使用新版本。

其他作业步骤会自动重新编号，以填补已删除的作业步骤留下的空缺。
 
#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。






### <a name="spstartjob"></a>sp_start_job

开始执行作业。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>参数
[ **@job_name =** ] 'job_name'  
要从其中删除步骤的作业的名称。 job_name 为 nvarchar(128)，没有默认值。

[ **@job_execution_id =** ] job_execution_id OUTPUT  
一个输出参数，将会为其分配作业执行操作的 ID。job_version 为 uniqueidentifier。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
无。
 
#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

### <a name="spstopjob"></a>sp_stop_job

停止作业的执行。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>参数
[ **@job_execution_id =** ] job_execution_id  
要停止的作业执行操作的标识号。 job_execution_id 为 uniqueidentifier，默认值为 NULL。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
无。
 
#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。


### <a name="spaddtargetgroup"></a>sp_add_target_group

添加目标组。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>参数
[ **@target_group_name =** ] 'target_group_name'  
要创建的目标组的名称。 target_group_name 为 nvarchar(128)，没有默认值。

[ **@target_group_id =** ] target_group_id OUTPUT 分配给已成功创建的作业的目标组标识号。 target_group_id 是类型为 uniqueidentifier 的输出变量，默认值为 NULL。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
可以通过目标组轻松地将作业的目标确定为数据库集合。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

删除目标组。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>参数
[ **@target_group_name =** ] 'target_group_name'  
要删除的目标组的名称。 target_group_name 为 nvarchar(128)，没有默认值。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
无。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

向目标组添加一个或一组数据库。

#### <a name="syntax"></a>语法

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] ‘membership_type’ ]   
        [ , [ @target_type = ] ‘target_type’ ]   
        [ , [ @refresh_credential_name = ] ‘refresh_credential_name’ ]   
        [ , [ @server_name = ] ‘server_name’ ]   
        [ , [ @database_name = ] ‘database_name’ ]   
        [ , [ @elastic_pool_name = ] ‘elastic_pool_name’ ]   
        [ , [ @shard_map_name = ] ‘shard_map_name’ ]   
        [ , [ @target_id = ] ‘target_id’ OUTPUT ]
```

#### <a name="arguments"></a>参数
[ **@target_group_name =** ] 'target_group_name'  
要向其添加成员的目标组的名称。 target_group_name 为 nvarchar(128)，没有默认值。

[ **@membership_type =** ] 'membership_type'  
指定是包括目标组成员还是将其排除。 target_group_name 为 nvarchar(128)，默认值为 ‘Include’。 target_group_name 的有效值为 ‘Include’ 或 ‘Exclude’。

[ **@target_type =** ] 'target_type'  
目标数据库或数据库集合的类型，其中包括一个服务器中的所有数据库、一个弹性池中的所有数据库、一个分片映射中的所有数据库，或者一个单独的数据库。 target_type 为 nvarchar(128)，没有默认值。 target_type 的有效值为 ‘SqlServer’、‘SqlElasticPool’、‘SqlDatabase’ 或 ‘SqlShardMap’。 

[ **@refresh_credential_name =** ] 'refresh_credential_name'  
逻辑服务器的名称。 refresh_credential_name 为 nvarchar(128)，没有默认值。

[ **@server_name =** ] 'server_name'  
应添加到指定目标组的逻辑服务器的名称。 当 target_type 为 ‘SqlServer’ 时，应指定 server_name。 server_name 为 nvarchar(128)，没有默认值。

[ **@database_name =** ] 'database_name'  
应添加到指定目标组的数据库的名称。 当 target_type 为 ‘SqlDatabase’ 时，应指定 database_name。 database_name 为 nvarchar(128)，没有默认值。

[ **@elastic_pool_name =** ] ‘elastic_pool_name'  
应添加到指定目标组的弹性池的名称。 当 target_type 为 ‘SqlElasticPool’ 时，应指定 elastic_pool_name。 elastic_pool_name 为 nvarchar(128)，没有默认值。

[ **@shard_map_name =** ] ‘shard_map_name'  
应添加到指定目标组的分片映射池的名称。 当 target_type 为 ‘SqlSqlShardMap’ 时，应指定 elastic_pool_name。 shard_map_name 为 nvarchar(128)，没有默认值。

[ **@target_id =** ] target_group_id OUTPUT  
分配给目标组成员的目标标识号，前提是其已创建并添加到目标组。 target_id 是类型为 uniqueidentifier 的输出变量，默认值为 NULL。
返回代码值 0（成功）或 1（失败）

#### <a name="remarks"></a>备注
如果将逻辑服务器或弹性池包括在目标组中，则一旦执行作业，作业就会在服务器或弹性池中的所有数据库上执行。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

#### <a name="examples"></a>示例
以下示例将 London 和 NewYork 服务器中的所有数据库添加到“Servers Maintaining Customer Information”组。 必须在创建作业代理（在此示例中为 ElasticJobs）时连接到指定的作业数据库。

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

从目标组中删除目标组成员。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] ‘target_id’]
```



Arguments [ @target_group_name = ] 'target_group_name'  
要从其删除目标组成员的目标组的名称。 target_group_name 为 nvarchar(128)，没有默认值。

[ @target_id = ] target_id  
 分配给要删除的目标组成员的目标标识号。 target_id 为 uniqueidentifier，默认值为 NULL。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败）

#### <a name="remarks"></a>备注
可以通过目标组轻松地将作业的目标确定为数据库集合。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

#### <a name="examples"></a>示例
以下示例从“Servers Maintaining Customer Information”组删除 London 服务器。 必须在创建作业代理（在此示例中为 ElasticJobs）时连接到指定的作业数据库。

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

删除作业的历史记录。

#### <a name="syntax"></a>语法


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>参数
[ **@job_name =** ] 'job_name'  
要删除其历史记录的作业的名称。 job_name 为 nvarchar(128)，默认值为 NULL。 必须指定 job_id 或 job_name，但不得二者同时指定。

[ **@job_id =** ] job_id  
 要删除其记录的作业的作业标识号。 job_id 为 uniqueidentifier，默认值为 NULL。 必须指定 job_id 或 job_name，但不得二者同时指定。

[ **@oldest_date =** ] oldest_date  
 要保留在历史记录中的最旧记录。 oldest_date 为 DATETIME2，默认值为 NULL。 指定 oldest_date 后，sp_purge_jobhistory 仅删除比指定值更早的记录。

#### <a name="return-code-values"></a>返回代码值
0（成功）或 1（失败） 备注 可以通过目标组轻松地将作业的目标确定为数据库集合。

#### <a name="permissions"></a>权限
默认情况下，sysadmin 固定服务器角色的成员可以执行此存储过程。 如果将用户限制为只能监视作业，则可授予用户相应权限，使之成为作业代理数据库（在创建作业代理时指定）中以下数据库角色的成员：
- jobs_reader

若要详细了解这些角色的权限，请参阅本文档中的“权限”部分。 仅 sysadmin 的成员可以使用此存储过程来编辑其他用户拥有的作业的属性。

#### <a name="examples"></a>示例
以下示例将 London 和 NewYork 服务器中的所有数据库添加到“Servers Maintaining Customer Information”组。 必须在创建作业代理（在此示例中为 ElasticJobs）时连接到指定的作业数据库。

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>作业视图

以下视图在[作业数据库](elastic-jobs-overview.md#job-database)中提供。


|查看  |说明  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  显示作业执行历史记录。      |
|[jobs](#jobs-view)     |   显示所有作业。      |
|[job_versions](#jobversions-view)     |   显示所有作业版本。      |
|[jobsteps](#jobsteps-view)     |     显示每项作业的当前版本中的所有步骤。    |
|[jobstep_versions](#jobstepversions-view)     |     显示每项作业的所有版本中的所有步骤。    |
|[target_groups](#targetgroups-view)     |      显示所有目标组。   |
|[target_group_members](#targetgroups-view)     |   显示所有目标组的所有成员。      |


### <a name="jobsexecutions-view"></a>jobs_executions 视图

[jobs].[jobs_executions]

显示作业执行历史记录。


|列名称|   数据类型   |说明|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  一个作业执行操作实例的唯一 ID。
|**job_name**   |nvarchar(128)  |作业的名称。
|**job_id** |uniqueidentifier|  作业的唯一 ID。
|**job_version**    |int    |作业的版本（每次修改作业时自动更新）。
|**step_id**    |int|   步骤的唯一（就此作业来说）标识符。 NULL 指示这是父作业执行操作。
|**is_active**| bit |指示信息是处于活动状态还是非活动状态。 1 指示作业处于活动状态，0 指示作业处于非活动状态。
|**lifecycle**| nvarchar(50)|指示作业状态的值：‘Created’、‘In Progress’、‘Failed’、‘Succeeded’、‘Skipped’、'SucceededWithSkipped’|
|**create_time**|   datetime2(7)|   作业的创建日期和时间。
|**start_time** |datetime2(7)|  作业开始执行的日期和时间。 如果作业尚未执行，则为 NULL。
|**end_time**|  datetime2(7)    |作业执行完毕的日期和时间。 如果作业尚未执行或尚未执行完毕，则为 NULL。
|**current_attempts**   |int    |步骤重试的次数。 父作业执行次数将为 0，子作业执行次数将为 1 或更大值，具体取决于执行策略。
|**current_attempt_start_time** |datetime2(7)|  作业开始执行的日期和时间。 NULL 指示这是父作业执行操作。
|**last_message**   |nvarchar(max)| 作业或步骤历史记录消息。 
|**target_type**|   nvarchar(128)   |目标数据库或数据库集合的类型，其中包括一个服务器中的所有数据库、一个弹性池中的所有数据库，或者单个数据库。 target_type 的有效值为 ‘SqlServer’、‘SqlElasticPool’ 或 ‘SqlDatabase’。 NULL 指示这是父作业执行操作。
|**target_id**  |uniqueidentifier|  目标组成员的唯一 ID。  NULL 指示这是父作业执行操作。
|**target_group_name**  |nvarchar(128)  |目标组的名称。 NULL 指示这是父作业执行操作。
|**target_server_name**|    nvarchar(256)|  包含在目标组中的逻辑服务器的名称。 仅当 target_type 为 ‘SqlServer’ 时指定。 NULL 指示这是父作业执行操作。
|**target_database_name**   |nvarchar(128)| 包含在目标组中的数据库的名称。 仅当 target_type 为 ‘SqlDatabase’ 时指定。 NULL 指示这是父作业执行操作。


### <a name="jobs-view"></a>作业视图

[jobs].[jobs]

显示所有作业。

|列名称|   数据类型|  说明|
|------|------|-------|
|**job_name**|  nvarchar(128)   |作业的名称。|
|**job_id**|    uniqueidentifier    |作业的唯一 ID。|
|**job_version**    |int    |作业的版本（每次修改作业时自动更新）。|
|**说明**    |nvarchar(512)| 作业的说明。 enabled 为 bit    指示作业是已启用还是已禁用。 1 指示作业已启用，0 指示作业已禁用。|
|**schedule_interval_type** |nvarchar(50)   |指示何时执行作业的值：'Once'、'Minutes'、'Hours'、'Days'、'Weeks'、'Months'
|**schedule_interval_count**|   int|    每次执行作业时，其间会出现的 schedule_interval_type 期间数。|
|**schedule_start_time**    |datetime2(7)|  作业上次开始执行的日期和时间。|
|**schedule_end_time**| datetime2(7)|   作业上次完成执行的日期和时间。|


### <a name="jobversions-view"></a>job_versions 视图

[jobs].[job_verions]

显示所有作业版本。

|列名称|   数据类型|  说明|
|------|------|-------|
|**job_name**|  nvarchar(128)   |作业的名称。|
|**job_id**|    uniqueidentifier    |作业的唯一 ID。|
|**job_version**    |int    |作业的版本（每次修改作业时自动更新）。|


### <a name="jobsteps-view"></a>jobsteps 视图

[jobs].[jobsteps]

显示每项作业的当前版本中的所有步骤。

|列名称    |数据类型| 说明|
|------|------|-------|
|**job_name**   |nvarchar(128)| 作业的名称。|
|**job_id** |uniqueidentifier   |作业的唯一 ID。|
|**job_version**|   int|    作业的版本（每次修改作业时自动更新）。|
|**step_id**    |int    |步骤的唯一（就此作业来说）标识符。|
|**step_name**  |nvarchar(128)  |步骤的唯一（就此作业来说）名称。|
|**command_type**   |nvarchar(50)   |要在作业步骤中执行的命令的类型。 就 v1 来说，值必须等于 ‘TSql’（默认设置）。|
|**command_source** |nvarchar(50)|  命令的位置。 就 v1 来说，‘Inline’ 是默认值，也是唯一接受的值。|
|**command**|   nvarchar(max)|  将要由弹性作业按 command_type 执行的命令。|
|**credential_name**|   nvarchar(128)   |用于执行作业的数据库范围的凭据的名称。|
|**target_group_name**| nvarchar(128)   |目标组的名称。|
|**target_group_id**|   uniqueidentifier|   目标组的唯一 ID。|
|**initial_retry_interval_seconds**|    int |首次重试尝试之前的延迟。 默认值为 1。|
|**maximum_retry_interval_seconds** |int|   重试尝试之间的最大延迟。 如果重试之间的延迟大于此值，则会将其削减到此值。 默认值为 120。|
|**retry_interval_backoff_multiplier**  |real|  将要应用到重试延迟的乘数，前提是多个作业步骤执行尝试失败。 默认值为 2.0。|
|**retry_attempts** |int|   在步骤失败的情况下可以使用的重试尝试次数。 默认值为 0，表示不允许重试尝试。|
|**step_timeout_seconds**   |int|   两次重试尝试之间的时间（以分钟为单位）。 默认值为 0，表示时间间隔为 0 分钟。|
|**output_type**    |nvarchar(11)|  命令的位置。 在当前的预览版中，'Inline' 是默认值，也是唯一接受的值。|
|**output_credential_name**|    nvarchar(128)   |需要存储结果集时，用于连接到目标服务器的凭据的名称。|
|**output_subscription_id**|    uniqueidentifier|   执行查询后，所得结果集的目标服务器\数据库的订阅的唯一 ID。|
|**output_resource_group_name** |nvarchar(128)| 目标服务器所在资源组的名称。|
|**output_server_name**|    nvarchar(256)   |结果集的目标服务器的名称。|
|**output_database_name**   |nvarchar(128)| 结果集的目标数据库的名称。|
|**output_schema_name** |nvarchar(max)| 目标架构的名称。 默认为 dbo（如果未指定）。|
|**output_table_name**| nvarchar(max)|  表名，用于存储查询结果的结果集。 如果表不存在，则会根据结果集的架构自动创建表。 架构必须与结果集的架构匹配。|
|**max_parallelism**|   int|    每次在弹性池的数据库上执行此作业步骤时，该弹性池允许的最大数据库数。 默认值为 NULL，这意味着没有限制。 |


### <a name="jobstepversions-view"></a>jobstep_versions 视图

[jobs].[jobstep_versions]

显示每项作业的所有版本中的所有步骤。 架构与 [jobsteps](#jobsteps-view) 相同。

### <a name="targetgroups-view"></a>target_groups 视图

[jobs].[target_groups]

列出所有目标组。

|列名称|数据类型| 说明|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |目标组（数据库集合）的名称。 
|**target_group_id**    |uniqueidentifier   |目标组的唯一 ID。

### <a name="targetgroupsmembers-view"></a>target_groups_members 视图

[jobs].[target_groups_members]

显示所有目标组的所有成员。

|列名称|数据类型| 说明|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|目标组（数据库集合）的名称。 |
|**target_group_id**    |uniqueidentifier   |目标组的唯一 ID。|
|**membership_type**    |int|   指定是在目标组中包括目标组成员还是将其排除。 target_group_name 的有效值为 ‘Include’ 或 ‘Exclude’。|
|**target_type**    |nvarchar(128)| 目标数据库或数据库集合的类型，其中包括一个服务器中的所有数据库、一个弹性池中的所有数据库，或者单个数据库。 target_type 的有效值为 ‘SqlServer’、‘SqlElasticPool’、‘SqlDatabase’ 或 ‘SqlShardMap’。|
|**target_id**  |uniqueidentifier|  目标组成员的唯一 ID。|
|**refresh_credential_name**    |nvarchar(128)  |用于连接到目标组成员的数据库范围的凭据的名称。|
|subscription_id    |uniqueidentifier|  订阅的唯一 ID。|
|**resource_group_name**    |nvarchar(128)| 目标组成员所在资源组的名称。|
|**server_name**    |nvarchar(128)  |包含在目标组中的逻辑服务器的名称。 仅当 target_type 为 ‘SqlServer’ 时指定。 |
|**database_name**  |nvarchar(128)  |包含在目标组中的数据库的名称。 仅当 target_type 为 ‘SqlDatabase’ 时指定。|
|**elastic_pool_name**  |nvarchar(128)| 包含在目标组中的弹性池的名称。 仅当 target_type 为 ‘SqlElasticPool’ 时指定。|
|**shard_map_name** |nvarchar(128)| 包含在目标组中的分片映射的名称。 仅当 target_type 为 ‘SqlShardMap’ 时指定。|


## <a name="resources"></a>资源

 - ![主题链接图标](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "主题链接图标") [Transact-SQL 语法约定](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  


## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 创建和管理弹性作业](elastic-jobs-powershell.md)
- [SQL Server 中的授权和权限](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  