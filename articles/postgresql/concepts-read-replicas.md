---
title: Azure Database for PostgreSQL 中的只读副本
description: 本文介绍 Azure Database for PostgreSQL 中的只读副本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 9270c3290bd7be0bbb79d30aff8becc04dcfc603
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903989"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的只读副本

> [!IMPORTANT]
> 只读副本功能目前以公共预览版提供。

使用只读副本功能，可将数据从 Azure Database for PostgreSQL 服务器（主服务器）复制到同一 Azure 区域中最多五个只读服务器（只读副本）。 只读副本是使用 PostgreSQL 引擎的本机复制技术以异步方式更新的。

副本是一些新的服务器，可以像管理普通的独立 Azure Database for PostgreSQL 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及预配的每月 GB 存储量计费。

## <a name="when-to-use-read-replicas"></a>何时使用只读副本
只读副本功能旨在帮助改善读取密集型工作负荷的性能与规模。 例如，读取工作负荷可与副本相隔离，而写入工作负荷可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，它们不能直接缓解主服务器上的写入容量负担，因此，此功能并不适用于写入密集型工作负荷。

只读副本功能使用 PostgreSQL 的异步复制，因此不适用于同步复制方案。 主服务器与副本之间存在明显的延迟。 副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

## <a name="creating-a-replica"></a>创建副本
主服务器的 **azure.replication_support** 必须设置为 REPLICA。 更改此参数后，需要重启服务器才能使更改生效。 （**Azure.replication_support** 参数仅适用于“常规用途”和“内存优化”层）。

启动“创建副本”工作流时，将创建空白的 Azure Database for PostgreSQL 服务器。 新服务器中填充了主服务器上的数据。 创建新副本所花费的时间取决于主服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

只读副本功能使用 PostgreSQL 的物理复制（不是逻辑复制）。 使用复制槽位的流复制是默认的操作模式。 必要时，使用日志传送来跟上进度。

> [!NOTE]
> 如果尚未在服务器上设置存储警报，我们建议进行设置，以便在服务器即将达到其存储限制（这会影响复制）时可以收到通知。

[了解如何在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connecting-to-a-replica"></a>连接到副本
创建副本时，该副本不会继承主服务器的防火墙规则或 VNet 服务终结点。 必须单独为副本设置这些规则。

副本从主服务器继承其管理员帐户。 主服务器上的所有用户帐户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for PostgreSQL 服务器上连接一样。 例如，如果服务器的名称为 myreplica，管理员用户名为 myadmin，则可按如下所示从 psql 连接到该服务器：

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
在提示符下，输入用户帐户的密码。

## <a name="monitoring-replication"></a>监视复制
Azure Monitor 中提供了“副本的最大滞后时间”指标。 此指标仅适用于主服务器。 该指标显示主服务器与滞后时间最长的副本之间的滞后时间。 

Azure Monitor 中还提供了“副本滞后时间”指标。 此指标仅适用于副本。 

该指标是从 pg_stat_wal_receiver 视图计算的：

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
请记住，“副本滞后时间”指标显示的是自上次重放事务以来所经历的时间。 如果主服务器上未发生任何事务，则该指标会反映此滞后时间。

我们建议设置警报，以便在副本滞后时间达到工作负荷不可接受的值时收到通知。 

若要获取更多见解，请直接查询主服务器，以获取所有副本上的复制滞后情况（以字节为单位）。
在 PG 10 上：
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

在 PG 9.6 及更低版本上：
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 如果主服务器或副本服务器重启，“副本滞后时间”指标中会反映重启以及跟上进度所花费的时间。

## <a name="stopping-replication-to-a-replica"></a>停止复制到副本
可以选择停止主服务器与副本之间的复制。 这将导致副本重启，以删除其复制设置。 在主服务器与副本服务器之间停止复制后，副本服务器将成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本服务器上可用的数据。 此独立服务器与主服务器不同步。

此服务器不能再次成为副本服务器。

在停止复制之前，请确保副本包含所需的全部数据。

可以[在操作指南文档中了解如何停止副本](howto-read-replicas-portal.md)。


## <a name="considerations"></a>注意事项

### <a name="preparing-for-replica"></a>准备副本
在创建副本之前，必须将主服务器上的 **azure.replication_support** 设置为 REPLICA。 更改此参数后，需要重启服务器才能使更改生效。 此参数仅适用于“常规用途”和“内存优化”层。

### <a name="stopped-replicas"></a>停止的副本
当你选择停止主服务器与副本之间的复制时，副本将会重启以应用这些更改。 以后，此服务器不能再次成为副本。

### <a name="replicas-are-new-servers"></a>副本服务器是新服务器
副本创建为新的 Azure Database for PostgreSQL 服务器。 现有服务器不能成为副本服务器。

### <a name="replica-server-configuration"></a>副本服务器配置
使用与主服务器相同的服务器配置创建副本服务器，其中包括以下配置：
- 定价层
- 计算的代
- vCore 数
- 存储
- 备份保留期
- 备份冗余选项
- PostgreSQL 引擎版本

创建副本后，可以独立于主服务器更改定价层（“基本”除外）、计算代系、vCore 数、存储和备份保留期。

> [!IMPORTANT]
> 将主服务器的配置更新为新值之前，应将副本的配置更新为与这些新值相等或更大的值。 这可以确保副本能够与主服务器的更改保持同步。

具体而言，Postgres 要求副本服务器的 max_connections 参数值大于或等于主服务器的值，否则副本不会启动。 在 Azure Database for PostgreSQL 中，max_connections 值是根据 SKU 设置的。 有关详细信息，请阅读[限制文档](concepts-limits.md)。 

违背此要求尝试更新会导致出错。


### <a name="deleting-the-master"></a>删除主服务器
删除主服务器后，其所有只读副本将成为独立服务器。 副本将会重启以反映此项更改。

### <a name="other"></a>其他
- 只能在主服务器所在的同一 Azure 区域中创建只读副本。
- 不支持创建副本服务器的副本。

## <a name="next-steps"></a>后续步骤
- [如何在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。
