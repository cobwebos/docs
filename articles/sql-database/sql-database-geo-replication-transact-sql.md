---
title: "使用 Transact-SQL 为 Azure SQL 数据库配置异地复制 | Microsoft Docs"
description: "使用 Transact-SQL 为 Azure SQL 数据库配置异地复制"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: dad35a2b3beb2b07d5b12afb8a04ba48f8b8ef7e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-transact-sql"></a>使用 Transact-SQL 为 Azure SQL 数据库配置活动异地复制

本文说明如何使用 Transact-SQL 为 Azure SQL 数据库配置活动异地复制。

若要使用 Transact-SQL 启动故障转移，请参阅[使用 Transact-SQL 为 Azure SQL 数据库启动计划内或计划外故障转移](sql-database-geo-replication-failover-transact-sql.md)。

> [!NOTE]
> 将活动异地复制（可读辅助数据库）用于灾难恢复时，应为应用程序内所有数据库配置故障转移组，以启用自动和透明故障转移。 此功能为预览版。 有关详细信息，请参阅[自动故障转移组和异地复制](sql-database-geo-replication-overview.md)。
> 
> 

若要使用 Transact-SQL 配置活动异地复制，需要以下项：

* Azure 订阅
* 逻辑 Azure SQL 数据库服务器 <MyLocalServer> 和 SQL 数据库 <MyDB> - 要复制的主数据库
* 一个或多个逻辑 Azure SQL 数据库服务器 <MySecondaryServer(n)> - 可充当伙伴服务器（将在其中创建辅助数据库）的逻辑服务器
* 作为主数据库上的 DBManager 的登录名
* 具有要异地复制的本地数据库的 db_ownership
* 作为要将异地复制配置到的伙伴服务器上的 DBManager
* 最新版本的 SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

## <a name="add-secondary-database"></a>添加辅助数据库
可以使用 **ALTER DATABASE** 语句在伙伴服务器上创建异地复制的辅助数据库。 在包含要复制的数据库服务器的 master 数据库上执行此语句。 异地复制数据库（“主数据库”）具备与要复制的数据库相同的名称，并且默认与主数据库具有相同的服务级别。 辅助数据库可以是可读或不可读，并且可以是单一数据库或处于弹性池中。 有关详细信息，请参阅 [ALTER DATABASE (Transact-SQL) ](https://msdn.microsoft.com/library/mt574871.aspx)和[服务层](sql-database-service-tiers.md)。
创建辅助数据库并设定种子之后，数据将开始以异步方式从主数据库复制。 以下步骤说明如何使用 Management Studio 配置异地复制。 提供采用单一数据库形式或是在弹性池中创建不可读和可读辅助副本的步骤。

> [!NOTE]
> 如果指定的伙伴服务器上的数据库的名称与主数据库相同，该命令会失败。
> 

### <a name="add-readable-secondary-single-database"></a>添加可读的辅助数据库（单一数据库）
可以使用以下步骤将可读辅助数据库创建为单一数据库。

1. 在 Management Studio 中，连接到你的 Azure SQL 数据库逻辑服务器。
2. 打开“数据库”文件夹、展开“系统数据库”、右键单击“master”，然后单击“新建查询”。
3. 使用以下 **ALTER DATABASE** 语句使本地数据库成为具有辅助服务器上可读辅助数据库的异地复制主数据库。
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. 单击“执行”运行查询。

### <a name="add-readable-secondary-elastic-pool"></a>添加可读的辅助数据库（弹性池）
可以使用以下步骤在弹性池中创建可读的辅助数据库。

1. 在 Management Studio 中，连接到你的 Azure SQL 数据库逻辑服务器。
2. 打开“数据库”文件夹、展开“系统数据库”、右键单击“master”，然后单击“新建查询”。
3. 使用以下 **ALTER DATABASE** 语句使本地数据库成为具有弹性池中辅助服务器上的可读辅助数据库的异地复制主数据库。
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. 单击“执行”运行查询。

## <a name="remove-secondary-database"></a>删除辅助数据库
可以使用 **ALTER DATABASE** 语句永久终止辅助数据库与其主数据库之间的复制关系。 此语句将在主数据库所在的 master 数据库上运行。 终止关系后，辅助数据库将成为常规读写数据库。 如果与辅助数据库的连接断开，命令将会成功，但辅助数据库必须等到连接恢复后才变为可读写。 有关详细信息，请参阅 [ALTER DATABASE (Transact-SQL) ](https://msdn.microsoft.com/library/mt574871.aspx)和[服务层](sql-database-service-tiers.md)。

使用以下步骤从异地复制合作关系中删除异地复制的辅助数据库。

1. 在 Management Studio 中，连接到你的 Azure SQL 数据库逻辑服务器。
2. 打开“数据库”文件夹、展开“系统数据库”、右键单击“master”，然后单击“新建查询”。
3. 使用以下 **ALTER DATABASE** 语句来删除异地复制的辅助数据库。
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. 单击“执行”运行查询。

## <a name="monitor-active-geo-replication-configuration-and-health"></a>监视活动异地复制配置和运行状况

监视任务包括监视异地复制配置和监视数据复制运行状况。  可以使用 master 数据库中的 **sys.dm_geo_replication_links** 动态管理视图返回 Azure SQL 数据库逻辑服务器上每个数据库的所有现有复制链接的相关信息。 此视图针对每个主数据库和辅助数据库之间的复制链接包含一行。 可以使用 **sys.dm_replication_link_status** 动态管理视图针对当前参与复制链接的每个 Azure SQL 数据库返回一行。 这包括主数据库和辅助数据库。 如果给定主数据库有多个连续复制链接，此表将对每种关系包含一行。 将在所有数据库（包括逻辑 master）中创建该视图。 但是，在逻辑 master 中查询此视图会返回空集。 可以使用 **sys.dm_operation_status** 动态管理视图来显示所有数据库操作的状态，包括复制链接的状态。 有关详细信息，请参阅 [sys.geo_replication_links（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt575501.aspx)、[sys.dm_geo_replication_link_status（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt575504.aspx)和 [sys.dm_operation_status（Azure SQL 数据库）](https://msdn.microsoft.com/library/dn270022.aspx)。

使用以下步骤监视活动异地复制合作关系。

1. 在 Management Studio 中，连接到你的 Azure SQL 数据库逻辑服务器。
2. 打开“数据库”文件夹、展开“系统数据库”、右键单击“master”，然后单击“新建查询”。
3. 使用以下语句显示具有异地复制链接的所有数据库。
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].dm_geo_replication_links;
4. 单击“执行”运行查询。
5. 打开“数据库”文件夹、展开“系统数据库”、右键单击“MyDB”，然后单击“新建查询”。
6. 使用以下语句显示复制延迟和辅助数据库 MyDB 上次复制的开始时间。
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. 单击“执行”运行查询。
8. 使用以下语句显示与 MyDB 数据库关联的最近异地复制操作。
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. 单击“执行”运行查询。

## <a name="next-steps"></a>后续步骤
* 若要深入了解活动异地复制，请参阅[活动异地复制](sql-database-geo-replication-overview.md)
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)


