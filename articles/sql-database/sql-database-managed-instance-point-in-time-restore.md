---
title: 托管实例 - 时间点还原 （PITR）
description: 将托管实例中的 SQL 数据库还原到以前的某个时间点。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268803"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>将托管实例中的 SQL 数据库还原到以前的某个时间点

使用时间点还原 (PITR)，可以创建一个数据库作为另一个数据库在过去某个时间点的副本。 本文介绍如何对 Azure SQL 数据库托管实例中的数据库执行时间点还原。

可以在恢复方案中使用时间点还原，例如，解决错误导致的事件、错误加载了数据或删除了重要数据等问题。 还可以使用时间点还原进行测试或审核。 备份文件将根据数据库设置保留 7 到 35 天。

时间点还原可以还原数据库：

- 从现有数据库。
- 从已删除的数据库。
- 到同一托管实例，或另一个托管实例。 

## <a name="limitations"></a>限制

还原到托管实例的时间点具有以下限制：

- 从一个托管实例还原到另一个托管实例时，这两个实例必须位于同一个订阅和区域中。 目前不支持跨区域和跨订阅的还原。
- 无法对整个托管实例执行时间点还原。 本文仅介绍可行的操作：对托管实例上托管的数据库执行时间点还原。

> [!WARNING]
> 请注意托管实例的存储大小。 根据要还原数据的大小，可能会耗尽实例存储。 如果没有足够的空间用于存储还原的数据，请使用其他方法。

下表显示了托管实例的时间点还原方案：

|           |将现有的数据库还原到同一个托管实例| 将现有的数据库还原到另一个托管实例|将已删除的数据库还原到同一个托管实例|将已删除的数据库还原到另一个托管实例|
|:----------|:----------|:----------|:----------|:----------|
|**Azure 门户**| 是|否 |是|否|
|**Azure CLI**|是 |是 |否|否|
|**电源外壳**| 是|是 |是|是|

## <a name="restore-an-existing-database"></a>还原现有数据库

使用 Azure 门户、PowerShell 或 Azure CLI 将现有数据库还原到同一实例。 要将数据库还原到另一个实例，请使用 PowerShell 或 Azure CLI，以便指定目标托管实例和资源组的属性。 如果不指定这些参数，数据库默认将还原到现有实例。 目前不支持通过 Azure 门户还原到另一个实例。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 Azure[门户](https://portal.azure.com)。 
2. 转到你的托管实例，并选择要还原的数据库。
3. 在数据库页上选择“还原”：****

    ![使用 Azure 门户还原数据库](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. 在“还原”页上，选择要将数据库还原到的日期时间点。****
5. 选择“确认”还原该数据库。**** 此操作会启动还原过程，期间会创建一个新数据库，并在其中填充原始数据库在指定时间点的数据。 有关恢复过程的详细信息，请参阅[恢复时间](sql-database-recovery-using-backups.md#recovery-time)。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

如果尚未安装 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要使用 PowerShell 还原数据库，请在以下命令中指定参数值。 然后运行该命令：

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

要将数据库还原到另一个托管实例，请指定目标资源组和目标托管实例的名称：  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

有关详细信息，请参阅 [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要使用 Azure CLI 还原数据库，请在以下命令中指定参数值。 然后运行该命令：

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

若要将数据库还原到另一个托管实例，另请指定目标资源组和托管实例的名称：  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

有关可用参数的详细说明，请参阅[有关在托管实例中还原数据库的 CLI 文档](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)。

---

## <a name="restore-a-deleted-database"></a>还原已删除数据库

还原已删除的数据库可以通过使用 PowerShell 或 Azure 门户来完成。 要将已删除的数据库还原到同一实例，请使用 Azure 门户或 PowerShell。 要将已删除的数据库还原到另一个实例，请使用 PowerShell。 

### <a name="portal"></a>门户 


要使用 Azure 门户恢复托管数据库，请打开托管实例概览页，然后选择 **"已删除的数据库**"。 选择要还原的已删除数据库，然后键入将使用从备份还原的数据创建的新数据库的名称。

  ![还原已删除的 Azure SQL 实例数据库的屏幕截图](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

要将数据库还原到同一实例，请更新参数值，然后运行以下 PowerShell 命令： 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

要将数据库还原到另一个托管实例，请指定目标资源组和目标托管实例的名称：

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>覆盖现有数据库

若要覆盖现有数据库，必须执行以下操作：

1. 删除要覆盖的现有数据库。
2. 将时间点还原的数据库重命名为已删除的数据库的名称。

### <a name="drop-the-original-database"></a>删除原始数据库

可以使用 Azure 门户、PowerShell 或 Azure CLI 删除数据库。

还可以通过直接连接到托管实例，启动 SQL Server Management Studio (SSMS) 并运行以下 Transact-SQL (T-SQL) 命令，来删除数据库：

```sql
DROP DATABASE WorldWideImporters;
```

使用以下方法之一连接到托管实例中的数据库：

- [在 Azure 虚拟机中使用 SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [点对点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户上选择托管实例中的数据库，然后选择“删除”。****

   ![使用 Azure 门户删除数据库](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

使用以下 PowerShell 命令删除托管实例中的现有数据库：

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 Azure CLI 命令删除托管实例中的现有数据库：

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>更改新数据库名称，使之与原始数据库名称匹配

直接连接到托管实例并启动 SQL Server Management Studio。 然后运行以下 Transact-SQL (T-SQL) 查询。 该查询将已还原数据库的名称更改为要覆盖的已删除数据库的名称。

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

使用以下方法之一连接到托管实例中的数据库：

- [Azure 虚拟机](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [点对点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>后续步骤

了解[自动备份](sql-database-automated-backups.md)。
