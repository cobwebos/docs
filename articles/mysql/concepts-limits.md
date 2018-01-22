---
title: "Azure Database for MySQL 中的限制 | Microsoft Docs"
description: "说明 Azure Database for MySQL 中的预览限制。"
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/11/2018
ms.openlocfilehash: f0f9a10f987f19d8ae77a07038cffe23446856fd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的限制
Azure Database for MySQL 服务目前为公共预览版。 以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。 另请参阅适用于 MySQL 数据库引擎的[常规限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)。

## <a name="service-tier-maximums"></a>服务层最大值
创建服务器时，Azure Database for MySQL 中提供多个可供选择的服务层。 有关详细信息，请参阅[了解每个服务层中有哪些可用资源](concepts-service-tiers.md)。  

在预览版期间，每个服务层中的连接数、计算单元数和存储均有最大值，如下所示： 

|                            |                   |
| :------------------------- | :---------------- |
| 最大连接数        |                   |
| 基本 50 个计算单元     | 50 个连接    |
| 基本 100 个计算单元    | 100 个连接   |
| 标准 100 个计算单元 | 200 个连接   |
| 标准 200 个计算单元 | 400 个连接   |
| 标准 400 个计算单元 | 800 个连接   |
| 标准 800 个计算单元 | 1600 个连接  |
| 最大计算单元数      |                   |
| 基本服务层         | 100 个计算单元 |
| 标准服务层      | 800 个计算单元 |
| 最大存储            |                   |
| 基本服务层         | 1 TB              |
| 标准服务层      | 1 TB              |

连接数过多时，可能会收到以下错误：
> 错误 1040 (08004): 连接过多

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
- DBA 角色：许多服务器参数和设置可能会无意中导致服务器性能下降或使 DBMS 的 ACID 属性无效。 因此，为了维护产品级别的服务完整性和 SLA，我们不向客户公开 DBA 角色。 默认用户帐户（在创建新的数据库实例时构造）允许客户执行托管数据库实例中的大部分 DDL 和 DML 语句。 
- SUPER 特权：[SUPER 特权](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)同样也受到限制。

## <a name="data-manipulation-statement-support"></a>数据操作语句支持

### <a name="supported"></a>支持
- LOAD DATA INFILE：受支持，但它必须指定定向到 UNC 路径（通过 XSMB 装载的 Azure 存储）的 [LOCAL] 参数。

### <a name="unsupported"></a>不支持
- SELECT ...INTO OUTFILE

## <a name="preview-functional-limitations"></a>预览版功能限制

### <a name="scale-operations"></a>缩放操作
- 目前不支持跨服务层动态缩放服务器。 也就是说，不能在基本和标准服务层之间进行切换。
- 目前不支持在预先创建的服务器上按需动态增加存储大小。
- 不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="point-in-time-restore"></a>时间点还原
- 不允许还原到不同的服务层和/或计算单元和存储大小。
- 不支持还原已删除的服务器。

## <a name="functional-limitations"></a>功能限制

### <a name="subscription-management"></a>订阅管理
- 目前不支持跨订阅和资源组动态移动预先创建的服务器。

## <a name="current-known-issues"></a>当前已知的问题
- 建立连接后，MySQL 服务器实例显示错误的服务器版本。 若要获得正确的服务器实例版本控制，请在 MySQL 提示符处使用 select version(); 命令。

## <a name="next-steps"></a>后续步骤
- [每个服务层中有哪些可用资源](concepts-service-tiers.md)
- [支持的 MySQL 数据库版本](concepts-supported-versions.md)
