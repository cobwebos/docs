---
title: 限制-Azure Database for MySQL
description: 本文介绍了 Azure Database for MySQL 中的限制，例如连接数和存储引擎选项。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 6954f306e0d0a346bd8f39776d987af99f7574dd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299084"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的限制
以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。 另请参阅适用于 MySQL 数据库引擎的[常规限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)。

## <a name="server-parameters"></a>服务器参数

几个常用服务器参数的最小值和最大值由定价层和 Vcore 确定。 有关限制，请参阅下表。

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
|内存优化|2|600|10|800|
|内存优化|4|1250|10|2500|
|内存优化|8|2500|10|5000|
|内存优化|16|5000|10|10000|
|内存优化|32|10000|10|20000|

当连接数超出限制时，可能会收到以下错误：
> 错误 1040 (08004): 连接过多

> [!IMPORTANT]
> 为了获得最佳体验，我们建议使用 ProxySQL 等连接池来有效地管理连接。

与 MySQL 建立新的客户端连接需要花费一段时间，并且在建立后，这些连接会占用数据库资源，甚至是在空闲时。 大多数应用程序会请求很多生存期较短的连接，这将会出现这种情况。 结果是，为实际工作负荷提供的资源越少，从而降低性能。 减少空闲连接并重复使用现有连接的连接池会有助于避免这种情况。 若要了解如何设置 ProxySQL，请访问我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

## <a name="query_cache_size"></a>query_cache_size

查询缓存默认情况下处于关闭状态。 若要启用查询缓存，请配置 `query_cache_type` 参数。 

有关此参数的详细信息，请参阅[MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size)。

> [!NOTE]
> 在 mysql 5.7.20 中，查询缓存已弃用，已在 MySQL 8.0 中被删除

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
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

## <a name="sort_buffer_size"></a>sort_buffer_size

有关此参数的详细信息，请参阅[MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size)。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
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

## <a name="join_buffer_size"></a>join_buffer_size

有关此参数的详细信息，请参阅[MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size)。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
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

## <a name="max_heap_table_size"></a>max_heap_table_size

有关此参数的详细信息，请参阅[MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size)。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
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

## <a name="tmp_table_size"></a>tmp_table_size

有关此参数的详细信息，请参阅[MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size)。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
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

## <a name="storage-engine-support"></a>存储引擎支持

### <a name="supported"></a>支持
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>不支持
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>特权支持

### <a name="unsupported"></a>不支持
- DBA 角色：许多服务器参数和设置可能会无意中导致服务器性能下降或使 DBMS 的 ACID 属性无效。 因此，为了维护产品级别的服务完整性和 SLA，此服务不公开 DBA 角色。 默认用户帐户（在创建新的数据库实例时构造）允许该用户执行托管数据库实例中的大部分 DDL 和 DML 语句。 
- SUPER 特权：[SUPER 特权](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)同样也受到限制。
- DEFINER：需要超级权限才能创建和受限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `CREATE DEFINER` 命令删除 `--skip-definer` 命令。

## <a name="data-manipulation-statement-support"></a>数据操作语句支持

### <a name="supported"></a>支持
- 支持 `LOAD DATA INFILE`，但必须指定 `[LOCAL]` 参数，并将其定向到 UNC 路径（通过 SMB 装载的 Azure 存储空间）。

### <a name="unsupported"></a>不支持
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>功能限制

### <a name="scale-operations"></a>缩放操作
- 目前不支持向/从基本定价层动态缩放。
- 不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。 如果要升级到下一主要版本，请将其[转储和还原](./concepts-migrate-dump-restore.md)到使用新引擎版本创建的服务器。

### <a name="point-in-time-restore"></a>时间点还原
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的配置创建新服务器。
- 不支持还原已删除的服务器。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

### <a name="storage-size"></a>存储大小
- 有关每个定价层的存储大小限制，请参阅[定价层](concepts-pricing-tiers.md)。

## <a name="current-known-issues"></a>当前已知的问题
- 建立连接后，MySQL 服务器实例显示错误的服务器版本。 若要获取正确的服务器实例引擎版本，请使用 `select version();` 命令。

## <a name="next-steps"></a>后续步骤
- [每个服务层提供的功能](concepts-pricing-tiers.md)
- [支持的 MySQL 数据库版本](concepts-supported-versions.md)
