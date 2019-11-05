---
title: Azure Database for PostgreSQL 中的服务器概念
description: 本文提供配置和管理 Azure Database for PostgreSQL 服务器的注意事项和指南。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4a5ebf810771efe49ee40e272d1fa4683140eda1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482764"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>表归置 in Azure Database for PostgreSQL –超大规模（Citus）

归置表示将相关信息一起存储在相同的节点上。 当所有必要的数据都可用且没有任何网络流量时，查询的速度会加快。 不同节点上的归置相关数据允许查询在每个节点上并行运行。

## <a name="data-colocation-for-hash-distributed-tables"></a>哈希分布式表的数据归置

在 Azure Database for PostgreSQL –超大规模（Citus）中，如果分布列中的值的哈希值在分片的哈希范围内，则该行存储在分片中。 具有相同哈希范围的分片始终放置在同一个节点上。 分布列值相等的行始终在表中的相同节点上。

![分片](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>归置的一个实际示例

请考虑以下可能是多租户 web analytics SaaS 的一部分的表：

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

现在，我们想要回答可能由面向客户的仪表板发出的查询。 例如，在 "/blog" （在租户6中以 "" 开头）返回过去一周内的访问次数。

如果我们的数据是单服务器部署选项，则可以通过使用 SQL 提供的一组丰富的关系操作来轻松表达查询：

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

只要该查询的[工作集](https://en.wikipedia.org/wiki/Working_set)适合内存，单服务器表就是一个合适的解决方案。 让我们考虑一下，通过超大规模（Citus）部署选项缩放数据模型的机会。

### <a name="distribute-tables-by-id"></a>按 ID 分配表

当租户数和为每个租户存储的数据增长时，单服务器查询开始减速。 工作集在内存中停止占用，CPU 成为瓶颈。

在这种情况下，我们可以使用超大规模（Citus）在多个节点上分片数据。 当我们决定分片是分布列时，我们需要做的第一个也是最重要的选择。 首先，让我们以一种简单的选择，使用事件表的 `event_id` 和 `page` 表的 `page_id`：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

当数据分散到不同的工作线程中时，不能像在单个 PostgreSQL 节点上那样执行联接。 相反，我们需要发出两个查询：

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

之后，应用程序需要合并两个步骤的结果。

运行查询必须查阅分散在节点上的分片中的数据。

![低效的查询](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

在这种情况下，数据分布产生了重大的缺点：

-   查询每个分片并运行多个查询时的开销。
-   Q1 的开销会将许多行返回到客户端。
-   第2季度变大。
-   如果需要在多个步骤中编写查询，则需要在应用程序中进行更改。

数据是分散的，因此可以并行化查询。 仅当查询所做的工作量大大超过查询许多分片的开销时，此功能才有好处。

### <a name="distribute-tables-by-tenant"></a>按租户分发表

在超大规模（Citus）中，具有相同分布列值的行保证位于同一个节点上。 从开始，我们可以创建表，并将 `tenant_id` 作为分布列。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

现在，超大规模（Citus）可以在不修改的情况下应答原始单服务器查询（Q1）：

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

由于 tenant_id 上的筛选器和联接，超大规模（Citus）知道可以使用包含该特定租户的数据的一组协同工作分片来回答整个查询。 单个 PostgreSQL 节点可以通过单个步骤来回答查询。

![更好的查询](media/concepts-hyperscale-colocation/colocation-better-query.png)

在某些情况下，必须更改查询和表架构以在 unique 约束和联接条件中包含租户 ID。 此更改通常非常简单。

## <a name="next-steps"></a>后续步骤

- 请参阅[多租户教程](tutorial-design-database-hyperscale-multi-tenant.md)中的租户数据的协同工作方式。
