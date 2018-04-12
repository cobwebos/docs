---
title: Azure SQL 数据库数据相关路由 | Microsoft Docs
description: 如何将 .NET 应用中的 ShardMapManager 类用于数据相关路由（Azure SQL 数据库中共享数据库的一项功能）
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 8a9b0b2c0541504954c4a1352180552cc650a31f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="data-dependent-routing"></a>数据依赖型路由
**数据依赖型路由**是使用查询中的数据将请求路由到相应数据库的功能。 在使用分片数据库时，它是一种基础模式。 请求上下文也可用于路由请求，尤其是当分片键不是查询的一部分时。 将应用程序中使用数据依赖路由的每个特定查询和事务限制为针对每个请求访问单一数据库。 对于 Azure SQL 数据库弹性工具，这种路由是通过 ShardMapManager（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)）类实现的。

应用程序无需在分片环境中跟踪与不同的数据片相关联的各种连接字符串或数据库位置。 但是，[分片映射管理器](sql-database-elastic-scale-shard-map-management.md)在需要时基于分片映射中的数据以及作为应用程序请求目标的分片键值，将开放连接分发给正确的数据库。 该键通常为 customer_id、tenant_id、date_key 或一些作为数据库请求的基础参数的其他特定标识符。 

有关详细信息，请参阅[使用数据依赖型路由缩放 SQL Server](https://technet.microsoft.com/library/cc966448.aspx)。

## <a name="download-the-client-library"></a>下载客户端库
若要下载：
* Java 版本的库，请参阅 [Maven 中央存储库](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools)。
* .NET 版本的库，请参阅 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>在数据相关的路由应用程序中使用 ShardMapManager
应用程序应使用工厂调用 GetSQLShardMapManager（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)）在初始化期间实例化 ShardMapManager。 在本示例中，将同时初始化 **ShardMapManager** 以及它所包含的特定 **ShardMap**。 本例演示 GetSqlShardMapManager 和 GetRangeShardMap（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getrangeshardmap)、[.NET](https://msdn.microsoft.com/library/azure/dn824173.aspx)）方法。

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>尽可能使用最低特权凭据来获取分片映射
如果应用程序本身无法处理分片映射，在工厂方法中使用的凭据应该对**全局分片映射**数据库仅有只读权限。 这些凭据通常与用于到分片映射管理器的开放连接的凭据不同。 另请参阅[用于访问弹性数据库客户端库的凭据](sql-database-elastic-scale-manage-credentials.md)。 

## <a name="call-the-openconnectionforkey-method"></a>调用 OpenConnectionForKey 方法
ShardMap.OpenConnectionForKey 方法（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)）将返回连接，该连接可随时基于 key 参数的值将命令分发到相应的数据库中。 **ShardMapManager** 将分片信息缓存在应用程序中，因此这些请求通常不会针对**全局分片映射**数据库调用数据库查找。 

```Java
// Syntax: 
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax: 
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```
* **key** 参数在分片映射中用作查找键，以确定该请求的相应数据库。 
* **connectionString** 用于仅传递所需连接的用户凭据。 此 connectionString 中不包含数据库名称或服务器名称，因为该方法使用 ShardMap 确定数据库和服务器。 
* 在分片映射可能会发生更改并且行可能会由于拆分或合并操作而移到其他数据库的环境中，connectionOptions（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)）应设置为 ConnectionOptions.Validate。 在将连接传送到应用程序之前，这涉及在目标数据库上简要查询局部分片映射（而不是全局分片映射）。 

如果针对局部分片映射进行的验证失败（指示缓存不正确），分片映射管理器会查询全局分片映射来获取新的正确值以供查找、更新缓存以及获取和返回相应的数据库连接。 

仅在应用程序处于联机状态时没有按预期进行分片映射更改的情况下，才使用 **ConnectionOptions.None**。 在该情况下，可以假设缓存的值始终正确，并且可以安全地跳过对目标数据库的额外双向验证调用。 这可以减少数据库流量。 还可以通过配置文件中的某个值设置 **connectionOptions**，以指示在此期间是否按预期进行了分片更改。  

本示例使用整数键 **CustomerID** 的值，并使用名为 **customerShardMap** 的 **ShardMap** 对象。  

```Java 
int customerId = 12345; 
int productId = 4321; 
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

// Connect to the shard for that customer ID. No need to call a SqlConnection 
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
{ 
    // Execute a simple command. 
    SqlCommand cmd = conn.CreateCommand(); 
    cmd.CommandText = @"UPDATE Sales.Customer 
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID"; 

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
    cmd.ExecuteNonQuery(); 
}  
```

**OpenConnectionForKey** 方法返回与正确数据库建立的、新的且已打开的连接。 此方法中所采用的连接仍然可以充分利用连接池。 

如果应用程序使用异步编程，也可以使用 OpenConnectionForKeyAsync 方法（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkeyasync)、[.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)）。

## <a name="integrating-with-transient-fault-handling"></a>与暂时性故障处理集成
在云中开发数据访问应用程序的最佳做法是，确保暂时性故障是由应用引起的，并且确保在引发错误之前重试几次这些操作。 暂时性故障处理（[Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling)、[.NET](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx)）中讨论了云应用程序的暂时性故障处理。 

暂时性故障处理在本质上可以与数据依赖路由模式共存。 关键需求是重试整个数据访问请求，包括已获取数据依赖型路由连接的 **using** 块。 可以将上述示例重写为以下内容。 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>示例 - 数据相关的路由与暂时性故障处理
```Java 
int customerId = 12345; 
int productId = 4321; 
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; 
{
    // Connect to the shard for a customer ID. 
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 

        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 

        Console.WriteLine("Update completed"); 
    } 
}); 
```

生成弹性数据库示例应用程序时，会自动下载需要实现暂时性故障处理的程序包。 

## <a name="transactional-consistency"></a>事务一致性
确保分片的所有局部操作的事务属性。 例如，通过数据依赖路由提交的事务会在目标分片范围内执行以供连接。 此时，没有提供用于将多个连接包含在一个事务中的功能，因此对于在分片上执行的操作，没有事务保证。

## <a name="next-steps"></a>后续步骤
若要分离分片或重新附加分片，请参阅[使用 RecoveryManager 类解决分片映射问题](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

