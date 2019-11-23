---
title: 弹性数据库作业（预览版）
description: Configure Elastic Database Jobs (preview) to run Transact-SQL (T-SQL) scripts across a set of one or more Azure SQL databases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 283b4004f34372104eb083496400772884f5965e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420378"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>创建、配置和管理弹性作业

在本文中，你将了解如何创建、配置和管理弹性作业。

如果未使用弹性作业，请[详细了解 Azure SQL 数据库中的作业自动化概念](sql-database-job-automation-overview.md)。

## <a name="create-and-configure-the-agent"></a>创建并配置代理

1. 创建或标识空的 S0 或更高级别的 SQL 数据库。 该数据库在弹性作业代理创建期间将用作“作业数据库”。
2. Create an Elastic Job agent in the [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) or with [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![创建弹性作业代理](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>创建、运行和管理作业

1. Create a credential for job execution in the *Job database* using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Define the target group (the databases you want to run the job against) using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. 在作业将运行的每个数据库中创建作业代理凭据[（向组中的每个数据库添加用户（或角色））](sql-database-control-access.md)。 有关示例，请参阅 [PowerShell 教程](elastic-jobs-powershell.md)。
4. Create a job using [PowerShell](elastic-jobs-powershell.md) or [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. 使用 [PowerShell](elastic-jobs-powershell.md) 或 [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) 添加作业步骤。
6. Run a job using [PowerShell](elastic-jobs-powershell.md#run-the-job) or [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitor job execution status using the portal, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) or [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![门户](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>运行作业所需的凭据

作业在执行时使用[数据库范围的凭据](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)连接到目标组指定的数据库。 如果目标组包含服务器或池，则可使用这些数据库范围的凭据连接到 master 数据库，以便枚举可用的数据库。

设置运行作业所需的适当凭据可能不太容易，因此请注意以下要点：

- 必须在作业数据库中创建数据库范围的凭据。
- **所有目标数据库必须有一个具有[足够权限](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)的登录名，否则作业无法成功完成**（下图中的 `jobuser`）。
- 凭据可以跨作业反复使用，而凭据密码经过加密后，无法供只能通过只读方式访问作业对象的用户访问。

下图旨在帮助用户了解和设置适当的作业凭据。 **记住在作业需运行的每个数据库（所有目标用户数据库）中创建该用户**。

![弹性作业凭据](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>安全性最佳实践

使用弹性作业时的一些最佳做法注意事项：

- 将 API 的使用限制为受信任的个人。
- 凭据应该具有执行作业步骤所需的最低权限。 有关详细信息，请参阅 [SQL Server 中的授权和权限](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)。
- 使用服务器和/或池目标组成员时，强烈建议创建另外一个凭据，该凭据有权在 master 数据库上查看/列出数据库，用于在作业执行之前展开服务器和/或池的数据库列表。

## <a name="agent-performance-capacity-and-limitations"></a>代理性能、容量和限制

弹性作业在等待长时间运行的作业完成时使用极少的计算资源。

根据目标数据库组的大小和作业所需执行时间（并发辅助角色数）的不同，代理需要的计算量和作业数据库性能也会有所不同（目标数和作业数越多，所需计算量越大）。

目前，预览版的限制是 100 个并发作业。

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>防止作业降低目标数据库性能

若要确保针对 SQL 弹性池中的数据库运行作业时资源不会超负荷，可以对作业进行配置，限制可以在同一时间对其运行作业的数据库数。

Set the number of concurrent databases a job runs on by setting the `sp_add_jobstep` stored procedure's `@max_parallelism` parameter in T-SQL, or `Add-AzSqlElasticJobStep -MaxParallelism` in PowerShell.

## <a name="best-practices-for-creating-jobs"></a>创建作业的最佳做法

### <a name="idempotent-scripts"></a>幂等脚本
作业的 T-SQL 脚本必须[幂等](https://en.wikipedia.org/wiki/Idempotence)。 “幂等”是指如果脚本成功，则再次运行时，会出现相同的结果。 脚本可能由于暂时性网络问题而失败。 在此情况下，作业会自动重试运行脚本，达到默认的次数才停止。 即使幂等脚本已成功运行两次（或更多次），也仍会返回相同的结果。

一个简单的策略是在创建对象之前测试其是否存在。


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

同样地，脚本必须以逻辑方式测试并反驳它所找到的任何条件，才能成功执行。



## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 创建和管理弹性作业](elastic-jobs-powershell.md)
- [使用 Transact-SQL (T-SQL) 创建和管理弹性作业](elastic-jobs-tsql.md)
