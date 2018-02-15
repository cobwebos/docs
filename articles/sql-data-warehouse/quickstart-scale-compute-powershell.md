---
title: "快速入门：在 Azure SQL 数据仓库中横向扩展计算资源 - PowerShell | Microsoft Docs"
description: "若要向外扩展的 Powershell 任务通过调整数据仓库单位计算资源。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: ff6c14ced42385d81f3c42b4b4bf1fd06464c0d1
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>快速入门：使用 PowerShell 在 Azure SQL 数据仓库中缩放计算资源

使用 PowerShell 在 Azure SQL 数据仓库中缩放计算资源 横向扩展计算为提高性能或缩放重新计算以节约成本。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

本教程需要 Azure PowerShell 模块版本 5.1.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找当前版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps.md)。 

## <a name="before-you-begin"></a>开始之前

本快速入门教程假定已有可缩放的 SQL 数据仓库。 如果需要创建一个 SQL 数据仓库，可使用[创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的数据仓库。 

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Add-AzureRmAccount
```

若要查看正在使用的订阅，请运行 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription)。

```powershell
Get-AzureRmSubscription
```

如果需要使用与默认订阅不同的订阅，请运行 [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription)。

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>查找数据仓库信息

查找计划暂停和恢复的数据仓库的数据库名称、服务器名称和资源组。 

按照以下步骤查找数据仓库的位置信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧页面中，单击“SQL 数据库”。
3. 从“SQL 数据库”页中选择“mySampleDataWarehouse”。 此操作打开数据仓库。 

    ![服务器名称和资源组](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. 记下将用作数据库名称的数据仓库名称。 同时记下服务器名称和资源组。 执行暂停和恢复命令时会用到。
5. 如果服务器是 foo.database.windows.net，请在 PowerShell cmdlet 中仅使用第一部分作为服务器名称。 在上图中，完整的服务器名称为 newserver-20171113.database.windows.net。 我们将使用 newserver-20171113 作为 PowerShell cmdlet 中的服务器名称。

## <a name="scale-compute"></a>缩放计算

在 SQL 数据仓库中，可以通过调整数据仓库单位来增加或减少计算资源。 [创建和 Connect - 门户](create-data-warehouse-portal.md)创建 **mySampleDataWarehouse** 并初始化 400 DWU。 以下步骤调整为 DWU **mySampleDataWarehouse**。

若要更改数据仓库单位，请使用 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell cmdlet。 下面的示例将 DW300 到数据库的设置数据仓库单位 **mySampleDataWarehouse** 资源组中承载 **myResourceGroup** 服务器上 **mynewserver 20171113**。

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>检查数据库状态

若要查看数据仓库的当前状态，使用 [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) PowerShell cmdlet。 这将得到的状态**mySampleDataWarehouse**资源组的数据库**myResourceGroup**和服务器**mynewserver 20171113.database.windows.net**。

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

这会导致以下类似的结果

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

然后，可以在其中进行检查，了解查看数据库的状态。 在本例中，可以看到此数据库处于联机状态。  运行此命令后，应收到“联机”、“正在暂停”、“正在恢复”、“正在缩放”和“已暂停”等状态值。

## <a name="next-steps"></a>后续步骤
现在已暂停并恢复了数据仓库的计算。 若要了解有关 Azure SQL 数据仓库的详细信息，请继续有关加载数据的教程。

> [!div class="nextstepaction"]
>[将数据加载到 SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)
