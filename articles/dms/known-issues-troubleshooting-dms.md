---
title: 常见问题-Azure 数据库迁移服务
description: 了解如何排查与使用 Azure 数据库迁移服务关联的常见已知问题/错误。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649101"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>排查常见的 Azure 数据库迁移服务问题和错误

本文介绍 Azure 数据库迁移服务用户可能会遇到的一些常见问题和错误。 本文还包括有关如何解决这些问题和错误的信息。

## <a name="migration-activity-in-queued-state"></a>处于排队状态的迁移活动

在 Azure 数据库迁移服务项目中创建新活动时，活动仍处于排队状态。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例已达到并发运行的任务的最大容量，则会出现此问题。 在容量可用之前，将对任何新活动进行排队。 | 验证数据迁移服务实例是否在项目中运行了活动。 您可以继续创建新活动，自动将其添加到队列以便执行。 一旦现有的任何正在运行的活动完成，下一个排队活动就会开始运行，并且状态将自动更改为 "正在运行" 状态。 您无需执行任何其他操作即可启动排队活动的迁移。<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>选择用于迁移的数据库的最大数目

为迁移到 Azure SQL 数据库或 Azure SQL 数据库托管实例的数据库迁移项目创建活动时，将发生以下错误：

* **错误**：迁移设置验证错误 "，" errorDetail "：" 已选择 "数据库" 的最大数目 "4" 对象进行迁移。 "

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果为单个迁移活动选择了四个以上的数据库，则会显示此错误。 目前，每个迁移活动限制为四个数据库。 | 为每个迁移活动选择四个或更少的数据库。 如果需要并行迁移四个以上的数据库，请预配 Azure 数据库迁移服务的另一个实例。 目前，每个订阅最多支持两个 Azure 数据库迁移服务实例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>与恢复失败的 MySQL 迁移到 Azure MySQL 的错误

使用 Azure 数据库迁移服务从 MySQL 迁移到 Azure Database for MySQL 时，迁移活动将失败，并出现以下错误：

* **错误**：数据库迁移错误-由于 [n] 连续恢复失败，任务 "TaskID" 已挂起。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果执行迁移的用户缺少 ReplicationAdmin 角色和/或复制客户端、复制副本和超级权限（MySQL 5.6.6 之前的版本），则可能出现此错误。<br><br><br><br><br><br><br><br><br><br><br><br><br> | 请确保在 Azure Database for MySQL 实例上准确配置了用户帐户的[先决条件特权](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites)。 例如，可以遵循以下步骤来创建一个名为 "migrateuser" 的用户，该用户具有所需的权限：<br>1. 创建 "secret" 标识的用户 migrateuser@ "%"; <br>2. 向 "secret" 标识的 "migrateuser" @ "%" 授予对 db_name. * 的所有特权。重复此步骤以授予对更多数据库的访问权限 <br>3. 授予复制从属 *。* 到 "secret" 标识的 "migrateuser" @ "%";<br>4. 在上授予复制客户端 *。* 到 "secret" 标识的 "migrateuser" @ "%";<br>5. 刷新权限; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>尝试停止 Azure 数据库迁移服务时出错

停止 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务未能停止。 错误: {'error':{'code':'InvalidRequest','message':'当前正在运行一个或多个活动。 若要停止该服务，请等待活动完成或手动停止这些活动，然后重试。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 当你尝试停止的服务实例包括仍在运行或在迁移项目中存在的活动时，将显示此错误。 <br><br><br><br><br><br> | 请确保正在尝试停止的 Azure 数据库迁移服务实例中没有运行任何活动。 你还可以在尝试停止服务之前删除活动或项目。 以下步骤演示了如何通过删除所有正在运行的任务来删除项目以清理迁移服务实例：<br>1. AzureRM-Name Microsoft.datamigration <br>2. 登录名-Add-azurermaccount <br>3. Get-azurermsubscription-SubscriptionName "\<>" <br> 4. AzureRmDataMigrationProject-Name \<项目名称 >-ResourceGroupName \<rgName >-ServiceName \<serviceName >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>尝试启动 Azure 数据库迁移服务时出错

启动 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务无法启动。 错误： {"errorDetail"： "服务无法启动，请联系 Microsoft 支持部门"}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 当上一个实例在内部失败时，将显示此错误。 此错误很少发生，并且工程团队认识到了这一点。 <br> | 删除无法启动的服务实例，然后设置新的实例以替换它。 |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>将 SQL 迁移到 Azure SQL DB 托管实例时还原数据库时出错

执行从 SQL Server 到 Azure SQL 数据库托管实例的联机迁移时，转换失败并出现以下错误：

* **错误**：操作 Id "operationId" 的还原操作失败。 代码 "AuthorizationFailed" （对象 id 为 "objectId" 的客户端 "clientId"）没有授权，无法执行操作 "/subscriptions/subscriptionId" 上的操作 ""。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 此错误表明，要用于从 SQL Server 到 Azure SQL 数据库托管实例的联机迁移的应用程序主体没有针对订阅的 "参与" 权限。 对于包含托管实例的某些 API 调用，需要对订阅的此权限才能执行还原操作。 <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | 使用错误消息中具有 `-ObjectId` 的 `Get-AzureADServicePrincipal` PowerShell cmdlet 列出所使用的应用程序 ID 的显示名称。<br><br> 验证对此应用程序的权限，并确保其在订阅级别具有[参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)。 <br><br> Azure 数据库迁移服务工程团队正在努力限制订阅上当前参与角色所需的访问权限。 如果你的业务要求不允许使用参与角色，请联系 Azure 支持以获取更多帮助。 |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>删除与 Azure 数据库迁移服务关联的 NIC 时出错

尝试删除与 Azure 数据库迁移服务关联的网络接口卡时，删除尝试将失败，并出现以下错误：

* **错误**：无法删除与 Azure 数据库迁移服务相关联的 nic，因为 DMS 服务利用了 nic

| 原因         | 解决方法    |
| ------------- | ------------- |
| 当 Azure 数据库迁移服务实例可能仍然存在并使用 NIC 时，会发生此问题。 <br><br><br><br><br><br><br><br> | 若要删除此 NIC，请删除自动删除该服务使用的 NIC 的 DMS 服务实例。<br><br> **重要提示**：请确保正在删除的 Azure 数据库迁移服务实例没有正在运行的活动。<br><br> 删除所有与 Azure 数据库迁移服务实例关联的项目和活动后，您可以删除该服务实例。 服务实例使用的 NIC 在删除服务时自动清除。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 时出现连接错误

当你尝试连接到 Azure 数据库迁移服务项目向导中的源时，如果该源使用 ExpressRoute 进行连接，将在很长时间后超时且连接失败。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 使用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)时，Azure 数据库迁移服务[需要](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)在与服务相关联的虚拟网络子网上预配三个服务终结点：<br> --服务总线终结点<br> --存储终结点<br> --目标数据库终结点（例如 SQL 终结点，Cosmos DB 终结点）<br><br><br><br><br> | 在源与 Azure 数据库迁移服务之间[启用](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online)ExpressRoute 连接所需的服务终结点。 <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>将 MySQL 数据库迁移到 Azure DB for MySQL 时出现锁定等待超时错误

通过 Azure 数据库迁移服务将 MySQL 数据库迁移到 Azure Database for MySQL 实例时，迁移将失败，并出现以下锁定等待超时错误：

* **错误**：数据库迁移错误-无法加载文件-无法启动文件 "n" 的加载进程 RetCode： SQL_ERROR SQLSTATE： HY000 NativeError：1205消息： [MySQL] [ODBC Driver] [mysqld] 超出了锁等待超时时间;尝试重新启动事务

| 原因         | 解决方法    |
| ------------- | ------------- |
| 如果迁移失败，因为在迁移过程中锁等待超时，则会出现此错误。 | 请考虑增大服务器参数 **"innodb_lock_wait_timeout"** 的值。 允许的最大值为1073741824。 |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>使用动态端口或命名实例连接到源 SQL Server 时出错

当你尝试将 Azure 数据库迁移服务连接到在任一命名实例或动态端口上运行 SQL Server 源时，连接将失败并出现以下错误：

* **错误**：-1-SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 请验证实例名称是正确的，且将 SQL Server 配置为允许远程连接。 （提供程序： SQL 网络接口，错误： 26-定位指定的服务器/实例时出错）

| 原因         | 解决方法    |
| ------------- | ------------- |
| 当 Azure 数据库迁移服务尝试连接到的源 SQL Server 实例具有动态端口或正在使用命名实例时，会发生此问题。 SQL Server Browser 服务侦听 UDP 端口1434，以便在使用动态端口时传入连接到命名实例。 每次重新启动服务 SQL Server，动态端口可能会更改。 可以通过 SQL Server 配置管理器中的网络配置来检查分配给实例的动态端口。<br><br><br> |验证 Azure 数据库迁移服务能否通过动态分配的 TCP 端口（如果适用），连接到 UDP 端口1434上的源 SQL Server Browser 服务和 SQL Server 实例。 |

## <a name="additional-known-issues"></a>其他已知问题

* [联机迁移到 Azure SQL Database 时的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [联机迁移到 Azure Database for MySQL 的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [联机迁移到 Azure Database for PostgreSQL 的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 查看[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)一文。
* 查看[如何使用 Azure 门户在 Azure Database for MySQL 中配置服务器参数](https://docs.microsoft.com/azure/mysql/howto-server-parameters)一文。
* 查看[有关使用 Azure 数据库迁移服务的先决条件的文章概述](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 请参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
