---
title: Azure Database for PostgreSQL（单一服务器）中的只读副本
description: 本文介绍 Azure Database for PostgreSQL（单一服务器）中的只读副本功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 75a3c8a9912fe9ace70e411983996167da755128
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734656"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的只读副本

使用只读副本功能可将数据从 Azure Database for PostgreSQL 服务器复制到只读服务器。 可将主服务器中的数据复制到最多 5 个副本。 副本是使用 PostgreSQL 引擎的本机复制技术以异步方式更新的。

> [!IMPORTANT]
> 可以在主服务器所在的区域或所选的任何其他 Azure 区域创建只读副本。 跨区域复制目前为公共预览版。

副本是新的服务器，可以像管理普通的 Azure Database for PostgreSQL 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

了解如何[创建和管理副本](howto-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本
只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 读取工作负载可以与副本服务器隔离，而写入工作负载可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，它们不能直接缓解主服务器上的写入容量负担。 此功能并非面向写入密集型工作负荷。

只读副本功能使用 PostgreSQL 本机异步复制。 该功能不适用于同步复制方案。 主服务器与副本之间存在明显的延迟。 副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

只读副本可以增强灾难恢复计划。 首先需在不同于主区域的 Azure 区域中设置一个副本。 如果发生区域灾难，可以停止将数据复制到该副本，然后将工作负荷重定向到其中。 停止复制以后，副本就可以开始接受写入和读取操作。 在[停止复制](#stop-replication)部分了解详细信息。 

## <a name="create-a-replica"></a>创建副本
主服务器的 `azure.replication_support` 参数必须设置为 **REPLICA**。 更改此参数后，需要重启服务器才能使更改生效。 （`azure.replication_support` 参数仅适用于“常规用途”和“内存优化”层）。

启动“创建副本”工作流时，将创建空白的 Azure Database for PostgreSQL 服务器。 新服务器中填充了主服务器上的数据。 创建时间取决于主服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

每个副本可用于存储[自动增长](concepts-pricing-tiers.md#storage-auto-grow)。 增长功能允许及时了解数据复制到它，并防止中断超出存储错误引起的复制中的副本。

只读副本功能使用 PostgreSQL 的物理复制，而不使用逻辑复制。 使用复制槽位的流复制是默认的操作模式。 必要时，使用日志传送来跟上进度。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本
创建副本时，该副本不会继承主服务器的防火墙规则或 VNet 服务终结点。 必须单独为副本设置这些规则。

副本从主服务器继承其管理员帐户。 主服务器上的所有用户帐户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for PostgreSQL 服务器上连接一样。 对于名称为 **my replica**、管理员用户名为 **myadmin** 的服务器，可以使用 psql 连接到副本：

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制
Azure Database for PostgreSQL 提供用于监视复制的两个指标。 两个指标**跨副本数上限延隔**并**副本滞后**。 若要了解如何查看这些指标，请参阅**监视副本**一部分[读取副本操作指南文章](howto-read-replicas-portal.md)。

**跨副本数上限延隔**指标显示在主机和大多数滞后复制副本之间 （字节） 之间的延迟。 此指标仅适用于主服务器。

**副本滞后**度量值显示的时间，因为最后一个重播事务。 如果主服务器上未发生任何事务，则该指标会反映此滞后时间。 此指标是可用于仅副本服务器。 从计算副本滞后`pg_stat_wal_receiver`视图：

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

请设置警报，以便在副本滞后时间达到工作负荷不可接受的值时收到通知。 

若要获取更多见解，请直接查询主服务器，以获取所有副本上的复制滞后情况（以字节为单位）。

在 PostgreSQL 版本 10 中：

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

在 PostgreSQL 9.6 和更低版本中：

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 如果主服务器或只读副本重启，“副本滞后时间”指标中会反映重启以及跟上进度所花费的时间。

## <a name="stop-replication"></a>停止复制
可以停止主服务器与副本之间的复制。 停止操作会导致副本重启，并删除其复制设置。 在主服务器与只读副本之间停止复制后，副本将成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本上可用的数据。 独立服务器与主服务器不同步。

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

停止复制后，副本会丢失指向其以前的主服务器和其他副本的所有链接。 在主服务器与副本之间无法自动进行故障转移。 

了解如何[停止复制到副本](howto-read-replicas-portal.md)。


## <a name="considerations"></a>注意事项

本部分汇总了有关只读副本功能的注意事项。

### <a name="prerequisites"></a>必备组件
创建只读副本之前，必须将主服务器上的 `azure.replication_support` 参数设置为 **REPLICA**。 更改此参数后，需要重启服务器才能使更改生效。 `azure.replication_support` 参数仅适用于“常规用途”和“内存优化”层。

### <a name="new-replicas"></a>新副本
只读副本创建为新的 Azure Database for PostgreSQL 服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置
副本是使用与主服务器相同的服务器配置创建的。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本与主服务器发生的任何更改保持同步。

PostgreSQL 要求只读副本上的 `max_connections` 参数值大于或等于主服务器上的值，否则副本不会启动。 在 Azure Database for PostgreSQL 中，`max_connections` 参数值基于 SKU。 有关详细信息，请参阅 [Azure Database for PostgreSQL 中的限制](concepts-limits.md)。 

在不遵守限制的情况下尝试更新服务器值会导致出错。

### <a name="stopped-replicas"></a>停止的副本
如果停止主服务器与只读副本之间的复制，副本会重启以应用更改。 已停止的副本将成为可接受读取和写入的独立服务器。 独立服务器不能再次成为副本。

### <a name="deleted-master-and-standalone-servers"></a>删除的主服务器和独立服务器
删除主服务器后，其所有只读副本将成为独立服务器。 副本将会重启以反映此项更改。

## <a name="next-steps"></a>后续步骤
了解如何[在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。
