---
title: "PowerShell：创建和管理单一 Azure SQL 数据库 | Microsoft 文档"
description: "有关如何使用 Azure 门户创建和管理单一 Azure SQL 数据库的快速参考"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>使用 PowerShell 创建和管理单一 Azure SQL 数据库

可以使用 [Azure 门户](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 创建和管理单一 Azure SQL 数据库。 本主题说明如何使用 PowerShell。 有关 Azure 门户，请参阅[使用 Azure 门户创建和管理单一数据库](sql-database-manage-single-databases-powershell.md)。 有关 Transact-SQL，请参阅[使用 Transact-SQL 创建和管理单一数据库](sql-database-manage-single-databases-tsql.md)。 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>使用 PowerShell 创建 Azure SQL 数据库

若要创建 SQL 数据库，请使用 [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase) cmdlet。 资源组和服务器必须已存在于订阅中。 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

> [!TIP]
> 如需脚本示例，请参阅[创建 SQL 数据库 PowerShell 脚本](sql-database-get-started-powershell.md)。
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>更改单一数据库的服务层和性能级别

运行 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) cmdlet 并将 **-RequestedServiceObjectiveName** 设置为所需定价层的性能级别；例如 *S0*、*S1*、*S2*、*S3*、*P1*、*P2*...

将 ```{variables}``` 替换为自己的值（不要包含大括号）。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>后续步骤
* 有关管理工具的概述，请参阅[管理工具概述](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 门户执行管理任务，请参阅[使用 Azure 门户管理 Azure SQL 数据库](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 执行管理任务，请参阅[使用 PowerShell 管理 Azure SQL 数据库](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 执行管理任务，请参阅 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 有关 SQL 数据库服务的信息，请参阅[什么是 SQL 数据库](sql-database-technical-overview.md)。 
* 有关 Azure 数据库服务器和数据库功能的信息，请参阅[功能](sql-database-features.md)。



<!--HONumber=Feb17_HO2-->


