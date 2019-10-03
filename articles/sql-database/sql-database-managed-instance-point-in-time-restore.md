---
title: SQL 数据库托管实例-时间点还原 |Microsoft Docs
description: 如何将 SQL 托管实例中的数据库还原到以前的时间点。
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862130"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>将 SQL 托管实例数据库还原到以前的时间点

使用时间点还原（PITR），可以在过去某个时间点创建数据库作为另一个数据库的副本。 本文介绍如何在托管实例中执行数据库的时点还原。

可以在恢复方案中使用时间点还原，如因错误、错误地加载数据、删除重要数据、删除重要数据、删除重要数据、删除重要数据、以及对测试或审核目的而导致的事件。 根据您的数据库设置，备份文件保留7到35天之间的一段时间。

时间点还原可用于：

- 从现有数据库还原数据库。
- 从已删除的数据库还原数据库。

此外，对于托管实例，时间点还原可用于： 

- 将数据库还原到同一个托管实例。
- 将数据库还原到另一个托管实例。


> [!NOTE]
> 不能对整个托管实例进行时间点还原。 本文中所述的内容是托管实例上托管的数据库的时点还原。


## <a name="limitations"></a>限制

还原到另一个托管实例时，两个实例都必须在同一订阅和区域中。 当前不支持跨区域和跨订阅还原。

> [!WARNING]
> 请注意托管实例的存储大小-根据还原数据的大小，可能会用尽实例存储。 如果没有足够的空间用于还原的数据，请使用另一种方法。

下表显示了托管实例的时间点恢复方案：

|           |还原现有数据库| 还原现有数据库|还原已删除的数据库| 还原已删除的数据库|
|:----------|:----------|:----------|:----------|:----------|
|目标| 相同英里|其他英里 |相同英里|其他英里 |
|Azure 门户| 是|否 |否|否|
|Azure CLI|是 |是 |否|否|
|PowerShell| 是|是 |是|是|


## <a name="restore-existing-database"></a>还原现有数据库

使用 Azure 门户、Powershell 或 Azure CLI 将现有数据库还原到相同的实例。 通过指定目标托管实例和资源组属性，使用 Powershell 或 Azure CLI 将数据库还原到另一个实例。 如果未指定这些参数，则默认情况下将数据库还原到现有实例。 当前不支持通过 Azure 门户还原到另一个实例。 

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到托管实例，并选择要还原的数据库。 
1. 在 "数据库" 页上选择 "**还原**"。 

    ![还原现有数据库](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. 在 "**还原**" 页上，选择要将数据库还原到的历史记录中的日期和时间的点。
1. 选择 "**确认**" 以还原数据库。 这会启动还原过程，该过程将创建一个新的数据库，并使用原始数据库中所需时间点的数据进行填充。 有关恢复过程的详细信息，请参阅[恢复时间](sql-database-recovery-using-backups.md#recovery-time)。 

1. 查找托管实例
1. 选择要还原的数据库
1. 在 "数据库" 屏幕上，单击 "还原操作"
1. 在 "还原" 屏幕上，选择要将数据库还原到的历史记录中的时间点日期和时间
1. 确认后，还原过程将开始，并且根据数据库的大小，将创建新数据库，并在所需的时间点用原始数据库中的数据填充新数据库。 对于还原过程的持续时间，请参阅使用备份进行恢复一文。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

如果尚未安装 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要使用 PowerShell 还原数据库，请使用值更新参数，并运行以下命令：

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

若要将数据库还原到另一个托管实例，请设置目标资源组名称和目标托管实例的名称。  

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

若要使用 Azure CLI 还原数据库，请使用值更新参数，并运行以下命令：


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


若要将数据库还原到另一个托管实例，请设置目标资源组名称和目标托管实例的名称。  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

有关可用参数的详细说明，请参阅[托管实例 CLI](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)。 

---

## <a name="restore-a-deleted-database"></a>还原已删除的数据库 
 
只能使用 PowerShell 来还原已删除的数据库。 可以将数据库还原到相同的实例或其他实例。 

若要使用 PowerShell 还原已删除的数据库，请使用值更新参数，并运行以下命令：

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

若要将已删除的数据库还原到另一个实例，请更改资源组名称和托管实例名称。

Location 参数应匹配资源组和托管实例的位置。

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>覆盖现有数据库 
 
若要覆盖现有数据库，还必须执行以下操作：

1. 删除要覆盖的现有数据库。
1. 将时间点还原的数据库重命名为已删除的数据库的名称。 


### <a name="drop-original-database"></a>删除原始数据库 
 
可以通过 Azure 门户、PowerShell 或 Azure CLI 来删除数据库。 

您还可以通过直接连接到托管实例、启动 SQL Server Management Studio （SSMS）并运行下面的 Transact-sql （T-sql）命令来删除数据库。

```sql
DROP DATABASE WorldWideImporters;
```

使用以下方法之一连接到托管实例数据库： 

- [SQL 虚拟机](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [点到站点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，从托管实例中选择数据库，然后选择 "**删除**"。

   ![还原现有数据库](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

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


### <a name="alter-new-database-name-to-original"></a>将新的数据库名称更改为原始名称

直接连接到托管实例，启动 SQL Server Management Studio，然后执行以下 Transact-sql （T-sql）查询，将还原的数据库的名称更改为你打算覆盖的已删除数据库的名称。 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


使用以下方法之一连接到托管实例数据库： 

- [SQL 虚拟机](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [点到站点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公共终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>后续步骤

了解长期[保留](sql-database-long-term-retention.md)和[自动备份](sql-database-automated-backups.md)。 
