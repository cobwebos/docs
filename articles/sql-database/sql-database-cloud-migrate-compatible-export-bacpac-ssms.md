---
title: "SSMS：将 SQL Server 数据库导出到 BACPAC 文件 (Azure) | Microsoft Docs"
description: "本文说明如何使用 SQL Server Management Studio 中的“导出数据层应用程序”向导将 SQL Server 数据库导出到 BACPAC 文件。"
keywords: "Microsoft Azure SQL 数据库, 数据库迁移, 导出数据库, 导出 BACPAC 文件,“导出数据层应用程序”向导"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f0238ac34a4a047bd0286a1a87d2427caf0e06fd


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 将 SQL Server 数据库导出到 BACPAC 文件
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

本文说明如何使用 SQL Server Management Studio 中的“导出数据层应用程序”向导将 SQL Server 数据库导出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件。 

1. 确认你安装了最新版本的 SQL Server Management Studio。 Management Studio 的新版本将每月更新一次，以与 Azure 门户的更新保持同步。
   
   > [!IMPORTANT]
   > 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
   > 
   > 
2. 打开 Management Studio 并连接到你在对象资源管理器中的源数据库。
   
    ![通过“任务”菜单导出数据层应用程序](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. 右键单击对象资源管理器中的源数据库，指向“任务”，然后单击“导出数据层应用程序...”
   
    ![通过“任务”菜单导出数据层应用程序](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. 在导出向导中，配置导出以将 BACPAC 文件保存到本地磁盘位置或 Azure blob。 导出的 BACPAC 始终包括完整的数据库架构，默认情况下还包括所有表中的数据。 若要排除部分或全部表中的数据，请使用“高级”选项卡。 例如，你可以选择仅导出引用表的数据，而不是导出所有表中的数据。

    ![导出设置](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > 将 BACPAC 导出到 Azure Blob 存储时，请使用标准存储。 不支持从高级存储导入 BACPAC。
   >
   
## <a name="next-steps"></a>后续步骤
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [使用 SSMS 将 BACPAC 导入 Azure SQL 数据库](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [将 BACPAC 导入 Azure SQL 数据库 SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [将 BACPAC 导入 Azure SQL 数据库 Azure 门户](sql-database-import.md)
* [将 BACPAC 导入 Azure SQL 数据库 PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>其他资源
* [SQL 数据库功能](sql-database-features.md)
* [Transact-SQL 部分支持或不支持的函数](sql-database-transact-sql-information.md)
* [使用 SQL Server 迁移助手迁移非 SQL Server 数据库](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


