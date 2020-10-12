---
title: SQL 函数–超大规模 (Citus) -Azure Database for PostgreSQL
description: 超大规模 (Citus) SQL API 中的函数
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 16c3a45e0d88a0546772b3fdc855c90f2e450d14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250325"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>超大规模 (Citus) SQL API 中的函数

本部分包含超大规模 (Citus) 提供的用户定义函数的参考信息。 这些函数有助于向超大规模 () Citus 提供额外的分布式功能，而不是标准 SQL 命令。

> [!NOTE]
>
> 超大规模 (Citus) 运行旧版 Citus 引擎的服务器组可能不提供以下列出的所有功能。

## <a name="table-and-shard-ddl"></a>表和分片 DDL

### <a name="create_distributed_table"></a>创建 \_ 分布式 \_ 表

Create \_ distributed \_ table ( # A1 函数用于定义分布式表，如果它是哈希分布式表，则创建它的分片。 此函数采用表名称、分布列和可选的分发方法，并插入适当的元数据以将表标记为分布式。 如果未指定分发方法，则函数默认为 "哈希" 分布。 如果表是哈希分布式的，则该函数还基于分片计数和分片复制因子配置值创建 worker 分片。 如果表中包含任何行，它们会自动分发到工作节点。

此函数将 \_ \_ \_ ( # A1 后跟 master \_ create \_ worker \_ 分片 ( # A3 的用法替换为 master create 分布式 table。

#### <a name="arguments"></a>参数

**表 \_ 名称：** 需要分发的表的名称。

**分布 \_ 列：** 要在其上分配表的列。

**分布 \_ 类型：** (可选) 要根据其分发表的方法。 允许的值为 append 或 hash，默认值为 "hash"。

**归置 \_ with：** (Optional) 将当前表包含在另一个表的归置组中。 默认情况下，当表是由同一类型的列分配、具有相同的分片计数，并且具有相同的复制系数时，它们将被定位。 的可能值 `colocate_with` 为 `default` ， `none` 启动新的归置组，或使用该表归置的另一个表的名称。   (，请参阅 [归置表](concepts-hyperscale-colocation.md)。 ) 

请记住，的默认值为 `colocate_with` 隐式归置。 在表相关或将联接时，[归置](concepts-hyperscale-colocation.md)可能是很好的做法。  但是，如果两个表不相关，但对于它们的分布列使用相同的数据类型，则意外归置它们会在 [分片重新平衡](howto-hyperscale-scaling.md#rebalance-shards)期间降低性能。  表分片将在级联中不必要地一起移动 \" 。\"

如果新的分布式表与其他表无关，最好指定 `colocate_with => 'none'` 。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例 \_ 通过哈希在存储库 id 列上通知数据库应分发 github 事件表 \_ 。

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>创建 \_ 引用 \_ 表

Create \_ reference \_ table ( # A1 函数用于定义小引用表或维度表。 此函数采用表名称，创建一个只包含一个分片的分布式表，并将其复制到每个辅助角色节点。

#### <a name="arguments"></a>参数

**表 \_ 名称：** 需要分布的小型维度表或引用表的名称。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例通知数据库，全国表应定义为引用表

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>升级 \_ 到 \_ 引用 \_ 表

升级 \_ 到 \_ 引用 \_ 表 ( # A1 函数会获得一个分片计数为1的现有分布式表，并将其升级为可识别的引用表。 调用此函数后，表就像是用 [create_reference_table](#create_reference_table)创建的一样。

#### <a name="arguments"></a>参数

**表 \_ 名：** 包含分片 count = 1) 的分布式 (表的名称，将以引用表的形式分发。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例通知数据库，全国表应定义为引用表

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>标记被定位的 \_ 表 \_

使用 \_ ( # A1 函数定位的标记表将 \_ (源) 使用分布式表，并将 (目标) 的其他列表，并将目标置于与源相同的归置组中。 如果源尚不在组中，则此函数将创建一个，并为其分配源和目标。

归置表应通过 create_distributed_table 的参数在表分布时间完成 `colocate_with` ，但[create_distributed_table](#create_distributed_table) `mark_tables_colocated` 如果需要，可以在以后处理。

#### <a name="arguments"></a>参数

**源表 \_ \_ 名称：** 要为其分配目标以匹配的归置组的分布式表的名称。

**目标 \_ 表 \_ 名称：** 分布式目标表的名称数组，必须为非空。 这些分布式表必须与中的源表匹配：

> -   distribution 方法
> -   分布列类型
> -   复制类型
> -   分片计数

如果上述条件均不适用，则超大规模 (Citus) 将引发错误。 例如，尝试归置 `apples` 的表和 `oranges` 其分布列类型会产生不同的结果：

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

此示例 `products` 将和置于与 `line_items` 相同的归置组中 `stores` 。 该示例假定这些表均分布在具有匹配类型的列中，这很可能是 \" 商店 id。\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>创建 \_ 分布式 \_ 函数

将函数从协调器节点传播到辅助角色，并将其标记为分布式执行。 在协调器上调用分布式函数时，超大规模 (Citus) 使用 \" 分布参数的值 \" 选取辅助角色节点来运行该函数。 对辅助角色执行函数会增加并行度，并可以使代码更接近分片中的数据，以降低延迟。

在分布式函数执行期间，Postgres 搜索路径不会从协调器传播到辅助角色，因此分布式函数代码应完全限定数据库对象的名称。 函数发出的通知也不会显示给用户。

#### <a name="arguments"></a>参数

**函数 \_ 名：** 要分发的函数的名称。 名称必须在括号中包含函数的参数类型，因为在 PostgreSQL 中有多个函数可以具有相同的名称。 例如，与 `'foo(int)'` 不同 `'foo(int, text)'` 。

**分布 \_ arg \_ Name：** (可选) 用于分发的参数名称。 为方便起见 (或函数参数没有名称) 时，允许使用位置占位符，如 `'$1'` 。 如果未指定此参数，则 `function_name` 仅在辅助角色上创建名为的函数。 如果将来添加辅助角色节点，该函数也会自动创建。

**归置 \_ with：** (可选) 当分布式函数向分布式表中读取或写入数据 (或更归置的情况下) ，请确保使用参数命名该表 `colocate_with` 。 然后，该函数的每个调用都将在包含相关分片的辅助角色节点上运行。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>元数据/配置信息

### <a name="master_get_table_metadata"></a>主 \_ 获取 \_ 表 \_ 元数据

主 \_ 获取 \_ 表 \_ 元数据 ( # A1 函数可用于返回分布式表的与分发相关的元数据。 此元数据包括表的关系 ID、存储类型、分发方法、分布列、复制计数、最大分片大小和分片放置策略。 在涵盖的内容后面，此函数将查询超大规模 (Citus) 元数据表，以获取所需的信息，并将其连接到元组，然后将其返回给用户。

#### <a name="arguments"></a>参数

**表 \_ 名：** 要为其提取元数据的分布式表的名称。

#### <a name="return-value"></a>返回值

包含以下信息的元组：

**逻辑 \_ relid：** 分布式表的 Oid。 它引用 pg \_ 类系统目录表中的 relfilenode 列。

**部分 \_ 存储 \_ 类型：** 用于表的存储的类型。 不能为 " (标准表) ，" f " (外表) 或" c " (列列表) 。

**part \_ 方法：** 用于表的分发方法。 可以是 "a" (追加) 或 "h" (哈希) 。

**部分 \_ 键：** 表的分布列。

**部分 \_ 副本 \_ 计数：** 当前分片复制计数。

**部分 \_ 最大 \_ 大小：** 当前最大分片大小（以字节为单位）。

**部件 \_ 放置 \_ 策略：** 用于放置表的分片的分片放置策略。 可能为 1 (本地节点优先) 或 2 (轮循机制) 。

#### <a name="example"></a>示例

下面的示例提取并显示 github events 表的表元数据 \_ 。

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>获取 \_ \_ \_ \_ 分布列的分片 \_ id

超大规模 (Citus) 根据行的分布列的值和表的分发方法，将分布式表的每一行分配给分片。 在大多数情况下，精确映射是数据库管理员可忽略的低级别详细信息。 但对于手动数据库维护任务或只是为了满足好奇心，确定行的分片会很有用。 `get_shard_id_for_distribution_column`函数提供了用于哈希和范围分布式表以及引用表的信息。 它不适用于追加分布。

#### <a name="arguments"></a>参数

**表 \_ 名：** 分布式表。

**分布 \_ 值：** 分布列的值。

#### <a name="return-value"></a>返回值

分片 ID 超大规模 (Citus) 与给定表的分布列值关联。

#### <a name="example"></a>示例

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>列 \_ 与 \_ 列的 \_ 名称

将 `partkey` 的列转换 `pg_dist_partition` 为文本列名称。 转换可用于确定分布式表的分布列。

有关更详细的讨论，请参阅 [选择分布列](concepts-hyperscale-choose-distribution-column.md)。

#### <a name="arguments"></a>参数

**表 \_ 名：** 分布式表。

**列 \_ var \_ 文本：** `partkey` 表中的值 `pg_dist_partition` 。

#### <a name="return-value"></a>返回值

的 `table_name` 分布列的名称。

#### <a name="example"></a>示例

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

输出：

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus \_ 关系 \_ 大小

获取指定的分布式表的所有分片所使用的磁盘空间。
磁盘空间包括 \" 主分叉的大小，但不包括 \" 分片的可见性映射和可用空间图。

#### <a name="arguments"></a>参数

**logicalrelid：** 分布式表的名称。

#### <a name="return-value"></a>返回值

Bigint 形式的大小（以字节为单位）。

#### <a name="example"></a>示例

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus \_ 表 \_ 大小

获取指定的分布式表的所有分片所使用的磁盘空间，但不包括 TOAST、可用空间图和可见性映射) 的索引 (。

#### <a name="arguments"></a>参数

**logicalrelid：** 分布式表的名称。

#### <a name="return-value"></a>返回值

Bigint 形式的大小（以字节为单位）。

#### <a name="example"></a>示例

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus \_ 总 \_ 关系 \_ 大小

获取指定的分布式表的所有分片使用的总磁盘空间，包括所有索引和 TOAST 数据。

#### <a name="arguments"></a>参数

**logicalrelid：** 分布式表的名称。

#### <a name="return-value"></a>返回值

Bigint 形式的大小（以字节为单位）。

#### <a name="example"></a>示例

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus \_ stat \_ 语句 \_ 重置

从 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)中移除所有行。
此函数独立于使用 `pg_stat_statements_reset()` 。 若要重置所有统计信息，请调用这两个函数。

#### <a name="arguments"></a>参数

不适用

#### <a name="return-value"></a>返回值

无

## <a name="server-group-management-and-repair"></a>服务器组管理和修复

### <a name="master_copy_shard_placement"></a>主 \_ 副本 \_ 分片 \_ 放置

如果在修改命令或 DDL 操作过程中无法更新分片位置，则会将其标记为非活动状态。 然后， \_ \_ 可以调用主副本分片 \_ 位置函数，以使用正常位置的数据修复不活动的分片位置。

若要修复分片，该函数首先删除不正常的分片位置，并使用协调器上的架构重新创建它。 创建分片放置后，该函数将从正常位置复制数据，并更新元数据以将新的分片位置标记为正常。 此函数可确保在修复过程中，将对分片进行任何并发修改保护。

#### <a name="arguments"></a>参数

**分片 \_ id：** 要修复的分片的 id。

**源 \_ 节点 \_ 名称：** (\" 源节点) 存在具有正常分片位置的节点的 DNS 名称 \" 。

**源 \_ 节点 \_ 端口：** 数据库服务器正在侦听的源辅助角色节点上的端口。

**目标 \_ 节点 \_ 名称：** 存在无效分片位置的节点的 DNS 名称 \")  (目标 \" 节点。

**目标 \_ 节点 \_ 端口：** 数据库服务器正在侦听的目标辅助角色节点上的端口。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

下面的示例将修复在端口5432上的 "错误主机" 上运行的数据库服务器上的分片12345的非活动分片位置 \_ 。 若要修复此问题，它将使用端口上 "良好 \_ 主机" 上运行的服务器上的运行状况良好的分片布局中的数据
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>主 \_ 移动 \_ 分片 \_ 放置

此函数将) 从一个节点移到另一个节点的给定分片 (和分片。 它通常在分片重新平衡期间间接使用，而不是由数据库管理员直接调用。

可以通过两种方式移动数据：阻止或非阻止。 阻止方法意味着在将所有修改移到分片期间暂停。
第二种方法可避免阻塞分片写入，它依赖于 Postgres 10 个逻辑复制。

成功移动操作后，会删除源节点中的分片。 如果移动在任何点上都失败，则此函数将引发错误，并使源节点和目标节点保持不变。

#### <a name="arguments"></a>参数

**分片 \_ id：** 要移动的分片的 id。

**源 \_ 节点 \_ 名称：** (\" 源节点) 存在具有正常分片位置的节点的 DNS 名称 \" 。

**源 \_ 节点 \_ 端口：** 数据库服务器正在侦听的源辅助角色节点上的端口。

**目标 \_ 节点 \_ 名称：** 存在无效分片位置的节点的 DNS 名称 \")  (目标 \" 节点。

**目标 \_ 节点 \_ 端口：** 数据库服务器正在侦听的目标辅助角色节点上的端口。

**分片 \_ 传输 \_ 模式：** (可选) 指定复制方法，是使用 PostgreSQL 逻辑复制还是跨辅助角色复制命令。 可能的值为：

> -   `auto`：如果可以进行逻辑复制，则需要副本标识，否则使用旧行为 (例如，对于分片 repair，PostgreSQL 9.6) 。 这是默认值。
> -   `force_logical`：使用逻辑复制，即使该表没有副本标识。 在复制过程中对表的任何并发 update/delete 语句都将失败。
> -   `block_writes`：对缺少主键或副本标识的表使用复制 (阻止写入) 。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>重新平衡 \_ 表 \_ 分片

重新平衡 \_ 表 \_ 分片 ( # A1 函数会移动给定表的分片，以使它们在辅助角色之间均匀分布。 函数首先计算需要执行的移动列表，以确保服务器组在给定的阈值内平衡。 然后，它将分片位置逐个移动到目标节点，并更新相应的分片元数据以反映移动。

确定分片是否均匀分布时，会为每个分片分配成本 \" 。 \" 默认情况下，每个分片都具有相同的开销 (值为 1) ，因此分布以均衡辅助角色的成本与每个 equalizing 的分片数量相同。 常量成本策略由 \" \_ 分片 \_ 计数调用 \" ，是默认的重新平衡策略。

默认策略在以下情况下适用：

*  分片的大小大致相同
*  分片获得大致相同数量的流量
*  辅助角色节点大小/类型相同
*  分片尚未固定到特定工作进程

如果这些假定中有任何一个不包含，则默认重新平衡可能会导致计划不佳。 在这种情况下，可以使用参数自定义策略 `rebalance_strategy` 。

建议在运行分片重新均衡表之前调用 [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) \_ \_ ，以查看并验证要执行的操作。

#### <a name="arguments"></a>参数

**表 \_ 名称：** (可选) 其分片需要为重新平衡的表的名称。 如果为 NULL，则重新平衡所有现有的归置组。

**阈值：** (可选) 介于0.0 和1.0 之间的浮点数，指示节点利用率与平均利用率的最大差值。 例如，如果指定0.1，分片 rebalancer 会尝试平衡所有节点，使其保持相同数量的分片±10%。
具体而言，分片 rebalancer 会尝试将所有工作节点的利用率汇聚到 (1-阈值) \* 平均 \_ 利用率 \. (1
+ 阈值) \* 平均 \_ 利用率范围。

**最大 \_ 分片 \_ 移动：** (可选) 要移动的分片的最大数目。

**排除的 \_ 分片 \_ 列表：** 在重新平衡操作期间，不应移动的分片 (可选) 标识符。

**分片 \_ 传输 \_ 模式：** (可选) 指定复制方法，是使用 PostgreSQL 逻辑复制还是跨辅助角色复制命令。 可能的值为：

> -   `auto`：如果可以进行逻辑复制，则需要副本标识，否则使用旧行为 (例如，对于分片 repair，PostgreSQL 9.6) 。 这是默认值。
> -   `force_logical`：使用逻辑复制，即使该表没有副本标识。 在复制过程中对表的任何并发 update/delete 语句都将失败。
> -   `block_writes`：对缺少主键或副本标识的表使用复制 (阻止写入) 。

** \_ 仅限排出：** (可选的) 如果为 true，则将 `shouldhaveshards` 分片中已设置为 false 的工作节点移到 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table); 不移动其他。

重新**平衡 \_ 策略：** (可选) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)中策略的名称。
如果省略此参数，则函数将选择默认策略，如表中所示。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

下面的示例将在默认阈值内尝试重新平衡 github \_ 事件表的分片。

```postgresql
SELECT rebalance_table_shards('github_events');
```

此示例使用情况将尝试重新平衡 github \_ 事件表，而不移动 ID 为1和2的分片。

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>获取重新 \_ 均衡 \_ 表 \_ 分片 \_ 计划

输出 [rebalance_table_shards](#rebalance_table_shards) 的计划的分片运动而不执行它们。
虽然不太可能，但 \_ "获取重新平衡" \_ 表 \_ 分片 \_ 计划可以输出略微不同的计划，而不是 \_ 使用相同的参数进行重新平衡表 \_ 分片调用。 它们不会同时执行，因此有关服务器组的事实数据（ \- 例如，磁盘空间） \- 可能会因调用而异。

#### <a name="arguments"></a>参数

与重新平衡表分片的参数相同 \_ \_ ：关系、阈值、max \_ 分片 \_ 移动、排除 \_ 分片 \_ 列表和仅排出 \_ 。 有关参数的含义，请参阅该函数的文档。

#### <a name="return-value"></a>返回值

包含这些列的元组：

-   **表 \_ 名**：要移动其分片的表
-   **shardid**：相关分片
-   **分片 \_ 大小**：大小（字节）
-   未**用：源**节点的主机名
-   **sourceport**：源节点的端口
-   **targetname**：目标节点的主机名
-   **targetport**：目标节点的端口

### <a name="get_rebalance_progress"></a>获取重新 \_ 平衡 \_ 进度

分片重新平衡开始后，该 `get_rebalance_progress()` 函数将列出所涉及的每个分片的进度。 它监视由计划和执行的移动 `rebalance_table_shards()` 。

#### <a name="arguments"></a>参数

不适用

#### <a name="return-value"></a>返回值

包含这些列的元组：

-   **sessionid**：重新平衡监视器的 Postgres PID
-   **表 \_ 名**：要移动其分片的表
-   **shardid**：相关分片
-   **分片 \_ 大小**：大小（字节）
-   未**用：源**节点的主机名
-   **sourceport**：源节点的端口
-   **targetname**：目标节点的主机名
-   **targetport**：目标节点的端口
-   **进度**： 0 = 正在等待移动;1 = 正在移动;2 = 完成

#### <a name="example"></a>示例

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ 添加重新 \_ 平衡 \_ 策略

向 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) 追加行。

#### <a name="arguments"></a>参数

有关这些参数的详细信息，请参阅中的相应列值 `pg_dist_rebalance_strategy` 。

**name：** 新策略的标识符

**分片 \_ cost \_ 函数：** 标识用于确定 \" \" 每个分片的开销的函数

**节点 \_ 容量 \_ 函数：** 标识用于测量节点容量的函数

** \_ \_ \_ 节点 \_ 函数上允许的分片：** 标识函数，该函数确定哪些分片可放置在哪些节点上

**默认 \_ 阈值：** 一个浮点阈值，用于调整在节点间平衡累积分片开销的精度

**最小 \_ 阈值：** (可选) 保存重新平衡 \_ 表 \_ 分片 ( # A3 的阈值参数所允许的最小值的 "保护" 列。 其默认值为0

#### <a name="return-value"></a>返回值

不适用

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ 设置 \_ 默认重新 \_ 平衡 \_ 策略

更新 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 表，将其参数命名的策略更改为在重新平衡分片时选择的默认策略。

#### <a name="arguments"></a>参数

**名称：** pg \_ dist 重新 \_ 平衡 \_ 策略中的策略的名称

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus \_ 远程 \_ 连接 \_ 统计信息

Citus \_ 远程 \_ 连接 \_ 统计 ( # A1 函数显示每个远程节点的活动连接数。

#### <a name="arguments"></a>参数

不适用

#### <a name="example"></a>示例

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>主 \_ 排出 \_ 节点

Master \_ 排出 \_ 节点 ( # A1 函数将分片移出指定节点，并移到 `shouldhaveshards` 在 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中设置为 true 的其他节点上。 在从服务器组删除节点并关闭节点的物理服务器之前调用函数。

#### <a name="arguments"></a>参数

**nodename：** 要排出的节点的主机名名称。

**nodeport：** 要排出的节点的端口号。

**分片 \_ 传输 \_ 模式：** (可选) 指定复制方法，是使用 PostgreSQL 逻辑复制还是跨辅助角色复制命令。 可能的值为：

> -   `auto`：如果可以进行逻辑复制，则需要副本标识，否则使用旧行为 (例如，对于分片 repair，PostgreSQL 9.6) 。 这是默认值。
> -   `force_logical`：使用逻辑复制，即使该表没有副本标识。 在复制过程中对表的任何并发 update/delete 语句都将失败。
> -   `block_writes`：对缺少主键或副本标识的表使用复制 (阻止写入) 。

重新**平衡 \_ 策略：** (可选) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)中策略的名称。
如果省略此参数，则函数将选择默认策略，如表中所示。

#### <a name="return-value"></a>返回值

不适用

#### <a name="example"></a>示例

下面是在标准 PostgreSQL 端口上删除单个节点 (例如 "10.0.0.1"）的典型步骤) ：

1.  排出节点。

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  等待命令完成

3.  删除节点

当排出多个节点时，建议改为使用 [rebalance_table_shards](#rebalance_table_shards) 。 这样做可使超大规模 (Citus) 提前计划并将分片移动到最小次数。

1.  为要删除的每个节点运行：

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  用[rebalance_table_shards](#rebalance_table_shards)一次释放所有这些文件

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  等待耗尽重新平衡完成

4.  删除节点

### <a name="replicate_table_shards"></a>复制 \_ 表 \_ 分片

复制 \_ 表 \_ 分片 ( # A1 函数将复制给定表中复制的分片。 函数首先计算分片复制的列表和可从其提取用于复制的位置。 然后，该函数将复制这些分片，并更新相应的分片元数据以反映副本。

#### <a name="arguments"></a>参数

**表 \_ 名称：** 需要复制其分片的表的名称。

**分片 \_ 复制 \_ 因子：** (可选) 要为每个分片实现的所需复制因子。

**最大 \_ 分片 \_ 副本：** (可选) 要复制的最大分片数，以达到所需的复制因子。

**排除的 \_ 分片 \_ 列表：** 在复制操作过程中不应复制的分片 (可选) 标识符。

#### <a name="return-value"></a>返回值

不可用

#### <a name="examples"></a>示例

下面的示例将尝试将 github events 表的分片复制 \_ 到分片 \_ 复制 \_ 因子。

```postgresql
SELECT replicate_table_shards('github_events');
```

此示例将使用最多10个分片副本，尝试将 github 事件表的分片导入 \_ 到所需的复制因子。 Rebalancer 会在其尝试中复制最多10个分片，以达到所需的复制因子。

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>\_将租户隔离 \_ 到 \_ 新 \_ 分片

此函数创建一个新的分片，用于保存分布列中具有特定单个值的行。 这对于多租户超大规模 (Citus) 用例特别有用，其中，大型租户可单独放置在其自己的分片上，并最终位于其自身的物理节点上。

#### <a name="arguments"></a>参数

**表 \_ 名：** 要获取新分片的表的名称。

**租户 \_ id：** 将分配给新分片的分布列的值。

**cascade \_ 选项：** 当设置为时， \" (可选) CASCADE， \" 还会将分片与当前表的 [归置组](concepts-hyperscale-colocation.md)中的所有表隔离开来。

#### <a name="return-value"></a>返回值

**分片 \_ id：** 函数返回分配给新创建的分片的唯一 id。

#### <a name="examples"></a>示例

创建新的分片来保存租户135的 lineitems：

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>后续步骤

* 本文中的许多函数修改系统 [元数据表](reference-hyperscale-metadata.md)
* [服务器参数](reference-hyperscale-parameters.md) 自定义一些函数的行为
