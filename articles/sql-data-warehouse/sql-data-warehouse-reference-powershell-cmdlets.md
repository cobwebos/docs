---
title: "适用于 Azure SQL 数据仓库的 PowerShell cmdlet"
description: "了解 Azure SQL 数据仓库的最常用 PowerShell cmdlet，包括如何暂停和恢复数据库。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 6f0d5772-f05f-4cc8-9749-4adb153dfd50
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e993e8c0cb7b7143f9e7be5bd413f42742666fa8


---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>适用于 SQL 数据仓库的 PowerShell cmdlet 和 REST API
可以使用 Azure PowerShell cmdlet 或 REST API 来管理许多 SQL 数据仓库管理任务。  下面是如何使用 PowerShell 命令自动执行 SQL 数据仓库中的常见任务的一些示例。  如需一些好的 REST 示例，请参阅[使用 REST 管理可伸缩性][使用 REST 管理可伸缩性]一文。

> [!NOTE]
> 若要对 SQL 数据仓库使用 Azure PowerShell，需要安装 Azure PowerShell 1.0.3 或更高版本。  可以通过运行 **Get-Module -ListAvailable -Name Azure** 来检查版本。  可通过 [Microsoft Web 平台安装程序][Microsoft Web 平台安装程序]安装最新版本。  有关安装最新版本的详细信息，请参阅 [如何安装和配置 Azure PowerShell][如何安装和配置 Azure PowerShell]。
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet 入门
1. 打开 Windows PowerShell。
2. 在 PowerShell 提示符下，运行以下命令以登录到 Azure Resource Manager，然后选择你的订阅。
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>暂停 SQL 数据仓库示例
暂停名为“Server01”的服务器上托管的名为“Database02”的数据库。  该服务器位于名为“ResourceGroup1”的 Azure 资源组中。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
作为一种变体，此示例可通过管道将检索到的对象传递给 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]。  因此将会暂停该数据库。 最后一个命令显示结果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>启动 SQL 数据仓库示例
恢复“Server01”的服务器上托管的“Database02”数据库的运行。 该服务器包含在名为“ResourceGroup1”的资源组中。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

作为一种变体，此示例可从“ResourceGroup1”资源组包含的“Server01”服务器中检索“Database02”数据库。 它通过管道将检索到的对象传递给 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> 注意，如果服务器是 foo.database.windows.net，请使用“foo”作为 Powershell cmdlet 中的 -ServerName。
> 
> 

## <a name="frequently-used-powershell-cmdlets"></a>经常使用的 PowerShell cmdlet
以下 PowerShell cmdlet 经常与 Azure SQL 数据仓库配合使用。

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>后续步骤
有关更多的 PowerShell 示例，请参阅：

* [使用 PowerShell 创建 SQL 数据仓库][使用 PowerShell 创建 SQL 数据仓库]
* [数据库还原][数据库还原]

如需可以使用 PowerShell 自动执行的所有任务的列表，请参阅 [Azure SQL 数据库 Cmdlet][Azure SQL 数据库 Cmdlet]。  如需可以使用 REST 自动执行的任务的列表，请参阅 [Azure SQL 数据库的操作][Azure SQL 数据库的操作]。

<!--Image references-->

<!--Article references-->
[如何安装和配置 Azure PowerShell]: ../powershell-install-configure.md
[使用 PowerShell 创建 SQL 数据仓库]: ./sql-data-warehouse-get-started-provision-powershell.md
[数据库还原]: ./sql-data-warehouse-restore-database-powershell.md
[使用 REST 管理可伸缩性]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL 数据库 Cmdlet]: https://msdn.microsoft.com/library/mt574084.aspx
[Azure SQL 数据库的操作]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web 平台安装程序]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


