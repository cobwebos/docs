---
title: 只读副本 - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍 Azure Database for PostgreSQL（单一服务器）中的只读副本功能。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 124034fc6c999c37c6e79547b062508c957d1bac
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939828"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的只读副本

使用只读副本功能可将数据从 Azure Database for PostgreSQL 服务器复制到只读服务器。 可以从主服务器复制到最多5个副本。 副本是使用 PostgreSQL 引擎的本机复制技术以异步方式更新的。

副本是新的服务器，可以像管理普通的 Azure Database for PostgreSQL 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

了解如何[创建和管理副本](howto-read-replicas-portal.md)。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本
只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 读取工作负荷可以独立于副本，而编写工作负荷可以定向到主副本。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，因此它们不会直接在主副本上减少写入容量负担。 此功能并非面向写入密集型工作负荷。

只读副本功能使用 PostgreSQL 本机异步复制。 该功能不适用于同步复制方案。 主副本和副本之间将有可度量的延迟。 副本上的数据最终将与主副本上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

## <a name="cross-region-replication"></a>跨区域复制
你可以在主服务器的不同区域中创建读取副本。 跨区域复制对于灾难恢复规划或使数据更接近用户等方案非常有用。

>[!NOTE]
> 基本层服务器仅支持相同区域复制。

你可以在任何 [Azure Database for PostgreSQL 区域](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql)中拥有主服务器。 主服务器可以在其配对区域或通用副本区域中拥有副本。 下图显示了哪些副本区域可用，具体取决于你的主要区域。

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="读取副本区域":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>通用副本区域
无论主服务器位于何处，始终可以在以下任何区域中创建读取副本。 下面是通用副本区域：

澳大利亚东部、澳大利亚东南部、美国中部、东亚、美国东部、美国东部2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、西欧、美国西部、美国西部2、美国中部。

### <a name="paired-regions"></a>配对区域
除通用副本区域外，还可以在主服务器的 Azure 配对区域中创建读取副本。 如果你不知道所在区域的配对，可以从 [Azure 配对区域](../best-practices-availability-paired-regions.md)一文中了解更多信息。

如果你使用跨区域副本进行灾难恢复规划，建议你在配对区域而不是其他某个区域中创建副本。 配对区域可避免同时更新，并优先考虑物理隔离和数据驻留。  

需要考虑以下限制： 

* 区域可用性： Azure Database for PostgreSQL 在法国中部、阿拉伯联合酋长国北部和德国中部提供。 但是，它们的配对区域不可用。
    
* 单向对：某些 Azure 区域仅在一个方向上配对。 这些区域包括印度西部、巴西南部。 
   这意味着印度西部的主服务器可以在印度南部创建副本。 但是，印度南部的主服务器无法在印度西部创建副本。 这是因为印度西部的次要区域是印度南部，但印度南部的次要区域不是印度西部。


## <a name="create-a-replica"></a>创建副本
启动“创建副本”工作流时，将创建空白的 Azure Database for PostgreSQL 服务器。 新服务器将用主服务器上的数据进行填充。 创建时间取决于主副本上的数据量和自上次每周完整备份后的时间。 具体所需时间从几分钟到几小时不等。

每个副本都启用了存储[自动增长](concepts-pricing-tiers.md#storage-auto-grow)。 自动增长功能允许副本与复制到它的数据保持同步，并防止由于存储空间不足错误而导致的复制中断。

只读副本功能使用 PostgreSQL 的物理复制，而不使用逻辑复制。 使用复制槽位的流复制是默认的操作模式。 必要时，使用日志传送来跟上进度。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本
创建副本时，它不会继承主服务器的防火墙规则或 VNet 服务终结点。 必须单独为副本设置这些规则。

该副本从主服务器继承管理员帐户。 主服务器上的所有用户帐户都将复制到读取副本。 只能使用主服务器上提供的用户帐户连接到读取副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for PostgreSQL 服务器上连接一样。 对于名称为 **my replica**、管理员用户名为 **myadmin** 的服务器，可以使用 psql 连接到副本：

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制
Azure Database for PostgreSQL 提供了两个用于监视复制的指标。 这两个指标是**副本的最大滞后时间**和**副本滞后时间**。 若要了解如何查看这些指标，请参阅[只读副本操作指南文章](howto-read-replicas-portal.md)的“监视副本”部分。

**副本的最大延迟**指标显示主副本和最滞后副本之间的滞后时间（以字节为单位）。 此指标仅在主服务器上可用，且仅当至少一个读取副本连接到主服务器时才可用。

“副本滞后时间”指标显示的是自上次重放事务以来所经历的时间。 如果在主服务器上没有发生事务，则该度量值将反映此时间延迟。 此指标仅适用于副本服务器。 “副本滞后时间”是从 `pg_stat_wal_receiver` 视图计算得出的：

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

请设置警报，以便在副本滞后时间达到工作负荷不可接受的值时收到通知。 

有关其他见解，请直接查询主服务器以获取所有副本的复制滞后时间（以字节为单位）。

在 PostgreSQL 版本 10 中：

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

在 PostgreSQL 9.6 和更低版本中：

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> 如果主服务器或读取副本重新启动，则重新启动和更新所用的时间将反映在 "副本滞后时间" 指标中。

## <a name="stop-replication"></a>停止复制
您可以停止主副本和副本之间的复制。 停止操作会导致副本重启，并删除其复制设置。 在主服务器和读取副本之间停止复制后，副本将成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本上可用的数据。 独立服务器不与主服务器保持同步。

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

当你停止复制时，副本将丢失所有指向其以前的主副本和其他副本的链接。

了解如何[停止复制到副本](howto-read-replicas-portal.md)。

## <a name="failover"></a>故障转移
主服务器和副本服务器之间没有自动故障转移。 

由于复制是异步的，因此主副本和副本之间存在延迟。 延迟量可能会受到许多因素的影响，例如，在主服务器上运行的工作负荷的繁重程度以及数据中心之间的延迟。 通常情况下，副本滞后时间范围在几秒钟到几分钟之间。 然而，如果主站点运行的工作负荷非常繁重并且副本的捕获速度不够快，则延迟可能会更高。 可以使用“副本延迟”指标来跟踪实际的副本延迟，该指标适用于每个副本。 该指标显示的是自上次重播事务以来所经历的时间。 建议观察一段时间的副本延迟，以便确定平均延迟。 可以针对副本延迟设置警报，这样，当它超出预期范围时，你就可以采取行动。

> [!Tip]
> 如果故障转移到副本，则从主副本解除链接陈旧副本时的滞后时间将指示丢失的数据量。

一旦决定要故障转移到某个副本， 

1. 请停止将数据复制到副本<br/>
   此步骤是使副本服务器能够接受写入所必需的。 作为此过程的一部分，副本服务器将重新启动，并从主要副本 delinked。 启动停止复制的操作后，后端进程通常需要大约 2 分钟才能完成。 请参阅本文的[停止复制](#stop-replication)部分，了解此操作的潜在影响。
    
2. 将应用程序指向（以前的）副本<br/>
   每个服务器都有唯一的连接字符串。 更新应用程序，使之指向 (以前) 副本，而不是主副本。
    
如果应用程序成功处理了读取和写入操作，则表明故障转移已完成。 应用程序经历的停机时间取决于何时检测到问题并完成上面的步骤 1 和 2。

### <a name="disaster-recovery"></a>灾难恢复

当出现重大灾难事件（如可用性区域级别或区域故障）时，可以通过提升读取副本来执行灾难恢复操作。 在 UI 门户中，可以导航到读取副本服务器。 然后单击 "复制" 选项卡，可以停止副本以将其升级为独立服务器。 或者，你可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/postgres/server/replica?view=azure-cli-latest#az_postgres_server_replica_stop) 来停止和升级副本服务器。

## <a name="considerations"></a>注意事项

本部分汇总了有关只读副本功能的注意事项。

### <a name="prerequisites"></a>先决条件
只读副本和[逻辑解码](concepts-logical.md)都依赖 Postgres 预写日志 (WAL) 来获取信息。 这两个功能需要来自 Postgres 的不同级别的日志记录。 逻辑解码需要的日志记录的级别比只读副本需要的更高。

若要配置正确的日志记录级别，请使用 Azure 复制支持参数。 Azure 复制支持有三个设置选项：

* **关闭** - 在 WAL 中包含最少的信息。 大多数 Azure Database for PostgreSQL 服务器上都不提供此设置。  
* **副本** - 比“关闭”详细。 这是运行[只读副本](concepts-read-replicas.md)所需的最低日志记录级别。 此设置是大多数服务器上的默认设置。
* **逻辑** - 比“副本”详细。 这是运行逻辑解码所需的最低日志记录级别。 使用此设置时，只读副本也可以运行。

更改此参数后，需要重启服务器。 在内部，此参数设置 Postgres 参数 `wal_level`、`max_replication_slots` 和 `max_wal_senders`。

### <a name="new-replicas"></a>新副本
只读副本创建为新的 Azure Database for PostgreSQL 服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置
使用与主副本相同的计算和存储设置创建副本。 创建副本后，可以更改多个设置，包括存储和备份保留期。

创建副本时或之后，防火墙规则、虚拟网络规则和参数设置不会从主服务器继承到副本。

### <a name="scaling"></a>扩展
缩放 vCore 或者在“常规用途”和“内存优化”之间缩放：
* PostgreSQL 要求辅助服务器上的 `max_connections` 设置[大于或等于主服务器上的设置](https://www.postgresql.org/docs/current/hot-standby.html)，否则辅助服务器将不会启动。
* 在 Azure Database for PostgreSQL 中，所允许的每台服务器的最大连接数已固定到计算 sku，因为连接会占用内存。 可以详细了解 [max_connections 与计算 sku 之间的映射](concepts-limits.md)。
* 纵向扩展：先纵向扩展副本的计算，然后纵向扩展主服务器。 此顺序可防止因违反 `max_connections` 要求而出现错误。
* 纵向缩减：先纵向缩减主服务器的计算，然后纵向缩减副本。 如果尝试将副本缩放至低于主服务器的级别，将会出现错误，因为这样会违反 `max_connections` 要求。

缩放存储：
* 所有副本都启用了存储自动增长，以防止存储空间已满的副本出现复制问题。 无法禁用此设置。
* 你也可以手动纵向扩展存储，就像在任何其他服务器上一样


### <a name="basic-tier"></a>基本层
基本层服务器仅支持相同区域复制。

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL 要求](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` 读取副本上的参数值大于或等于主值，否则副本将无法启动。 如果要 `max_prepared_transactions` 在主副本上更改，请先在副本上更改它。

### <a name="stopped-replicas"></a>停止的副本
如果停止主服务器和读取副本之间的复制，副本将重启以应用更改。 已停止的副本将成为可接受读取和写入的独立服务器。 独立服务器不能再次成为副本。

### <a name="deleted-primary-and-standalone-servers"></a>删除的主服务器和独立服务器
删除主服务器后，其所有读取副本都将成为独立服务器。 副本将会重启以反映此项更改。

## <a name="next-steps"></a>后续步骤
* 了解如何[在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。
* 了解如何[通过 Azure CLI 和 REST API 创建和管理只读副本](howto-read-replicas-cli.md)。
