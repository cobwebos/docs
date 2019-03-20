---
title: SQL 数据库审核日志格式 |Microsoft Docs
description: 了解如何构建 SQL 数据库审核日志。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001685"
---
# <a name="sql-database-audit-log-format"></a>SQL 数据库审核日志格式

[Azure SQL 数据库审核](sql-database-auditing.md)跟踪数据库事件，并将写入的审核日志在 Azure 存储帐户中，或将其发送到事件中心或 Log Analytics 进行下游处理和分析。

## <a name="naming-conventions"></a>命名约定

### <a name="blob-audit"></a>Blob 审核

Blob 存储中存储的审核日志存储在名为的容器`sqldbauditlogs`Azure 存储帐户中。 在容器内的目录层次结构的形式`<ServerName>/<DatabaseName>/<AuditName>/<Date>/`。 Blob 文件名格式为`<CreationTime>_<FileNumberInSession>.xel`，其中`CreationTime`采用 utc 格式`hh_mm_ss_ms`格式，和`FileNumberInSession`是会话日志范围跨多个 Blob 文件的情况下，是正在运行的索引。

例如，对于数据库`Database1`上`Server1`下面是可能的有效路径：

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>事件中心

审核事件写入到审核配置过程中进行定义和捕获的正文中的命名空间和事件中心[Apache Avro](https://avro.apache.org/)事件使用 JSON 使用 utf-8 编码格式设置和存储。 若要读取审核日志，可以使用 [Avro 工具](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools)或处理此格式的类似工具。

### <a name="log-analytics"></a>Log Analytics

审核事件写入到审核配置期间定义的 Log Analytics 工作区`AzureDiagnostics`类别的表`SQLSecurityAuditEvents`。 有关 Log Analytics 搜索语言和命令的其他有用信息，请参阅 [Log Analytics 搜索参考](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)。

## <a id="subheading-1"></a>审核日志字段

| 名称 (Blob) | 名称 （事件中心/日志分析） | 描述 | Blob 类型 | 事件中心/日志分析类型 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 操作的 ID | varchar(4) | 字符串 |
| action_name | action_name_s | 操作的名称 | 不适用 | 字符串 |
| additional_information | additional_information_s | 有关该事件，以 XML 形式存储的任何其他信息 | nvarchar(4000) | 字符串 |
| affected_rows | affected_rows_d | 该查询影响的行数 | bigint | int |
| application_name | application_name_s| 客户端应用程序的名称 | nvarchar(128) | 字符串 |
| audit_schema_version | audit_schema_version_d | 始终为 1 | int | int |
| class_type | class_type_s | 发生审核的可审核实体的类型 | varchar(2) | 字符串 |
| class_type_desc | class_type_description_s | 发生审核的可审核实体的说明 | 不适用 | 字符串 |
| client_ip | client_ip_s | 源 IP 的客户端应用程序 | nvarchar(128) | 字符串 |
| connection_id | 不适用 | 在服务器中的连接的 ID | GUID | 不适用 |
| data_sensitivity_information | data_sensitivity_information_s | 信息类型和返回的已审核的查询，基于数据库中的已分类列的敏感度标签。 详细了解[Azure SQL 数据库数据发现和分类](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | 字符串 |
| database_name | database_name_s | 发生操作的数据库上下文 | sysname | 字符串 |
| database_principal_id | database_principal_id_d | 数据库用户上下文中执行的操作的 ID | int | int |
| database_principal_name | database_principal_name_s | 在其中执行操作的数据库用户上下文的名称 | sysname | 字符串 |
| duration_milliseconds | duration_milliseconds_d | 查询执行持续时间以毫秒为单位 | bigint | int |
| event_time | event_time_t | 日期和时间时触发可审核操作 | datetime2 | datetime |
| host_name | 不适用 | 客户端主机名称 | 字符串 | 不适用 |
| is_column_permission | is_column_permission_s | 该标志指明如果这是列级别权限。 1 = true，0 = false | bit | 字符串 |
| 不适用 | is_server_level_audit_s | 标志，用于指示此审核处于服务器级别 | 不适用 | 字符串 |
| object_ id | object_id_d | 发生审核的实体的 ID。 这包括： 服务器对象、 数据库、 数据库对象和架构对象。 0 或如果实体是服务器本身审核，则不在对象级别执行 | int | int |
| object_name | object_name_s | 发生审核的实体的名称。 这包括： 服务器对象、 数据库、 数据库对象和架构对象。 0 或如果实体是服务器本身审核，则不在对象级别执行 | sysname | 字符串 |
| permission_bitmask | permission_bitmask_s | 如果适用，说明了授予、 拒绝或撤消的权限 | varbinary(16) | 字符串 |
| response_rows | response_rows_d | 在结果集中返回的行数 | bigint | int |
| schema_name | schema_name_s | 在其中发生操作的架构上下文。 对于在架构外发生的审核，为 NULL | sysname | 字符串 |
| 不适用 | securable_class_type_s | 映射到要审核的 class_type 的安全对象 | 不适用 | 字符串 |
| sequence_group_id | sequence_group_id_g | 唯一标识符 | varbinary | GUID |
| sequence_number | sequence_number_d | 跟踪过大，审核将写入缓冲区中容纳不下的单个审核记录中记录的序列 | int | int |
| server_instance_name | server_instance_name_s | 发生审核的服务器实例的名称 | sysname | 字符串 |
| server_principal_id | server_principal_id_d | 在执行操作时的登录上下文的 ID | int | int |
| server_principal_name | server_principal_name_s | 当前登录名 | sysname | 字符串 |
| server_principal_sid | server_principal_sid_s | 当前登录名 SID | varbinary | 字符串 |
| session_id | session_id_d | 发生事件的会话 ID | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 会话的服务器主体 | sysname | 字符串 |
| 语句 | statement_s | （如果有） 已执行的 T-SQL 语句 | nvarchar(4000) | 字符串 |
| 已成功 | succeeded_s | 指示触发事件的操作是否成功。 有关事件而不是登录名和批处理，它仅报告权限检查是成功还是失败，不是操作。 1 = success,0 = fail | bit | 字符串 |
| target_database_principal_id | target_database_principal_id_d | 执行 GRANT/DENY/REVOKE 操作的数据库主体。 如果不适用，则为 0 | int | int |
| target_database_principal_name | target_database_principal_name_s | 操作的目标用户。 如果不适用，则为 NULL | 字符串 | 字符串 |
| target_server_principal_id | target_server_principal_id_d | 执行 GRANT/DENY/REVOKE 操作的服务器主体。 如果不适用，则返回 0 | int | int |
| target_server_principal_name | target_server_principal_name_s | 操作的目标登录名。 如果不适用，则为 NULL | sysname | 字符串 |
| target_server_principal_sid | target_server_principal_sid_s | 目标登录名的 SID。 如果不适用，则为 NULL | varbinary | 字符串 |
| transaction_id | transaction_id_d | SQL Server 仅 （从 2016年开始）-适用于 Azure SQL DB 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | 用户定义的事件 id 作为参数传递给 sp_audit_write。 对于系统事件 （默认值） 为 NULL，对于用户定义的事件显示的非零值。 有关详细信息，请参阅[sp_audit_write (TRANSACT-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | 用户定义的信息作为参数传递给 sp_audit_write。 对于系统事件 （默认值） 为 NULL，对于用户定义的事件显示的非零值。 有关详细信息，请参阅[sp_audit_write (TRANSACT-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | 字符串 |

## <a name="next-steps"></a>后续步骤

详细了解如何[Azure SQL 数据库审核](sql-database-auditing.md)。