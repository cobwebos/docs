---
title: 审核日志-Azure Database for MariaDB
description: 介绍 Azure Database for MariaDB 中可用的审核日志，以及用于启用日志记录级别的可用参数。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 9c5f6aa2900570aa00ddbc50ec8be4dbb0d16a34
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978043"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的审核日志

在 Azure Database for MariaDB 中，审核日志可供用户使用。 审核日志可用于跟踪数据库级活动，通常用于相容性。

> [!IMPORTANT]
> 审核日志功能目前处于预览阶段。

## <a name="configure-audit-logging"></a>配置审核日志记录

默认情况下，审核日志处于禁用状态。 若要启用它，请将 `audit_log_enabled` 设置为 ON。

可以调整的其他参数包括：

- `audit_log_events`：控制要记录的事件。 有关特定审核事件，请参阅下表。
- `audit_log_include_users`：要包含在日志记录中的 MariaDB 用户。 此参数的默认值为空，这将包括所有用户进行日志记录。 此优先级高于 `audit_log_exclude_users`。 参数的最大长度为512个字符。
> [!Note]
> `audit_log_include_users` 的优先级高于 `audit_log_exclude_users`。 例如，如果 `audit_log_include_users` = `demouser` 并 `audit_log_exclude_users` = `demouser`，则会将该用户包括在审核日志中，因为 `audit_log_include_users` 的优先级较高。
- `audit_log_exclude_users`：要从日志记录中排除的 MariaDB 用户。 最多允许四个用户。 参数的最大长度为256个字符。

| **事件** | **说明** |
|---|---|
| `CONNECTION` | -连接启动（成功或不成功） <br> -会话过程中用不同用户/密码进行的用户重新进行身份验证 <br> -连接终止 |
| `DML_SELECT`| SELECT 查询 |
| `DML_NONSELECT` | 插入/删除/更新查询 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 类似于 "删除数据库" 的查询 |
| `DCL` | 类似于 "授予权限" 的查询 |
| `ADMIN` | 查询，如 "显示状态" |
| `GENERAL` | 全部 DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 中 |

## <a name="access-audit-logs"></a>访问审核日志

审核日志与 Azure Monitor 诊断日志集成在一起。 在 MariaDB 服务器上启用审核日志后，可以将它们发出到 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解如何在 Azure 门户中启用诊断日志，请参阅[审核日志门户](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>诊断日志架构

以下各节介绍了基于事件类型 MariaDB 审核日志的输出。 包括的字段以及它们的出现顺序可能有所不同，具体取决于输出方法。

### <a name="connection"></a>连接

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`，`DISCONNECT` |
| `connection_id_d` | MariaDB 生成的唯一连接 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MariaDB 的客户端的 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `db_s` | 连接到的数据库的名称 |
| `\_ResourceId` | 资源 URI |

### <a name="general"></a>一般信息

下面的架构适用于常规、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件类型。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`、`ERROR`、`RESULT` |
| `event_time` | UNIX 时间戳中的查询开始秒数 |
| `error_code_d` | 如果查询失败，则为错误代码。 `0` 表示无错误 |
| `thread_id_d` | 执行查询的线程的 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MariaDB 的客户端的 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `sql_text_s` | 完整的查询文本 |
| `\_ResourceId` | 资源 URI |

### <a name="table-access"></a>表访问

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`、`INSERT`、`UPDATE` 或 `DELETE` |
| `connection_id_d` | MariaDB 生成的唯一连接 ID |
| `db_s` | 访问的数据库的名称 |
| `table_s` | 访问的表的名称 |
| `sql_text_s` | 完整的查询文本 |
| `\_ResourceId` | 资源 URI |

## <a name="next-steps"></a>后续步骤

- [如何在 Azure 门户中配置审核日志](howto-configure-audit-logs-portal.md)