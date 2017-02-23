---
title: "SSMS：将数据库导出到 BACPAC 文件 (Azure) | Microsoft Docs"
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
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 将 Azure SQL 数据库或 SQL Server 数据库导出到 BACPAC 文件 | Microsoft Azure        
        
本文说明如何使用 SQL Server Management Studio 中的“导出数据层应用程序”向导将 Azure SQL 数据库或 SQL Server 数据库导出到 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 文件。 有关导出到 BACPAC 文件的概述，请参阅[导出到 BACPAC](sql-database-export.md)。    
        
1. 确认你安装了最新版本的 SQL Server Management Studio。 Management Studio 的新版本将每月更新一次，以与 Azure 门户的更新保持同步。        
           
   > [!IMPORTANT]        
   > 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [最新版本的 SSMS](https://msdn.microsoft.com/library/mt238290.aspx)。        
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
* [最新版本的 SSMS](https://msdn.microsoft.com/library/mt238290.aspx)
* 若要了解如何将 BACPAC 导入到 SQL 数据库，请参阅[将 BACPCAC 导入 SQL 数据库](https://msdn.microsoft.com/library/hh710052.aspx)
* 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
* 若要了解 Azure SQL 数据库备份的长期备份保留 - 作为导出数据库进行存档的替代方法，请参阅[长期备份保留](sql-database-long-term-retention.md)


    



<!--HONumber=Feb17_HO2-->


