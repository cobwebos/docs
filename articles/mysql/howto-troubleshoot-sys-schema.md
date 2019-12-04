---
title: 利用 sys_schema Azure Database for MySQL
description: 了解如何使用 sys_schema 在 Azure Database for MySQL 中查找性能问题和维护数据库。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 12/02/2019
ms.openlocfilehash: 50552b87fad9d8f58ff8c48dc03463d4c901bf99
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775939"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>如何在 Azure Database for MySQL 中使用 sys_schema 进行性能优化和数据库维护

Mysql 5.5 中第一次提供的 MySQL performance_schema 为许多重要的服务器资源（如内存分配、存储程序、元数据锁定等）提供检测。但 performance_schema 包含80个以上的表，并且获取所需的信息通常需要联接 performance_schema 内的表以及 information_schema 中的表。 sys_schema 在 performance_schema 和 information_schema 的基础上构建，在一个只读的数据库中提供[用户友好视图](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html)的强大集合，并且完全在 Azure Database for MySQL 版本 5.7 中启用。

![sys_schema 的视图](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

sys_schema 中有 52 个视图，每个视图具有以下前缀之一：

- Host_summary 或 IO：I/O 相关的延迟。
- InnoDB：InnoDB 缓冲区状态和锁。
- Memory：按主机和用户列出的内存用量。
- Schema：架构相关的信息，例如增量、索引，等等。
- Statement：有关 SQL 语句（导致扫描整个表或长时间查询的语句）的信息。
- User：按用户分组的消耗资源。 示例包括文件 I/O、连接和内存。
- Wait：等待按主机或用户分组的事件。

现在，让我们了解 sys_schema 的一些常见使用模式。 首先，我们将使用模式分组为两个类别：**性能优化**和**数据库维护**。

## <a name="performance-tuning"></a>性能调优

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO 是数据库中开销最高的操作。 我们可以通过查询 *sys.user_summary_by_file_io* 视图找出平均 IO 延迟。 使用 125 GB 默认预配存储时，IO 延迟大约为 15 秒。

![125 GB 时的 IO 延迟](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

由于 Azure Database for MySQL 可根据存储缩放 IO，将预配存储增大到 1 TB 后，IO 延迟减小为 571 毫秒。

![1 TB 时的 IO 延迟](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

尽管经过认真规划，但许多查询仍可能导致全表扫描。 有关索引类型及其优化方式的其他信息，请参阅此文：[如何排查查询性能问题](./howto-troubleshoot-query-performance.md)。 全表扫描属于资源密集型操作，会降低数据库性能。 查找执行全表扫描的表的最快方法是查询 *sys.schema_tables_with_full_table_scans* 视图。

![全表扫描](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

若要排查数据库性能问题，识别数据库中发生的事件可能很有帮助，而使用 *sys.user_summary_by_statement_type* 视图就能实现此目的。

![语句摘要](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

在此示例中，Azure Database for MySQL 花费了 53 分钟来刷新 slog 查询日志 44579。 此时间很长，并且消耗了大量 IO 资源。 可以在 Azure 门户中禁用慢速查询日志或降低慢速查询日志的频率，来减少此类活动。

## <a name="database-maintenance"></a>数据库维护

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

InnoDB 缓冲池驻留在内存中，是 DBMS 与存储之间的主要缓存机制。 InnoDB 缓冲池大小与性能层密切相关，除非选择不同的产品 SKU，否则不能更改。 与操作系统中的内存一样，旧页面将被换出，以便为较新数据留出空间。 若要了解哪些表占用了大部分 InnoDB 缓冲池内存，可以查询 *sys.innodb_buffer_stats_by_table* 视图。

![InnoDB 缓冲状态](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

在上图中，很明显，除系统表和视图以外，mysqldatabase033 数据库中的每个表（托管某个 WordPress 站点）占用了 16 KB 或 1 个页面的内存中数据。

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

索引是提高读取性能的极佳工具，但它们确实会产生额外的插入和存储开销。 *Sys.schema_unused_indexes* 和 *sys.schema_redundant_indexes* 提供未使用或重复索引的洞察信息。

![未使用的索引](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![冗余的索引](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>结束语

总而言之，sys_schema 是用于优化性能和维护数据库的极佳工具。 请务必在 Azure Database for MySQL 中利用此功能。 

## <a name="next-steps"></a>后续步骤
- 若要查找同行对你最关心的问题的解答或者发布新的问题/解答，请访问 [MSDN 论坛](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) 或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)。
