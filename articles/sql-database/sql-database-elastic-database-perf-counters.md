---
title: "分片映射管理器的性能计数器"
description: "ShardMapManager 类和数据依赖路由性能计数器"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: b090aba0-2e30-454c-96b3-dffa281f539a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: ddove
ms.openlocfilehash: 60bb26fe6833998137ede71b363d5d40479a4c60
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="performance-counters-for-shard-map-manager"></a>分片映射管理器的性能计数器
你可以捕获的性能[分片映射管理器](sql-database-elastic-scale-shard-map-management.md)，尤其是当使用[数据相关的路由](sql-database-elastic-scale-data-dependent-routing.md)。 Microsoft.Azure.SqlDatabase.ElasticScale.Client 类的方法来创建计数器。  

用于跟踪的性能计数器[数据相关的路由](sql-database-elastic-scale-data-dependent-routing.md)操作。 这些计数器都可以访问在性能监视器中，在"弹性数据库:: 分片管理"类别下。

**有关最新版本：**转到[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 另请参阅[升级应用程序以使用最新的弹性数据库客户端库](sql-database-elastic-scale-upgrade-client-library.md)。

## <a name="prerequisites"></a>必备条件
* 若要创建性能类别和计数器，用户必须属于本地**管理员**组承载应用程序的计算机上。  
* 若要创建性能计数器实例和更新的计数器，用户必须是的成员**管理员**或**Performance Monitor Users**组。 

## <a name="create-performance-category-and-counters"></a>创建性能类别和计数器
若要创建的计数器，调用的 CreatePeformanceCategoryAndCounters 方法[ShardMapManagmentFactory 类](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx)。 只有管理员可以执行方法： 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

你还可以使用[这](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283)PowerShell 脚本以执行方法。 该方法将创建以下性能计数器：  

* **缓存映射**： 映射的分片映射缓存数量。
* **DDR 操作数/秒**： 适用于分片映射的数据相关路由操作的速率。 此计数器进行更新时调用[OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)导致到目标分片的成功连接。 
* **映射查找缓存命中数/秒**： 成功的缓存的分片映射中的映射的查找操作的速率。 
* **映射查找缓存未命中数/秒**： 失败的高速缓存的分片映射中的映射的查找操作的速率。
* **映射中添加或更新缓存/秒**： 在要添加的映射或适用于分片映射更新中的缓存的速率。 
* **映射从缓存/秒**： 从该处将映射删除从适用于分片映射缓存的速率。 

为每个进程每个缓存的分片映射创建性能计数器。  

## <a name="notes"></a>注意
以下事件触发的性能计数器创建：  

* 初始化[ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)与[预先加载](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx)，如果 ShardMapManager 包含任何分片映射。 其中包括[GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29)和[TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)方法。
* 成功查找的分片映射 (使用[GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx)， [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx)或[GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx))。 
* 成功创建使用 CreateShardMap() 的分片映射。

在分片映射和映射上执行的所有缓存操作将都更新的性能计数器。 成功删除的分片映射中删除性能计数器实例使用 DeleteShardMap （) reults。  

## <a name="best-practices"></a>最佳实践
* 在创建 ShardMapManager 对象之前，应仅一次执行性能类别和计数器的创建。 每次执行该命令 CreatePerformanceCategoryAndCounters() 清除以前的计数器 （报告的所有实例的数据丢失），并创建新的。  
* 每个进程创建性能计数器实例。 任何应用程序崩溃或删除从缓存的分片映射将导致删除性能计数器实例。  

### <a name="see-also"></a>另请参阅
[弹性数据库功能概述](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

