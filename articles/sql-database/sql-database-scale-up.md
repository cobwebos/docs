---
title: "Azure 门户：更改 Azure SQL 数据库定价层 | Microsoft Docs"
description: "了解如何使用 Azure 门户来更改 Azure SQL 数据库的服务层和性能级别，以及增加和减少 SQL 数据库的资源和定价。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cbd67e88-08d5-40e2-a223-0fb0c718a782
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 88104e0684281f77da497ef4dd608c6a0c64b274


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>使用 Azure 门户更改 SQL 数据库的服务层和性能级别（定价层）
> [!div class="op_single_selector"]
> * [**Azure 门户**](sql-database-scale-up.md)
> * [PowerShell](sql-database-scale-up-powershell.md)> 
> 

服务层和性能级别描述了适用于你的 SQL 数据库的功能和资源，并且可以随着应用程序更改的需要进行更新。 有关详细信息，请参阅“[服务层](sql-database-service-tiers.md)”。

请注意，更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 此窗口会有所不同，但平均起伏少于 4 秒，而在超过 99%的情况下为不足 30 秒。 极少情况下，尤其有很大数量的事务在连接禁用的瞬间正在进行中，则此窗口停留时间可能会更长。  

在大多数情况下，整个向上缩放过程的持续时间小于 5 分钟。 

使用 [Azure SQL 数据库服务层和性能级别](sql-database-service-tiers.md)中的信息为 Azure SQL 数据库确定适当的服务层和性能级别。

* 若要对数据库进行降级，数据库应小于目标服务层允许的最大大小。 
* 在启用了[异地复制](sql-database-geo-replication-overview.md)的情况下升级数据库时，必须先将辅助数据库升级到所需的性能层，然后再升级主数据库。
* 服务层降级时，必须先终止所有异地复制关系。 
* 各服务层提供的还原服务各不相同。 如果进行降级，你可能无法再还原到某个时间点，或者备份保留期变短。 有关详细信息，请参阅 [Azure SQL 数据库备份和还原](sql-database-business-continuity.md)。
* 更改数据库定价层不会更改最大数据库大小。 若要更改数据库最大大小，请使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。
* 所做的更改完成之前不会应用数据库的新属性。

**若要完成本文，需要以下各项：**

* Azure SQL 数据库。 如果你没有 SQL 数据库，请按照[创建你的第一个 Azure SQL 数据库](sql-database-get-started.md)文章中的步骤创建一个。

## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>更改数据库的服务层和性能级别
打开要增加或减少的数据库的 SQL 数据库边栏选项卡：

1. 在 [Azure 门户](https://portal.azure.com)中，依次单击“**更多服务**” > “**SQL 数据库**”。
2. 单击要更改的数据库。
3. 在“**SQL 数据库**”边栏选项卡中单击“**定价层(缩放 DTU)**”：
   
   ![定价层][1]
4. 选择新层，然后单击“**选择**”：
   
   单击“**选择**”将提交更改定价层的缩放请求。 根据数据库的大小，缩放操作可能需要一些时间才能完成（请参阅本文顶部的信息）。
   
   > [!NOTE]
   > 更改数据库定价层不会更改最大数据库大小。 若要更改数据库最大大小，请使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)。
   > 
   > 
   
   ![选择定价层][2]
5. 单击右上角中的通知图标（铃铛）：
   
   ![通知][3]
   
   单击通知文本打开详细信息窗格，可在其中查看请求状态。

## <a name="next-steps"></a>后续步骤
* 使用 [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 或 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx) 更改数据库最大大小。
* [扩大和缩小](sql-database-elastic-scale-get-started.md)
* [使用 SSMS 连接和查询 SQL 数据库](sql-database-connect-query-ssms.md)
* [导出 Azure SQL 数据库](sql-database-export.md)

## <a name="additional-resources"></a>其他资源
* [业务连续性概述](sql-database-business-continuity.md)
* [SQL 数据库文档](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png



<!--HONumber=Jan17_HO1-->


