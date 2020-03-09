---
title: '快速入门：暂停和继续计算 - PowerShell '
description: 使用 PowerShell 暂停 Azure Synapse Analytics SQL 池中的计算以节省成本。 在准备好使用数据仓库时恢复计算。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ce183edef9e5895d7b3f702f5466c505956a869a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200560"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 暂停和恢复 Azure Synapse Analytics SQL 池中的计算

使用 Azure PowerShell 暂停 SQL 池的计算以节省成本。 在准备好使用数据仓库时[还原计算](sql-data-warehouse-manage-compute-overview.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本快速入门假设已有一个可以暂停和恢复的 SQL 池。 如果需要创建一个 SQL 池，可以参考[创建和连接 - 门户](create-data-warehouse-portal.md)创建名为 **mySampleDataWarehouse** 的 SQL 池。

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Connect-AzAccount
```

若要查看正在使用的订阅，请运行 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)。

```powershell
Get-AzSubscription
```

如果需要使用与默认订阅不同的订阅，请运行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>查找数据仓库信息

查找计划暂停和恢复的 SQL 池的数据库名称、服务器名称和资源组。

按照以下步骤查找 SQL 池的位置信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在 Azure 门户的左侧页中单击“Azure Synapse Analytics (前称为 SQL 数据仓库)”。 
1. 在“Azure Synapse Analytics (前称为 SQL 数据仓库)”页中选择“mySampleDataWarehouse”。   此操作打开数据仓库。

    ![服务器名称和资源组](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. 记下将用作数据库名称的数据仓库名称。 同时记下服务器名称和资源组。
1. 在 PowerShell cmdlet 中请仅使用服务器名称的第一个组成部分。 在上图中，完整的服务器名称为 sqlpoolservername.database.windows.net。 我们在 PowerShell cmdlet 中使用 **sqlpoolservername** 作为服务器名称。

## <a name="pause-compute"></a>暂停计算

为了节省成本，可以按需暂停和恢复计算资源。 例如，如果晚上和周末不使用数据库，那么可以在这些时间暂停数据库的使用，然后在白天时恢复使用。 数据库暂停时，不对计算资源进行收费。 但是，仍将收取存储费用。

若要暂停数据库，请使用 [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) cmdlet。 以下示例暂停 **sqlpoolservername** 服务器上托管的 **mySampleDataWarehouse** 数据仓库。 该服务器位于名为 myResourceGroup 的 Azure 资源组中  。


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

一种变异，下一个示例将数据库检索到 $database 对象中。 然后，它通过管道将该对象传递给 [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase)。 结果存储在对象 resultDatabase 中。 最后一个命令显示结果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>恢复计算

若要启动数据库，请使用 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) cmdlet。 以下示例启动 **sqlpoolservername** 服务器上托管的 **mySampleDataWarehouse** 数据库。 该服务器位于名为 myResourceGroup 的 Azure 资源组中  。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

一种变异，下一个示例将数据库检索到 $database 对象中。 然后，它通过管道将对象传递给 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase)，并将结果存储在 $resultDatabase 中。 最后一个命令显示结果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>检查数据仓库操作的状态

若要检查数据仓库的状态，请使用 [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) cmdlet。

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>清理资源

针对数据仓库资源用量和数据仓库存储的数据，将会收取你的费用。 这些计算和存储资源是分开计费的。

- 要将数据保存在存储中，请暂停计算。
- 若要避免将来产生费用，可以删除该 SQL 池。

请按照下列步骤按需清理资源。

1. 登录到 [Azure 门户](https://portal.azure.com)并单击该 SQL 池。

    ![清理资源](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 要暂停计算，请单击“暂停”  按钮。 暂停 SQL 池后，会看到“启动”按钮。   要恢复计算，请单击“启动”  。

3. 若要删除 SQL 池以免产生计算或存储费用，请单击“删除”。 

4. 若要删除创建的 SQL 服务器，请依次单击“sqlpoolservername.database.windows.net”、“删除”。    请谨慎执行此删除操作，因为删除服务器的同时也会删除分配给该服务器的所有数据库。

5. 要删除资源组，请单击“myResourceGroup”  ，然后单击“删除资源组”  。


## <a name="next-steps"></a>后续步骤

现已暂停并恢复了 SQL 池的计算。 若要详细了解 SQL 池，请继续阅读有关加载数据的教程。

> [!div class="nextstepaction"]
> [将数据载入 SQL 池](load-data-from-azure-blob-storage-using-polybase.md)
