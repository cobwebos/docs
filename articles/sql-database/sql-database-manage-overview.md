---
title: "概述：SQL 数据库的管理工具 | Microsoft 文档"
description: "比较管理 Azure SQL 数据库的工具和选项"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 915292a191da82af9e24e89460d46dc61a062b18


---
# <a name="overview-management-tools-for-sql-database"></a>概述：SQL 数据库的管理工具
本主题探讨并比较用于管理 Azure SQL 数据库的工具和选项，便于用户挑选适合作业、业务以及自身需求的工具。 选择合适的工具取决于所管理的数据库数量、任务以及执行任务的频率。

## <a name="azure-portal"></a>Azure 门户
[Azure 门户](https://portal.azure.com)是一个基于 Web 的应用程序，可以在其中创建、更新和删除数据库及逻辑服务器并监视数据库活动。 如果刚开始使用 Azure（管理几个数据库）或需要快速执行某些操作，该工具是理想之选。

有关使用该门户的详细信息，请参阅[使用 Azure 门户管理 Azure 数据库](sql-database-manage-portal.md)。

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>Visual Studio 中的 SQL Server Management Studio 和 SQL Server Data Tools
SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 是在计算机上运行的客户端工具，可用于管理和开发云中的数据库。 如果你是熟悉 Visual Studio 或其他集成开发环境 (IDE) 的应用程序开发人员，请[尝试使用 Visual Studio 中的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。 许多数据库管理员都熟悉 SSMS（可用于 Azure SQL 数据库）。 [下载最新版本的 SSMS](https://msdn.microsoft.com/library/mt238290)，在使用 Azure SQL 数据库时始终使用该最新版本。 有关使用 SSMS 管理 Azure SQL 数据库的详细信息，请参阅[使用 SSMS 管理 SQL 数据库](sql-database-manage-azure-ssms.md)。

> [!IMPORTANT]
> 始终使用最新版本的 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) 和 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) 来保持与 Microsoft Azure 和 SQL 数据库的更新同步。
>  

## <a name="powershell"></a>PowerShell
用户可以使用 PowerShell 管理数据库和弹性池，并自动执行 Azure 资源部署。 Microsoft 建议在生产环境中使用此工具来管理大量的数据库并自动进行部署和资源更改。

有关详细信息，请参阅[使用 PowerShell 管理 SQL 数据库](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>弹性数据库工具
使用弹性数据库工具执行如下操作： 

* 使用[弹性作业](sql-database-elastic-jobs-overview.md)针对一组数据库执行 T-SQL 脚本
* 使用[拆分-合并工具](sql-database-elastic-scale-overview-split-and-merge.md)将多租户模型数据库移至单租户模型
* 使用[弹性扩展客户端库](sql-database-elastic-database-client-library.md)管理单租户模型或多租户模型中的数据库。

## <a name="additional-resources"></a>其他资源
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure 自动化](https://azure.microsoft.com/documentation/services/automation/)
* [Azure 计划程序](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Dec16_HO2-->


