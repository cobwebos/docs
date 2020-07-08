---
title: 服务器参数-Azure Database for MariaDB
description: 本主题提供有关在 Azure Database for MariaDB 中配置服务器参数的准则。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7d530180b499495e97cb635186fc6a0d5cbd9044
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392720"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的服务器参数

本文提供了有关在 Azure Database for MariaDB 中配置服务器参数的注意事项和指南。

## <a name="what-are-server-parameters"></a>什么是服务器参数？ 

MariaDB 引擎提供了许多可用于配置和优化引擎行为的不同服务器变量/参数。 某些参数可在运行时动态设置，而其他参数为 "静态"，需要重新启动服务器才能应用。

Azure Database for MariaDB 公开使用[Azure 门户](./howto-server-parameters.md)、 [Azure CLI](./howto-configure-server-parameters-cli.md)和[PowerShell](./howto-configure-server-parameters-using-powershell.md)更改各种 MariaDB 服务器参数的值的功能，以满足工作负荷的需求。

## <a name="configurable-server-parameters"></a>可配置的服务器参数

受支持服务器参数的列表还在不断增加。 使用 Azure 门户中的 "服务器参数" 选项卡来查看完整列表和配置服务器参数值。

请参阅以下部分，了解有关多个经常更新的服务器参数的限制的详细信息。 此限制取决于服务器的定价层和 Vcore。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

若要详细了解此参数，请查阅 [MariaDB 文档](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size)。

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

MariaDB 根据你在创建表期间提供的配置，将 InnoDB 表存储在不同的表空间中。 [系统表空间](https://mariadb.com/kb/en/innodb-system-tablespaces/)是 InnoDB 数据字典的存储区域。 [file-per-table 表空间](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/)包含单个 InnoDB 表的数据和索引，并存储在文件系统内它自己的数据文件中。 此行为由 `innodb_file_per_table` 服务器参数控制。 将 `innodb_file_per_table` 设置为 `OFF` 会导致 InnoDB 在系统表空间中创建表。 否则，InnoDB 将在 file-per-table 表空间中创建表。

在单个数据文件中，Azure Database for MariaDB 支持最大 **1 TB**。 如果数据库大小超过 1 TB，应在 [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) 表空间中创建表。 如果单个表的大小超过 1 TB，应使用分区表。

### <a name="join_buffer_size"></a>join_buffer_size

若要详细了解此参数，请查阅 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)。

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

与 MariaDB 建立新的客户端连接需要花费一段时间，一旦建立连接，这些连接便会占用数据库资源，即使空闲时，也不例外。 大多数应用程序会请求许多生存期短的连接，这加剧了这种情况。 其结果是用于实际工作负荷的资源更少，进而导致性能下降。 连接池程序不仅会减少空闲连接，还会重用现有连接，因而有助于避免这种情况。 若要了解如何设置 ProxySQL，请访问我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

>[!Note]
>ProxySQL 是开源社区工具。 Microsoft 支持此方法。 为了获得权威指导的生产支持，你可以评估和联系[ProxySQL 产品支持](https://proxysql.com/services/support/)。

### <a name="max_heap_table_size"></a>max_heap_table_size

若要详细了解此参数，请查阅 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)。

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

默认情况下，使用参数在 MariaDB 中启用查询缓存 `have_query_cache` 。 

若要详细了解此参数，请查阅 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)。

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

若要详细了解此参数，请查阅 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)。

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

若要详细了解此参数，请查阅 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)。

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

初始部署时，Azure for MariaDB 服务器包含时区信息的系统表，但不填充这些表。 可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程来填充时区表。 若要了解如何调用存储过程，以及如何设置全局或会话级时区，请参阅 [Azure 门户](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 文章。

## <a name="non-configurable-server-parameters"></a>不可配置的服务器参数

以下服务器参数在该服务中不可配置：

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