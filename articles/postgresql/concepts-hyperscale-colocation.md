---
title: 表主机代管 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 如何将相关信息存储在一起以加快查询速度
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967331"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure 数据库中的表主机代管，用于 PostgreSQL 和超大规模（Citus）

主机代管意味着将相关信息存储在同一节点上。 当所有必要的数据都可用时，查询可以进行得非常快，而没有任何网络流量。 在不同节点上协调相关数据允许查询在每个节点上高效并行运行。

## <a name="data-colocation-for-hash-distributed-tables"></a>哈希分布表的数据主机代管

在"PostgreSQL = 超大规模"（Citus）的 Azure 数据库中，如果分布列中值的哈希值位于分片的哈希范围内，则行将存储在分片中。 具有相同哈希范围的分片始终放置在同一节点上。 分布列值相等的行始终位于表之间的同一节点上。

![碎片](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>主机代管的实际示例

请考虑以下可能是多租户 Web 分析 SaaS 的一部分的表：

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

现在，我们要回答可能由面向客户的仪表板发出的查询。 示例查询是"返回过去一周中所有页面的访问次数，从租户 6 中的'/blog'开始。

如果我们的数据位于单服务器部署选项中，我们可以轻松地使用 SQL 提供的丰富的关系操作集来表达我们的查询：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

只要此查询[的工作集](https://en.wikipedia.org/wiki/Working_set)适合内存，单服务器表就是适当的解决方案。 让我们考虑使用超大规模 （Citus） 部署选项扩展数据模型的机会。

### <a name="distribute-tables-by-id"></a>按 ID 分发表

随着租户数量和为每个租户存储的数据增加，单服务器查询开始变慢。 工作集停止在内存中安装，CPU 成为瓶颈。

在这种情况下，我们可以使用超大规模 （Citus） 跨多个节点对数据进行分片。 当我们决定分片时，我们需要做出的第一个也是最重要的选择是分发列。 让我们从对事件表和`event_id``page_id``page`表的天真选择开始：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

当数据分散在不同的工作人员之间时，我们无法像在单个 PostgreSQL 节点上那样执行联接。 相反，我们需要发出两个查询：

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

之后，这两个步骤的结果需要由应用程序组合。

运行查询必须查阅分散在节点上的分片中的数据。

![低效查询](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

在这种情况下，数据分布会产生重大缺陷：

-   查询每个分片和运行多个查询的开销。
-   将许多行返回到客户端的第 1 季度开销。
-   问题 2 变大。
-   需要以多个步骤编写查询需要在应用程序中进行更改。

数据被分散，因此查询可以并行化。 只有当查询的工作量远远大于查询许多分片的开销时，才是有益的。

### <a name="distribute-tables-by-tenant"></a>按租户分发表

在超量程 （Citus） 中，具有相同分布列值的行保证位于同一节点上。 开始，我们可以创建表作为`tenant_id`分发列。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

现在，超大规模 （Citus） 无需修改即可回答原始单服务器查询 （Q1）：

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

由于tenant_id进行筛选和联接，Hyperscale （Citus） 知道可以使用包含该特定租户数据的一组 coe 分片来应答整个查询。 单个 PostgreSQL 节点可以在单个步骤中应答查询。

![更好的查询](media/concepts-hyperscale-colocation/colocation-better-query.png)

在某些情况下，必须更改查询和表架构，以将租户 ID 包含在唯一的约束和联接条件中。 此更改通常很简单。

## <a name="next-steps"></a>后续步骤

- 在[多租户教程](tutorial-design-database-hyperscale-multi-tenant.md)中了解如何将租户数据与位置有关。
