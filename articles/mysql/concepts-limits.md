---
title: 限制 - Azure Database for MySQL
description: 本文介绍了 Azure Database for MySQL 中的限制，例如连接数和存储引擎选项。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: c562b8a82ef21e78eccad2c2ed6159251056f4fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392686"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的限制
以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。 另请参阅适用于 MySQL 数据库引擎的[常规限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)。

## <a name="server-parameters"></a>服务器参数

> [!NOTE]
> 如果正在查找服务器参数（如和）的最小/最大值 `max_connections` `innodb_buffer_pool_size` ，则此信息已移至 "**[服务器参数](./concepts-server-parameters.md)**" 一文。

Azure Database for MySQL 支持优化服务器参数的值。 某些参数的最小值和最大值（例如 `max_connections`、 `join_buffer_size` 、 `query_cache_size` ）由服务器的定价层和 vcore 确定。 有关这些限制的详细信息，请参阅[服务器参数](./concepts-server-parameters.md)。

初始部署时，Azure for MySQL 服务器包含时区信息的系统表，但不填充这些表。 可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程来填充时区表。 有关如何调用存储过程并设置全局时区或会话级时区，请参阅文章 [Azure 门户](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter)。

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
- DEFINER：需要创建并限制超级权限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `--skip-definer` 命令删除 `CREATE DEFINER` 命令。


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
- [每个服务层级中有哪些可用资源](concepts-pricing-tiers.md)
- [支持的 MySQL 数据库版本](concepts-supported-versions.md)
