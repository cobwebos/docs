---
title: "使用 PowerShell 创建 SQL 数据仓库 | Microsoft Docs"
description: "使用 PowerShell 创建 SQL 数据仓库"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>使用 PowerShell 创建 SQL 数据仓库
> [!div class="op_single_selector"]
> * [Azure 门户](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

本文将介绍如何使用 PowerShell 创建 SQL 数据仓库。

## <a name="prerequisites"></a>先决条件
若要开始，您需要：

* **Azure 帐户**：访问 [Azure 免费试用版][Azure Free Trial]或者 [MSDN Azure 信用额度][MSDN Azure Credits]，以创建帐户。
* **Azure SQL 服务器**：有关详细信息，请参阅[使用 Azure 门户创建 Azure SQL 数据库逻辑服务器][Create an Azure SQL Database logical server with the Azure Portal]或[使用 PowerShell 创建 Azure SQL 数据库逻辑服务器][Create an Azure SQL Database logical server with PowerShell]。
* **资源组**：可使用同一资源组作为 Azure SQL Server，或参阅[如何创建资源组][how to create a resource group]。
* **PowerShell 1.0.3 或更高版本**：可以通过运行 **Get-Module -ListAvailable -Name Azure** 来检查版本。  可通过 [Microsoft Web 平台安装程序][Microsoft Web Platform Installer]安装最新版本。  有关安装最新版本的详细信息，请参阅 [如何安装和配置 Azure PowerShell][How to install and configure Azure PowerShell]。

> [!NOTE]
> 创建 SQL 数据仓库可能会导致新的计费服务。  有关定价的详细信息，请参阅 [SQL 数据仓库定价][SQL Data Warehouse pricing]。
> 
> 

## <a name="create-a-sql-data-warehouse"></a>创建 SQL 数据仓库
1. 打开 Windows PowerShell。
2. 运行此 cmdlet 以登录到 Azure Resource Manager 中。
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. 选择要用于当前会话的订阅。
   
    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. 创建数据库。 此示例将在名为“mywesteuroperesgp1”的资源组中的名为“sqldwserver1”的服务器中创建一个名为“mynewsqldw”且服务目标级别为“DW400”的数据库。
   
   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

所需的参数有：

* **RequestedServiceObjectiveName**：请求的 [DWU][DWU] 的数量。  支持的值包括：DW100、DW200、DW300、DW400、DW500、DW600、DW1000、DW1200、DW1500、DW2000、DW3000 和 DW6000。
* **DatabaseName**：要创建的 SQL 数据仓库的名称。
* **ServerName**：用于创建过程的服务器名称（必须是 V12）。
* **ResourceGroupName**：要使用的资源组。  若要查找订阅中可用的资源，请使用 Get-AzureResource。
* **Edition**：必须是“DataWarehouse”才能创建 SQL 数据仓库。

可选参数有：

* **CollationName**：在不指定的情况下，默认排序规则是 SQL_Latin1_General_CP1_CI_AS。  在数据库上不能更改排序规则。
* **MaxSizeBytes**：数据库的默认最大大小为 10 GB。

有关参数选项的更多详细信息，请参阅 [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] 和[创建数据库（Azure SQL 数据仓库）][Create Database (Azure SQL Data Warehouse)]。

## <a name="next-steps"></a>后续步骤
完成 SQL 数据仓库预配之后，可以尝试[加载示例数据][loading sample data]、或了解如何[开发][develop]、[加载][load]或[迁移][migrate]数据。

如果有兴趣进一步了解如何以编程方式管理 SQL 数据仓库，请查看有关如何使用 [PowerShell cmdlet 和 REST API][PowerShell cmdlets and REST APIs] 的文章。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[迁移
[开发]: ./sql-data-warehouse-overview-develop.md
[加载]: ./sql-data-warehouse-load-with-bcp.md
[加载示例数据]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlet 和 REST API]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[防火墙规则]: ../sql-database-configure-firewall-settings.md

[如何安装和配置 Azure PowerShell]: ../powershell-install-configure.md
[如何从 Azure 门户创建  SQL 数据仓库]: ./sql-data-warehouse-get-started-provision.md
[使用 Azure 门户创建 Azure SQL 数据库逻辑服务器]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[使用 PowerShell 创建 Azure SQL 数据库逻辑服务器]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[如何创建资源组]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[创建数据库（Azure SQL 数据仓库）]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web 平台安装程序]: https://aka.ms/webpi-azps
[SQL 数据仓库定价]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 免费试用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure 信用额度]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


