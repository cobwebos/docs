---
title: 감사 로그 형식
description: 了解 SQL 数据库审核日志的结构。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722078"
---
# <a name="sql-database-audit-log-format"></a>SQL 数据库审核日志格式

[AZURE SQL 数据库审核](sql-database-auditing.md)跟踪数据库事件，并将这些事件写入 Azure 存储帐户中的审核日志，或将其发送到事件中心或 Log Analytics 以进行下游处理和分析。

## <a name="naming-conventions"></a>명명 규칙

### <a name="blob-audit"></a>Blob 审核

存储在 Blob 存储中的审核日志存储在 Azure 存储帐户中名为 `sqldbauditlogs` 的容器中。 容器中的目录层次结构的格式为 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`。 Blob 文件名格式为 `<CreationTime>_<FileNumberInSession>.xel`，其中 `CreationTime` 采用 UTC 格式 `hh_mm_ss_ms` 格式，而 `FileNumberInSession` 为正在运行的索引，以防会话日志跨多个 Blob 文件。

例如，对于上的数据库 `Database1` `Server1` 以下是可能的有效路径：

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[只读副本](sql-database-read-scale-out.md)审核日志存储在同一容器中。 容器中的目录层次结构的格式为 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`。 Blob 文件名共享相同的格式。 只读副本的审核日志存储在同一容器中。


### <a name="event-hub"></a>이벤트 허브

审核事件将写入审核配置过程中定义的命名空间和事件中心，并在[Apache Avro](https://avro.apache.org/)事件的正文中捕获并使用 utf-8 格式的 JSON 格式进行存储。 감사 로그를 읽으려면 [Avro 도구](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) 또는 이 형식을 처리하는 유사한 도구를 사용할 수 있습니다.

### <a name="log-analytics"></a>Log Analytics

审核事件将写入到在审核配置过程中定义的 Log Analytics 工作区，以及类别 `SQLSecurityAuditEvents`的 `AzureDiagnostics` 表。 Log Analytics 검색 언어 및 명령에 대한 유용한 추가 정보는 [Log Analytics 검색 참조](../log-analytics/log-analytics-log-search.md)를 참조하세요.

## <a id="subheading-1"></a>审核日志字段

| 名称（Blob） | 名称（事件中心/Log Analytics） | Description | Blob 类型 | 事件中心/Log Analytics 类型 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 동작의 ID입니다. | varchar(4) | 문자열 |
| action_name | action_name_s | 操作的名称 | N/A | 문자열 |
| additional_information | additional_information_s | 有关事件的任何其他信息，存储为 XML | nvarchar(4000) | 문자열 |
| affected_rows | affected_rows_d | 受查询影响的行数 | bigint | int |
| application_name | application_name_s| 客户端应用程序的名称 | nvarchar(128) | 문자열 |
| audit_schema_version | audit_schema_version_d | 始终为1 | int | int |
| class_type | class_type_s | 发生审核的可审核实体的类型 | varchar(2) | 문자열 |
| class_type_desc | class_type_description_s | 发生审核的可审核实体的说明 | N/A | 문자열 |
| client_ip | client_ip_s | 客户端应用程序的源 IP | nvarchar(128) | 문자열 |
| connection_id | N/A | 服务器中的连接 ID | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 根据数据库中的已分类列，由审核查询返回的信息类型和敏感度标签。 了解有关[AZURE SQL 数据库数据发现和分类的](sql-database-data-discovery-and-classification.md)详细信息 | nvarchar(4000) | 문자열 |
| database_name | database_name_s | 发生此操作的数据库上下文 | sysname | 문자열 |
| database_principal_id | database_principal_id_d | 在其中执行操作的数据库用户上下文的 ID | int | int |
| database_principal_name | database_principal_name_s | 在其中执行操作的数据库用户上下文的名称 | sysname | 문자열 |
| duration_milliseconds | duration_milliseconds_d | 查询执行持续时间（毫秒） | bigint | int |
| event_time | event_time_t | 触发可审核操作的日期和时间 | datetime2 | Datetime |
| host_name | N/A | 客户端主机名 | 문자열 | N/A |
| is_column_permission | is_column_permission_s | 열 수준 사용 권한임을 나타내는 플래그입니다. 1 = true，0 = false | bit | 문자열 |
| N/A | is_server_level_audit_s | 指示此审核是否处于服务器级别的标志 | N/A | 문자열 |
| object_ id | object_id_d | 감사가 수행된 대상 엔터티의 ID입니다. 这包括： server 对象、数据库、数据库对象和架构对象。 如果实体是服务器本身，或如果未在对象级别执行审核，则为0 | int | int |
| object_name | object_name_s | 감사가 수행된 대상 엔터티의 이름입니다. 这包括： server 对象、数据库、数据库对象和架构对象。 如果实体是服务器本身，或如果未在对象级别执行审核，则为0 | sysname | 문자열 |
| permission_bitmask | permission_bitmask_s | 해당되는 경우 부여, 거부 또는 취소된 사용 권한을 표시합니다. | varbinary （16） | 문자열 |
| response_rows | response_rows_d | 在结果集中返回的行数 | bigint | int |
| schema_name | schema_name_s | 동작이 수행된 스키마 컨텍스트입니다. 对于架构外发生的审核，为 NULL | sysname | 문자열 |
| N/A | securable_class_type_s | 映射到正在审核的 class_type 的安全对象 | N/A | 문자열 |
| sequence_group_id | sequence_group_id_g | 고유 식별자 | varbinary | GUID |
| sequence_number | sequence_number_d | 跟踪单个审核记录中的记录顺序，该记录太大，无法容纳在写入缓冲区中以供审核 | int | int |
| server_instance_name | server_instance_name_s | 发生审核的服务器实例的名称 | sysname | 문자열 |
| server_principal_id | server_principal_id_d | 在其中执行操作的登录上下文的 ID | int | int |
| server_principal_name | server_principal_name_s | 当前登录 | sysname | 문자열 |
| server_principal_sid | server_principal_sid_s | 当前登录 SID | varbinary | 문자열 |
| session_id | session_id_d | 发生事件的会话的 ID | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 会话的服务器主体 | sysname | 문자열 |
| statement | statement_s | 已执行的 t-sql 语句（如果有） | nvarchar(4000) | 문자열 |
| succeeded | succeeded_s | 이벤트를 발생시킨 동작의 성공 여부를 나타냅니다. 对于除登录名和批处理之外的事件，此选项仅报告权限检查是成功还是失败，而不是操作。 1 = 成功，0 = 失败 | bit | 문자열 |
| target_database_principal_id | target_database_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 데이터베이스 보안 주체입니다. 0（如果不适用） | int | int |
| target_database_principal_name | target_database_principal_name_s | 동작의 대상 사용자입니다. 如果不适用，则为 NULL | 문자열 | 문자열 |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 서버 보안 주체입니다. 如果不适用，则返回0 | int | int |
| target_server_principal_name | target_server_principal_name_s | 동작의 대상 로그인입니다. 如果不适用，则为 NULL | sysname | 문자열 |
| target_server_principal_sid | target_server_principal_sid_s | 대상 로그인의 SID입니다. 如果不适用，则为 NULL | varbinary | 문자열 |
| transaction_id | transaction_id_d | 仅 SQL Server （从2016开始）-0 （对于 Azure SQL DB） | bigint | int |
| user_defined_event_id | user_defined_event_id_d | 作为参数传递给 sp_audit_write 的用户定义事件 id。 对于系统事件为 NULL （默认值），对于用户定义事件为非零值。 有关详细信息，请参阅[sp_audit_write （transact-sql）](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | 作为参数传递给 sp_audit_write 的用户定义的信息。 对于系统事件为 NULL （默认值），对于用户定义事件为非零值。 有关详细信息，请参阅[sp_audit_write （transact-sql）](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | 문자열 |

## <a name="next-steps"></a>다음 단계

了解有关[AZURE SQL 数据库审核](sql-database-auditing.md)的详细信息。
