---
title: Azure Database for PostgreSQL 中的限制
description: 本文介绍了 Azure Database for PostgreSQL 中的限制，例如连接数和存储引擎选项。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: dc1f8581df5dc7c5728094577298ba078cc2c527
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343164"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的限制
下列各部分介绍数据库服务中的容量和功能限制。

## <a name="maximum-connections"></a>最大连接数
每个定价层的最大连接数和 vCore 数如下所示： 

|**定价层**| **vCore(s)**| 最大连接数 |
|---|---|---|
|基本| 1| 50 |
|基本| 2| 100 |
|常规用途| 2| 150|
|常规用途| 4| 250|
|常规用途| 8| 480|
|常规用途| 16| 950|
|常规用途| 32| 1500|
|内存优化| 2| 150|
|内存优化| 4| 250|
|内存优化| 8| 480|
|内存优化| 16| 950|

当连接数超出限制时，可能会收到以下错误：
> 严重：很抱歉，客户端数过多

Azure 系统需要使用五个连接来监视 Azure Database for PostgreSQL 服务器。 

## <a name="functional-limitations"></a>功能限制
### <a name="scale-operations"></a>缩放操作
- 目前不支持向/从基本定价层动态缩放。
- 目前不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="subscription-management"></a>订阅管理
- 目前不支持跨订阅和资源组动态移动服务器。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

### <a name="point-in-time-restore-pitr"></a>时间点还原 (PITR)
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的配置创建新服务器。
- 不支持还原已删除的服务器。

## <a name="next-steps"></a>后续步骤
- 了解[每个定价层中有哪些可用资源](concepts-pricing-tiers.md)
- 了解[支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)
- 查看[如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器](howto-restore-server-portal.md)
