---
title: "导入 BACPAC 文件以创建 Azure SQL 数据库 | Microsoft 文档"
description: "通过导入现有 BACPAC 文件创建 Azure SQL 数据库。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ca15248d7ac5232e6308d8188332975f1d21c09b
ms.lasthandoff: 03/14/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>通过 Azure 门户导入 BACPAC 文件以创建 Azure SQL 数据库

本文说明如何使用 [Azure 门户](https://portal.azure.com)通过 BACPAC 文件创建 Azure SQL 数据库。

## <a name="prerequisites"></a>先决条件

若要从 BACPAC 导入 SQL 数据库，需要以下项：

* Azure 订阅。 
* Azure SQL 数据库 V12 服务器。 如果你没有 V12 服务器，可以遵循本文中的以下步骤创建一个：[创建你的第一个 Azure SQL 数据库](sql-database-get-started.md)。
* 数据库的 .bacpac 文件，要将其导入 [Azure 存储帐户（标准）](../storage/storage-create-storage-account.md)blob 容器中。

> [!IMPORTANT]
> 从 Azure Blob 存储导入 BACPAC 时，请使用标准存储。 不支持从高级存储导入 BACPAC。
> 

## <a name="import-the-database"></a>导入数据库
打开 SQL Server 边栏选项卡：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 单击“SQL Server”。
3. 单击要将数据库还原到的服务器。
4. 在 SQL Server 边栏选项卡中，单击“导入数据库”以打开“导入数据库”边栏选项卡：
   
   ![导入数据库][1]
5. 单击“存储”并选择存储帐户、blob 容器和 .bacpac 文件，然后单击“确定”。
   
   ![配置存储选项][2]
6. 为新数据库选择定价层，然后单击“选择”。 不支持直接将数据库导入弹性池，但可先导入为独立数据库，然后将数据库移入池中。
   
   ![选择定价层][3]
7. 对通过 BACPAC 文件创建的数据库输入**数据库名称**。
8. 选择身份验证类型，然后向服务器提供身份验证信息。 
9. 单击“创建”以从 BACPAC 创建数据库。
   
   ![创建数据库][4]

单击“创建”会将导入数据库请求提交到服务。 根据数据库的大小，导入操作可能需要一些时间才能完成。

## <a name="monitor-the-progress-of-the-import-operation"></a>监视导入操作的进度
1. 单击“SQL Server”。
2. 单击要还原到的服务器。
3. 在 SQL Server 边栏选项卡的操作区域，单击“导入/导出历史记录”：
   
   ![导入导出历史记录][5]
   ![导入导出历史记录][6]

4. 若要验证数据库在服务器上是否处于活动状态，请单击“SQL 数据库”并验证新数据库是否为“联机”。

## <a name="next-steps"></a>后续步骤
* 若要了解如何连接到并查询导入的 SQL 数据库，请参阅[使用 SQL Server Management Studio 连接到 SQL 数据库并执行示例 T-SQL 查询](sql-database-connect-query-ssms.md)。
* 有关 SQL Server 客户咨询团队介绍如何使用 BACPAC 文件进行迁移的博客，请参阅 [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)（使用 BACPAC 文件从 SQL Server 迁移到 Azure SQL 数据库）。
* 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

