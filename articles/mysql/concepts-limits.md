---
title: Azure Database for MySQL 中的限制
description: 本文介绍了 Azure Database for MySQL 中的限制，例如连接数和存储引擎选项。
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 2fa69182b4238cfd19fcc9571e4327512e9528c1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的限制
以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。 另请参阅适用于 MySQL 数据库引擎的[常规限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)。

## <a name="service-tier-maximums"></a>服务层最大值
创建服务器时，Azure Database for MySQL 中提供多个可供选择的服务层。 有关详细信息，请参阅 [Azure Database for MySQL 定价层](concepts-pricing-tiers.md)。  

每个服务层中的连接数、计算单位和存储均有最大值限制，如下所示： 

|**定价层**| **计算代**|**vCore(s)**| 最大连接数|
|---|---|---|---|
|基本| 第 4 代| 1| 50|
|基本| 第 4 代| 2| 100|
|基本| 第 5 代| 1| 50|
|基本| 第 5 代| 2| 100|
|常规用途| 第 4 代| 2| 300|
|常规用途| 第 4 代| 4| 625|
|常规用途| 第 4 代| 8| 1250|
|常规用途| 第 4 代| 16| 2500|
|常规用途| 第 4 代| 32| 5000|
|常规用途| 第 5 代| 2| 300|
|常规用途| 第 5 代| 4| 625|
|常规用途| 第 5 代| 8| 1250|
|常规用途| 第 5 代| 16| 2500|
|常规用途| 第 5 代| 32| 5000|
|内存优化| 第 5 代| 2| 600|
|内存优化| 第 5 代| 4| 1250|
|内存优化| 第 5 代| 8| 2500|
|内存优化| 第 5 代| 16| 5000|

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
- DBA 角色：许多服务器参数和设置可能会无意中导致服务器性能下降或使 DBMS 的 ACID 属性无效。 因此，为了维护产品级别的服务完整性和 SLA，此服务不公开 DBA 角色。 默认用户帐户（在创建新的数据库实例时构造）允许该用户执行托管数据库实例中的大部分 DDL 和 DML 语句。 
- SUPER 特权：[SUPER 特权](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)同样也受到限制。

## <a name="data-manipulation-statement-support"></a>数据操作语句支持

### <a name="supported"></a>支持
- LOAD DATA INFILE：受支持，但它必须指定定向到 UNC 路径（通过 XSMB 装载的 Azure 存储）的 [LOCAL] 参数。

### <a name="unsupported"></a>不支持
- SELECT ...INTO OUTFILE

## <a name="functional-limitations"></a>功能限制

### <a name="scale-operations"></a>缩放操作
- 目前不支持跨定价层动态缩放服务器。 即，在“基本”、“常规用途”和“内存优化”定价层之间进行切换。
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
- [每个服务层中有哪些可用资源](concepts-pricing-tiers.md)
- [支持的 MySQL 数据库版本](concepts-supported-versions.md)
