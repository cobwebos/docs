---
title: 日志 - Azure Database for PostgreSQL - 单一服务器
description: 介绍 Azure Database for PostgreSQL 中的日志记录配置、存储和分析 - 单一服务器
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 8d249cdabcbfd82fc555c486f29bbdba200e1da1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710492"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器中的日志

Azure Database for PostgreSQL 允许配置和访问 Postgres 的标准日志。 这些日志可用于识别、排除和修复配置错误和性能不佳问题。 可以配置和访问的日志信息包括错误、查询信息、autovacuum 记录、连接和检查点。 （无法访问事务日志）。

审核日志是通过 Postgres 扩展 pgaudit 提供的。 有关详细信息，请访问[审核的概念](concepts-audit.md)一文。


## <a name="configure-logging"></a>配置日志记录 
可以使用日志记录服务器参数在服务器上配置 Postgres 标准日志记录。 在每个 Azure Database for PostgreSQL 服务器上，默认已启用 `log_checkpoints` 和 `log_connections`。 还有一些其他参数，你可以调整它们来满足你的日志记录需求： 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Azure Database for PostgreSQL - 日志记录参数":::

若要详细了解 Postgres 日志参数，请访问 Postgres 文档的[何时记录日志](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)和[记录哪些内容](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)部分。 可以在 Azure Database for PostgreSQL 中配置大部分（但并非所有）PostgreSQL 日志记录参数。

若要了解如何在 Azure Database for PostgreSQL 中配置参数，请参阅[门户文档](howto-configure-server-parameters-using-portal.md)或 [CLI 文档](howto-configure-server-parameters-using-cli.md)。 

> [!NOTE]
> 配置大量日志（例如，语句日志记录）可能会显著增大性能开销。 

## <a name="access-log-files"></a>访问 .log 文件
Azure Database for PostgreSQL 中的默认日志格式为 .log。 此日志中的示例行如下所示：

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL 为 .log 文件提供短期存储位置。 每隔 1 小时或每达到 100 MB（以先达到的条件为准），就会开始创建一个新文件。 从 Postgres 发出日志后，这些日志将追加到当前文件。  

可以使用 `log_retention_period` 参数设置此短期日志存储的保留期。 默认值为 3 天；最大值为 7 天。 短期存储位置最多可以存储 1 GB 日志文件。 达到 1 GB 后，无论保留期有多长，都会删除最旧的文件，以便为新日志腾出空间。 

对于长期保留的日志和日志分析，可以下载 .log 文件并将其移到第三方服务。 可以使用 [Azure 门户](howto-configure-server-logs-in-portal.md)或 [Azure CLI](howto-configure-server-logs-using-cli.md) 下载文件。 此外，还可以配置 Azure Monitor 诊断设置，用于自动将日志（JSON 格式）发送到长期保留位置。 以下部分详细介绍了此选项。 

可以通过将参数 `logging_collector` 设置为 OFF 来停止生成 .log 文件。 如果使用 Azure Monitor 诊断设置，则我们建议禁用 .log 文件生成。 此配置可以减轻附加的日志记录对性能造成的影响。

## <a name="resource-logs"></a>资源日志

Azure Database for PostgreSQL 与 Azure Monitor 诊断日志设置相集成。 使用诊断设置可将 JSON 格式的 Postgres 日志发送到 Azure Monitor 日志用于分析和警报、发送到事件中心进行流式处理，或者发送到 Azure 存储进行存档。 

> [!IMPORTANT]
> 服务器日志的此诊断功能仅适用于“常规用途”和“内存优化”的[定价层](concepts-pricing-tiers.md)。


### <a name="configure-diagnostic-settings"></a>配置诊断设置

可以使用 Azure 门户、CLI、REST API 和 Powershell 为 Postgres 服务器启用诊断设置。 要选择的日志类别为“PostgreSQLLogs”。 （如果使用[查询存储](concepts-query-store.md)，则还可以配置其他日志。）

若要使用 Azure 门户启用资源日志：

   1. 在门户上 Postgres 服务器的导航菜单中，转到“诊断设置”。
   2. 选择“添加诊断设置”。
   3. 为此设置命名。 
   4. 选择首选的终结点（存储帐户、事件中心、Log Analytics）。 
   5. 选择日志类型“PostgreSQLLogs”。
   7. 保存设置。

若要使用 Powershell、CLI 或 REST API 启用资源日志，请访问[诊断设置](../azure-monitor/platform/diagnostic-settings.md)一文。

### <a name="access-resource-logs"></a>访问资源日志

访问日志的方式取决于所选的终结点。 有关 Azure 存储，请参阅[日志存储帐户](../azure-monitor/platform/resource-logs-collect-storage.md)一文。 有关事件中心，请参阅[流式传输 Azure 日志](../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

Azure Monitor 日志将发送到所选的工作区。 Postgres 日志使用 **AzureDiagnostics** 收集模式，因此可以从 AzureDiagnostics 表查询它们。 下面描述了该表中的字段。 在 [Azure Monitor 日志查询](../azure-monitor/log-query/log-query-overview.md)概述中详细了解查询和警报。

下面是可帮助你入门的查询。 可以基于查询配置警报。

搜索特定的服务器在过去一天生成的所有 Postgres 日志
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

搜索所有非 localhost 连接尝试
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
上面的查询将显示在此工作区中记录的任何 Postgres 服务器在过去 6 小时内的结果。

### <a name="log-format"></a>日志格式

下表描述了 **PostgreSQLLogs** 类型的字段。 包括的字段以及它们的出现顺序可能有所不同，具体取决于你选择的输出终结点。 

|**字段** | **说明** |
|---|---|
| TenantId | 租户 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | 记录日志时的时间戳 (UTC) |
| 类型 | 日志的类型。 始终是 `AzureDiagnostics` |
| SubscriptionId | 服务器所属的订阅的 GUID |
| resourceGroup | 服务器所属的资源组的名称 |
| ResourceProvider | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | 资源 URI |
| 资源 | 服务器的名称 |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 日志记录级别，例如：LOG, ERROR, NOTICE |
| Message | 主要日志消息 | 
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
- 详细了解[审核日志](concepts-audit.md)
