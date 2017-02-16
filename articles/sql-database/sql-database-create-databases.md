---
title: "创建 Azure SQL 数据库 | Microsoft Docs"
description: "有关如何使用 Azure 门户、PowerShell 和 Transact-SQL 创建 Azure SQL 数据库的快速参考。"
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
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: aca33871d83a34769fd2b09be4b382c872a65f0a


---
# <a name="create-an-azure-sql-database"></a>创建 Azure SQL 数据库

可以使用 [Azure 门户](https://portal.azure.com/)、PowerShell、Transact-SQL、REST API 或 C# 创建 Azure SQL 数据库。 

## <a name="create-an-azure-sql-database-using-the-azure-portal"></a>使用 Azure 门户创建 Azure SQL 数据库

1. 在 [Azure 门户](https://portal.azure.com/)中打开“SQL 数据库”边栏选项卡。 

    ![SQL 数据库](./media/sql-database-get-started/sql-databases.png)
2. 在“SQL 数据库”边栏选项卡中，单击“添加”。

    ![添加 SQL 数据库](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> 有关使用 Azure 门户和 SQL Server Management Studio 的入门教程，请参阅[通过 Azure 门户和 SQL Server Management Studio 开始使用 Azure SQL 数据库服务器、数据库和防火墙规则](sql-database-get-started.md)。
>

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

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中使用 Transact-SQL 创建 Azure SQL 数据库

若要在 SQL Server Management Studio 中使用 Transact-SQL 创建 SQL 数据库：

1. 在 SQL Server Management Studio 中使用服务器级别主体登录名或是 **dbmanager** 角色成员的登录名连接到 Azure 数据库服务器。 有关登录名的详细信息，请参阅[管理登录名](sql-database-manage-logins.md)。
2. 在对象资源管理器中，打开“数据库”节点、展开“系统数据库”文件夹、右键单击“master”，然后单击“新建查询”。
3. 使用 **CREATE DATABASE** 语句可创建数据库。 有关详细信息，请参阅 [CREATE DATABASE（SQL 数据库）](https://msdn.microsoft.com/library/dn268335.aspx)。 以下语句将创建名为 **myTestDB** 的数据库，并指定它是默认大小上限为 250 GB 的“标准 S0 版本”数据库。
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. 单击“执行”运行查询。
5. 在对象资源管理器中，右键单击“数据库”节点，然后单击“刷新”，以查看对象资源管理器中的新数据库。 

> [!TIP]
> 有关使用 Azure 门户和 SQL Server Management Studio 的入门教程，请参阅[通过 Azure 门户和 SQL Server Management Studio 开始使用 Azure SQL 数据库服务器、数据库和防火墙规则](sql-database-get-started.md)。
>

## <a name="additional-resources"></a>其他资源
* 有关管理工具的概述，请参阅[管理工具概述](sql-database-manage-overview.md)。
* 若要了解如何使用 Azure 门户执行管理任务，请参阅[使用 Azure 门户管理 Azure SQL 数据库](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 执行管理任务，请参阅[使用 PowerShell 管理 Azure SQL 数据库](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 执行管理任务，请参阅 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 有关 SQL 数据库服务的信息，请参阅[什么是 SQL 数据库](sql-database-technical-overview.md)。 
* 有关 Azure 数据库服务器和数据库功能的信息，请参阅[功能](sql-database-features.md)。



<!--HONumber=Dec16_HO3-->


