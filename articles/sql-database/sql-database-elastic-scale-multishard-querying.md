---
title: "查询分片的 Azure SQL 数据库 | Microsoft Docs"
description: "使用弹性数据库客户端库运行跨分片查询。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2016
ms.author: torsteng
ms.openlocfilehash: 67bcb3c7fe33341103f28bc70e8cc2acbb924cae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="multi-shard-querying"></a>多分片查询
## <a name="overview"></a>概述
可以使用[弹性数据库工具](sql-database-elastic-scale-introduction.md)创建分片数据库解决方案。 **多分片查询**用于诸如数据收集/报告等需要跨多个分片运行查询的任务。 （相比之下，[数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)会在单个分片上执行所有操作。） 

1. 使用 [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)、[**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) 或者 [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) 方法获取 [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) 或 [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx)。 请参阅[**构造 ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) 和[**获取 RangeShardMap 或 ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)。
2. 创建 **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** 对象。
3. 创建 **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**。 
4. 设置 T-SQL 命令的 **[CommandText 属性](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)**。
5. 通过调用 **[ExecuteReader 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**来执行命令。
6. 使用 **[MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)** 类来查看结果。 

## <a name="example"></a>示例
以下代码使用给定的 **ShardMap**（名为*myShardMap*）来演示多分片查询的用法。 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                 } 
           } 
    } 


主要区别在于多分片连接的构建。 其中 **SqlConnection** 在单一数据库上进行操作，而 **MultiShardConnection** 将***分片集***合用作其输入。 填充分片映射中的分片集合。 然后，使用 **UNION ALL** 语义组成一个总体结果在分片集合上执行查询。 或者，也可以在命令上使用 **ExecutionOptions** 属性，以将行所源自的分片的名称添加到输出。 

请注意对 **myShardMap.GetShards()** 的调用。 通过此方法可从分片映射中检索所有分片，还可轻松在该分片映射中的所有分片之间运行查询。 对通过调用 **myShardMap.GetShards()** 返回的集合执行 LINQ 查询，以进一步优化用于多分片查询的分片集合。 多分片查询中的当前功能已随部分结果策略一起被设计为供数十至数百种分片使用。

多分片查询目前存在的一个限制是，缺少对需要查询的分片和 shardlet 进行验证。 尽管数据相关路由会在查询时验证给定的分片是否为分片映射的一部分，但多分片查询不会执行此检查。 这可能会导致多分片查询在已从分片映射中删除的分片上运行。

## <a name="multi-shard-queries-and-split-merge-operations"></a>多分片查询和拆分/合并操作
多分片查询不会验证查询的分片上的 shardlet 是否参与了正在进行的拆分/合并操作。 （请参阅[使用弹性数据库拆分 / 合并工具进行缩放](sql-database-elastic-scale-overview-split-and-merge.md)。）这可能会导致不一致问题，即为同一多分片查询中的多个分片显示同一 shardlet 中的行。 请注意这些限制并在执行多分片查询时，考虑关闭正在进行的合拆分操作以及对分片映射的更改。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>另请参阅
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** 类和方法。

使用[弹性数据库客户端库](sql-database-elastic-database-client-library.md)管理分片。 包括名为 [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) 的命名空间，可以通过该空间使用单个查询和结果来查询多个分片。 它提供对分片集合进行查询抽象的功能。 它还提供了备用执行策略，尤其是部分结果，以处理在对多个分片进行查询时所出现的故障。  

