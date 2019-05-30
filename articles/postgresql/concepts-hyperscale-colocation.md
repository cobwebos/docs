---
title: Azure Database for PostgreSQL 中的服务器概念
description: 本文提供配置和管理 Azure Database for PostgreSQL 服务器的注意事项和指南。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077465"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>表共置在 Azure Database for PostgreSQL-超大规模 (Citus) （预览版）

共置意味着将相关的信息一起存储在相同节点上。 查询可以转而无需任何网络流量的所有必要的数据不可用时快速。 归置不同节点上的相关的数据允许查询并行中有效地运行每个节点上。

## <a name="data-colocation-for-hash-distributed-tables"></a>哈希分布表的数据归置

在超大规模，行存储在分片中，如果分布列中的值的哈希分片的哈希范围内。 具有相同的哈希范围分片始终放置在同一节点上。 相等分布列的值的行始终是同一节点上跨表。

![分片](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>主机托管一个实际示例

请考虑可能是多租户 web 分析 SaaS 的一部分的以下表：

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

现在，我们想要回答的面向客户的仪表板，如可能发出查询："在过去一周开头的所有页面中返回的访问量 / 博客六个租户中。"

如果我们的数据是在单服务器部署选项中，我们可以轻松地表示我们使用一组丰富的关系操作，提供的 SQL 的查询：

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

只要[工作集](https://en.wikipedia.org/wiki/Working_set)为此查询适合在内存中，单服务器表是合适的解决方案。 但是，让我们考虑缩放使用超大规模部署选项的数据模型的机会。

### <a name="distributing-tables-by-id"></a>分布表的 ID

单服务器查询启动速度变慢随着租户和存储为每个租户的数据的数量增加。 工作集，停止调整在内存和 CPU 成为瓶颈。

在这种情况下，我们可以跨多个节点上使用超大规模分片数据。 第一个也是最重要选择我们需要可以在分片是分布列时使用。 让我们开始使用的一个简单的选择`event_id`事件表和`page_id`为`page`表：

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

当数据分散在不同的工作人员时，我们无法执行联接，因为我们将在单个 PostgreSQL 节点上。 相反，我们将需要发出两个查询：

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

此后，来自两个步骤的结果需要合并到应用程序。

运行的查询必须查阅分片分散在节点中的数据。

![无效的查询](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

在这种情况下，数据分布创建重大缺点：

-   从查询运行多个查询的每个分片的开销
-   返回到客户端的许多行的第 1 季度的开销
-   第 2 季度变得大
-   需要在多个步骤中编写查询需要在应用程序中的更改

因为分散的数据，则可以并行执行查询。 但是，它才有益如果查询执行的工作量大大超过查询多个分片的开销。

### <a name="distributing-tables-by-tenant"></a>分布租户表

在超大规模，保证具有相同的分布列值的行可在同一节点上。 重新开始，我们可以创建与表格`tenant_id`作为分布列。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

现在超大规模可以回答原始的单服务器查询，无需修改即可 （第 1 季度）：

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

由于筛选器和联接 tenant_id，超大规模知道可以使用为该特定租户中包含的数据归置分片组回答整个查询。 单个 PostgreSQL 节点可以回答中单步执行的查询。

![更好的查询](media/concepts-hyperscale-colocation/colocation-better-query.png)

在某些情况下，必须更改查询和表架构为 unique 约束中包含租户 ID 和联接条件。 但是，这通常是简单的更改。

## <a name="next-steps"></a>后续步骤

- 请参阅如何将租户数据归置在[多租户教程](tutorial-design-database-hyperscale-multi-tenant.md)
