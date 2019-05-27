---
title: 有关已知与使用 Azure 数据库迁移服务相关联的常见的问题/错误故障排除文章 |Microsoft Docs
description: 了解有关如何解决常见已知的问题/错误与使用 Azure 数据库迁移服务相关联。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 5a7c6c4553f46e8a7308995e05d6c06c0eb10f27
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002210"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>对 Azure 数据库迁移服务常见问题和错误进行故障排除

本文介绍一些常见的问题和 Azure 数据库迁移服务的用户可以遇到的错误。 此文章还包括有关如何解决这些问题和错误的信息。

## <a name="migration-activity-in-queued-state"></a>在排队状态迁移活动

Azure 数据库迁移服务项目中创建新的活动，活动会保持在排队状态。

| 原因         | 解决方法 |
| ------------- | ------------- |
| Azure 数据库迁移服务实例已达到最大容量为正在进行的任务并发运行时，都会出现此问题。 任何新活动已排队，直到容量变得可用。 | 验证数据迁移服务实例都有跨项目运行活动。 您可以继续创建新自动被添加到队列的执行的活动。 只要任何现有正在运行的活动完成时，排队的下一个活动开始运行，状态将更改为自动运行状态。 您不必执行任何其他操作来开始排队的活动的迁移。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>选择要迁移的数据库的最大数量

创建用于将移动到 Azure SQL 数据库或 Azure SQL 数据库的数据库迁移项目的活动托管实例时，会发生以下错误：

* **错误**：迁移设置验证错误"、"errorDetail":"的数据库的多个最大数量"4"对象选择进行迁移。"

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果你已选择多个四个单一迁移活动的数据库会显示此错误。 目前，每个迁移活动被限制为四个数据库。 | 选择每个迁移活动的四个或更少的数据库。 如果你需要超过四个数据库并行迁移，预配 Azure 数据库迁移服务的另一个实例。 目前，每个订阅支持最多两个 Azure 数据库迁移服务实例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>MySQL 迁移到 Azure MySQL 与恢复失败的错误

当您从迁移 MySQL 到 Azure Database for MySQL 使用 Azure 数据库迁移服务时，迁移活动失败，出现以下错误：

* **错误**：由于 [n] 次连续的恢复失败，数据库迁移错误-任务 'TaskID' 已挂起。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 在执行迁移的用户缺少 ReplicationAdmin 角色和/或复制客户端、 复制副本和超级 （版本低于 MySQL 5.6.6） 的权限时，可能会发生此错误。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 请确保[必备权限](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites)对于用户帐户配置准确地在 Azure Database for MySQL 实例。 例如，可以执行以下步骤来创建一个名为 migrateuser 与所需权限的用户：<br>1.CREATE USER migrateuser@'%标识的机密; <br>2.授予所有特权 db_name.* 到 migrateuser'@'%标识机密;重复此步骤以授予对多个数据库的访问权限 <br>3.在授予复制从属 *。* 为 migrateuser'@'%标识机密;<br>4.授予复制客户端上的 *。* 为 migrateuser'@'%标识机密;<br>5.刷新基本权限。 |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>正在尝试停止 Azure 数据库迁移服务时出错

停止 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务无法停止。 错误: {错误: {代码: InvalidRequest，message: 一个或多个活动当前正在运行。 若要停止该服务，等待，直到活动已完成或手动停止这些活动，然后重试。}}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 你正在尝试停止的服务实例仍在运行或显示的活动包括在迁移项目中时，将显示此错误。 <br><br><br><br><br><br> | 不确保在您想要停止的 Azure 数据库迁移服务实例中运行任何活动。 您还可以尝试停止服务之前删除活动或项目。 以下步骤演示了如何删除项目，以通过删除所有正在运行的任务来清理迁移服务实例：<br>1.Install-Module -Name AzureRM.DataMigration <br>2.Login-AzureRmAccount <br>3.Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4.Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>还原数据库时迁移到 Azure SQL DB 的 SQL 托管实例时出错

当你从 SQL Server 中执行联机迁移到 Azure SQL 数据库托管实例时，直接转换迁移失败，出现以下错误：

* **错误**：还原操作失败，操作 Id operationId。 代码 AuthorizationFailed 消息客户端 clientId 对象 id 为 objectId 不具有执行操作 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read 对作用域 /subscriptions/subscriptionId。 '。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 此错误表示用于从 SQL Server 的联机迁移到 Azure SQL 数据库托管实例的应用程序主体都不会有影响的订阅的权限。 使用托管实例上，目前某些 API 调用以执行还原操作需要订阅此权限。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 使用`Get-AzureADServicePrincipal`PowerShell cmdlet 与`-ObjectId`可从错误消息，若要列出正在使用的应用程序 ID 的显示名称。<br><br> 验证到此应用程序的权限，并确保它具有[参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)在订阅级别。 <br><br> Azure 数据库迁移服务工程团队正致力于限制所需访问来自当前提供的订阅上的角色。 如果有一项业务要求不允许使用的参与角色、 联系 Azure 支持人员获取更多帮助。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>删除 NIC 关联的 Azure 数据库迁移服务时出错

当你尝试删除 Azure 数据库迁移服务与关联网络接口卡时，尝试删除失败，出现以下错误：

* **错误**：无法删除 Azure 数据库迁移服务由于 DMS 服务利用 NIC 关联的 NIC

| 原因         | 解决方法    |
| ------------- | ------------- |
| Azure 数据库迁移服务实例仍将存在时，都会出现此问题和使用 nic。 <br><br><br><br><br><br><br><br> | 若要删除此 NIC，请删除会自动删除该服务使用的 NIC 的 DMS 服务实例。<br><br> **重要**：请确保要删除的 Azure 数据库迁移服务实例具有任何正在运行的活动。<br><br> 删除所有项目和与 Azure 数据库迁移服务实例关联的活动后，可以删除服务实例。 删除服务的一部分自动清理服务实例使用的 NIC。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 时出现连接错误

当你尝试连接到 Azure 数据库迁移服务项目向导中的源时，连接将失败长时间的超时之后如果源使用 ExpressRoute 进行连接。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 使用时[ExpressRoute](https://azure.microsoft.com/services/expressroute/)，Azure 数据库迁移服务[需要](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)预配与服务关联的虚拟网络子网中的三个服务终结点：<br> --Service Bus 终结点<br> -存储终结点<br> -目标数据库终结点 （例如 SQL 终结点，Cosmos DB 终结点）<br><br><br><br><br> | [启用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)源和 Azure 数据库迁移服务之间的 ExpressRoute 连接的所需的服务终结点。 <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>将 MySQL 数据库迁移到 Azure MySQL 时的超时错误

MySQL 数据库迁移到 Azure Database for MySQL 通过 Azure 数据库迁移服务实例时，如果迁移失败，出现以下超时错误：

* **错误**：数据库迁移错误-无法加载文件的启动文件的加载进程失败 n 个 RetCode:SQL_ERROR SqlState:HY000 NativeError:1205 消息: [MySQL] [ODBC Driver] [mysqld] 锁定等待超时;请尝试重新启动事务

| 原因         | 解决方法    |
| ------------- | ------------- |
| 迁移在迁移期间由于锁等待超时而失败时，将发生此错误。 | 请考虑增大服务器参数的值**innodb_lock_wait_timeout**。 允许的最高值是 1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>当使用动态端口连接到源 SQL Server 实例或命名实例的错误

当你尝试连接到命名的实例或动态端口上运行的 SQL Server 源的 Azure 数据库迁移服务时，连接失败，出现此错误：

* **错误**:-1 的 SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称正确以及 SQL Server 配置为允许远程连接。 （提供程序：SQL 网络接口，错误：26-定位指定的服务器/实例时出错)

| 原因         | 解决方法    |
| ------------- | ------------- |
| Azure 数据库迁移服务尝试连接到源 SQL Server 实例有一个动态端口，或使用的命名的实例时，都会出现此问题。 SQL Server Browser 服务侦听的传入连接到命名实例或 UDP 端口 1434年，当使用动态端口。 动态端口可能会更改每个 SQL Server 服务重启的时间。 您可以检查分配给通过网络配置 SQL Server 配置管理器实例的动态端口。<br><br><br> |验证 Azure 数据库迁移服务可以连接到源上的 UDP 端口 1434年的 SQL Server Browser 服务和通过动态分配的 TCP 端口 （如果适用） 的 SQL Server 实例。 |

## <a name="additional-known-issues"></a>更多已知的问题

* [联机迁移到 Azure SQL 数据库的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [在线迁移到 Azure Database for MySQL 的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [在线迁移到 Azure Database for PostgreSQL 的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 查看文章[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 查看文章[如何配置服务器参数中 Azure Database for MySQL 通过使用 Azure 门户](https://docs.microsoft.com/azure/mysql/howto-server-parameters)。
* 查看文章[使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 请参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
