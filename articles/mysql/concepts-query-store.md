---
title: Azure Database for MySQL 中的查询存储
description: 了解 Azure Database for MySQL 中的查询存储功能，以帮助您跟踪一段时间内的性能。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c8891fc96e3e511e4127b4e114a45b5a865cf8eb
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603030"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>使用查询存储监视 Azure Database for MySQL 的性能

**适用于：** Azure Database for MySQL 5。7

使用 Azure Database for MySQL 中的查询存储功能可以跟踪一段时间内的查询性能。 通过帮助快速查找运行时间最长且资源最密集的查询，查询存储可简化性能故障排除。 查询存储自动捕获查询和运行时统计信息的历史记录，并保留它们以供查看。 它按时间范围分隔数据，以便可以查看数据库使用模式。 所有用户、数据库和查询的数据都存储在 Azure Database for MySQL 实例上的 **mysql** 架构数据库中。

## <a name="common-scenarios-for-using-query-store"></a>使用查询存储的常见场景

可以在许多场景中使用查询存储，包括：

- 检测回归查询
- 确定在给定时间范围内执行查询的次数
- 比较跨时间范围查询的平均执行时间，以查看较大的增量

## <a name="enabling-query-store"></a>启用查询存储

查询存储是一项选择加入功能，因此默认情况下它在服务器上未处于活动状态。 对于给定服务器上的所有数据库，查询存储处于全局启用或禁用状态，且无法为每个数据库打开或关闭它。

### <a name="enable-query-store-using-the-azure-portal"></a>使用 Azure 门户启用查询存储

1. 登录到 Azure 门户，选择你的 Azure Database for MySQL 服务器。
1. 在菜单的“设置”部分中选择“服务器参数”。
1. 搜索 query_store_capture_mode 参数。
1. 将值设置为 "全部" 并**保存**。

若要在查询存储中启用等待统计信息，请执行以下操作：

1. 搜索 query_store_wait_sampling_capture_mode 参数。
1. 将值设置为 "全部" 并**保存**。

第一批数据可在 mysql 数据库中最长保留 20 分钟。

## <a name="information-in-query-store"></a>查询存储中的信息

查询存储有两个存储：

- 用于保存查询执行统计信息的运行时统计信息存储。
- 用于保存等待统计信息的等待统计信息存储。

为尽量减少空间使用量，运行时统计信息存储中的运行时执行统计信息在一个固定的、可配置的时间范围内聚合。 通过查询这些查询存储视图，这些存储中的信息是可见的。

以下查询返回有关查询存储中的查询的信息：

```sql
SELECT * FROM mysql.query_store;
```

或此等待统计信息的查询：

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>查找等待查询

> [!NOTE]
> 不应在高峰工作负荷时段内启用等待统计信息，也不能无限期地开启。 <br>对于运行 CPU 使用率较高的工作负荷或使用较低 Vcore 配置的服务器上的工作负荷，请在启用等待统计信息时小心。 不应无限期地打开它。 

等待事件类型按相似性将不同的等待事件组合到存储桶中。 查询存储提供等待事件类型、特定等待事件名称和有争议的查询。 能将此等待信息与查询运行时统计信息相关联，意味着可更深入地了解有助于查询性能特征的因素。

以下是一些示例，说明如何使用查询存储中的等待统计信息获得有关工作负载的更多见解：

| **观测** | **操作** |
|---|---|
|高锁定等待 | 检查受影响查询的查询文本，并确定目标实体。 在查询存储中查找修改同一实体的其他查询，这些查询经常执行和/或持续很长时间。 确定这些查询后，请考虑更改应用程序逻辑以提高并发性，或使用限制较少的隔离级别。 |
|高缓冲 IO 等待 | 在查询存储中查找具有大量物理读取的查询。 如果它们匹配具有高 IO 等待的查询，考虑在基础实体上引入索引，以便进行搜索而不是扫描。 这将最小化查询的 IO 开销。 检查门户中服务器的“性能建议”，以查看是否存在可优化查询的此服务器的索引建议。 |
|高内存等待 | 在查询存储中查找消耗内存最多的查询。 这些查询可能会延迟受影响查询的进度。 检查门户中服务器的“性能建议”，以查看是否存在可优化这些查询的索引建议。|

## <a name="configuration-options"></a>配置选项

启用查询存储时，它会在 15 分钟的聚合时段内保存数据，每个时段最多可存储 500 个不同查询。

以下选项可用于配置查询存储参数。

| **Parameter** | **说明** | **默认** | **范围** |
|---|---|---|---|
| query_store_capture_mode | 根据值打开/关闭查询存储功能。 注意：如果 performance_schema 为 OFF，则启用 query_store_capture_mode 会启用 performance_schema，并将启用此功能所需的性能架构检测子集。 | ALL | NONE、ALL |
| query_store_capture_interval | 查询存储捕获时间间隔，以分钟为单位。 允许指定聚合查询指标的时间间隔 | 15 | 5 - 60 |
| query_store_capture_utility_queries | 打开或关闭捕获系统中正在执行的所有实用工具查询的功能。 | 否 | YES、NO |
| query_store_retention_period_in_days | 在查询存储中保留数据的时限，以天为单位。 | 7 | 1 - 30 |

以下选项专用于等待统计信息。

| **Parameter** | **说明** | **默认** | **范围** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 允许打开/关闭等待统计信息。 | 无 | NONE、ALL |
| query_store_wait_sampling_frequency | 更改等待采样的频率，以秒为单位。 5 到 300 秒。 | 30 | 5-300 |

> [!NOTE]
> 目前，**query_store_capture_mode** 将取代此配置，这意味着，**query_store_capture_mode** 和 **query_store_wait_sampling_capture_mode** 都必须启用 ALL，才能正常查询等待统计信息。 如果关闭 **query_store_capture_mode**，则等待统计信息也会关闭，因为等待统计信息利用已启用的 performance_schema，以及查询存储捕获的 query_text。

使用  [Azure 门户](howto-server-parameters.md)或  Azure CLI [ 获取或设置参数的不同值。](howto-configure-server-parameters-using-cli.md) 

## <a name="views-and-functions"></a>视图和函数

使用以下视图和函数查看并管理查询存储。 [选择特权公共角色](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql)中的任何人都可使用这些视图来查看查询存储中的数据。 这些视图仅在**mysql**数据库中可用。

删除文本和常数后，通过查看查询的结构来规范化查询。 如果除文本值之外两个查询相同，则它们将具有相同的哈希值。

### <a name="mysqlquery_store"></a>mysql.query_store

此视图返回查询存储中的所有数据。 每个不同的数据库 ID、用户 ID 和查询 ID 都有一行。

| **名称** | 数据类型 | **IS_NULLABLE** | **说明** |
|---|---|---|---|
| `schema_name`| varchar(64) | 否 | 架构名称 |
| `query_id`| bigint(20) | 否| 为特定查询生成的唯一 ID，如果在不同的架构中执行同一个查询，将生成新的 ID |
| `timestamp_id` | timestamp| 否| 执行查询时的时间戳。 此值基于 query_store_interval 配置|
| `query_digest_text`| longtext| 否| 删除所有文本后的规范化查询文本|
| `query_sample_text` | longtext| 否| 首次出现的包含文本的实际查询|
| `query_digest_truncated` | bit| 是| 查询文本是否已截断。 如果查询超过 1 KB，则值为 Yes|
| `execution_count` | bigint(20)| 否| 针对此时间戳 ID/在配置的间隔时间段内执行该查询的次数|
| `warning_count` | bigint(20)| 否| 此查询在该时间间隔内生成的警告数|
| `error_count` | bigint(20)| 否| 此查询在该时间间隔内生成的错误数|
| `sum_timer_wait` | double| 是| 此查询在该时间间隔内的总执行时间|
| `avg_timer_wait` | double| 是| 此查询在该时间间隔内的平均执行时间|
| `min_timer_wait` | double| 是| 此查询的最小执行时间|
| `max_timer_wait` | double| 是| 最大执行时间|
| `sum_lock_time` | bigint(20)| 否| 在此时间范围内对此查询执行的所有锁花费的总时间|
| `sum_rows_affected` | bigint(20)| 否| 受影响的行数|
| `sum_rows_sent` | bigint(20)| 否| 发送到客户端的行数|
| `sum_rows_examined` | bigint(20)| 否| 检查的行数|
| `sum_select_full_join` | bigint(20)| 否| 完整联接的数目|
| `sum_select_scan` | bigint(20)| 否| select 扫描数 |
| `sum_sort_rows` | bigint(20)| 否| 排序的行数|
| `sum_no_index_used` | bigint(20)| 否| 查询未使用任何索引的次数|
| `sum_no_good_index_used` | bigint(20)| 否| 查询执行引擎未使用任何适当索引的次数|
| `sum_created_tmp_tables` | bigint(20)| 否| 创建的临时表总数|
| `sum_created_tmp_disk_tables` | bigint(20)| 否| 在磁盘中创建的临时表总数（生成 I/O）|
| `first_seen` | timestamp| 否| 在聚合时段发生第一次查询的时间 (UTC)|
| `last_seen` | timestamp| 否| 在此聚合时段发生最后一次查询的时间 (UTC)|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

此视图返回查询存储中的等待事件数据。 每个不同的数据库 ID、用户 ID、查询 ID 和事件都有一行。

| **名称**| 数据类型 | **IS_NULLABLE** | **说明** |
|---|---|---|---|
| `interval_start` | timestamp | 否| 间隔开始时间（15 分钟增量）|
| `interval_end` | timestamp | 否| 间隔结束时间（15 分钟增量）|
| `query_id` | bigint(20) | 否| 针对规范化查询生成的唯一 ID（从查询存储）|
| `query_digest_id` | varchar(32) | 否| 删除所有文本后的规范化查询文本（从查询存储） |
| `query_digest_text` | longtext | 否| 首次出现的包含文本的实际查询（从查询存储） |
| `event_type` | varchar(32) | 否| 等待事件的类别 |
| `event_name` | varchar(128) | 否| 等待事件的名称 |
| `count_star` | bigint(20) | 否| 在查询间隔内采样的等待事件数 |
| `sum_timer_wait_ms` | double | 否| 此查询在该时间间隔内的总等待时间（以毫秒为单位） |

### <a name="functions"></a>函数

| **名称**| **说明** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 清除给定时间戳之前的所有查询存储数据 |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 清除给定时间戳之前的所有等待事件数据 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 清除其过期时间在给定时间戳之前的建议 |

## <a name="limitations-and-known-issues"></a>限制和已知问题

- 如果 MySQL 服务器启用了参数 `default_transaction_read_only`，查询存储将无法捕获数据。
- 如果遇到较长的 Unicode 查询（\>= 6000 个字节），查询存储功能可能会中断。
- 等待统计信息的保留期为 24 小时。
- 等待统计信息使用样本来捕获一小部分事件。 可以使用参数 `query_store_wait_sampling_frequency` 修改频率。

## <a name="next-steps"></a>后续步骤

- 了解有关[查询性能见解](concepts-query-performance-insight.md)的详细信息
