---
title: 服务器参数 - Azure Database for MariaDB
description: 本主题提供在 Azure Database for MariaDB 中配置服务器参数的指南。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: b5064e3cef7def1aca5aa0c97d031d519fd610cf
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626388"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的服务器参数

本文提供在 Azure Database for MariaDB 中配置服务器参数的注意事项和指南。

## <a name="what-are-server-parameters"></a>什么是服务器参数？ 

MariaDB 引擎提供了许多不同的服务器变量/参数，用于配置和优化引擎行为。 某些参数可在运行时动态设置，另外一些参数则为“静态”参数，需要重启服务器才能应用。

Azure Database for MariaDB 提供了通过 [Azure 门户](./howto-server-parameters.md)、[Azure CLI](./howto-configure-server-parameters-cli.md) 和 [PowerShell](./howto-configure-server-parameters-using-powershell.md) 更改各种 MariaDB 服务器参数值的功能，以满足工作负荷的需要。

## <a name="configurable-server-parameters"></a>可配置的服务器参数

受支持服务器参数的列表还在不断增加。 在 Azure 门户中使用服务器参数选项卡可查看完整列表并配置服务器参数值。

请参阅以下各部分，详细了解多个经常更新的服务器参数的限制。 这些限制取决于服务器的定价层和 vCore 数。

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

在 Azure Database for MariaDB 中，始终启用二进制日志 (即 `log_bin`) 设置为 ON。 如果你想要使用触发器，你会收到类似于 *你未启用超级权限和二进制日志记录的错误 (你可能想要使用较不安全的 `log_bin_trust_function_creators` 变量) *。

二进制日志记录格式始终为 **行** ，与服务器的所有连接 **始终** 使用基于行的二进制日志记录。 利用基于行的二进制日志记录，不存在安全问题，二进制日志记录无法中断，因此可以安全地将设置 [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) 为 **TRUE**。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size)详细了解此参数。

#### <a name="servers-supporting-up-to-4-tb-storage"></a>支持高达 4 TB 存储的服务器

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|基本|1|872415232|134217728|872415232|
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
|基本|1|872415232|134217728|872415232|
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
> `innodb_file_per_table` 只能在“常规用途”和“内存优化”定价层中更新。

MariaDB 根据你在创建表期间提供的配置，将 InnoDB 表存储在不同的表空间中。 [系统表空间](https://mariadb.com/kb/en/innodb-system-tablespaces/)是 InnoDB 数据字典的存储区域。 [file-per-table 表空间](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/)包含单个 InnoDB 表的数据和索引，并存储在文件系统内它自己的数据文件中。 此行为由 `innodb_file_per_table` 服务器参数控制。 将 `innodb_file_per_table` 设置为 `OFF` 会导致 InnoDB 在系统表空间中创建表。 否则，InnoDB 将在 file-per-table 表空间中创建表。

在单个数据文件中，Azure Database for MariaDB 支持最大 **1 TB**。 如果数据库大小超过 1 TB，应在 [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) 表空间中创建表。 如果单个表的大小超过 1 TB，应使用分区表。

### <a name="join_buffer_size"></a>join_buffer_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|空值|空值|
|基本|2|在基本层中不可配置|空值|空值|
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
|基本|1|50|10|50|
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

创建与 MariaDB 的新客户端连接需要时间，一旦建立，这些连接就会占用数据库资源，即使在空闲时也是如此。 大多数应用程序会请求许多生存期短的连接，这加剧了这种情况。 其结果是可用于实际工作负荷的资源减少，从而导致性能下降。 连接池程序不仅会减少空闲连接，还会重用现有连接，因而有助于避免这种情况。 若要了解如何设置 ProxySQL，请访问我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

>[!Note]
>ProxySQL 是一个开源社区工具。 Microsoft 尽最大努力为它提供支持。 若要获得包含权威指导的生产支持，可以评估并联系 [ProxySQL 产品支持](https://proxysql.com/services/support/)。

### <a name="max_heap_table_size"></a>max_heap_table_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|空值|空值|
|基本|2|在基本层中不可配置|空值|空值|
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

默认情况下，MariaDB 中的查询缓存是使用 `have_query_cache` 参数启用的。 

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值 **|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|空值|空值|
|基本|2|在基本层中不可配置|空值|空值|
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

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|空值|空值|
|基本|2|在基本层中不可配置|空值|空值|
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

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值（字节）**|**最小值（字节）**|**最大值（字节）**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|空值|空值|
|基本|2|在基本层中不可配置|空值|空值|
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

初始部署后，Azure for MariaDB 服务器包含用于时区信息的系统表，但这些表没有填充。 可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程来填充时区表。 若要了解如何调用存储过程并设置全局时区或会话级时区，请参阅 [Azure 门户](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 一文。

## <a name="non-configurable-server-parameters"></a>不可配置的服务器参数

以下服务器参数不可在服务中配置：

|**参数**|**固定值**|
| :------------------------ | :-------- |
|基本层中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

在 [MariaDB](https://mariadb.com/kb/en/server-system-variables/) 中，上表中未列出的其他服务器参数将设置为其 MariaDB 现成默认值。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户配置服务器参数](./howto-server-parameters.md)
- 了解如何[使用 Azure CLI 配置服务器参数](./howto-configure-server-parameters-cli.md)
- 了解如何[使用 PowerShell 配置服务器参数](./howto-configure-server-parameters-using-powershell.md)