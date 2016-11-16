---
title: "使用 TSQL 创建 SQL 数据仓库 | Microsoft Docs"
description: "了解如何使用 TSQL 创建 Azure SQL 数据仓库"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 443e74834a7181560e812ce31db460ef2c4ff911


---
# <a name="create-a-sql-data-warehouse-database-by-using-transactsql-tsql"></a>使用 Transact-SQL (TSQL) 创建 SQL 数据仓库数据库
> [!div class="op_single_selector"]
> * [Azure 门户](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

本文将介绍如何使用 T-SQL 创建 SQL 数据仓库。

## <a name="prerequisites"></a>先决条件
若要开始，您需要： 

* **Azure 帐户**：访问 [Azure 免费试用版][Azure Free Trial]或者 [MSDN Azure 信用额度][MSDN Azure Credits]来创建帐户。
* **Azure SQL 服务器**：有关详细信息，请参阅[使用 Azure 门户创建 Azure SQL 数据库逻辑服务器][Create an Azure SQL Database logical server with the Azure Portal]或[使用 PowerShell 创建 Azure SQL 数据库逻辑服务器][Create an Azure SQL Database logical server with PowerShell]。
* **资源组**：可使用同一资源组作为 Azure SQL Server，或参阅[如何创建资源组][how to create a resource group]。
* **执行 T-SQL 的环境：可以使用 **Visual Studio[][Installing Visual Studio and SSDT]、[sqlcmd][sqlcmd] 或 [SSMS][SSMS] 来执行 T-SQL。

> [!NOTE]
> 创建 SQL 数据仓库可能会导致新的计费服务。  有关定价的详细信息，请参阅 [SQL 数据仓库定价][SQL Data Warehouse pricing]。
> 
> 

## <a name="create-a-database-with-visual-studio"></a>使用 Visual Studio 创建数据库
如果不熟悉 Visual Studio，请参阅文章[查询 Azure SQL 数据仓库 (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]。  若要开始操作，请在 Visual Studio 中打开 SQL Server 对象资源管理器，并连接到要托管 SQL 数据仓库数据库的服务器。  连接后，可针对 **master** 数据库运行以下 SQL 命令来创建 SQL 数据仓库。  此命令可创建服务目标为 DW400 的数据库 MySqlDwDb，并允许此数据库增长到大小上限 10 TB。

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>使用 sqlcmd 创建数据库
也可以在命令提示符处运行以下命令，以使用 sqlcmd 运行相同的命令。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

在不指定的情况下，默认排序规则是 COLLATE SQL_Latin1_General_CP1_CI_AS。  `MAXSIZE` 可介于 250 GB 和 240 TB 之间。  `SERVICE_OBJECTIVE` 可以介于 DW100 与 DW2000 [DWU][DWU] 之间。  有关所有有效值的列表，请参阅 [CREATE DATABASE][CREATE DATABASE] 的 MSDN 文档。  MAXSIZE 和 SERVICE_OBJECTIVE 可通过 [ALTER DATABASE][ALTER DATABASE] T-SQL 命令进行更改。  数据库的排序规则在创建后不能更改。   更改 SERVICE_OBJECTIVE 时应谨慎，因为更改 DWU 会导致服务重新启动而取消所有正在进行的查询。  更改 MAXSIZE 不会重启服务，因为这只是简单的元数据操作。

## <a name="next-steps"></a>后续步骤
完成 SQL 数据仓库预配之后，可以[加载示例数据][loading sample data]、或了解如何[开发][develop]、[加载][load]或[迁移][migrate]数据。

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[如何从 Azure 门户创建 SQL 数据仓库]: sql-data-warehouse-get-started-provision.md
[查询 Azure SQL 数据仓库 (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[迁移
[开发]: sql-data-warehouse-overview-develop.md
[加载]: sql-data-warehouse-overview-load.md
[加载示例数据]: sql-data-warehouse-load-sample-databases.md
[使用 Azure 门户创建 Azure SQL 数据库逻辑服务器]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[使用 PowerShell 创建 Azure SQL 数据库逻辑服务器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何创建资源组]: ../resource-group-template-deploy-portal.md#create-resource-group
[安装 Visual Studio 和 SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL 数据仓库定价]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免费试用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 信用额度]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


