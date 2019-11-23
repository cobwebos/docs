---
title: 复制数据库
description: 在相同或不同的服务器上创建现有 Azure SQL 数据库的事务一致性副本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: b3bc99d0fbdb551af0fb3711d74db537d3f9b1a5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421345"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>复制 Azure SQL 数据库的事务一致性副本

Azure SQL Database provides several methods for creating a transactionally consistent copy of an existing Azure SQL database ([single database](sql-database-single-database.md)) on either the same server or a different server. 可以使用 Azure 门户、PowerShell 或 T-SQL 复制 SQL 数据库。

## <a name="overview"></a>概述

数据库副本是源数据库截至复制请求发出时的快照。 You can select the same server or a different server. Also you can choose to keep its service tier and compute size, or use a different compute size within the same service tier (edition). 在完成该复制后，副本将成为能够完全行使功能的独立数据库。 此时，可以升级或降级到任意版本。 登录名、用户和权限可单独进行管理。 The copy is created using the geo-replication technology and once seeding is completed the geo-replication link is automatically terminated. All the requirements for using geo-replication apply to the database copy operation. See [Active geo-replication overview](sql-database-active-geo-replication.md) for details.

> [!NOTE]
> 在创建数据库副本时，将用到[自动数据库备份](sql-database-automated-backups.md)。

## <a name="logins-in-the-database-copy"></a>数据库副本中的登录名

将某个数据库复制到同一 SQL 数据库服务器时，可以在这两个数据库上使用相同的登录名。 用于复制该数据库的安全主体将成为新数据库上的数据库所有者。 所有数据库用户、其权限及安全标识符 (SID) 都复制到数据库副本中。  

将数据库复制到不同的 SQL 数据库服务器时，新服务器上的安全主体将成为新数据库上的数据库所有者。 如果使用[包含的数据库用户](sql-database-manage-logins.md)进行数据访问，请确保主数据库和辅助数据库始终具有相同的用户凭据，这样在复制完成后，便可以使用相同的凭据立即访问。

如果使用 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，则完全无需管理副本中的凭据。 但是，将数据库复制到新服务器时，基于登录名的访问可能不起作用，因为登录名在新服务器上不存在。 要了解如何在将数据库复制到其他 SQL 数据库服务器时管理登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。

复制成功之后，重新映射其他用户之前，只有启动复制的登录名，即数据库所有者，才能登录到新数据库。 若要在复制操作完成后解析登录名，请参阅[解析登录名](#resolve-logins)。

## <a name="copy-a-database-by-using-the-azure-portal"></a>使用 Azure 门户复制数据库

要使用 Azure 门户复制数据库，请打开数据库页，并单击“复制”。

   ![数据库复制](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>使用 PowerShell 复制数据库

To copy a database, use the following examples.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

For PowerShell, use the [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet.

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) command.

* * *

如需完整的示例脚本，请参阅[将数据库复制到新的服务器](scripts/sql-database-copy-database-to-new-server-powershell.md)。

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC roles to manage database copy

To create a database copy, you will need to be in the following roles

- “订阅所有者”或
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To cancel a database copy, you will need to be in the following roles

- “订阅所有者”或
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To manage database copy using Azure portal, you will also need the following permissions:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

If you want to see the operations under deployments in the resource group on the portal, operations across multiple resource providers including SQL operations, you will need these additional RBAC roles:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>使用 Transact-SQL 复制数据库

使用服务器级别主体登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器级主体的登录名必须是 dbmanager 角色的成员。 有关登录名和链接到服务器的详细信息，请参阅[管理登录名](sql-database-manage-logins.md)。

使用 [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 语句开始复制源数据库。 执行此语句将启动数据库复制过程。 因为复制数据库是一个异步过程，所以，CREATE DATABASE 语句会在数据库完成复制前返回。

### <a name="copy-a-sql-database-to-the-same-server"></a>将 SQL 数据库复制到同一台服务器

使用服务器级别主体登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器级主体的登录名必须是 dbmanager 角色的成员。

此命令将 Database1 复制到同一服务器上名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>将 SQL 数据库复制到不同的服务器

登录到目标服务器（即要创建新数据库的 SQL 数据库服务器）的 master 数据库。 所用登录名的名称和密码应该与源 SQL 数据库服务器上源数据库的数据库所有者的名称和密码相同。 目标服务器上的登录名也必须是 dbmanager 角色的成员或服务器级主体登录名。

此命令将 server1 上的 Database1 复制到 server2 上名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Both servers' firewalls must be configured to allow inbound connection from the IP of the client issuing the T-SQL COPY command.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copy a SQL database to a different subscription

You can use the steps described in the previous section to copy your database to a SQL Database server in a different subscription. Make sure you use a login that has the same name and password as the database owner of the source database and it is a member of the dbmanager role or is the server-level principal login. 

> [!NOTE]
> The [Azure portal](https://portal.azure.com) does not support copy to a different subscription because Portal calls the ARM API and it uses the subscription certificates to access both servers involved in geo-replication.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>监视复制操作的进度

通过查询 sys.databases 和 sys.dm_database_copies 视图来监视复制过程。 在复制过程中，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **COPYING**。

* 如果复制失败，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **SUSPECT**。 对新数据库执行 DROP 语句并稍后重试。
* 如果复制成功，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **ONLINE**。 复制已完成并且新数据库是一个常规数据库，可独立于源数据库进行更改。

> [!NOTE]
> 如果决定在复制过程中取消复制，请对新数据库执行 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 语句。 此外，对源数据库执行 DROP DATABASE 语句也将取消复制过程。

> [!IMPORTANT]
> If you need to create a copy with a substantially smaller SLO than the source, the target database may not have sufficient resources to complete the seeding process and it can cause the copy operaion to fail. In this scenario use a geo-restore request to create a copy in a different server and/or a different region. See [Recover an Azure SQL database using database backups](sql-database-recovery-using-backups.md#geo-restore) for more informaion.

## <a name="resolve-logins"></a>解析登录名

当新数据库在目标服务器上联机后，使用 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 语句将新数据库中的用户重新映射到目标服务器上的登录名。 若要解析孤立用户，请参阅[孤立用户疑难解答](https://msdn.microsoft.com/library/ms175475.aspx)。 另请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。

新数据库中的所有用户都保持他们在源数据库中已有的权限。 启动数据库复制过程的用户将成为新数据库的数据库所有者，并且会为该用户分配一个新的安全标识符 (SID)。 复制成功之后，重新映射其他用户之前，只有启动复制的登录名，即数据库所有者，才能登录到新数据库。

要了解如何在将数据库复制到其他 SQL 数据库服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库的安全性](sql-database-geo-replication-security-config.md)。

## <a name="database-copy-errors"></a>数据库复制错误

在 Azure SQL 数据库中复制数据库时，可能会发生以下错误。 有关详细信息，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。

| 错误代码 | Severity | 描述 |
| ---:| ---:|:--- |
| 40635 |16 |IP 地址为“%.&#x2a;ls”的客户端已暂时禁用。 |
| 40637 |16 |创建数据库副本当前处于禁用状态。 |
| 40561 |16 |数据库复制失败。 源数据库或目标数据库不存在。 |
| 40562 |16 |数据库复制失败。 源数据库已删除。 |
| 40563 |16 |数据库复制失败。 目标数据库已删除。 |
| 40564 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40565 |16 |数据库复制失败。 不允许来自同一源的多个并发数据库复制。 请删除目标数据库，并重试。 |
| 40566 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40567 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40568 |16 |数据库复制失败。 源数据库已变得不可用。 请删除目标数据库，并重试。 |
| 40569 |16 |数据库复制失败。 目标数据库已变得不可用。 请删除目标数据库，并重试。 |
| 40570 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |
| 40571 |16 |数据库复制由于内部错误而失败。 请删除目标数据库，并重试。 |

## <a name="next-steps"></a>后续步骤

- 有关登录名的信息，请参阅[管理登录名](sql-database-manage-logins.md)和[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
- 要导出数据库，请参阅[将数据库导出到 BACPAC](sql-database-export.md)。
