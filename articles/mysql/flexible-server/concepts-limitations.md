---
title: 限制-Azure Database for MySQL-灵活服务器
description: 本文介绍 Azure Database for MySQL 灵活的服务器的限制，例如连接数和存储引擎选项。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 6fff7f22e7d265eb9b15bcec8604eeab692ac1c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650283"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活的服务器 (预览版中的限制) 

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍 Azure Database for MySQL 灵活的服务器服务的限制。 MySQL 数据库引擎的[一般限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html)也适用。 若要了解资源 (计算、内存、存储) 层，请参阅 [计算和存储](concepts-compute-storage.md) 一文。

## <a name="server-parameters"></a>服务器参数

> [!NOTE]
> 如果正在查找服务器参数（如和）的最小/最大值 `max_connections` `innodb_buffer_pool_size` ，则此信息已移至服务器参数概念 <!-- **[server parameters](./concepts-server-parameters.md)** --> 一文。

Azure Database for MySQL 支持优化服务器参数的值。 某些参数（例如 `max_connections`、 `join_buffer_size` 、 `query_cache_size`) 由服务器的计算层和计算大小决定。 请参阅服务器参数概念 <!-- [server parameters](./concepts-server-parameters.md)--> 有关这些限制的详细信息。

服务不支持密码插件，如 "validate_password" 和 "caching_sha2_password"。

## <a name="storage-engines"></a>存储引擎

MySQL 支持许多存储引擎。 在 Azure Database for MySQL 灵活的服务器上，支持和不支持以下存储引擎：

### <a name="supported"></a>支持
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>不支持
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>& 数据操作支持的权限

许多服务器参数和设置可能会无意中降低服务器性能或将 MySQL 服务器的 ACID 属性抵消。 为了维护产品级别的服务完整性和 SLA，此服务不公开多个角色。 

MySQL 服务不允许直接访问基础文件系统。 某些数据操作命令不受支持。 

### <a name="unsupported"></a>不支持

以下内容不受支持：
- DBA 角色：受限。 或者，你可以使用在新的服务器创建期间创建的管理员用户 () ，以便执行大部分 DDL 和 DML 语句。 
- 超级权限：同样， [超级权限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) 受到限制。
- DEFINER：需要创建并限制超级权限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `--skip-definer` 命令删除 `CREATE DEFINER` 命令。
- 系统数据库： [mysql 系统数据库](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) 是只读的，用于支持各种 PaaS 功能。 不能对 `mysql` 系统数据库进行更改。
- `SELECT ... INTO OUTFILE`：在服务中不受支持。

### <a name="supported"></a>支持
- 支持 `LOAD DATA INFILE`，但必须指定 `[LOCAL]` 参数，并将其定向到 UNC 路径（通过 SMB 装载的 Azure 存储空间）。

## <a name="functional-limitations"></a>功能限制

### <a name="zone-redundant-ha"></a>区域冗余 HA
- 只能在服务器创建过程中设置此配置。
- 在可突增计算层中不受支持。

### <a name="networking"></a>网络
- 创建服务器后，不能更改连接方法。 如果使用 *私有访问权限创建服务器 (VNet 集成) *，则在创建后，不能将其更改为 *公共访问 (允许的 IP 地址) * ，反之亦然
- TLS/SSL 默认处于启用状态，并且无法禁用。
- 服务器支持的最低 TLS 版本为 TLS 1.2。 有关详细信息，请参阅 [使用 TLS/SSL 连接](./how-to-connect-tls-ssl.md) 。

### <a name="stopstart-operation"></a>停止/启动操作
- 在主) 和备用上，不支持区域冗余 HA (配置。
-  (源和副本) 的读取副本配置不支持。

### <a name="scale-operations"></a>缩放操作
- 不支持降低预配服务器存储。

### <a name="read-replicas"></a>只读副本
- 在主) 和备用上，不支持区域冗余 HA (配置。

### <a name="server-version-upgrades"></a>服务器版本升级
- 不支持主数据库引擎版本之间的自动迁移。 如果要升级主要版本，请获取 [转储，并](../concepts-migrate-dump-restore.md) 将其还原到使用新引擎版本创建的服务器。

### <a name="restoring-a-server"></a>还原服务器
- 对于时间点还原，将创建新服务器，其计算和存储配置与它所基于的源服务器相同。 创建服务器后，新还原的服务器的计算可以缩小。
- 不支持还原已删除的服务器。

## <a name="next-steps"></a>后续步骤

- 了解 [可用于计算和存储的选项](concepts-compute-storage.md)
- 了解 [支持的 MySQL 版本](concepts-supported-versions.md)
- 查看 [如何使用 Azure 门户备份和还原服务器](how-to-restore-server-portal.md)