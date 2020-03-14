---
title: 容量限制-Azure Synapse Analytics （以前称为 SQL DW）
description: Azure Synapse 中 SQL Analytics 的各种组件允许的最大值。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 2/19/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a225c375d877ae44c2b21ea8e79e31f17db36878
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270077"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-capacity-limits"></a>Azure Synapse Analytics （以前称为 SQL DW）容量限制

Azure Synapse 的各种组件允许的最大值。

## <a name="workload-management"></a>工作负荷管理

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| [数据仓库单位 (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |单个 SQL 池（数据仓库）单元的最大 DWU | 第 1 代：DW6000<br></br>第 2 代：DW30000c |
| [数据仓库单位 (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |每个服务器的默认 DTU |54,000<br></br>默认情况下，每个 SQL server （例如 myserver.database.windows.net）的 DTU 配额为54000，这允许最多 DW5000c。 此配额仅仅只是安全限制。 可以通过[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)并选择“配额”作为请求类型来增加配额。  若要计算 DTU 需求，请将7.5 乘以所需的 DWU 总数，或将9.5 乘以所需的总 cDWU 数。 例如：<br></br>DW6000 x 7.5 = 45,000 DTU<br></br>DW5000c x 9.5 = 47500 Dtu。<br></br>可以在门户中的 SQL Server 选项中查看当前 DTU 消耗量。 已暂停和未暂停的数据库都计入 DTU 配额。 |
| 数据库连接 |并发打开的并发会话数 |1024<br/><br/>并发打开的会话数会根据所选的 DWU 而有所不同。 DWU600c 及更高版本最多支持1024个打开的会话。 DWU500c 和更高的并发打开会话限制为512。 请注意，可并发执行的查询数量是有限制的。 当超出并发限制时，请求将进入内部队列等待处理。 |
| 数据库连接 |预处理语句的最大内存 |20 MB |
| [工作负荷管理](resource-classes-for-workload-management.md) |最大并行查询 |128<br/><br/>  最多可执行128个并发查询，其余查询将排队。<br/><br/>将用户分配到更高的资源类或当[数据仓库单位](memory-concurrency-limits.md)设置降低时，并发查询数可能会降低。 某些查询（例如 DMV 查询）始终允许运行，并且不会影响并发查询限制。 有关并发查询执行的更多详细信息，请参阅[并发最大值](memory-concurrency-limits.md)一文。 |
| [tempdb](sql-data-warehouse-tables-temporary.md) |最大 GB |每 DW100c 399 GB。 因此，在 DWU1000c，tempdb 的大小调整为 3.99 TB。 |
||||

## <a name="database-objects"></a>数据库对象

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| 数据库 |最大大小 | 第 1 代：磁盘压缩后为 240TB。 此空间与 tempdb 或日志空间无关，因此，此空间专用于永久表。  聚集列存储压缩率估计为 5 倍。  此压缩率允许数据库在所有表都为聚集列存储（默认表类型）的情况下增长到大约 1 PB。 <br/><br/> Gen2：列存储表的无限制存储。  数据库的行存储部分仍限制为在磁盘上压缩 240 TB。 |
| 表 |最大大小 |列存储表的大小不受限制。 <br>磁盘上压缩的行存储表的 60 TB。 |
| 表 |每个数据库的表数 | 100,000 |
| 表 |每个表的列数 |1024 个列 |
| 表 |每个列的字节数 |取决于列[数据类型](sql-data-warehouse-tables-data-types.md)。 对于字符数据类型，MAX 限制可以存储多达 2 GB 的 off page （行溢出）存储空间。  在数据页中，非 Unicode 字符（如 char 或 varchar limit）为8000，数据页中的 Unicode 字符（如 nchar 或 nvarchar 限制）为4000。  使用数据页存储大小来提高性能。 |
| 表 |每行的字节数，定义的大小 |8060 字节<br/><br/>每行字节数的计算方式同于使用页面压缩的 SQL Server。 与 SQL Server 一样，支持行溢出存储，这使得**可变长度列**被推送到行外。 对可变长度行进行拖行推送时，只将 24 字节的根存储在主记录中。 有关详细信息，请参阅[超过 8-KB 的行溢出数据](https://msdn.microsoft.com/library/ms186981.aspx)。 |
| 表 |每个表的分区数 |15,000<br/><br/>为了实现高性能，建议在满足业务需求的情况下尽量减少所需的分区数。 随着分区数目的增长，数据定义语言 (DDL) 和数据操作语言 (DML) 操作的开销也会增长，导致性能下降。 |
| 表 |每个分区边界值的字符数。 |4000 |
| 索引 |每个表的非聚集索引数。 |50<br/><br/>仅适用于行存储表。 |
| 索引 |每个表的聚集索引数。 |1<br><br/>适用于行存储表和列存储表。 |
| 索引 |索引键大小。 |900 字节。<br/><br/>仅适用于行存储索引。<br/><br/>如果创建索引时列中的现有数据未超过 900 字节，那么可以创建最大大小超过 900 字节的 varchar 列上的索引。 但是，以后导致总大小超过 900 字节的对列的 INSERT 或 UPDATE 操作会失败。 |
| 索引 |每个索引的键列数。 |16<br/><br/>仅适用于行存储索引。 聚集列存储索引包括所有列。 |
| 统计信息 |组合的列值的大小。 |900 字节。 |
| 统计信息 |每个统计对象的列数。 |32 |
| 统计信息 |每个表的列上创建的统计信息条数。 |30,000 |
| 存储过程 |最大嵌套级数。 |8 |
| 查看 |每个视图的列数 |1,024 |
||||

## <a name="loads"></a>加载

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| Polybase 加载 |每行 MB 数 |1<br/><br/>Polybase 加载小于 1 MB 的行。 不支持将 LOB 数据类型加载到具有聚集列存储索引（CCI）的表中。<br/><br/> |
||||

## <a name="queries"></a>查询

| 类别 | 说明 | 最大值 |
|:--- |:--- |:--- |
| 查询 |用户表的排队查询数。 |1000 |
| 查询 |系统视图的并发查询数。 |100 |
| 查询 |系统视图的排队查询数。 |1000 |
| 查询 |最大值参数 |2098 |
| Batch |最大大小 |65,536\*4096 |
| SELECT 结果 |每个行的列数 |4096<br/><br/>在 SELECT 结果中每行的列数始终不得超过 4096。 无法保证最大值始终为 4096。 如果查询计划需要一个临时表，那么将应用每个表最多 1024 列的最大值。 |
| SELECT |嵌套子查询个数 |32<br/><br/>在 SELECT 语句中的嵌套子查询数始终不得超过 32 个。 无法保证最大值始终为 32 个。 例如，JOIN 可以将子查询引入查询计划。 还可以通过可用内存来限制子查询的数量。 |
| SELECT |每个 JOIN 的列数 |1024 个列<br/><br/>JOIN 中的列数始终不得超过 1024。 无法保证最大值始终为 1024。 如果 JOIN 计划需要列数多于 JOIN 结果的临时表，那么将 1024 限制应用于此临时表。 |
| SELECT |每个 GROUP BY 列的字节数。 |8060<br/><br/>GROUP BY 子句中的列的字节数最大为 8060 字节。 |
| SELECT |每个 ORDER BY 列的字节数 |8060 字节<br/><br/>ORDER BY 子句中的列的字节数最大为 8060 字节 |
| 每个语句的标识符数 |被引用的标识符数 |65,535<br/><br/> 查询的单个表达式中可包含的标识符数量是有限的。 超过此数字将导致 SQL Server 错误 8632。 有关详细信息，请参阅 [Internal error: An expression services limit has been reached](https://support.microsoft.com/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a)（内部错误：已达到表达式服务限制）。 |
| 字符串文本 | 一个语句中字符串文本的数量 | 20,000 <br/><br/>查询的单个表达式中的字符串常量数量是有限的。 超过此数字将导致 SQL Server 错误 8632。|
||||

## <a name="metadata"></a>元数据

| 系统视图 | 最大行数 |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10,000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |最近 1000 个 SQL 请求的 DMS 辅助角色的总数。 |
| sys.dm_pdw_errors |10,000 |
| sys.dm_pdw_exec_requests |10,000 |
| sys.dm_pdw_exec_sessions |10,000 |
| sys.dm_pdw_request_steps |sys.dm_pdw_exec_requests 中存储的最近 1000 个 SQL 请求的步骤总数。 |
| sys.dm_pdw_os_event_logs |10,000 |
| sys.dm_pdw_sql_requests |sys.dm_pdw_exec_requests 中存储的最近 1000 个 SQL 请求。 |
|||

## <a name="next-steps"></a>后续步骤

有关使用 Azure Synapse 的建议，请参阅备忘[单](cheat-sheet.md)。
