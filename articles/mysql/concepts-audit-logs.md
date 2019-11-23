---
title: Azure Database for MySQL 的审核日志
description: 介绍了 Azure Database for MySQL 中提供的审核日志，以及用于启用日志记录级别的可用参数。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434401"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的审核日志

在 Azure Database for MySQL 中，审核日志可供用户使用。 审核日志可以用来跟踪数据库级别的活动，通常用于确保符合性。

> [!IMPORTANT]
> 审核日志功能目前为预览版。

## <a name="configure-audit-logging"></a>配置审核日志记录

默认情况下，审核日志被禁用。 若要启用它，请将 `audit_log_enabled` 设置为 ON。

可以调整的其他参数包括：

- `audit_log_events`：控制要记录的事件。 请查看下表以了解具体的审核事件。
- `audit_log_include_users`：要包含在日志记录中的 MySQL 用户。 此参数的默认值为空，这将包括所有用户进行日志记录。 此参数的优先级高于 `audit_log_exclude_users`。 此参数的最大长度为 512 个字符。
> [!Note]
> `audit_log_include_users` 的优先级高于 `audit_log_exclude_users`。例如，如果 audit_log_include_users = `demouser` 并且 audit_log_exclude_users = `demouser`，它将审核日志，因为 `audit_log_include_users` 的优先级更高。
- `audit_log_exclude_users`：要从日志记录中排除的 MySQL 用户。 此参数的最大长度为 512 个字符。

> [!Note]
> 对于 `sql_text`，如果日志超过 2048 个字符，则会截断日志。

| **Event** | **说明** |
|---|---|
| `CONNECTION` | - 启动连接（成功或不成功） <br> - 在会话期间使用不同的用户/密码对用户重新进行身份验证 <br> - 终止连接 |
| `DML_SELECT`| SELECT 查询 |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE 查询 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 类似“DROP DATABASE”的查询 |
| `DCL` | 类似“GRANT PERMISSION”的查询 |
| `ADMIN` | 类似“SHOW STATUS”的查询 |
| `GENERAL` | All in DML_SELECT, DML_NONSELECT, DML, DDL, DCL, and ADMIN |
| `TABLE_ACCESS` | - 仅适用于 MySQL 5.7 <br> -表读取语句，如 SELECT 或 INSERT INTO .。。单击 <br> - 表删除语句，例如 DELETE 或 TRUNCATE TABLE <br> - 表插入语句，例如 INSERT 或 REPLACE <br> - 表更新语句，例如 UPDATE |

## <a name="access-audit-logs"></a>访问审核日志

审核日志与 Azure Monitor 诊断日志集成。 在 MySQL 服务器上启用审核日志后，可以将它们发送到 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解如何在 Azure 门户中启用诊断日志，请参阅[审核日志门户文章](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>诊断日志架构

以下部分介绍基于事件类型的 MySQL 审核日志的输出是什么。 包括的字段以及它们的出现顺序可能有所不同，具体取决于输出方法。

### <a name="connection"></a>连接

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`、`DISCONNECT`、`CHANGE USER`（仅适用于 MySQL 5.7） |
| `connection_id_d` | MySQL 生成的唯一连接 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MySQL 的客户端的 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `db_s` | 连接的数据库的名称 |
| `\_ResourceId` | 资源 URI |

### <a name="general"></a>一般信息

下面的架构适用于 GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件类型。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`、`ERROR`、`RESULT`（仅适用于 MySQL 5.6） |
| `event_time` | 查询开始时间（UTC 时间戳） |
| `error_code_d` | 查询失败时的错误代码。 `0` 意味着无错误 |
| `thread_id_d` | 执行了查询的线程的 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MySQL 的客户端的 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `sql_text_s` | 完整查询文本 |
| `\_ResourceId` | 资源 URI |

### <a name="table-access"></a>表访问权限

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 记录日志时的时间戳 (UTC) |
| `Type` | 日志类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 服务器的名称 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`、`INSERT`、`UPDATE` 或 `DELETE` |
| `connection_id_d` | MySQL 生成的唯一连接 ID |
| `db_s` | 访问的数据库的名称 |
| `table_s` | 访问的表的名称 |
| `sql_text_s` | 完整查询文本 |
| `\_ResourceId` | 资源 URI |

## <a name="next-steps"></a>后续步骤

- [如何在 Azure 门户中配置审核日志](howto-configure-audit-logs-portal.md)