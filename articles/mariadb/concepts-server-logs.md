---
title: Azure Database for MariaDB 的服务器日志
description: 介绍了 Azure Database for MariaDB 中提供的日志，以及用于启用不同日志记录级别的可用参数。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 7a517be49a249b0b73c901137381bd05946aa4cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065702"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>慢查询 Azure Database for 中日志 MariaDB
在 Azure Database for MariaDB 中，慢查询日志可供用户使用。 不支持访问事务日志。 可以使用慢查询日志来查明性能瓶颈以进行故障排除。

有关慢查询日志的详细信息，请参阅[慢查询日志](https://mariadb.com/kb/en/library/slow-query-log-overview/)的 MariaDB 文档。

## <a name="access-slow-query-logs"></a>访问慢查询日志
可以列出和下载 Azure Database for MariaDB 慢查询日志使用 Azure 门户和 Azure CLI。

在 Azure 门户中，选择 Azure Database for MariaDB 服务器。 在“监视”标题下，选择“服务器日志”页面。  

有关 Azure CLI 的详细信息，请参阅[使用 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-cli.md)。

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

有关慢查询日志参数的完整说明，请参阅 MariaDB [慢查询日志文档](https://mariadb.com/kb/en/library/slow-query-log-overview/)。

## <a name="diagnostic-logs"></a>诊断日志
Azure Database for MariaDB 集成了 Azure Monitor 诊断日志。 在 MariaDB 服务器上启用慢查询日志后，可以选择将它们发送到 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解如何启用诊断日志，请参阅[诊断日志文档](../azure-monitor/platform/diagnostic-logs-overview.md)中的操作说明部分。

> [!IMPORTANT]
> 服务器日志的此诊断功能仅适用于“常规用途”和“内存优化”的[定价层](concepts-pricing-tiers.md)。

下表介绍了每个日志中的内容。 根据输出方法，包含的字段以及这些字段出现的顺序可能会有所不同。

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

## <a name="next-steps"></a>后续步骤
- [如何通过 Azure 门户配置和访问服务器日志](howto-configure-server-logs-portal.md)。
