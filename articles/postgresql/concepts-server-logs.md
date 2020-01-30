---
title: 日志-Azure Database for PostgreSQL-单服务器
description: 介绍 Azure Database for PostgreSQL-单服务器中的日志记录配置、存储和分析
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844932"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 单服务器中的日志
Azure Database for PostgreSQL 允许配置和访问 Postgres 的标准日志。 日志可用于识别、排除和修复配置错误和性能不佳。 可以配置和访问的日志记录信息包括错误、查询信息、autovacuum 记录、连接和检查点。 （对事务日志的访问不可用）。

审核日志记录通过 Postgres 扩展 pgaudit 提供。 若要了解详细信息，请访问[审核概念](concepts-audit.md)一文。


## <a name="configure-logging"></a>配置日志记录 
可以使用日志记录服务器参数在服务器上配置 Postgres 标准日志记录。 在每个 Azure Database for PostgreSQL 服务器上，默认情况下 `log_checkpoints` 和 `log_connections`。 还有一些其他参数，你可以调整它们来满足你的日志记录需求： 

![Azure Database for PostgreSQL - 日志记录参数](./media/concepts-server-logs/log-parameters.png)

若要了解有关 Postgres 日志参数的详细信息，请访问 Postgres 文档的 "[记录时间](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)" 和 "[记录内容](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)" 部分。 大多数（但不是全部） Postgres 日志记录参数都可在 Azure Database for PostgreSQL 中进行配置。

若要了解如何在 Azure Database for PostgreSQL 中配置参数，请参阅[门户文档](howto-configure-server-parameters-using-portal.md)或[CLI 文档](howto-configure-server-parameters-using-cli.md)。 

> [!NOTE]
> 配置大量日志（例如，语句日志记录）可以增加显著的性能开销。 

## <a name="access-log-files"></a>访问 .log 文件
Azure Database for PostgreSQL 中的默认日志格式为 .log。 此日志中的示例行如下所示：

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL 为 .log 文件提供短期存储位置。 新文件每1小时或 100 MB 开始一次，以先达到的条件为准。 在从 Postgres 发出日志时，会将日志追加到当前文件。  

您可以使用 `log_retention_period` 参数设置此短期日志存储的保持期。 默认值为 3 天；最大值为 7 天。 短期存储位置可以容纳多达 1 GB 的日志文件。 1 GB 后，将删除最旧的文件，而不考虑保留期，以便为新日志腾出空间。 

对于日志和日志分析的长期保留，你可以下载 .log 文件并将它们移动到第三方服务。 您可以使用[Azure 门户](howto-configure-server-logs-in-portal.md) [Azure CLI](howto-configure-server-logs-using-cli.md)下载文件。 此外，还可以配置 Azure Monitor 诊断设置，这些设置会自动将日志（JSON 格式）发送到长期位置。 在以下部分中了解有关此选项的详细信息。 

可以通过将参数 `logging_collector` 设置为 OFF，停止生成 .log 文件。 如果使用 Azure Monitor 诊断设置，则建议使用 "关闭日志文件"。 此配置将减少其他日志记录对性能的影响。

## <a name="diagnostic-logs"></a>诊断日志
Azure Database for PostgreSQL 与 Azure Monitor 诊断设置集成。 通过诊断设置，可以将 Postgres 日志以 JSON 格式发送到 Azure Monitor 日志进行分析和警报、用于流式处理的事件中心以及用于存档的 Azure 存储。 

> [!IMPORTANT]
> 服务器日志的此诊断功能仅在 "常规用途" 和 "内存优化"[定价层](concepts-pricing-tiers.md)中提供。


### <a name="configure-diagnostic-settings"></a>配置诊断设置
你可以使用 Azure 门户、CLI、REST API 和 Powershell 为你的 Postgres 服务器启用诊断设置。 要选择的日志类别为**PostgreSQLLogs**。 （如果使用[查询存储](concepts-query-store.md)，还可以配置其他日志。）

使用 Azure 门户启用诊断日志：

   1. 在门户中，在 Postgres 服务器的导航菜单中转到 "*诊断设置*"。
   2. 选择 "*添加诊断设置*"。
   3. 为此设置命名。 
   4. 选择首选终结点（存储帐户、事件中心、log analytics）。 
   5. 选择日志类型**PostgreSQLLogs**。
   7. 保存设置。

若要使用 Powershell、CLI 或 REST API 启用诊断日志，请访问[诊断设置](../azure-monitor/platform/diagnostic-settings.md)一文。

### <a name="access-diagnostic-logs"></a>访问诊断日志

访问日志的方式取决于所选择的终结点。 对于 Azure 存储，请参阅[日志存储帐户](../azure-monitor/platform/resource-logs-collect-storage.md)一文。 对于事件中心，请参阅[流式传输 Azure 日志](../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

对于 Azure Monitor 日志，会将日志发送到所选的工作区。 Postgres 日志使用**AzureDiagnostics**收集模式，因此可以从 AzureDiagnostics 表中查询它们。 表中的字段如下所述。 详细了解[Azure Monitor 日志查询](../azure-monitor/log-query/log-query-overview.md)概述中的查询和警报。

下面是可以尝试开始的查询。 您可以基于查询配置警报。

在过去一天搜索特定服务器的所有 Postgres 日志
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

搜索所有非 localhost 连接尝试
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
上面的查询将在此工作区中的任何 Postgres 服务器日志记录的过去6小时显示结果。

### <a name="log-format"></a>日志格式

下表描述了**PostgreSQLLogs**类型的字段。 包括的字段以及它们的出现顺序可能有所不同，具体取决于你选择的输出终结点。 

|**字段** | **说明** |
|---|---|
| TenantId | 租户 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | 记录日志时的时间戳 (UTC) |
| 类型 | 日志类型。 始终是 `AzureDiagnostics` |
| SubscriptionId | 服务器所属的订阅的 GUID |
| ResourceGroup | 服务器所属的资源组的名称 |
| ResourceProvider | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 资源 URI |
| 资源 | 服务器的名称 |
| 类别 | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 日志记录级别，示例：LOG、ERROR、NOTICE |
| 消息 | 主要日志消息 | 
| 域 | 服务器版本，示例：postgres-10 |
| 详细信息 | 辅助日志消息（如果适用） |
| ColumnName | 列名称（如果适用） |
| SchemaName | 架构名称（如果适用） |
| DatatypeName | 数据类型名称（如果适用） |
| LogicalServerName | 服务器的名称 | 
| _ResourceId | 资源 URI |
| 前缀 | 日志行的前缀 |


## <a name="next-steps"></a>后续步骤
- 详细了解如何通过 [Azure 门户](howto-configure-server-logs-in-portal.md)或 [Azure CLI](howto-configure-server-logs-using-cli.md) 访问日志。
- 详细了解 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。
- 了解有关[审核日志](concepts-audit.md)的详细信息
