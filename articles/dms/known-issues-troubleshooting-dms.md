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
ms.openlocfilehash: e33f195ea821b34147c748e9c0aa64cb63b58fdc
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249985"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>排查常见的 Azure 数据库迁移服务问题和错误

本文描述了 Azure 数据库迁移服务用户可能遇到的一些常见问题和错误。 本文还包含有关如何解决这些问题和错误的信息。

## <a name="migration-activity-in-queued-state"></a>迁移活动处于排队状态

在 Azure 数据库迁移服务项目中创建新活动时，活动保持为排队状态。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例达到正在运行的任务的最大容量，则会出现此问题。 任何新活动将排队到容量可用为止。 | 验证数据迁移服务实例是否在运行各个项目中的活动。 可以继续创建新活动，这些活动会自动添加到队列中等待执行。 任何一个正在运行的现有活动完成后，下一个排队的活动会立即开始运行，其状态会自动更改为正在运行。 无需采取任何额外的措施即可开始迁移排队的活动。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>选择迁移的数据库数目上限

为要转移到 Azure SQL 数据库或 Azure SQL 数据库托管实例的数据库迁移项目创建活动时发生以下错误：

* **错误**：迁移设置验证错误: "errorDetail": 选择迁移的“数据库”对象数目超过最大数目 '4'。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果为单个迁移活动选择的数据库数目超过 4 个，则会显示此错误。 目前，每个迁移活动限制为 4 个数据库。 | 为每个迁移活动选择 4 个或更少的数据库。 如果需要同时迁移 4 个以上数据库，请预配 Azure 数据库迁移服务的另一个实例。 目前，每个订阅最多支持两个 Azure 数据库迁移服务实例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>从 MySQL 迁移到 Azure MySQL 出错并发生恢复失败

使用 Azure 数据库迁移服务从 MySQL 迁移到 Azure Database for MySQL 时，迁移活动失败并出现以下错误：

* **错误**：数据库迁移错误 - 由于 [n] 次连续恢复失败，任务 'TaskID' 已挂起。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 当执行迁移的用户缺少 ReplicationAdmin 角色和/或 REPLICATION CLIENT、REPLICATION REPLICA 和 SUPER（低于 MySQL 5.6.6 的版本）的特权时，可能会发生此错误。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 请确保在 Azure Database for MySQL 实例上准确配置了用户帐户的[先决条件特权](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites)。 例如，可以遵循以下步骤创建具有所需特权的名为“migrateuser”的用户：<br>1.CREATE USER migrateuser@'%' IDENTIFIED BY 'secret'; <br>2.将 db_name. * 上的所有权限授予 "secret" 标识的 "migrateuser" @ "%";重复此步骤以授予对更多数据库的访问权限 <br>3.授权复制从属 *。* 'secret' 标识的 'migrateuser'@'%'；<br>4.为复制客户端授予 *。* 'secret' 标识的 'migrateuser'@'%'；<br>5.刷新权限; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>尝试停止 Azure 数据库迁移服务时出错

停止 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务无法停止。 错误: {'error':{'code':'InvalidRequest','message': '一个或多个活动当前正在运行。 若要停止服务，请等到这些活动完成，或手动停止这些活动，然后重试。'}}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果尝试停止的服务实例包含仍在运行的或者存在于迁移项目中的活动，则会显示此错误。 <br><br><br><br><br><br> | 确保尝试停止的 Azure 数据库迁移服务实例中没有任何活动正在运行。 在尝试停止该服务之前，还可以删除活动或项目。 以下步骤演示如何通过删除所有正在运行的任务来删除项目，以清理迁移服务实例：<br>1.Install-Module -Name AzureRM.DataMigration <br>2.Login-AzureRmAccount <br>3.Get-azurermsubscription-SubscriptionName "\<>" <br> 4.AzureRmDataMigrationProject-Name 项目名称\<>-ResourceGroupName \<rgName >-servicename \<servicename >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>尝试启动 Azure 数据库迁移服务时出错

启动 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务无法启动。 错误: {'errorDetail':'服务无法启动，请联系 Microsoft 支持部门'}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 上一实例发生内部故障时，会显示此错误。 此错误极少发生，工程团队已了解这一情况。 <br> | 请删除无法启动的服务的实例，然后预配一个新的来代替它。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>在将 SQL 迁移到 Azure SQL DB 托管实例的过程中还原数据库时出错

从 SQL Server 联机迁移到 Azure SQL 数据库托管实例时，直接转换失败，出现以下错误：

* **错误**：操作 ID 为“operationId”的还原操作失败。 代码为“AuthorizationFailed”，消息为“对象 ID 为 'objectId' 的客户端 'clientId' 无权执行作用域为 '/订阅/subscriptionId' 的操作 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read'。”。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 此错误表明，用于从 SQL Server 联机迁移到 Azure SQL 数据库托管实例的应用程序主体在订阅上没有参与权限。 目前，使用托管实例进行的某些 API 调用需要在订阅上有此权限才能执行还原操作。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 将 `Get-AzureADServicePrincipal` PowerShell cmdlet 与错误消息中提供的 `-ObjectId` 配合使用即可列出所用应用程序 ID 的显示名称。<br><br> 验证此应用程序的权限，确保其在订阅级别有[参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)。 <br><br> Azure 数据库迁移服务工程团队会限定从订阅上的当前参与角色进行访问这一必需权限。 如果你的业务要求不允许使用参与角色，请联系 Azure 支持部门以获取更多帮助。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>删除与 Azure 数据库迁移服务关联的 NIC 时出错

尝试删除与 Azure 数据库迁移服务关联的网络接口卡时，该操作失败并出现以下错误：

* **错误**：无法删除与 Azure 数据库迁移服务关联的 NIC，因为 DMS 服务正在使用该 NIC

| 原因         | 解决方法    |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例仍然存在并在使用该 NIC，则会出现此问题。 <br><br><br><br><br><br><br><br> | 若要删除此 NIC，请删除 DMS 服务实例，这会自动删除该服务使用的 NIC。<br><br> **重要说明**：确保要删除的 Azure 数据库迁移服务实例中没有任何正在运行的活动。<br><br> 删除与 Azure 数据库迁移服务实例关联的所有项目和活动后，可以删除服务实例。 在删除服务的过程中，会自动清理服务实例使用的 NIC。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 时出现连接错误

尝试在 Azure 数据库迁移服务项目向导中连接到源时，如果源使用 ExpressRoute 进行连接，在长时间的超时后，连接将会失败。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 使用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 时，Azure 数据库迁移服务[要求](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)在与它关联的虚拟网络子网中预配三个服务终结点：<br> -- 服务总线终结点<br> -- 存储终结点<br> -- 目标数据库终结点（例如 SQL 终结点、Cosmos DB 终结点）<br><br><br><br><br> | 请[启用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)所需的服务终结点，以便在源与 Azure 数据库迁移服务之间建立 ExpressRoute 连接。 <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>将 MySQL 数据库迁移到 Azure MySQL 时发生超时错误

通过 Azure 数据库迁移服务将 MySQL 数据库迁移到 Azure Database for MySQL 实例时，迁移失败并出现以下超时错误：

* **错误**：数据库迁移错误 - 无法加载文件 - 无法针对文件 'n' RetCode 启动加载进程:SQL_ERROR SqlState:HY000 NativeError:1205 消息: [MySQL][ODBC Driver][mysqld] 锁定等待超时；请尝试重启事务

| 原因         | 解决方法    |
| ------------- | ------------- |
| 之所以在迁移失败时发生此错误，是因为迁移期间发生锁定等待超时。 | 考虑增大服务器参数 **'innodb_lock_wait_timeout'** 的值。 允许的最大值为 1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>使用动态端口或命名实例连接到源 SQL Server 时出错

尝试将 Azure 数据库迁移服务连接到在命名实例或动态端口上运行的 SQL Server 源时，连接失败并出现以下错误：

* **错误**：-1 - SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称是否正确，以及 SQL Server 是否已配置为允许远程连接。 （提供程序：SQL 网络接口，错误：26 - 查找指定的服务器/实例时出错）

| 原因         | 解决方法    |
| ------------- | ------------- |
| 当 Azure 数据库迁移服务尝试连接到的源 SQL Server 实例有一个动态端口或者使用命名实例时，会发生此问题。 通过传入连接连接到命名实例或者使用动态端口时，SQL Server 浏览器服务会侦听 UDP 端口 1434。 每次 SQL Server 服务重启时，动态端口可能会更改。 可以通过 SQL Server 配置管理器中的网络配置检查分配给实例的动态端口。<br><br><br> |验证 Azure 数据库迁移服务能否在适用情况下通过动态分配的 TCP 端口连接到 UDP 端口 1434 上的 SQL Server 浏览器服务和 SQL Server 实例。 |

## <a name="additional-known-issues"></a>其他已知问题

* [联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [在联机迁移到 Azure Database for MySQL 时存在的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [在联机迁移到 Azure Database for PostgreSQL 时存在的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 参阅[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)一文。
* 查看[如何使用 Azure 门户在 Azure Database for MySQL 中配置服务器参数](https://docs.microsoft.com/azure/mysql/howto-server-parameters)一文。
* 查看[有关使用 Azure 数据库迁移服务的先决条件的文章概述](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 请参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
