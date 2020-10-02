---
title: 限制 - Azure Database for MariaDB
description: 本文介绍了 Azure Database for MariaDB 中的限制，例如连接数和存储引擎选项。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: c3bef7a368c6c0f2a08acdfd8da9236899a51a27
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650980"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的限制
以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。

## <a name="server-parameters"></a>服务器参数

> [!NOTE]
> 如果要查找服务器参数（如 `max_connections` 和 `innodb_buffer_pool_size`）的最小值/最大值，请参阅[服务器参数](./concepts-server-parameters.md)一文。

Azure Database for MariaDB 支持优化服务器参数的值。 某些参数（例如 `max_connections`、`join_buffer_size`、`query_cache_size`）的最小值和最大值由服务器的定价层和 vCore 数决定。 有关这些限制的详细信息，请参阅[服务器参数](./concepts-server-parameters.md)。

初始部署后，Azure for MariaDB 服务器包含用于时区信息的系统表，但这些表没有填充。 可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程来填充时区表。 若要了解如何调用存储过程并设置全局时区或会话级时区，请参阅 [Azure 门户](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 一文。

服务不支持密码插件，如 "validate_password" 和 "caching_sha2_password"。

## <a name="storage-engine-support"></a>存储引擎支持

### <a name="supported"></a>支持
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>不支持
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>& 数据操作支持的权限

许多服务器参数和设置可能会无意中降低服务器性能或否定 MariaDB 服务器的 ACID 属性。 为了维护产品级别的服务完整性和 SLA，此服务不公开多个角色。 

MariaDB 服务不允许直接访问基础文件系统。 某些数据操作命令不受支持。 

## <a name="privilege-support"></a>特权支持

### <a name="unsupported"></a>不支持

以下内容不受支持：
- DBA 角色：受限。 或者，你可以使用在新的服务器创建期间创建的管理员用户 () ，以便执行大部分 DDL 和 DML 语句。 
- 超级权限：同样，也会限制 [超级权限](https://mariadb.com/kb/en/library/grant/#global-privileges) 。
- DEFINER：需要创建并限制超级权限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `--skip-definer` 命令删除 `CREATE DEFINER` 命令。
- 系统数据库： [mysql 系统数据库](https://mariadb.com/kb/en/the-mysql-database-tables/) 是只读的，用于支持各种 PaaS 功能。 不能对 `mysql` 系统数据库进行更改。
- `SELECT ... INTO OUTFILE`：在服务中不受支持。

### <a name="supported"></a>支持
- 支持 `LOAD DATA INFILE`，但必须指定 `[LOCAL]` 参数，并将其定向到 UNC 路径（通过 SMB 装载的 Azure 存储空间）。

## <a name="functional-limitations"></a>功能限制

### <a name="scale-operations"></a>缩放操作
- 目前不支持动态缩放到“基本”定价层或从该层动态缩放。
- 不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="point-in-time-restore"></a>时间点还原
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的配置创建新服务器。
- 不支持还原已删除的服务器。

### <a name="subscription-management"></a>订阅管理
- 目前不支持跨订阅和资源组动态移动预先创建的服务器。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

### <a name="storage-size"></a>存储大小
- 有关每个定价层的存储大小限制，请参阅[定价层](concepts-pricing-tiers.md)。

## <a name="current-known-issues"></a>当前已知的问题
- 建立连接后，MariaDB 服务器实例显示的服务器版本不正确。 若要获取正确的服务器实例引擎版本，请使用 `select version();` 命令。

## <a name="next-steps"></a>后续步骤
- [每个服务层级中有哪些可用资源](concepts-pricing-tiers.md)
- [支持的 MariaDB 数据库版本](concepts-supported-versions.md)
