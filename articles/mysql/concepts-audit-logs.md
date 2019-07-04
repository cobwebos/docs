---
title: 审核日志的 Azure Database for MySQL
description: 介绍 for MySQL，并启用日志记录级别的可用参数的 Azure 数据库中可用的审核日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443845"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>中的审核日志 Azure Database for MySQL

在 Azure Database for MySQL，审核日志可供用户。 审核日志可用于跟踪数据库级别的活动，通常用于符合性。

> [!IMPORTANT]
> 审核日志功能当前处于预览状态。

## <a name="configure-audit-logging"></a>配置审核日志记录

默认情况下禁用审核日志。 若要启用它，请设置`audit_log_enabled`为 ON。

可以调整的其他参数包括：

- `audit_log_events`： 控制要记录的事件。 请参阅下表了解特定的审核事件。
- `audit_log_exclude_users`：要从日志记录中排除的 MySQL 用户。 允许最多四个用户。 参数的最大长度为 256 个字符。

| **事件** | **说明** |
|---|---|
| `CONNECTION` | -连接启动 （成功或失败） <br> 用户重新进行身份验证使用不同的用户密码在会话过程中 <br> 的终止连接 |
| `DML_SELECT`| SELECT 查询 |
| `DML_NONSELECT` | 插入/删除/更新查询 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 与"删除数据库"类似的查询 |
| `DCL` | 与类似的查询"授予权限" |
| `ADMIN` | 查询，例如"显示状态"等 |
| `GENERAL` | 所有在 DML_SELECT、 DML_NONSELECT、 DML、 DDL、 DCL 和管理员 |
| `TABLE_ACCESS` | -仅适用于 MySQL 5.7 <br> -表读取语句，如 SELECT 或 INSERT INTO...SELECT <br> 表的 delete 语句，如 DELETE 或 TRUNCATE TABLE <br> 表的 insert 语句，如插入或替换 <br> 表的 update 语句，如更新 |

## <a name="access-audit-logs"></a>访问审核日志

与 Azure Monitor 诊断日志集成在审核日志。 在 MySQL 服务器上启用审核日志后，您可以将它们发出到 Azure Monitor 日志、 事件中心或 Azure 存储。 若要了解有关如何在 Azure 门户中启用诊断日志的详细信息，请参阅[审核日志的门户项目](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>诊断日志架构

以下各节介绍什么是通过 MySQL 审核日志的事件类型的输出。 包括的字段以及它们的出现顺序可能有所不同，具体取决于输出方法。

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
| `event_subclass_s` | `CONNECT``DISCONNECT`， `CHANGE USER` （仅适用于 MySQL 5.7） |
| `connection_id_d` | MySQL 由生成的唯一连接 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MySQL 的客户端 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `db_s` | 连接到数据库的名称 |
| `\_ResourceId` | 资源 URI |

### <a name="general"></a>常规

下面的架构适用于常规、 DML_SELECT、 DML_NONSELECT、 DML、 DDL、 DCL 和管理事件类型。

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
| `event_subclass_s` | `LOG``ERROR`， `RESULT` （仅适用于 MySQL 5.6） |
| `event_time` | 查询中的 UNIX 时间戳开始秒 |
| `error_code_d` | 如果查询失败，错误代码。 `0` 意味着没有错误 |
| `thread_id_d` | 执行查询的线程 ID |
| `host_s` | 空白 |
| `ip_s` | 连接到 MySQL 的客户端 IP 地址 |
| `user_s` | 执行查询的用户的名称 |
| `sql_text_s` | 完整的查询文本 |
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
| `connection_id_d` | MySQL 由生成的唯一连接 ID |
| `db_s` | 访问数据库的名称 |
| `table_s` | 访问的表的名称 |
| `sql_text_s` | 完整的查询文本 |
| `\_ResourceId` | 资源 URI |

## <a name="next-steps"></a>后续步骤

- [如何在 Azure 门户中配置审核日志](howto-configure-audit-logs-portal.md)