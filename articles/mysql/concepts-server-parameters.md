---
title: 服务器参数-Azure Database for MySQL
description: 本主题提供有关在 Azure Database for MySQL 中配置服务器参数的准则。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: ce8e8b083b108d24c11d828ae1cbd4e47e090fc0
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963200"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的服务器参数

本文提供了有关在 Azure Database for MySQL 中配置服务器参数的注意事项和指南。

## <a name="what-are-server-parameters"></a>什么是服务器参数？ 

MySQL 引擎提供了许多不同的服务器变量/参数，可用于配置和优化引擎行为。 某些参数可在运行时动态设置，而其他参数为 "静态"，需要重新启动服务器才能应用。

Azure Database for MySQL 公开使用[Azure 门户](./howto-server-parameters.md)、 [Azure CLI](./howto-configure-server-parameters-using-cli.md)和[PowerShell](./howto-configure-server-parameters-using-powershell.md)更改各种 MySQL server 参数的值的功能，以满足工作负荷的需求。

## <a name="configurable-server-parameters"></a>可配置的服务器参数

受支持服务器参数的列表还在不断增加。 使用 Azure 门户中的 "服务器参数" 选项卡来查看完整列表和配置服务器参数值。

请参阅以下部分，了解有关多个经常更新的服务器参数的限制的详细信息。 此限制取决于服务器的定价层和 Vcore。

### <a name="thread-pools"></a>线程池

MySQL 通常为每个客户端连接分配一个线程。 随着并发用户数量的增加，性能会相应下降。 由于增加了上下文切换、线程争用以及 CPU 缓存的错误位置，许多活动线程可能会显著影响性能。

线程池是服务器端功能，不同于连接池，可通过引入工作线程的动态池来最大程度地提高性能，该工作线程可用于限制在服务器上运行的活动线程数，并最大程度地减少线程流失。 这有助于确保连接突发不会导致服务器耗尽资源，也不会导致内存不足错误。 对于短查询和 CPU 密集型工作负荷（例如 OLTP 工作负载），线程池最有效。

若要了解有关线程池的详细信息，请参阅[Azure Database for MySQL 中的线程池简介](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> MySQL 5.6 版本不支持线程池功能。 

### <a name="configuring-the-thread-pool"></a>配置线程池
若要启用线程池，请将 `thread_handling` 服务器参数更新为 "线程池"。 默认情况下，此参数设置为 `one-thread-per-connection` ，这意味着 MySQL 将为每个新连接创建新的线程。 请注意，这是一个静态参数，需要重新启动服务器才能应用。

还可以通过设置以下服务器参数，配置池中的最大和最小线程数： 
- `thread_pool_max_threads`：此值确保池中的线程数不超过此数目。
- `thread_pool_min_threads`：此值设置即使在连接关闭后也将保留的线程数。

为了改进线程池上的简短查询的性能问题，Azure Database for MySQL 允许您启用批处理，其中，在执行查询后，线程将始终处于活动状态，而不是在执行查询后立即返回到线程池。 然后，该线程会快速执行查询并完成，并等待下一个查询完成，直到此进程的总消耗时间超过阈值。 批处理执行行为是使用以下服务器参数确定的：  

-  `thread_pool_batch_wait_timeout`：此值指定线程等待另一个查询处理的时间。
- `thread_pool_batch_max_time`：此值确定一个线程将重复执行查询并等待下一个查询的最大时间。

> [!IMPORTANT]
> 在生产环境中开启之前，请测试线程池。 

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)。

#### <a name="servers-supporting-up-to-4-tb-storage"></a>支持多达 4 TB 存储空间的服务器

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|基本|2|2684354560|134217728|2684354560|
|常规用途|2|3758096384|134217728|3758096384|
|常规用途|4|8053063680|134217728|8053063680|
|常规用途|8|16106127360|134217728|16106127360|
|常规用途|16|32749125632|134217728|32749125632|
|常规用途|32|66035122176|134217728|66035122176|
|常规用途|64|132070244352|134217728|132070244352|
|内存优化|2|7516192768|134217728|7516192768|
|内存优化|4|16106127360|134217728|16106127360|
|内存优化|8|32212254720|134217728|32212254720|
|内存优化|16|65498251264|134217728|65498251264|
|内存优化|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>服务器最多支持 16 TB 的存储

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|基本|2|2684354560|134217728|2684354560|
|常规用途|2|7516192768|134217728|7516192768|
|常规用途|4|16106127360|134217728|16106127360|
|常规用途|8|32212254720|134217728|32212254720|
|常规用途|16|65498251264|134217728|65498251264|
|常规用途|32|132070244352|134217728|132070244352|
|常规用途|64|264140488704|134217728|264140488704|
|内存优化|2|15032385536|134217728|15032385536|
|内存优化|4|32212254720|134217728|32212254720|
|内存优化|8|64424509440|134217728|64424509440|
|内存优化|16|130996502528|134217728|130996502528|
|内存优化|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table`只能在常规用途和内存优化定价层中进行更新。

MySQL 根据你在表创建期间提供的配置，将 InnoDB 表存储在不同的表空间中。 [系统表空间](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)是 InnoDB 数据字典的存储区域。 [file-per-table 表空间](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)包含单个 InnoDB 表的数据和索引，并存储在文件系统内它自己的数据文件中。 此行为由 `innodb_file_per_table` 服务器参数控制。 将 `innodb_file_per_table` 设置为 `OFF` 会导致 InnoDB 在系统表空间中创建表。 否则，InnoDB 在 file-per-table 表空间中创建表。

在单个数据文件中，Azure Database for MySQL 支持最大 1TB。 如果数据库大小超过 1TB，应在 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 表空间中创建表。 如果单个表的大小超过 1 TB，应使用分区表。

### <a name="join_buffer_size"></a>join_buffer_size

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size)。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|Basic|1|在基本层中不可配置|不适用|N/A|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|262144|128|268435455|
|常规用途|4|262144|128|536870912|
|常规用途|8|262144|128|1073741824|
|常规用途|16|262144|128|2147483648|
|常规用途|32|262144|128|4294967295|
|常规用途|64|262144|128|4294967295|
|内存优化|2|262144|128|536870912|
|内存优化|4|262144|128|1073741824|
|内存优化|8|262144|128|2147483648|
|内存优化|16|262144|128|4294967295|
|内存优化|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|基本|2|100|10|100|
|常规用途|2|300|10|600|
|常规用途|4|625|10|1250|
|常规用途|8|1250|10|2500|
|常规用途|16|2500|10|5000|
|常规用途|32|5000|10|10000|
|常规用途|64|10000|10|20000|
|内存优化|2|625|10|1250|
|内存优化|4|1250|10|2500|
|内存优化|8|2500|10|5000|
|内存优化|16|5000|10|10000|
|内存优化|32|10000|10|20000|

当连接数超出限制时，可能会收到以下错误：
> 错误 1040 (08004)：连接过多

> [!IMPORTANT]
> 为了获得最佳体验，建议使用 ProxySQL 等连接池程序来高效地管理连接。

与 MySQL 建立新的客户端连接需要花费一段时间，一旦建立连接，这些连接会占用数据库资源，即使空闲时，也不例外。 大多数应用程序请求许多生存期短的连接，这加剧了这种情况。 其结果是用于实际工作负荷的资源更少，进而导致性能下降。 连接池程序不仅会减少空闲连接，还会重用现有连接，因而有助于避免这种情况。 若要了解如何设置 ProxySQL，请访问我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

>[!Note]
>ProxySQL 是开源社区工具。 Microsoft 支持此方法。 为了获得权威指导的生产支持，你可以评估和联系[ProxySQL 产品支持](https://proxysql.com/services/support/)。

### <a name="max_heap_table_size"></a>max_heap_table_size

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size)。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|Basic|1|在基本层中不可配置|不适用|N/A|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|16777216|16384|268435455|
|常规用途|4|16777216|16384|536870912|
|常规用途|8|16777216|16384|1073741824|
|常规用途|16|16777216|16384|2147483648|
|常规用途|32|16777216|16384|4294967295|
|常规用途|64|16777216|16384|4294967295|
|内存优化|2|16777216|16384|536870912|
|内存优化|4|16777216|16384|1073741824|
|内存优化|8|16777216|16384|2147483648|
|内存优化|16|16777216|16384|4294967295|
|内存优化|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

默认情况下，查询缓存被禁用。 若要启用查询缓存，请配置 `query_cache_type` 参数。 

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size)。

> [!NOTE]
> 自 MySQL 5.7.20 起，查询缓存已被弃用；在 MySQL 8.0 中，查询缓存已被删除

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|* * 最大值 * *|
|---|---|---|---|---|
|Basic|1|在基本层中不可配置|不适用|N/A|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|0|0|16777216|
|常规用途|4|0|0|33554432|
|常规用途|8|0|0|67108864|
|常规用途|16|0|0|134217728|
|常规用途|32|0|0|134217728|
|常规用途|64|0|0|134217728|
|内存优化|2|0|0|33554432|
|内存优化|4|0|0|67108864|
|内存优化|8|0|0|134217728|
|内存优化|16|0|0|134217728|
|内存优化|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size)。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|Basic|1|在基本层中不可配置|不适用|N/A|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|524288|32768|4194304|
|常规用途|4|524288|32768|8388608|
|常规用途|8|524288|32768|16777216|
|常规用途|16|524288|32768|33554432|
|常规用途|32|524288|32768|33554432|
|常规用途|64|524288|32768|33554432|
|内存优化|2|524288|32768|8388608|
|内存优化|4|524288|32768|16777216|
|内存优化|8|524288|32768|33554432|
|内存优化|16|524288|32768|33554432|
|内存优化|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

若要详细了解此参数，请查阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size)。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|Basic|1|在基本层中不可配置|不适用|N/A|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|16777216|1024|67108864|
|常规用途|4|16777216|1024|134217728|
|常规用途|8|16777216|1024|268435456|
|常规用途|16|16777216|1024|536870912|
|常规用途|32|16777216|1024|1073741824|
|常规用途|64|16777216|1024|1073741824|
|内存优化|2|16777216|1024|134217728|
|内存优化|4|16777216|1024|268435456|
|内存优化|8|16777216|1024|536870912|
|内存优化|16|16777216|1024|1073741824|
|内存优化|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

初始部署时，Azure for MySQL 服务器包含时区信息的系统表，但不填充这些表。 可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程来填充时区表。 若要了解如何调用存储过程，以及如何设置全局或会话级时区，请参阅 [Azure 门户](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) 文章。

## <a name="non-configurable-server-parameters"></a>不可配置的服务器参数

以下服务器参数在该服务中不可配置：

|**参数**|**固定值**|
| :------------------------ | :-------- |
|基本层中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

此处未列出的其他变量将设置为默认的 MySQL 现成值。 有关默认值，请参阅版本[8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)、 [5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)和[5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html)的 MySQL 文档。 

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户配置服务器参数](./howto-server-parameters.md)
- 了解如何[使用 Azure CLI 配置服务器参数](./howto-configure-server-parameters-using-cli.md)
- 了解如何[使用 PowerShell 配置服务器参数](./howto-configure-server-parameters-using-powershell.md)
