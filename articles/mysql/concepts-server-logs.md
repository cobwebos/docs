---
title: "Azure Database for MySQL 的服务器日志 | Microsoft Docs"
description: "介绍了 Azure Database for MySQL 中提供的日志，以及用于启用不同日志记录级别的可用参数。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 13b30df82c1a6c4c45a621a1f7a40148a55a7648
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2017
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的服务器日志
在 Azure Database for MySQL 中，慢查询日志可供用户使用。 不支持访问事务日志。 可以使用慢查询日志来查明性能瓶颈以进行故障排除。 

有关 MySQL 慢查询日志的详细信息，请参阅 MySQL 参考手册中的[慢查询日志部分](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="access-server-logs"></a>访问服务器日志
可以使用 Azure 门户和 Azure CLI 列出和下载 Azure Database for MySQL 服务器日志。

在 Azure 门户中，选择 Azure Database for MySQL 服务器。 在“监视”标题下，选择“服务器日志”页面。

有关 Azure CLI 的详细信息，请参阅[使用 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-in-cli.md)。

## <a name="log-retention"></a>日志保留期
日志从其创建时开始算起，最多可保留七天。 如果可用日志的总大小超过了 7.5 GB，则会删除最旧的文件，直到有空间可用。 

日志每 24 小时或每 7.5 GB 轮换一次（以先达到的条件为准）。


## <a name="configure-logging"></a>配置日志记录 
默认情况下，慢查询日志被禁用。 若要启用它，请将 slow_query_log 设置为 ON。

可以调整的其他参数包括：

- **long_query_time**：如果某个查询花费的时间超过了 long_query_time（以秒为单位），则会记录该查询。 默认为 10 秒。
- **log_slow_admin_statements**：如果为 ON，则会在写入到 slow_query_log 的语句中包括管理性语句，例如 ALTER_TABLE 和 ANALYZE_TABLE。
- **log_queries_not_using_indexes**：确定是否将未使用索引的查询记录到 slow_query_log 中
- **log_throttle_queries_not_using_indexes**：此参数限制可以写入到慢查询日志的非索引查询的数目。 当 log_queries_not_using_indexes 设置为 ON 时，此参数生效。

有关慢查询日志参数的完整说明，请参阅 MySQL [慢查询日志文档](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="next-steps"></a>后续步骤
- [如何通过 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-in-cli.md)。
