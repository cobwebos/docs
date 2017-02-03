---
title: "使用事务复制迁移到 SQL 数据库 | Microsoft 文档"
description: "Microsoft Azure SQL 数据库, 数据库迁移, 导入数据库, 事务复制"
services: sql-database
documentationcenter: 
author: jognanay
manager: jhubbard
editor: 
ms.assetid: eebdd725-833d-4151-9b2b-a0303f39e30f
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 12/09/2016
ms.author: carlrab; jognanay;
translationtype: Human Translation
ms.sourcegitcommit: 8baeadbf7ef24e492c4115745c0d384e4f526188
ms.openlocfilehash: 8380925a56d39bd53fe737bed539b862cc835fad


---
# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>使用事务复制将 SQL Server 数据库迁移到 Azure SQL 数据库
> [!div class="op_single_selector"]
> * [SSMS 迁移向导](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
> * [导出到 BACPAC 文件](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [从 BACPAC 文件导入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [事务复制](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
> 
> 

在本文中，你将了解如何使用 SQL Server 事务复制以最短的停机时间将兼容的 SQL Server 数据库迁移到 Azure SQL 数据库。

## <a name="understanding-the-transactional-replication-architecture"></a>了解事务复制的体系结构
如果在发生迁移时你无法承受从生产中删除 SQL Server 数据库的后果，可以使用 SQL Server 事务复制作为你的迁移解决方案。 若要使用此解决方案，请将 Azure SQL 数据库配置为你想要迁移的本地 SQL Server 实例的订阅服务器。 在新的事务不断发生时，本地事务复制分发器将对要同步的本地数据库（发布服务器）中的数据进行同步。 

还可以使用事务复制来迁移本地数据库的子集。 复制到 Azure SQL 数据库的发布可以限制为复制的数据库中表的子集。 对于所复制的每一个表，可以将数据限制为行的子集和/或列的子集。

使用事务复制时，对数据或架构所做的所有更改都会显示在 Azure SQL 数据库中。 同步完成后，如果已准备好进行迁移，则可更改应用程序的连接字符串，使其指向 Azure SQL 数据库。 一旦事务复制清空保留在本地数据库中的任何更改，并且所有应用程序都指向 Azure DB，即可卸载事务复制。 Azure SQL 数据库现在是用户的生产系统。

 ![SeedCloudTR 示意图](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="how-transactional-replication-works"></a>事务复制工作原理

事务复制涉及 3 个主要组件。 它们是发布服务器、分发服务器和订阅服务器。 这些组件可以一起执行复制。 分发服务器负责控制在服务器之间移动数据的过程。 经设置后，分发 SQL 将创建分发数据库。 每个发布服务器都需要绑定到分发数据库。 分发数据库在每个复制过程中都保留每个关联的发布和数据的元数据。 对于事务复制，它将保留需要在订阅服务器中执行的所有事务。

发布服务器是所有迁移数据起始的数据库。 在发布服务器中有很多发布。 这些发布包含映射到所有表的文章和需要复制的数据。 可复制所有还是部分数据库，取决于定义复制和文章的方式。 

在复制中，订阅服务器是从发布接收所有数据和事务的服务器。 每个发布均有许多复制。

## <a name="transactional-replication-requirements"></a>事务复制要求
[转到此链接，查看已更新的要求列表。](https://msdn.microsoft.com/en-US/library/mt589530.aspx)
> [!IMPORTANT]
> 使用最新版本的 SQL Server Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 较旧版本的 SQL Server Management Studio 不能将 SQL 数据库设置为订阅服务器。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>使用事务复制迁移到 SQL 数据库的工作流

1. 设置分发
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. 创建发布
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. 创建订阅
   -  [使用 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [使用 Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

## <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>迁移到 SQL 数据库的一些提示和差异

1. 使用本地分发服务器 
   - 这将对服务器产生性能影响。 
   - 如果性能影响无法接受，可使用其他服务器，但这将增加管理的复杂性。
2. 选择快照文件夹时，请确保所选文件夹足以保留想要复制的每张表的 BCP。 
3. 请注意，创建快照将锁定关联的表，直至完成为止，在计划快照时应记住这一点。 
4. Azure SQL 数据库仅支持推送订阅
   - 仅可从本地数据库一端添加订阅服务器。

## <a name="next-steps"></a>后续步骤
* [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server 2016](https://www.microsoft.com/sql-server/sql-server-2016)

## <a name="additional-resources"></a>其他资源
* 若要了解有关事务复制的详细信息，请参阅[事务复制](https://msdn.microsoft.com/library/mt589530.aspx)。
* 若要了解整体迁移过程和选项，请参阅[将 SQL Server 数据库迁移到云中的 SQL 数据库](sql-database-cloud-migrate.md)。



<!--HONumber=Dec16_HO2-->


