---
title: 分布式数据 = 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 在用于 PostgreSQL 的 Azure 数据库中了解分布式表、参考表、本地表和分片。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243648"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 数据库中的分布式数据，用于 PostgreSQL 和超大规模（Citus）

本文概述了 Azure 数据库中的三种表类型，用于 PostgreSQL 和超大规模 （Citus）。
它显示了分布式表如何存储为分片，以及碎片放置在节点上的方式。

## <a name="table-types"></a>表类型

Hyperscale （Citus） 服务器组中有三种类型的表，每种表都用于不同的用途。

### <a name="type-1-distributed-tables"></a>类型 1：分布式表

第一种类型（也是最常见的类型）是分布式表。 它们似乎是 SQL 语句的正常表，但它们跨工作节点水平分区。 这意味着表的行存储在不同的节点上，在称为分片的片段表中。

超大规模 （Citus） 不仅在整个群集中运行 SQL，还运行 DDL 语句。
更改分布式表的架构级联以跨工作程序更新表的所有分片。

#### <a name="distribution-column"></a>分发列

超大规模 （Citus） 使用算法分片将行分配给分片。 根据称为分布列的表列的值，确定分配。 分发表时，群集管理员必须指定此列。
做出正确的选择对于性能和功能非常重要。

### <a name="type-2-reference-tables"></a>类型 2：参考表

参考表是一种分布式表，其整个内容集中到单个分片中。 分片将复制到每个工作线程上。 任何工作人员的查询都可以在本地访问引用信息，而无需从另一个节点请求行的网络开销。 引用表没有分布列，因为无需区分每行的单独分片。

引用表通常很小，用于存储与任何辅助节点上运行的查询相关的数据。 例如枚举值，如订单状态或产品类别。

### <a name="type-3-local-tables"></a>类型 3：本地表

使用超大规模 （Citus） 时，连接到的协调员节点是常规 PostgreSQL 数据库。 您可以在协调器上创建普通表，并选择不分片。

本地表的一个好候选是不参与联接查询的小型管理表。 例如，应用程序登录和身份验证的用户表。

## <a name="shards"></a>碎片

上一节介绍了如何在辅助节点上将分布式表存储为分片。 本节讨论更多的技术细节。

协调`pg_dist_shard`器上的元数据表包含系统中每个分布式表的每个分片的行。 该行将分片 ID 与哈希空间中的一系列整数（分片值、分片最大值）匹配。

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

如果协调器节点想要确定哪些分片包含一`github_events`行，它将对行中的分布列的值进行哈希。 然后，分片\'范围的节点检查包含哈希值。 定义范围，以便哈希函数的图像是它们不相交的联合。

### <a name="shard-placements"></a>碎片放置

假设分片 102027 与有问题的行相关联。 该行在其中一个辅助器中调用`github_events_102027`的表中读取或写入。 哪个工人？ 这完全由元数据表决定。 碎片映射到辅助角色称为分片放置。

协调器节点将查询重写为引用特定表的`github_events_102027`片段，并在相应的辅助程序上运行这些片段。 下面是一个查询在后台运行的示例，以查找包含分片 ID 102027 的节点。

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>后续步骤
- 了解如何为分布式表[选择分发列](concepts-hyperscale-choose-distribution-column.md)。
