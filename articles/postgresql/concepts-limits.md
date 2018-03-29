---
title: Azure Database for PostgreSQL 中的限制
description: 本文介绍了 Azure Database for PostgreSQL 中的限制，例如连接数和存储引擎选项。
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的限制
下列各部分介绍数据库服务中的容量和功能限制。

## <a name="pricing-tier-maximums"></a>定价层最大值
创建服务器时，Azure Database for PostgreSQL 中提供了多个可供选择的定价层。 有关详细信息，请参阅 [Azure Database for PostgreSQL 中的定价层](concepts-pricing-tiers.md)。  

每个定价层中的连接数、计算单位和存储均有最大值限制，如下所示： 

|定价层| 计算代| vCore| 最大连接数 |
|---|---|---|---|
|基本| 第 4 代| 1| 50 |
|基本| 第 4 代| 2| 100 |
|基本| 第 5 代| 1| 50 |
|基本| 第 5 代| 2| 100 |
|常规用途| 第 4 代| 2| 150|
|常规用途| 第 4 代| 4| 250|
|常规用途| 第 4 代| 8| 480|
|常规用途| 第 4 代| 16| 950|
|常规用途| 第 4 代| 32| 1500|
|常规用途| 第 5 代| 2| 150|
|常规用途| 第 5 代| 4| 250|
|常规用途| 第 5 代| 8| 480|
|常规用途| 第 5 代| 16| 950|
|常规用途| 第 5 代| 32| 1500|
|内存优化| 第 5 代| 2| 150|
|内存优化| 第 5 代| 4| 250|
|内存优化| 第 5 代| 8| 480|
|内存优化| 第 5 代| 16| 950|

当连接数超出限制时，可能会收到以下错误：
> 严重：很抱歉，客户端数过多

Azure 系统需要使用五个连接来监视 Azure Database for PostgreSQL 服务器。 

## <a name="functional-limitations"></a>功能限制
### <a name="scale-operations"></a>缩放操作
1.  目前不支持跨定价层动态缩放服务器。 即，在基本、常规用途或内存优化层之间进行切换。
2.  目前不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="subscription-management"></a>订阅管理
- 目前不支持跨订阅和资源组动态移动服务器。

### <a name="point-in-time-restore-pitr"></a>时间点还原 (PITR)
1.  使用 PITR 功能时，将使用与新服务器所基于的服务器相同的配置创建新服务器。
2.  不支持还原已删除的服务器。

## <a name="next-steps"></a>后续步骤
- 了解[每个定价层中有哪些可用资源](concepts-pricing-tiers.md)
- 了解[支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)
- 查看[如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器](howto-restore-server-portal.md)
