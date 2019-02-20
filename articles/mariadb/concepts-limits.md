---
title: Azure Database for MariaDB 中的限制
description: 本文介绍了 Azure Database for MariaDB 中的限制，例如连接数和存储引擎选项。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 79d6e185b64fdaf332f877718487809ba6273441
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895782"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的限制
以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。

## <a name="maximum-connections"></a>最大连接数
每个定价层的最大连接数和 vCore 数如下所示：

|**定价层**|**vCore(s)**| 最大连接数|
|---|---|---|
|基本| 1| 50|
|基本| 2| 100|
|常规用途| 2| 300|
|常规用途| 4| 625|
|常规用途| 8| 1250|
|常规用途| 16| 2500|
|常规用途| 32| 5000|
|内存优化| 2| 600|
|内存优化| 4| 1250|
|内存优化| 8| 2500|
|内存优化| 16| 5000|

当连接数超出限制时，可能会收到以下错误：
> 错误 1040 (08004)：连接过多

## <a name="storage-engine-support"></a>存储引擎支持

### <a name="supported"></a>支持
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>不支持
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>特权支持

### <a name="unsupported"></a>不支持
- DBA 角色：许多服务器参数和设置可能会无意中导致服务器性能下降或使 DBMS 的 ACID 属性无效。 因此，为了维护产品级别的服务完整性和 SLA，此服务不公开 DBA 角色。 默认用户帐户（在创建新的数据库实例时构造）允许该用户执行托管数据库实例中的大部分 DDL 和 DML 语句。
- SUPER 特权：[SUPER 特权](https://mariadb.com/kb/en/library/grant/#global-privileges)同样也受到限制。
- 定义者：需要创建并限制超级权限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `--skip-definer` 命令删除 `CREATE DEFINER` 命令。

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
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="point-in-time-restore"></a>时间点还原
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的配置创建新服务器。
- 不支持还原已删除的服务器。

### <a name="subscription-management"></a>订阅管理
- 目前不支持跨订阅和资源组动态移动预先创建的服务器。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

## <a name="current-known-issues"></a>当前已知的问题
- 建立连接后，MariaDB 服务器实例显示的服务器版本不正确。 若要获取正确的服务器实例引擎版本，请使用 `select version();` 命令。

## <a name="next-steps"></a>后续步骤
- [每个服务层中有哪些可用资源](concepts-pricing-tiers.md)
- [支持的 MariaDB 数据库版本](concepts-supported-versions.md)
