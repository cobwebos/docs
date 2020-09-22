---
title: 审核日志记录-Azure Database for PostgreSQL-灵活服务器
description: Azure Database for PostgreSQL-灵活服务器中的 pgAudit 审核日志记录的概念。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2fccf5431666990919faf7e6378b46c41d682437
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933353"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-灵活服务器中的审核日志记录

Azure Database for PostgreSQL-灵活服务器中的数据库活动的审核日志记录通过 PostgreSQL Audit extension： [pgAudit](https://www.pgaudit.org/)提供。 pgAudit 提供详细的会话和/或对象审核日志记录。

> [!IMPORTANT]
> Azure Database for PostgreSQL-灵活的服务器处于预览阶段

如果需要对计算和存储缩放之类的操作进行 Azure 资源级别的日志记录，请参阅 [Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)。

## <a name="usage-considerations"></a>使用注意事项
默认情况下，使用 Postgres 的标准日志记录设备将 pgAudit 日志语句与常规日志语句一起发出。 在 Azure Database for PostgreSQL 灵活的服务器中，你可以配置要发送到 Azure Monitor 日志存储的所有日志，以便在 Log Analytics 中进行后续分析。 如果启用 Azure Monitor 资源日志记录，系统会将日志（以 JSON 格式）自动发送到 Azure 存储、事件中心和/或 Azure Monitor 日志，具体取决于你的选择。

若要了解如何设置将日志记录到 Azure 存储、事件中心或 Azure Monitor 日志的功能，请访问[服务器日志文章](concepts-logging.md)的资源日志部分。

## <a name="enabling-pgaudit"></a>启用 pgAudit

若要启用 pgAudit，需要使用客户端 (（如 psql) ）连接到服务器，并通过运行以下命令启用 pgAudit 扩展：
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>pgAudit 设置

可以通过 pgAudit 配置会话或对象审核日志记录。 [会话审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)会发出已执行语句的详细日志。 [对象审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)是局限于特定关系的审核。 可以选择设置一个或两个类型的日志记录。 

> [!NOTE]
> pgAudit 设置是全局指定的，不能在数据库或角色级别指定。

[启用 pgAudit](#enabling-pgaudit)后，可以将其参数配置为开始记录。 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供每个参数的定义。 请先测试参数，确认获取的是预期的行为。

> [!NOTE]
> 将 `pgaudit.log_client` 设置为 ON 会将日志重定向到客户端进程（例如 psql）而不是写入文件。 通常应让此设置保持禁用状态。 <br> <br>
> `pgaudit.log_level` 只有在 `pgaudit.log_client` 为 on 的情况下启用。

> [!NOTE]
> 在 Azure Database for PostgreSQL 灵活的服务器中， `pgaudit.log` 无法使用 `-` (减) 签名快捷方式进行设置，如 pgAudit 文档中所述。 所有必需的语句类 (读取、写入等 ) 应该单独指定。

## <a name="audit-log-format"></a>审核日志格式
每个审核条目通过靠近日志行开头的 `AUDIT:` 进行指示。 条目其余部分的格式详见 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

## <a name="getting-started"></a>入门
若要快速入门，请将设置 `pgaudit.log` 为 `WRITE` ，并打开服务器日志以查看输出。 

## <a name="viewing-audit-logs"></a>查看审核日志
访问日志的方式取决于所选的终结点。 有关 Azure 存储，请参阅[日志存储帐户](../../azure-monitor/platform/resource-logs-collect-storage.md)一文。 有关事件中心，请参阅[流式传输 Azure 日志](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)一文。

Azure Monitor 日志将发送到所选的工作区。 Postgres 日志使用 **AzureDiagnostics** 收集模式，因此可以从 AzureDiagnostics 表查询它们。 下面描述了该表中的字段。 在 [Azure Monitor 日志查询](../../azure-monitor/log-query/log-query-overview.md)概述中详细了解查询和警报。

可以通过此查询开始使用。 可以基于查询配置警报。

在过去一天中搜索特定服务器的 Postgres 日志中的所有 pgAudit 条目
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>后续步骤
- [了解 Azure Database for PostgreSQL-灵活服务器中的日志记录](concepts-logging.md)
- [了解如何在 Azure Database for PostgreSQL 灵活的服务器中设置日志记录以及如何访问日志](howto-configure-and-access-logs.md)