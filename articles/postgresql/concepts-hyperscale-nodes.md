---
title: 节点–超大规模 (Citus) -Azure Database for PostgreSQL
description: 了解 Azure Database for PostgreSQL 中的服务器组中节点和表的类型。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314820"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 中的节点和表–超大规模 (Citus) 

## <a name="nodes"></a>Nodes

超大规模 (Citus) 承载类型允许 Azure Database for PostgreSQL 服务器 (称为节点，) 在 "无共享" 体系结构中彼此协调。 服务器组中的节点共同保存更多的数据，并使用比单个服务器上的更多的 CPU 内核。 该体系结构还允许通过向服务器组中添加更多节点来缩放数据库。

### <a name="coordinator-and-workers"></a>协调器和辅助角色

每个服务器组都有一个协调器节点和多个辅助角色。 应用程序将其查询发送到协调器节点，该节点将其中继到相关的工作线程并累计其结果。 应用程序无法直接连接到辅助角色。

超大规模 (Citus) 使数据库管理员能够在不同的工作节点上 *分发* 表，并存储不同的行。 分布式表是超大规模 (Citus) 性能的关键。 如果无法分发表，则会将它们完全留在协调器节点上，并且无法利用跨计算机并行。

对于分布式表的每个查询，协调器会将其路由到单个辅助角色节点，或将其并行在多个节点上，具体取决于所需的数据是位于单个节点上还是在多个节点上。 协调器通过咨询元数据表确定要执行的操作。 这些表跟踪辅助角色节点的 DNS 名称和运行状况，以及跨节点分布的数据。

## <a name="table-types"></a>表类型

超大规模 (Citus) 服务器组中有三种类型的表，每个表在节点上以不同方式存储，并用于不同目的。

### <a name="type-1-distributed-tables"></a>类型1：分布式表

第一种类型，最常见的是分布式表。 它们看起来像是 SQL 语句的普通表，但它们在工作节点之间水平分区。 这意味着表中的行存储在名为分片的碎片表中的不同节点上。

超大规模 (Citus) 只在整个群集中运行 SQL 但 DDL 语句。
更改分布式表的架构会进行级联以更新所有表在工作线程中的分片。

#### <a name="distribution-column"></a>分布列

超大规模 (Citus) 使用算法分片将行分配到分片。 根据名为分布列的表列的值，赋值是明确的。 群集管理员必须在分发表时指定此列。
做出正确的选择对性能和功能非常重要。

### <a name="type-2-reference-tables"></a>类型2：引用表

引用表是一种分布式表，其整个内容将集中到单个分片中。 分片在每个辅助角色上复制。 对任何工作人员的查询都可以在本地访问引用信息，而无需从另一个节点请求行的网络开销。 引用表没有分布列，因为无需区分单独的分片每行。

引用表通常很小，用于存储与在任何辅助节点上运行的查询相关的数据。 例如订单状态或产品类别等枚举值。

### <a name="type-3-local-tables"></a>类型3：本地表

使用超大规模 (Citus) 时，连接到的协调器节点是一个常规的 PostgreSQL 数据库。 可以在协调器上创建普通表，并选择不分片。

对于本地表，最好是不参与联接查询的小型管理表。 例如，应用程序登录和身份验证的用户表。

## <a name="shards"></a>分片

上一部分介绍了如何将分布式表存储为辅助角色节点上的分片。 本部分将讨论更多技术详细信息。

`pg_dist_shard`协调器的元数据表为系统中每个分布式表的每个分片都包含一行。 该行将分片 ID 与 (shardminvalue，shardmaxvalue) 的哈希空间中的整数范围匹配。

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

如果协调器节点要确定哪些分片包含一行 `github_events` ，则将对该行中分布列的值进行哈希处理。 然后，该节点检查哪些分片的 \' 范围包含哈希值。 定义这些范围是为了使哈希函数的图像是其不相交的联合。

### <a name="shard-placements"></a>分片放置

假设分片102027与相关行相关联。 在其中一个辅助角色中调用的表中读取或写入该行 `github_events_102027` 。 哪个工作线程？ 这完全由元数据表确定。 分片到辅助角色的映射称为分片位置。

协调器节点将查询重写为引用特定表的片段， `github_events_102027` 并在相应的辅助角色上运行这些片段。 下面是在幕后运行的查询示例，用于查找包含分片 ID 102027 的节点。

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

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>后续步骤

- 确定要为数据建模准备的[应用程序类型](concepts-hyperscale-app-type.md)
