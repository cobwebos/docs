---
title: 有关排查使用 Azure 数据库迁移服务时出现的常见已知问题/错误的文章 | Microsoft Docs
description: 了解有关如何排查使用 Azure 数据库迁移服务时出现的常见已知问题/错误。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: 1d639a8b1d5c7a5dd2b7bac7c5e020be7c8b1c50
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190948"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>排查常见的 Azure 数据库迁移服务问题和错误

本文描述了 Azure 数据库迁移服务用户可能遇到的一些常见问题和错误。 本文还包含有关如何解决这些问题和错误的信息。

## <a name="migration-activity-in-queued-state"></a>迁移活动处于排队状态

Azure 数据库迁移服务项目中创建新的活动，活动会保持在排队状态。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例达到正在运行的任务的最大容量，则会出现此问题。 任何新活动将排队到容量可用为止。 | 验证数据迁移服务实例是否在运行各个项目中的活动。 可以继续创建新活动，这些活动会自动添加到队列中等待执行。 任何一个正在运行的现有活动完成后，下一个排队的活动会立即开始运行，其状态会自动更改为正在运行。 无需采取任何额外的措施即可开始迁移排队的活动。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>选择迁移的数据库数目上限

创建用于将移动到 Azure SQL 数据库或 Azure SQL 数据库的数据库迁移项目的活动托管实例时，会发生以下错误：

* **错误**：迁移设置验证错误: "errorDetail": 选择迁移的“数据库”对象数目超过最大数目 '4'。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果为单个迁移活动选择的数据库数目超过 4 个，则会显示此错误。 目前，每个迁移活动限制为 4 个数据库。 | 为每个迁移活动选择 4 个或更少的数据库。 如果需要同时迁移 4 个以上数据库，请预配 Azure 数据库迁移服务的另一个实例。 目前，每个订阅最多支持两个 Azure 数据库迁移服务实例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>从 MySQL 迁移到 Azure MySQL 出错并发生恢复失败

使用 Azure 数据库迁移服务从 MySQL 迁移到 Azure Database for MySQL 时，迁移活动失败并出现以下错误：

* **错误**：数据库迁移错误 - 由于 [n] 次连续恢复失败，任务 'TaskID' 已挂起。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 在执行迁移的用户缺少 ReplicationAdmin 角色和/或复制客户端、 复制副本和超级 （版本低于 MySQL 5.6.6） 的权限时，可能会发生此错误。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 请确保[必备权限](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites)对于用户帐户配置准确地在 Azure Database for MySQL 实例。 例如，可以遵循以下步骤创建具有所需特权的名为“migrateuser”的用户：<br>1.CREATE USER migrateuser@'%' IDENTIFIED BY 'secret'; <br>2.授予所有特权 db_name.* 到 migrateuser'@'%标识机密;重复此步骤以授予对多个数据库的访问权限 <br>3.在授予复制从属 *。* 'secret' 标识的 'migrateuser'@'%'；<br>4.授予复制客户端上的 *。* 'secret' 标识的 'migrateuser'@'%'；<br>5.刷新基本权限。 |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>正在尝试停止 Azure 数据库迁移服务时出错

停止 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务无法停止。 错误: {'error':{'code':'InvalidRequest','message': '一个或多个活动当前正在运行。 若要停止服务，请等到这些活动完成，或手动停止这些活动，然后重试。'}}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果尝试停止的服务实例包含仍在运行的或者存在于迁移项目中的活动，则会显示此错误。 <br><br><br><br><br><br> | 确保尝试停止的 Azure 数据库迁移服务实例中没有任何活动正在运行。 在尝试停止该服务之前，还可以删除活动或项目。 以下步骤演示如何通过删除所有正在运行的任务来删除项目，以清理迁移服务实例：<br>1.Install-Module -Name AzureRM.DataMigration <br>2.Login-AzureRmAccount <br>3.Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4.Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>正尝试以启动 Azure 数据库迁移服务时出错

启动 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务启动失败。 错误: {errorDetail: 服务无法启动，请联系 Microsoft 支持部门}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 此错误显示在前一个实例在内部失败时。 很少，出现此错误并且工程团队已意识到这一点。 <br> | 删除服务无法启动，并设置新密码以将其替换为的实例。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>还原数据库时迁移到 Azure SQL DB 的 SQL 托管实例时出错

当你从 SQL Server 中执行联机迁移到 Azure SQL 数据库托管实例时，直接转换迁移失败，出现以下错误：

* **错误**：还原操作失败，操作 Id operationId。 代码 AuthorizationFailed 消息客户端 clientId 对象 id 为 objectId 不具有执行操作 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read 对作用域 /subscriptions/subscriptionId。 '。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 此错误表示用于从 SQL Server 的联机迁移到 Azure SQL 数据库托管实例的应用程序主体都不会有影响的订阅的权限。 使用托管实例上，目前某些 API 调用以执行还原操作需要订阅此权限。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 使用`Get-AzureADServicePrincipal`PowerShell cmdlet 与`-ObjectId`可从错误消息，若要列出正在使用的应用程序 ID 的显示名称。<br><br> 验证到此应用程序的权限，并确保它具有[参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)在订阅级别。 <br><br> Azure 数据库迁移服务工程团队正致力于限制所需访问来自当前提供的订阅上的角色。 如果有一项业务要求不允许使用的参与角色、 联系 Azure 支持人员获取更多帮助。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>删除与 Azure 数据库迁移服务关联的 NIC 时出错

尝试删除与 Azure 数据库迁移服务关联的网络接口卡时，该操作失败并出现以下错误：

* **错误**：无法删除与 Azure 数据库迁移服务关联的 NIC，因为 DMS 服务正在使用该 NIC

| 原因         | 解决方法    |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例仍然存在并在使用该 NIC，则会出现此问题。 <br><br><br><br><br><br><br><br> | 若要删除此 NIC，请删除 DMS 服务实例，这会自动删除该服务使用的 NIC。<br><br> **重要**：确保要删除的 Azure 数据库迁移服务实例中没有任何正在运行的活动。<br><br> 删除与 Azure 数据库迁移服务实例关联的所有项目和活动后，可以删除服务实例。 在删除服务的过程中，会自动清理服务实例使用的 NIC。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 时出现连接错误

尝试在 Azure 数据库迁移服务项目向导中连接到源时，如果源使用 ExpressRoute 进行连接，在长时间的超时后，连接将会失败。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 使用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 时，Azure 数据库迁移服务[要求](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)在与它关联的虚拟网络子网中预配三个服务终结点：<br> -- 服务总线终结点<br> -- 存储终结点<br> -- 目标数据库终结点（例如 SQL 终结点、Cosmos DB 终结点）<br><br><br><br><br> | 请[启用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)所需的服务终结点，以便在源与 Azure 数据库迁移服务之间建立 ExpressRoute 连接。 <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>将 MySQL 数据库迁移到 Azure MySQL 时的超时错误

通过 Azure 数据库迁移服务将 MySQL 数据库迁移到 Azure Database for MySQL 实例时，迁移失败并出现以下超时错误：

* **错误**：数据库迁移错误 - 无法加载文件 - 无法针对文件 'n' RetCode 启动加载进程:SQL_ERROR SqlState:HY000 NativeError:1205 消息: [MySQL][ODBC Driver][mysqld] 锁定等待超时；请尝试重启事务

| 原因         | 解决方法    |
| ------------- | ------------- |
| 之所以在迁移失败时发生此错误，是因为迁移期间发生锁定等待超时。 | 考虑增大服务器参数 **'innodb_lock_wait_timeout'** 的值。 允许的最大值为 1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>当使用动态端口连接到源 SQL Server 实例或命名实例的错误

当你尝试连接到命名的实例或动态端口上运行的 SQL Server 源的 Azure 数据库迁移服务时，连接失败，出现此错误：

* **错误**:-1 的 SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称正确以及 SQL Server 配置为允许远程连接。 （提供程序：SQL 网络接口，错误：26-定位指定的服务器/实例时出错)

| 原因         | 解决方法    |
| ------------- | ------------- |
| Azure 数据库迁移服务尝试连接到源 SQL Server 实例有一个动态端口，或使用的命名的实例时，都会出现此问题。 SQL Server Browser 服务侦听的传入连接到命名实例或 UDP 端口 1434年，当使用动态端口。 动态端口可能会更改每个 SQL Server 服务重启的时间。 您可以检查分配给通过网络配置 SQL Server 配置管理器实例的动态端口。<br><br><br> |验证 Azure 数据库迁移服务可以连接到源上的 UDP 端口 1434年的 SQL Server Browser 服务和通过动态分配的 TCP 端口 （如果适用） 的 SQL Server 实例。 |

## <a name="additional-known-issues"></a>其他已知问题

* [联机迁移到 Azure SQL 数据库的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [在线迁移到 Azure Database for MySQL 的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [在线迁移到 Azure Database for PostgreSQL 的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 查看文章[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 查看文章[如何配置服务器参数中 Azure Database for MySQL 通过使用 Azure 门户](https://docs.microsoft.com/azure/mysql/howto-server-parameters)。
* 查看文章[使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 请参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
