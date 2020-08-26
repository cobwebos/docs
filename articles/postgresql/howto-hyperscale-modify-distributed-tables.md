---
title: 修改分布式表-超大规模 (Citus) -Azure Database for PostgreSQL
description: '用于创建和修改分布式表的 SQL 命令-使用 Azure 门户的超大规模 (Citus) '
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 628944f9763dc79148e0b64c97158064208412bf
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136759"
---
# <a name="distribute-and-modify-tables"></a>分发和修改表

## <a name="distributing-tables"></a>分布表

若要创建分布式表，需要首先定义表架构。 为此，您可以使用[CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html)语句来定义表，就像使用常规 PostgreSQL 表执行此操作一样。

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

接下来，可以使用创建 \_ 分布式 \_ 表 ( # A1 函数指定表分布列并创建辅助角色分片。

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

函数调用通知超大规模 (Citus) github \_ 事件表应在存储库 id 列上分布， \_ (通过将列值哈希处理) 。 函数还使用 citus \_ 和 citus \_ 复制 \_ 因子配置值在辅助角色节点上创建分片。

它会创建总数为 citus 的分片 \_ 计数，其中每个分片拥有部分哈希空间，并基于默认 citus \_ 复制 \_ 因子配置值进行复制。 在辅助角色上创建的分片副本与协调器上的表具有相同的表架构、索引和约束定义。 创建副本后，函数会将所有分布式元数据保存在协调器中。

为每个创建的分片分配一个唯一的分片 ID，其所有副本都具有相同的分片 ID。 分片在辅助节点上表示为名为 tablename shardid 的常规 PostgreSQL 表 \' \_ \' ，其中 tablename 是分布式表的名称，而分片 id 是分配的唯一 id。 可以连接到 worker postgres 实例，以查看或运行单个分片上的命令。

你现在已准备好将数据插入分布式表，并对其运行查询。 你还可以在[表和分片 DDL](reference-hyperscale-functions.md#table-and-shard-ddl)引用中了解有关此部分中使用的 UDF 的详细信息。

### <a name="reference-tables"></a>引用表

上述方法将表分布到多个水平分片中。  另一种可能是将表分发到单个分片，并将分片复制到每个辅助角色节点。 以这种方式分发的表称为*引用表。* 它们用于存储群集中的多个节点需要经常访问的数据。

引用表的常见候选项包括：

-   需要与较大的分布式表联接的小型表。
-   多租户应用中缺少租户 ID 列或 \' 与租户关联的表。 在迁移期间 (或，即使对于与租户关联的某些表也是如此。 ) 
-   需要跨多个列的唯一约束的表，足够小。

例如，假设多租户电子商务网站需要为其任何商店中的交易计算销售税。 税务信息 \' 并不特定于任何租户。 将其放在共享表中是有意义的。 以美国为中心的引用表可能如下所示：

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

现在，在购物车中计算税金的查询可以在表上联接 `states` ，无网络开销，还可以将外键添加到状态代码中，以实现更好的验证。

除了将表作为单个复制的分片分发外，UDF 还 `create_reference_table` 会将其标记为超大规模 (Citus) 元数据表中的引用表。 超大规模 (Citus) 会自动执行两阶段提交 ([2pc](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) 来修改以这种方式标记的表，这样可提供强大的一致性保证。

如果某个分布式表的分片计数为1，则可以将其升级为可识别的引用表，如下所示：

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

有关使用引用表的另一个示例，请参阅[多租户数据库教程](tutorial-design-database-hyperscale-multi-tenant.md)。

### <a name="distributing-coordinator-data"></a>分布协调器数据

如果将现有的 PostgreSQL 数据库转换为超大规模 (Citus) 群集的协调器节点，则其表中的数据可以有效地分发，并且最大程度地减少了应用程序的中断。

`create_distributed_table`前面所述的函数适用于空表和非空表，对于后者，它会自动在整个群集中分布表行。 你将知道是否会按消息的状态复制数据， \" 注意：正在从本地表复制数据 \. 。 \"例如：

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

在迁移数据时，对表的写入将被阻止，并且在函数提交后，将挂起写入作为分布式查询处理。  (如果函数失败，则查询将再次成为本地查询。 ) 读取可继续正常运行，一旦函数提交，就会成为分布式查询。

在将表 A 和表 B （其中 A 的外键为 B）进行分布时，首先分发 key destination 表 B。 以错误的顺序执行操作会导致错误：

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

如果不能按正确的顺序进行分发，则删除外键，分发表，然后重新创建外键。

从外部数据库（如从 Amazon RDS 到超大规模 (Citus) 云）迁移数据时，首先通过创建超大规模 (Citus) 分布式表 `create_distributed_table` ，然后将数据复制到表中。
复制到分布式表可以避免协调器节点上的空间不足。

## <a name="colocating-tables"></a>归置表

归置表示将相关信息保留在相同的计算机上。 它实现了高效查询，同时利用了整个数据集的水平可扩展性。 有关详细信息，请参阅[归置](concepts-hyperscale-colocation.md)。

表已在组中定位。 若要手动控制表的归置组分配，请使用的可选 `colocate_with` 参数 `create_distributed_table` 。 如果你不 \' 关心表的归置， \' 则忽略此参数。 它的默认值为 `'default'` ，该值将表与具有相同分布列类型、分片计数和复制因子的任何其他默认归置表进行分组。 如果要中断或更新此隐式归置，可以使用 `update_distributed_table_colocation()` 。

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

如果新表与归置组中的其他表不相关，请指定 `colocated_with => 'none'` 。

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

将不相关的表拆分为其自己的归置组将改善分片的重新[平衡](howto-hyperscale-scaling.md#rebalance-shards)性能，因为同一组中的分片必须一起移动。

如果表确实是相关的 (例如，当它们将联接) 时，显式归置它们可能有意义。 适当的归置收益比任何重新平衡开销更重要。

若要显式归置多个表，请分配一个表，然后将其他表放入其归置组。 例如：

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

有关归置组的信息存储在[pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table)表中，而[pg_dist_partition](reference-hyperscale-metadata.md#partition-table)显示分配给哪些组的表。

## <a name="dropping-tables"></a>删除表

可以使用标准 PostgreSQL DROP TABLE 命令删除分布式表。 与常规表一样，DROP TABLE 删除目标表中存在的所有索引、规则、触发器和约束。 此外，它还会删除辅助角色节点上的分片，并清除其元数据。

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>修改表

超大规模 (Citus) 会自动传播多种类型的 DDL 语句。
修改协调器节点上的分布式表也会更新工作线程上的分片。 其他 DDL 语句需要手动传播，某些其他 DDL 语句会被禁止，如任何其他 DDL 语句会修改分布列。
尝试运行无法自动传播的 DDL 将引发错误，并使表在协调器节点上保持不变。

下面是传播的 DDL 语句类别的参考。
可以使用[配置参数](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)启用或禁用自动传播

### <a name="addingmodifying-columns"></a>添加/修改列

超大规模 (Citus) 自动传播大多数[ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html)命令。 添加列或更改其默认值的工作方式与在单计算机 PostgreSQL 数据库中的工作方式相同：

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

对现有列的重大更改（例如，对其进行重命名或更改其数据类型）也很好。 但无法更改[分布列](concepts-hyperscale-nodes.md#distribution-column)的数据类型。
此列确定表数据如何通过超大规模 (Citus) 群集进行分布，并修改其数据类型将需要移动数据。

尝试这样做会导致错误：

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>添加/删除约束

使用超大规模 (Citus) 使你可以继续享受关系数据库的安全性，包括数据库约束 (参阅 PostgreSQL[文档](https://www.postgresql.org/docs/current/static/ddl-constraints.html)) 。
由于分布式系统的性质，超大规模 (Citus) 不会交叉引用唯一性约束或辅助节点之间的引用完整性。

若要在分散的分布式表之间设置外键，请始终在键中包含分布列。 包含分布列可能涉及到密钥复合。

可以在以下情况下创建外键：

-   在两个本地 (非分布式) 表之间，
-   在两个引用表之间，
-   键包含分布列时，[位于两个](concepts-hyperscale-colocation.md)定位的分布式表之间，或
-   作为引用[引用表](concepts-hyperscale-nodes.md#type-2-reference-tables)的分布式表

不支持从引用表到分布式表的外键。

> [!NOTE]
>
> 主键和唯一性约束必须包含分布列。 将它们添加到非分布列会生成错误

此示例演示如何在分布式表中创建主键和外键：

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

同样，在唯一性约束中包含分布列：

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Not-null 约束可以应用于 (分布的任何列，也不能) ，因为它们不需要在辅助角色之间进行查找。

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>使用无效约束

在某些情况下，对新行强制约束可能会很有用，同时允许现有的非一致性行保持不变。 超大规模 (Citus) 使用 PostgreSQL \' s， \" 而不是有效的约束指定，对 CHECK 约束和外键支持此功能 \" 。

例如，请考虑将用户配置文件存储在[引用表](concepts-hyperscale-nodes.md#type-2-reference-tables)中的应用程序。

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

在这种情况中，假设有几个非地址进入了表中。

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

我们想要验证地址，但 PostgreSQL 通常不允许添加对现有行失败的 CHECK 约束。 *但允许标记*为无效的约束：

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

新行现在受保护。

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

稍后，在非高峰时段，数据库管理员可以尝试修复错误的行并重新验证约束。

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

PostgreSQL 文档包含有关 "[更改表](https://www.postgresql.org/docs/current/sql-altertable.html)" 部分中无效和验证约束的详细信息。

### <a name="addingremoving-indices"></a>添加/删除索引

超大规模 (Citus) 支持添加和删除[索引](https://www.postgresql.org/docs/current/static/sql-createindex.html)：

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

添加索引需要写入锁定，这在多租户系统记录中可能是不必要的 \" 。 \"若要最大程度地减少应用程序停机时间，[请改为](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY)创建索引。 此方法需要的总工作量超过标准索引生成的时间，并且需要更长的时间才能完成。 但是，因为它允许在生成索引时正常操作继续，所以此方法对于在生产环境中添加新索引很有用。

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
