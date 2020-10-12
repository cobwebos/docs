---
title: 日志-Azure Database for PostgreSQL-灵活的服务器
description: 介绍 Azure Database for PostgreSQL 灵活的服务器中的日志记录配置、存储和分析
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934757"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-灵活服务器中的日志

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 允许配置和访问 Postgres 的标准日志。 这些日志可用于识别、排除和修复配置错误和性能不佳问题。 可以配置和访问的日志信息包括错误、查询信息、autovacuum 记录、连接和检查点。 （无法访问事务日志）。

审核日志记录通过 Postgres 扩展提供 `pgaudit` 。 有关详细信息，请访问[审核的概念](concepts-audit.md)一文。

## <a name="configure-logging"></a>配置日志记录

可以使用日志记录服务器参数在服务器上配置 Postgres 标准日志记录。 若要详细了解 Postgres 日志参数，请访问 Postgres 文档的[何时记录日志](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN)和[记录哪些内容](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT)部分。 可以在 Azure Database for PostgreSQL 中配置大部分（但并非所有）PostgreSQL 日志记录参数。

若要了解如何在 Azure Database for PostgreSQL 中配置参数，请参阅[门户文档](howto-configure-server-parameters-using-portal.md)或 [CLI 文档](howto-configure-server-parameters-using-cli.md)。

> [!NOTE]
> 配置大量日志（例如，语句日志记录）可能会显著增大性能开销。 

## <a name="accessing-logs"></a>访问日志

Azure Database for PostgreSQL 与 Azure Monitor 诊断日志设置相集成。 使用诊断设置可将 JSON 格式的 Postgres 日志发送到 Azure Monitor 日志用于分析和警报、发送到事件中心进行流式处理，或者发送到 Azure 存储进行存档。 

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

- 详细了解如何 [配置和访问日志](howto-configure-and-access-logs.md)。
- 详细了解 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。
- 详细了解[审核日志](concepts-audit.md)
