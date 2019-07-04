---
title: Azure Database for MySQL 中的查询存储
description: 本指南介绍了用于 MySQL 的 Azure 数据库中的查询存储功能
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a24bba0786201f4ea1d1be431107f7bfe26a2a8f
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461724"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>有关使用查询存储的 MySQL 性能监视 Azure 数据库

**适用于：**  Azure Database for MySQL 5.7

> [!NOTE]
> 查询存储处于预览状态。

Azure Database for MySQL 中的查询存储功能提供了一种方法来跟踪一段时间内的查询性能。 通过帮助快速查找运行时间最长且资源最密集的查询，查询存储可简化性能故障排除。 查询存储自动捕获查询和运行时统计信息的历史记录，并保留它们以供查看。 它按时间范围分隔数据，以便可以查看数据库使用模式。 为所有用户、 数据库和查询数据存储在**mysql**架构数据库在 Azure Database for MySQL 实例。

## <a name="common-scenarios-for-using-query-store"></a>使用查询存储的常见方案

查询存储可在多种方案，其中包括：

- 检测回归的查询
- 确定在给定时间范围内执行查询的次数
- 比较跨时间范围查询的平均执行时间，以查看较大的增量

## <a name="enabling-query-store"></a>启用查询存储

查询存储是一项选择加入功能，因此默认情况下它在服务器上未处于活动状态。 查询存储是启用还是禁用给定服务器上的所有数据库的全局和不能打开或关闭每个数据库。

### <a name="enable-query-store-using-the-azure-portal"></a>使用 Azure 门户启用查询存储

1. 登录到 Azure 门户并选择 Azure Database for MySQL 服务器。
1. 选择 **服务器参数** 中 **设置** 菜单的部分。
1. 搜索 query_store_capture_mode 参数。
1. 将值设置为所有并 **保存**。

若要在查询存储中启用等待统计信息，请执行以下操作：

1. 搜索 query_store_wait_sampling_capture_mode 参数。
1. 将值设置为所有并 **保存**。

允许最多 20 分钟的数据保留在 mysql 数据库中的第一批。

## <a name="information-in-query-store"></a>查询存储中的信息

查询存储有两个存储：

- 运行时统计信息将存储用于永久保存的查询执行统计信息。
- 用于永久保存的等待统计信息存储等待统计信息。

为了尽量减少空间使用情况，在运行时统计信息存储区中的运行时执行统计信息聚合在固定的可配置时间窗口上。 通过查询这些查询存储视图，这些存储中的信息是可见的。

以下查询返回有关查询存储中的查询的信息：

```sql
SELECT * FROM mysql.query_store;
```

或此查询的等待统计信息：

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>查找等待查询

等待事件类型按相似性将不同的等待事件组合到存储桶中。 查询存储提供等待事件类型、特定等待事件名称和有争议的查询。 能将此等待信息与查询运行时统计信息相关联，意味着可更深入地了解有助于查询性能特征的因素。

以下是一些示例，说明如何使用查询存储中的等待统计信息获得有关工作负载的更多见解：

| **观测** | **Action** |
|---|---|
|高锁定等待 | 检查受影响查询的查询文本，并确定目标实体。 在查询存储中查找修改同一实体的其他查询，这些查询经常执行和/或持续很长时间。 确定这些查询后，请考虑更改应用程序逻辑以提高并发性，或使用限制较少的隔离级别。 |
|高缓冲 IO 等待 | 在查询存储中查找具有大量物理读取的查询。 如果它们匹配与 IO 等待时间较长的查询，请考虑引入关于基础实体，执行索引查找而不是扫描。 这将最小化查询的 IO 开销。 检查 **性能建议** 门户以查看是否存在此服务器将优化的查询的索引建议中的服务器。 |
|高内存等待 | 在查询存储中查找消耗内存最多的查询。 这些查询可能会延迟受影响查询的进度。 检查 **性能建议** 门户以查看是否存在会优化这些查询的索引建议中的服务器。|

## <a name="configuration-options"></a>配置选项

启用查询存储时，它会在 15 分钟的聚合时段内保存数据，每个时段最多可存储 500 个不同查询。

以下选项可用于配置查询存储参数。

| **Parameter** | **说明** | **默认** | **范围** |
|---|---|---|---|
| query_store_capture_mode | 打开开/关基于值的查询存储功能。 请注意:如果 performance_schema 为 OFF，开启 query_store_capture_mode 将启用 performance_schema 和此功能所需的性能架构 instruments 的子集。 | ALL | 无、 所有 |
| query_store_capture_interval | 查询存储捕获时间间隔以分钟为单位。 允许指定在其中查询指标的聚合的时间间隔 | 15 | 5 - 60 |
| query_store_capture_utility_queries | 打开 ON 或 OFF，捕获系统中执行的所有实用程序查询。 | 否 | 是的否 |
| query_store_retention_period_in_days | 时间范围内保留查询存储中的数据的天数。 | 7 | 1 - 30 |

以下选项专用于等待统计信息。

| **Parameter** | **说明** | **默认** | **范围** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 允许启用 ON / OFF 等待统计信息。 | 无 | 无、 所有 |
| query_store_wait_sampling_frequency | 等待采样以秒为单位的更改频率。 5 到 300 秒。 | 30 | 5-300 |

> [!NOTE]
> 目前**query_store_capture_mode**取代了此配置中，这意味着同时**query_store_capture_mode**并**query_store_wait_sampling_capture_mode**必须启用到所有等待统计信息，若要运行的。 如果**query_store_capture_mode**处于关闭状态，则等待统计信息会关闭也由于等待统计信息还可以利用已启用，performance_schema 和查询存储捕获 query_text。

使用 [Azure 门户](howto-server-parameters.md) 或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 要获取或设置某个参数的不同值。

## <a name="views-and-functions"></a>视图和函数

使用以下视图和函数查看并管理查询存储。 中的任何人[选择权限公共角色](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql)可以使用这些视图以查看查询存储中的数据。 这些视图中才有**mysql** 数据库。

删除文本和常数后，通过查看查询的结构来规范化查询。 如果除文本值之外两个查询相同，则它们将具有相同的哈希值。

### <a name="mysqlquerystore"></a>mysql.query_store

此视图返回查询存储中的所有数据。 每个不同的数据库 ID、用户 ID 和查询 ID 都有一行。

| **Name** | 数据类型  | **IS_NULLABLE** | **说明** |
|---|---|---|---|
| `schema_name`| varchar(64) | 否 | 架构的名称 |
| `query_id`| bigint(20) | 否| 将生成生成特定查询，如果在不同架构中，新的 ID 相同的查询执行的唯一 ID |
| `timestamp_id` | timestamp| 否| 在其中执行查询的时间戳。 这基于 query_store_interval 配置|
| `query_digest_text`| longtext| 否| 删除所有文本之后的规范化的查询文本|
| `query_sample_text` | longtext| 否| 首次出现时的实际查询的文本|
| `query_digest_truncated` | bit| 是| 是否已对查询文本被截断。 如果查询超过 1 KB 值将为是|
| `execution_count` | bigint(20)| 否| 此时间戳 id / 配置的间隔时间段内执行查询的次数|
| `warning_count` | bigint(20)| 否| 在内部过程生成此查询的警告数|
| `error_count` | bigint(20)| 否| 此查询时间间隔内生成的错误数|
| `sum_timer_wait` | double| 是| 此查询间隔期间的总执行时间|
| `avg_timer_wait` | double| 是| 此查询间隔期间的平均执行时间|
| `min_timer_wait` | double| 是| 此查询的最小执行时间|
| `max_timer_wait` | double| 是| 最长执行时间|
| `sum_lock_time` | bigint(20)| 否| 为此时间范围内，此查询执行的所有锁定所用的时间总量|
| `sum_rows_affected` | bigint(20)| 否| 受影响行数|
| `sum_rows_sent` | bigint(20)| 否| 发送到客户端的行数|
| `sum_rows_examined` | bigint(20)| 否| 检查的行数|
| `sum_select_full_join` | bigint(20)| 否| 完整联接的数目|
| `sum_select_scan` | bigint(20)| 否| 选择的扫描数 |
| `sum_sort_rows` | bigint(20)| 否| 排序的行数|
| `sum_no_index_used` | bigint(20)| 否| 当查询未使用任何索引的次数|
| `sum_no_good_index_used` | bigint(20)| 否| 查询执行引擎未使用任何合适的索引的次数|
| `sum_created_tmp_tables` | bigint(20)| 否| 创建的临时表的总数|
| `sum_created_tmp_disk_tables` | bigint(20)| 否| （生成 I/O） 的磁盘中创建的临时表的总数|
| `first_seen` | timestamp| 否| 第一个匹配项 (UTC) 的查询的聚合窗口期|
| `last_seen` | timestamp| 否| 最后一个匹配项 (UTC) 的查询在此聚合时段|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

此视图返回查询存储中的等待事件数据。 每个不同的数据库 ID、用户 ID、查询 ID 和事件都有一行。

| **Name**| 数据类型  | **IS_NULLABLE** | **说明** |
|---|---|---|---|
| `interval_start` | timestamp | 否| 间隔 （15 分钟增量） 开始日期|
| `interval_end` | timestamp | 否| 间隔 （15 分钟增量） 结束|
| `query_id` | bigint(20) | 否| 生成的唯一 ID （从查询存储区） 的规范化查询|
| `query_digest_id` | varchar(32) | 否| 删除了 （从查询存储区） 的所有文本规范化的查询文本 |
| `query_digest_text` | longtext | 否| 首次出现时的实际查询的文本 （从查询存储区） |
| `event_type` | varchar(32) | 否| 等待事件类别 |
| `event_name` | varchar(128) | 否| 等待事件的名称 |
| `count_star` | bigint(20) | 否| 查询间隔期间采样的等待事件数 |
| `sum_timer_wait_ms` | double | 否| 总等待时间 （以毫秒为单位） 的此查询在此时间间隔 |

### <a name="functions"></a>函数

| **Name**| **说明** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 将给定的时间戳之前的所有查询存储数据中都清除 |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 清除所有等待给定的时间戳之前的事件数据 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 清除其过期时间是在给定的时间戳之前的建议 |

## <a name="limitations-and-known-issues"></a>限制和已知问题

- 如果 MySQL 服务器参数`default_transaction_read_only`，查询存储无法捕获数据。
- 如果遇到较长的 Unicode 查询查询存储功能可能会中断 (\>= 6000 个字节)。
- 等待统计信息的保留期为 24 小时。
- 等待统计信息使用示例 ti 捕获的事件的一部分。 可以使用参数修改频率`query_store_wait_sampling_frequency`。

## <a name="next-steps"></a>后续步骤

- 详细了解[查询性能见解](concepts-query-performance-insight.md)