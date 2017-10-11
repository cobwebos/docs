---
title: "Azure SQL 数据仓库的 PowerShell cmdlet"
description: "Azure SQL 数据仓库包括如何暂停和恢复数据库中找到的顶部的 PowerShell cmdlet。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 6f0d5772-f05f-4cc8-9749-4adb153dfd50
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: ce3e11587c2e0cb92923868a4f26d7f59c7ef4ca
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell cmdlet 和 REST Api，可用于 SQL 数据仓库
可以使用 Azure PowerShell cmdlet 或 REST Api 管理许多 SQL 数据仓库管理任务。  下面是如何使用 PowerShell 命令自动执行 SQL 数据仓库中的常见任务的一些示例。  一些良好的 REST 示例，请参阅文章[管理与其余部分的可伸缩性][Manage scalability with REST]。

> [!NOTE]
> 若要与 SQL 数据仓库配合使用 Azure PowerShell，你需要 Azure PowerShell 1.0.3 版本或更高版本。  你可以通过运行检查你的版本**Get-module-ListAvailable-Name Azure**。  可以从安装最新版本[Microsoft Web 平台安装程序][Microsoft Web Platform Installer]。  安装最新版本的详细信息，请参阅[如何安装和配置 Azure PowerShell][How to install and configure Azure PowerShell]。
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>要开始使用 Azure PowerShell cmdlet
1. 打开 Windows PowerShell。
2. 在 PowerShell 提示符下运行以下命令以登录到 Azure 资源管理器并选择你的订阅。
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>暂停 SQL 数据仓库示例
暂停"database02"名为"Server01。"的服务器上托管的数据库  服务器是在 Azure 资源组名为"ResourceGroup1"。

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
变体，此示例通过管道传递检索到的对象[挂起 AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]。  因此，数据库已暂停。 最后一条命令显示的结果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>启动 SQL 数据仓库示例
恢复操作的"database02"名为"Server01。"的服务器上托管的数据库 服务器包含在资源组名为"ResourceGroup1"。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

变体，此示例检索"database02"从包含在名为"ResourceGroup1。"的资源组的"server01"服务器数据库 它通过管道传递检索到的对象[恢复 AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> 请注意，如果你的服务器是 foo.database.windows.net，请使用"foo"作为 PowerShell cmdlet 中的-ServerName。
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>其他支持 PowerShell cmdlet
这些 PowerShell cmdlet 支持与 Azure SQL 数据仓库中。

* [Get AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [新 AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-azurermsqldatabase][Remove-AzureRmSqlDatabase]
* [还原 AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [恢复 AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-azurermsubscription][Select-AzureRmSubscription]
* [Set-azurermsqldatabase][Set-AzureRmSqlDatabase]
* [挂起 AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>后续步骤
有关更多的 PowerShell 示例，请参阅：

* [创建 SQL 数据仓库使用 PowerShell][Create a SQL Data Warehouse using PowerShell]
* [数据库还原][Database restore]

可以使用 PowerShell 自动执行其他任务，请参阅[Azure SQL Database Cmdlet][Azure SQL Database Cmdlets]。 请注意，将不是所有 Azure SQL 数据库 cmdlet 都支持的 Azure SQL 数据仓库。  有关使用 REST 可以自动化任务的列表，请参阅[对 Azure SQL 数据库的操作][Operations for Azure SQL Databases]。

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
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
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
