---
title: 通过 Azure PowerShell 暂停和恢复 Synapse SQL 池中的计算
description: 可以使用 Azure PowerShell 暂停和恢复 Synapse SQL 池（数据仓库）。 计算资源。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 44ce56238140de90145f69e966c94be8572c7749
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81252296"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>快速入门：通过 Azure PowerShell 暂停和恢复 Synapse SQL 池中的计算

可以使用 Azure PowerShell 暂停和恢复 Synapse SQL 池（数据仓库）计算资源。
如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本快速入门假设已有一个可以暂停和恢复的 SQL 池。 如果需要创建一个 SQL 池，可以参考[创建和连接 - 门户](create-data-warehouse-portal.md)创建名为 **mySampleDataWarehouse** 的 SQL 池。

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Connect-AzAccount
```

若要查看正在使用的订阅，请运行 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

```powershell
Get-AzSubscription
```

如果需要使用与默认订阅不同的订阅，请运行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>查找 SQL 池信息

查找计划暂停和恢复的 SQL 池的数据库名称、服务器名称和资源组。

按照以下步骤查找 SQL 池的位置信息：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在 Azure 门户的左侧页中单击“Azure Synapse Analytics (前称为 SQL 数据仓库)”。 
1. 从“Azure Synapse Analytics (以前称为 SQL DW)”  页中选择 **mySampleDataWarehouse**。 此时将打开 SQL 池。

    ![服务器名称和资源组](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. 记下 SQL 池名称，即数据库名称。 同时记下服务器名称和资源组。
1. 在 PowerShell cmdlet 中请仅使用服务器名称的第一个组成部分。 在上图中，完整的服务器名称为 sqlpoolservername.database.windows.net。 我们在 PowerShell cmdlet 中使用 **sqlpoolservername** 作为服务器名称。

## <a name="pause-compute"></a>暂停计算

为了节省成本，可以按需暂停和恢复计算资源。 例如，如果晚上和周末不使用数据库，那么可以在这些时间暂停数据库的使用，然后在白天时恢复使用。

>[!NOTE]
>数据库暂停时，不对计算资源进行收费。 但是，仍将收取存储费用。

若要暂停数据库，请使用 [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet。 以下示例暂停 **sqlpoolservername** 服务器上托管的 SQL 池 **mySampleDataWarehouse**。 该服务器位于名为 myResourceGroup 的 Azure 资源组中  。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

以下示例将数据库检索到 $database 对象中。 然后，它通过管道将该对象传递给 [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 结果存储在对象 resultDatabase 中。 最后一个命令显示结果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>恢复计算

若要启动数据库，请使用 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet。 以下示例启动 **sqlpoolservername** 服务器上托管的 **mySampleDataWarehouse** 数据库。 该服务器位于名为 myResourceGroup 的 Azure 资源组中  。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

下一示例将数据库检索到 $database 对象中。 然后，它通过管道将对象传递给 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)，并将结果存储在 $resultDatabase 中。 最后一个命令显示结果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>检查 SQL 池操作的状态

若要检查 SQL 池的状态，请使用 [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet。

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>清理资源

针对 SQL 池的数据仓库单元数和存储的数据收费。 这些计算和存储资源是分开计费的。

- 要将数据保存在存储中，请暂停计算。
- 若要避免将来产生费用，可以删除该 SQL 池。

请按照下列步骤按需清理资源。

1. 登录到 [Azure 门户](https://portal.azure.com)并单击该 SQL 池。

    ![清理资源](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 要暂停计算，请单击“暂停”  按钮。 暂停 SQL 池后，会看到“启动”按钮。   要恢复计算，请单击“启动”  。

3. 若要删除 SQL 池以免产生计算或存储费用，请单击“删除”。 

4. 若要删除创建的 SQL 服务器，请依次单击“sqlpoolservername.database.windows.net”、“删除”。    请谨慎执行此删除操作，因为删除服务器的同时也会删除分配给该服务器的所有数据库。

5. 要删除资源组，请单击“myResourceGroup”  ，然后单击“删除资源组”  。

## <a name="next-steps"></a>后续步骤

若要详细了解 SQL 池，请继续阅读[将数据加载到 SQL 池](load-data-from-azure-blob-storage-using-polybase.md)一文。 有关管理计算功能的其他信息，请参阅[管理计算概述](sql-data-warehouse-manage-compute-overview.md)一文。
