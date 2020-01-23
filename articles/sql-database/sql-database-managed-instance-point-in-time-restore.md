---
title: 托管实例-时间点还原
description: 将托管实例中的 SQL 数据库还原到以前的时间点。
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
ms.openlocfilehash: 9ed694ec524c4e3e033c3139735e8e079141ec4a
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515116"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>将托管实例中的 SQL 数据库还原到以前的时间点

使用时间点还原（PITR）将数据库作为另一数据库的副本创建，从过去的某个时间开始。 本文介绍如何在 Azure SQL 数据库托管实例中执行数据库的时点还原。

时间点还原在恢复方案中非常有用，例如，由于错误、错误地加载数据或删除关键数据导致的事件。 你还可以将其仅用于测试或审核。 备份文件保留7到35天，具体取决于你的数据库设置。

时间点还原可以：

- 从现有数据库还原数据库。
- 从已删除的数据库还原数据库。

对于托管实例，时间点还原也可以：

- 将数据库还原到同一个托管实例。
- 将数据库还原到另一个托管实例。

> [!NOTE]
> 不能对整个托管实例进行时间点还原。 本文仅介绍了可能的功能：托管实例上托管的数据库的时点还原。

## <a name="limitations"></a>限制

从一个托管实例还原到另一个托管实例时，两个实例必须位于相同的订阅和区域中。 当前不支持跨区域和跨订阅还原。

> [!WARNING]
> 请注意托管实例的存储大小。 根据要还原的数据的大小，可能会用尽实例存储。 如果没有足够的空间用于还原的数据，请使用其他方法。

下表显示了托管实例的时间点还原方案：

|           |将现有数据库还原到同一个托管实例| 将现有数据库还原到另一个托管实例|将删除的数据库还原到同一个托管实例|将删除的数据库还原到另一个托管实例|
|:----------|:----------|:----------|:----------|:----------|
|**Azure 门户**| 是|否 |否|否|
|**Azure CLI**|是 |是 |否|否|
|**PowerShell**| 是|是 |是|是|

## <a name="restore-an-existing-database"></a>还原现有数据库

使用 Azure 门户、Powershell 或 Azure CLI 将现有数据库还原到相同的实例。 若要将数据库还原到另一个实例，请使用 Powershell 或 Azure CLI 以便可以指定目标托管实例和资源组的属性。 如果未指定这些参数，则默认情况下，数据库将还原到现有实例。 Azure 门户当前不支持还原到另一个实例。

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 中转到托管实例，并选择要还原的数据库。
3. 在 "数据库" 页上选择 "**还原**"：

    ![使用 Azure 门户还原数据库](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. 在 "**还原**" 页上，选择要将数据库还原到的日期和时间的点。
5. 选择 "**确认**" 以还原数据库。 此操作启动还原过程，该过程将创建一个新的数据库，并在指定的时间点使用原始数据库中的数据填充该数据库。 有关恢复过程的详细信息，请参阅[恢复时间](sql-database-recovery-using-backups.md#recovery-time)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

如果尚未安装 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要使用 PowerShell 还原数据库，请在以下命令中指定参数的值。 然后，运行以下命令：

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

若要将数据库还原到另一个托管实例，还需要指定目标资源组和托管实例的名称：  

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

有关详细信息，请参阅[AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要使用 Azure CLI 还原数据库，请在以下命令中指定参数的值。 然后，运行以下命令：

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

若要将数据库还原到另一个托管实例，还需要指定目标资源组和托管实例的名称：  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

有关可用参数的详细说明，请参阅在[托管实例中还原数据库的 CLI 文档](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)。

---

## <a name="restore-a-deleted-database"></a>还原已删除数据库

可以使用 PowerShell 或 Azure 门户还原已删除的数据库。请使用本文档通过[Azure 门户](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#managed-instance-database-1)执行此操作。 可以将数据库还原到同一个实例或另一个实例。

若要使用 PowerShell 还原已删除的数据库，请在以下命令中指定参数的值。 然后，运行以下命令：

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

若要将已删除的数据库还原到另一个实例，请更改资源组和托管实例的名称。 此外，请确保 location 参数与资源组和托管实例的位置匹配。

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>覆盖现有数据库

若要覆盖现有数据库，必须执行以下操作：

1. 删除要覆盖的现有数据库。
2. 将时间点还原的数据库重命名为你删除的数据库的名称。

### <a name="drop-the-original-database"></a>删除原始数据库

您可以使用 Azure 门户、PowerShell 或 Azure CLI 删除数据库。

您还可以通过直接连接到托管实例，从 SQL Server Management Studio （SSMS）中删除数据库，然后运行以下 Transact-sql （T-sql）命令：

```sql
DROP DATABASE WorldWideImporters;
```

使用以下方法之一连接到托管实例中的数据库：

- [通过 Azure 虚拟机进行 SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [点到站点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，从托管实例中选择数据库，然后选择 "**删除**"。

   ![使用 Azure 门户删除数据库](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell 命令从托管实例中删除现有数据库：

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 Azure CLI 命令从托管实例中删除现有数据库：

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>更改新数据库名称以匹配原始数据库名称

直接连接到托管实例并开始 SQL Server Management Studio。 然后，运行以下 Transact-sql （T-sql）查询。 查询会将已还原数据库的名称更改为要覆盖的已删除数据库的名称。

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

使用以下方法之一连接到托管实例中的数据库：

- [Azure 虚拟机](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [点到站点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>后续步骤

了解[自动备份](sql-database-automated-backups.md)。
