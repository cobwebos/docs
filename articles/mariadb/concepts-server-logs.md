---
title: 慢速查询日志-Azure Database for MariaDB
description: 介绍了 Azure Database for MariaDB 中提供的日志，以及用于启用不同日志记录级别的可用参数。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: b38838c20e4ab18b64cabcb2749ec39163f1b52d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515048"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的查询日志缓慢
在 Azure Database for MariaDB 中，慢查询日志可供用户使用。 不支持访问事务日志。 可以使用慢查询日志来查明性能瓶颈以进行故障排除。

有关慢查询日志的详细信息，请参阅[慢查询日志](https://mariadb.com/kb/en/library/slow-query-log-overview/)的 MariaDB 文档。

## <a name="access-slow-query-logs"></a>访问慢查询日志
您可以使用 Azure 门户和 Azure CLI 来列出和下载 Azure Database for MariaDB 慢速查询日志。

在 Azure 门户中，选择 Azure Database for MariaDB 服务器。 在“监视”标题下，选择“服务器日志”页面。

有关 Azure CLI 的详细信息，请参阅[使用 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-cli.md)。

同样，你可以使用诊断日志通过管道将日志传递给 Azure Monitor。 有关详细信息，请参阅[下文](concepts-server-logs.md#diagnostic-logs)。

## <a name="log-retention"></a>日志保留期
日志从其创建时开始算起，最多可保留七天。 如果可用日志的总大小超过了 7 GB，则会删除最旧的文件，直到有空间可用。

日志每 24 小时或每 7 GB 轮换一次（以先达到的条件为准）。

## <a name="configure-slow-query-logging"></a>配置慢查询日志记录
默认情况下，慢查询日志被禁用。 若要启用它，请将 slow_query_log 设置为 ON。

可以调整的其他参数包括：

- **long_query_time**：如果某个查询花费的时间超过了 long_query_time（以秒为单位），则会记录该查询。 默认为 10 秒。
- **log_slow_admin_statements**：如果为 ON，则会在写入到 slow_query_log 的语句中包括管理性语句，例如 ALTER_TABLE 和 ANALYZE_TABLE。
- **log_queries_not_using_indexes**：确定是否将未使用索引的查询记录到 slow_query_log 中
- **log_throttle_queries_not_using_indexes**：此参数限制可以写入到慢查询日志的非索引查询的数目。 当 log_queries_not_using_indexes 设置为 ON 时，此参数生效。
- **log_output**：如果为 "File"，则允许将慢速查询日志同时写入本地服务器存储和 Azure Monitor 诊断日志。 如果为 "无"，则慢速查询日志将仅写入 Azure Monitor 诊断日志。 

> [!IMPORTANT]
> 如果表未编制索引，则将 `log_queries_not_using_indexes` 和 `log_throttle_queries_not_using_indexes` 参数设置为 ON 可能会影响 MariaDB 性能，因为对这些非索引表运行的所有查询都将写入慢查询日志。<br><br>
> 如果你计划长时间记录缓慢查询，建议将 `log_output` 设置为 "None"。 如果设置为 "File"，则这些日志将写入本地服务器存储，并可能会影响 MariaDB 的性能。 

有关慢查询日志参数的完整说明，请参阅 MariaDB [慢查询日志文档](https://mariadb.com/kb/en/library/slow-query-log-overview/)。

## <a name="diagnostic-logs"></a>诊断日志
Azure Database for MariaDB 与 Azure Monitor 诊断日志集成。 在 MariaDB 服务器上启用慢查询日志后，可以选择让其发出 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解如何启用诊断日志，请参阅[诊断日志文档](../azure-monitor/platform/platform-logs-overview.md)中的操作说明部分。

> [!IMPORTANT]
> 服务器日志的此诊断功能仅在 "常规用途" 和 "内存优化"[定价层](concepts-pricing-tiers.md)中提供。

下表介绍了每个日志中的内容。 包括的字段以及它们的出现顺序可能有所不同，具体取决于输出方法。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 服务器的名称 |
| `start_time_t` [UTC] | 查询开始时间 |
| `query_time_s` | 查询执行所用的总时间 |
| `lock_time_s` | 查询锁定的总时间 |
| `user_host_s` | 用户名 |
| `rows_sent_s` | 发送的行数 |
| `rows_examined_s` | 检查的行数 |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | 插入 ID |
| `sql_text_s` | 完整的查询 |
| `server_id_s` | 服务器 ID |
| `thread_id_s` | 线程 ID |
| `\_ResourceId` | 资源 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>在 Azure Monitor 日志中分析日志

将慢速查询日志传输到通过诊断日志 Azure Monitor 日志后，可以进一步分析慢查询。 下面是一些帮助您入门的示例查询。 请确保用您的服务器名称更新下面的。

- 特定服务器上的查询超过10秒

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 列出特定服务器上的前5个最长查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 在特定服务器上按最小值、最大值、平均值和标准偏差查询时间汇总慢速查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 在特定服务器上绘制慢速查询分布图

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 在启用了诊断日志的所有 MariaDB 服务器上显示超过10秒的查询

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>后续步骤
- [如何从 Azure 门户配置慢查询日志](howto-configure-server-logs-portal.md)
- [如何从 Azure CLI 配置慢查询日志](howto-configure-server-logs-cli.md)
