---
title: "管理 Azure SQL 数据仓库中的计算能力 (PowerShell) | Microsoft 文档"
description: "用于管理计算能力的 PowerShell 任务。 通过调整 DWU 缩放计算资源。 或者，暂停和恢复计算资源来节省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b2b87ed07a26fa30e49a19e34ca8b06b283da9b3
ms.contentlocale: zh-cn
ms.lasthandoff: 03/28/2017


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>管理 Azure SQL 数据仓库中的计算能力 (PowerShell)
> [!div class="op_single_selector"]
> * [概述](sql-data-warehouse-manage-compute-overview.md)
> * [门户](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

## <a name="before-you-begin"></a>开始之前
### <a name="install-the-latest-version-of-azure-powershell"></a>安装最新版本的 Azure PowerShell
> [!NOTE]
> 若要对 SQL 数据仓库使用 Azure PowerShell，需要安装 Azure PowerShell 1.0.3 或更高版本。  若要验证当前版本，请运行命令 **Get-Module -ListAvailable -Name Azure**。 可以从 [Microsoft Web 平台安装程序][Microsoft Web Platform Installer]安装最新版本。  有关详细信息，请参阅[如何安装和配置 Azure PowerShell][How to install and configure Azure PowerShell]。
>
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet 入门
开始操作：

1. 打开 Azure PowerShell。
2. 在 PowerShell 提示符下，运行以下命令以登录到 Azure Resource Manager，然后选择你的订阅。

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>缩放计算能力
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要更改 DWU，请使用 [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase] PowerShell cmdlet。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暂停计算
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暂停数据库，请使用 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase] cmdlet。 以下示例将暂停 Server01 服务器上托管的 Database02 数据库。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

> [!NOTE]
> 注意，如果服务器是 foo.database.windows.net，请使用“foo”作为 Powershell cmdlet 中的 -ServerName。
>
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
一种变异，下一个示例将数据库检索到 $database 对象中。 然后，它通过管道将该对象传递给 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]。 结果存储在对象 resultDatabase 中。 最后一个命令显示结果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>恢复计算
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要启动数据库，请使用 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] cmdlet。 以下示例将启动 Server01 服务器上托管的 Database02 数据库。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

一种变异，下一个示例将数据库检索到 $database 对象中。 然后，它通过管道将对象传递给 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]，并将结果存储在 $resultDatabase 中。 最后一个命令显示结果。

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state"></a>检查数据库状态

如以上示例所示，用户可以使用 [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase] cmdlet 获取数据库的信息，从而不仅可以用来检查状态，而且还可以用作参数。 

```powershell
Get-AzureRmSqlDatabase [-ResourceGroupName] <String> [-ServerName] <String> [[-DatabaseName] <String>]
 [-InformationAction <ActionPreference>] [-InformationVariable <String>] [-Confirm] [-WhatIf]
 [<CommonParameters>]
```

这将导致以下类似的结果 

```powershell    
ResourceGroupName             : nytrg
ServerName                    : nytsvr
DatabaseName                  : nytdb
Location                      : West US
DatabaseId                    : 86461aae-8e3d-4ded-9389-ac9d4bc69bbb
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1General_CP1CI_AS
CatalogCollation              :
MaxSizeBytes                  : 32212254720
Status                        : Online
CreationDate                  : 10/26/2016 4:33:14 PM
CurrentServiceObjectiveId     : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
CurrentServiceObjectiveName   : System2
RequestedServiceObjectiveId   : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           : 1/1/0001 12:00:00 AM
```

然后，你可以在其中进行检查，了解查看数据库的状态。 在本例中，你可以看到此数据库处于联机状态。 

运行此命令后，应收到“联机”、“正在暂停”、“正在恢复”、“正在缩放”和“已暂停”等状态值。

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>后续步骤
有关其他管理任务，请参阅[管理概述][Management overview]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Get-AzureRmSqlDatabase]: /powershell/servicemanagement/azure.sqldatabase/v1.6.1/get-azuresqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/

