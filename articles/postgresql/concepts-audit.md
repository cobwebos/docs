---
title: 在 Azure Database for PostgreSQL（单一服务器）中使用 pgAudit 进行的审核日志记录
description: Azure Database for PostgreSQL（单一服务器）中的 pgAudit 审核日志记录的概念。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 49ad7334c418e29c821320608be729e060b4a8ae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331331"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的审核日志记录

Azure Database for PostgreSQL（单一服务器）中数据库活动的审核日志记录可以通过 PostgreSQL 审核扩展 [pgAudit](https://www.pgaudit.org/) 来获取。 pgAudit 提供详细的会话和/或对象审核日志记录。

> [!NOTE]
> 在 Azure Database for PostgreSQL 上，pgAudit 为预览版。
> 此扩展只能在“常规用途”和“内存优化”服务器上启用。

如果需要对计算和存储缩放之类的操作进行 Azure 资源级别的日志记录，请参阅 [Azure 活动日志](../azure-monitor/platform/activity-logs-overview.md)。

## <a name="usage-considerations"></a>使用注意事项
默认情况下，使用 Postgres 的标准日志记录设备将 pgAudit 日志语句与常规日志语句一起发出。 在 Azure Database for PostgreSQL 中，这些 .log 文件可以通过 Azure 门户或 CLI 下载。 文件集合的最大存储为 1 GB，每个文件的可用时间最长为七天（默认为三天）。 此服务是一个短期存储选项。

也可将所有日志配置为发出到 Azure Monitor 的诊断日志服务。 如果启用 Azure Monitor 诊断日志记录，系统会将日志（以 JSON 格式）自动发送到 Azure 存储、事件中心和/或 Azure Monitor 日志，具体取决于你的选择。

启用 pgAudit 会在服务器上生成大量日志记录，影响性能和日志存储。 建议使用 Azure 诊断日志服务，该服务提供较长期的存储选项以及分析和警报功能。 建议关闭标准日志记录，降低额外的日志记录带来的性能影响：

   1. 将参数 `logging_collector` 设置为 OFF。 
   2. 重启服务器以应用该更改。

若要了解如何设置将数据记录到 Azure 存储、事件中心或 Azure Monitor 日志的功能，请访问[服务器日志文章](concepts-server-logs.md)的诊断日志部分。

## <a name="installing-pgaudit"></a>安装 pgAudit

若要安装 pgAudit，需将其包括在服务器的共享预加载库中。 更改 Postgres 的 `shared_preload_libraries` 参数需要重启服务器才能生效。 可以使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md) 或 [REST API](/rest/api/postgresql/configurations/createorupdate) 更改参数。

使用 [Azure 门户](https://portal.azure.com)：

   1. 选择你的 Azure Database for PostgreSQL 服务器。
   2. 在侧栏中选择“服务器参数”。
   3. 搜索 `shared_preload_libraries` 参数。
   4. 选择 **pgaudit**。
   5. 重启服务器以应用更改。

   6. 使用客户端（例如 psql）连接到服务器并启用 pgAudit 扩展
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 如果看到错误，请确认是否已在保存 `shared_preload_libraries` 后重启服务器。

## <a name="pgaudit-settings"></a>pgAudit 设置

可以通过 pgAudit 配置会话或对象审核日志记录。 [会话审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)会发出已执行语句的详细日志。 [对象审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)是局限于特定关系的审核。 可以选择设置一个或两个类型的日志记录。 

> [!NOTE]
> pgAudit 设置在全局范围指定，不能在数据库或角色级别指定。

[安装 pgAudit](#installing-pgaudit)以后，即可配置其参数，以便开始日志记录。 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供每个参数的定义。 请先测试参数，确认获取的是预期的行为。

> [!NOTE]
> 将 `pgaudit.log_client` 设置为 ON 会将日志重定向到客户端进程（例如 psql）而不是写入文件。 通常应让此设置保持禁用状态。

> [!NOTE]
> `pgaudit.log_level` 只有在 `pgaudit.log_client` 为 on 的情况下启用。 另外，在 Azure 门户中，`pgaudit.log_level` 目前存在 Bug：会显示一个组合框，这意味着可以选择多个级别。 但是，只应选择一个级别。 

> [!NOTE]
> 在 Azure Database for PostgreSQL 中，根据 pgAudit 文档中的说明，不能使用 `pgaudit.log`（减号）快捷方式来设置 `-`。 所有必需的声明类（READ、WRITE 等）应该单独指定。

### <a name="audit-log-format"></a>审核日志格式
每个审核条目通过靠近日志行开头的 `AUDIT:` 进行指示。 条目其余部分的格式详见 [pgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)。

如果需要任何其他的字段来满足审核要求，请使用 Postgres 参数 `log_line_prefix`。 `log_line_prefix` 是一个字符串，是每个 Postgres 日志行开头的输出。 例如，以下 `log_line_prefix` 设置提供时间戳、用户名、数据库名称和进程 ID：

```
t=%m u=%u db=%d pid=[%p]:
```

若要了解有关 `log_line_prefix` 的详细信息，请访问 [PostgreSQL 文档](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)。

### <a name="getting-started"></a>入门
若要快速开始，请将 `pgaudit.log` 设置为 `WRITE`，并打开日志来查看输出。 


## <a name="next-steps"></a>后续步骤
- [了解 Azure Database for PostgreSQL 中的日志记录](concepts-server-logs.md)
- 了解如何使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)、[Azure CLI](howto-configure-server-parameters-using-cli.md) 或 [REST API](/rest/api/postgresql/configurations/createorupdate) 设置参数。
