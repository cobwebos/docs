---
title: Azure Database for MySQL 的服务器日志
description: 介绍了 Azure Database for MySQL 中提供的日志，以及用于启用不同日志记录级别的可用参数。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b1b5dffed0a82e3e3c91efd4024bafdc64f0d3d2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119031"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的服务器日志
在 Azure Database for MySQL 中，慢查询日志可供用户使用。 不支持访问事务日志。 可以使用慢查询日志来查明性能瓶颈以进行故障排除。 

有关 MySQL 慢查询日志的详细信息，请参阅 MySQL 参考手册中的[慢查询日志部分](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="access-server-logs"></a>访问服务器日志
可以使用 Azure 门户和 Azure CLI 列出和下载 Azure Database for MySQL 服务器日志。

在 Azure 门户中，选择 Azure Database for MySQL 服务器。 在“监视”标题下，选择“服务器日志”页面。

有关 Azure CLI 的详细信息，请参阅[使用 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-in-cli.md)。

## <a name="log-retention"></a>日志保留期
日志从其创建时开始算起，最多可保留七天。 如果可用日志的总大小超过了 7 GB，则会删除最旧的文件，直到有空间可用。 

日志每 24 小时或每 7 GB 轮换一次（以先达到的条件为准）。


## <a name="configure-logging"></a>配置日志记录 
默认情况下，慢查询日志被禁用。 若要启用它，请将 slow_query_log 设置为 ON。

可以调整的其他参数包括：

- **long_query_time**：如果某个查询花费的时间超过了 long_query_time（以秒为单位），则会记录该查询。 默认为 10 秒。
- **log_slow_admin_statements**：如果为 ON，则会在写入到 slow_query_log 的语句中包括管理性语句，例如 ALTER_TABLE 和 ANALYZE_TABLE。
- **log_queries_not_using_indexes**：确定是否将未使用索引的查询记录到 slow_query_log 中
- **log_throttle_queries_not_using_indexes**：此参数限制可以写入到慢查询日志的非索引查询的数目。 当 log_queries_not_using_indexes 设置为 ON 时，此参数生效。

有关慢查询日志参数的完整说明，请参阅 MySQL [慢查询日志文档](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="diagnostic-logs"></a>诊断日志
Azure Database for MySQL 集成了 Azure Monitor 诊断日志。 具有 MySQL 服务器上启用慢查询日志，您可以选择将它们发送至 Azure Monitor 日志、 事件中心或 Azure 存储。 若要详细了解如何启用诊断日志，请参阅[诊断日志文档](../azure-monitor/platform/diagnostic-logs-overview.md)中的操作说明部分。

> [!IMPORTANT]
> 此诊断功能的服务器日志中的常规用途和内存优化才[定价层](concepts-pricing-tiers.md)。

下表介绍了每个日志中的内容。 包括的字段以及它们的出现顺序可能有所不同，具体取决于输出方法。

| **属性** | **说明** |
|---|---|
| TenantId | 租户 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | 记录日志时的时间戳 (UTC) |
| Type | 日志类型。 始终是 `AzureDiagnostics` |
| SubscriptionId | 服务器所属的订阅的 GUID |
| resourceGroup | 服务器所属的资源组的名称 |
| ResourceProvider | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| ResourceType | `Servers` |
| ResourceId | 资源 URI |
| 资源 | 服务器的名称 |
| 类别 | `MySqlSlowLogs` |
| OperationName | `LogEvent` |
| Logical_server_name_s | 服务器的名称 |
| start_time_t [UTC] | 查询开始时间 |
| query_time_s | 查询执行所用的总时间 |
| lock_time_s | 查询锁定的总时间 |
| user_host_s | 用户名 |
| rows_sent_s | 发送的行数 |
| rows_examined_s | 检查的行数 |
| last_insert_id_s | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| insert_id_s | 插入 ID |
| sql_text_s | 完整的查询 |
| server_id_s | 服务器的 ID |
| thread_id_s | 线程 ID |
| \_ResourceId | 资源 URI |

## <a name="next-steps"></a>后续步骤
- [如何通过 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-in-cli.md)。
