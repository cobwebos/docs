---
title: Azure Database for MariaDB 中读取副本
description: 本文介绍 Azure Database for MariaDB 只读的副本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 8abe257090b5159053a37350c9e24cc27073679b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079361"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中读取副本

读取的副本功能，可将数据从 Azure Database for MariaDB 服务器复制到只读的服务器。 可以从主服务器复制到作为主节点在同一区域中最多五个副本。 使用具有全局事务 ID (GTID) MariaDB 引擎的二进制日志 (binlog) 文件基于位置的复制技术以异步方式更新副本。 要详细了解 binlog 复制，请参阅 [binlog 复制概述](https://mariadb.com/kb/en/library/replication-overview/)。

> [!IMPORTANT]
> 同一个区域读取副本当前处于公共预览状态。

副本是管理类似于常规的 Azure Database for MariaDB 服务器的新服务器。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

若要了解有关 GTID 复制的详细信息，请参阅[MariaDB 复制文档](https://mariadb.com/kb/en/library/gtid/)。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本

只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 读取工作负载可以与副本服务器隔离，而写入工作负载可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，它们不能直接缓解主服务器上的写入容量负担。 此功能并非面向写入密集型工作负荷。

读取的副本功能使用异步复制。 该功能不适用于同步复制方案。 主服务器与副本之间存在明显的延迟。 副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

只读副本可以增强灾难恢复计划。 如果没有区域性灾难并且主服务器不可用，可以将定向到另一个区域中的副本工作负荷。 若要执行此操作，首先让通过停止复制函数接受写入的副本。 然后可以通过更新连接字符串来重定向应用程序。 在[停止复制](#stop-replication)部分了解详细信息。

## <a name="create-a-replica"></a>创建副本

如果主服务器没有现有的副本服务器，主服务器会先重启，以自行准备复制。

创建副本工作流启动时，创建空的 Azure Database for MariaDB 服务器。 新服务器中填充了主服务器上的数据。 创建时间取决于主服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

> [!NOTE]
> 如果尚未在服务器上设置存储警报，我们建议进行设置。 当服务器即将达到其存储限制（这会影响复制）时，警报可以向你发出通知。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本

创建副本时，该副本不会继承主服务器的防火墙规则或 VNet 服务终结点。 必须单独为副本设置这些规则。

副本从主服务器继承其管理员帐户。 主服务器上的所有用户帐户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

您可以通过连接到副本使用其主机名和有效的用户帐户，就像常规的 Azure Database for MariaDB 服务器上。 对于名称为 **myreplica**、管理员用户名为 **myadmin** 的服务器，可以使用 mysql CLI 连接到副本：

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制

Azure Database for MariaDB 提供**复制延迟 （秒）** 在 Azure Monitor 指标。 此指标仅适用于副本。

使用计算此指标`seconds_behind_master`MariaDB 的中可用的指标`SHOW SLAVE STATUS`命令。

设置警报来通知你的复制延迟时达到不可接受的工作负荷的值。

## <a name="stop-replication"></a>停止复制

可以停止主服务器与副本之间的复制。 在主服务器与只读副本之间停止复制后，副本将成为独立服务器。 独立服务器中的数据是启动“停止复制”命令时副本上可用的数据。 独立服务器与主服务器不同步。

选择停止复制到副本时，副本会丢失指向其以前的主服务器和其他副本的所有链接。 在主服务器与其副本之间无法自动进行故障转移。

> [!IMPORTANT]
> 独立服务器不能再次成为副本。
> 在只读副本上停止复制之前，请确保副本包含所需的全部数据。

了解如何[停止复制到副本](howto-read-replicas-portal.md)。

## <a name="considerations-and-limitations"></a>注意事项和限制

### <a name="pricing-tiers"></a>定价层

只读副本当前仅适用于“常规用途”和“内存优化”的定价层。

### <a name="master-server-restart"></a>主服务器重启

如果为没有现有副本的主服务器创建副本，主服务器将首先重启以便为复制准备自身。 请考虑这一点并在非高峰期执行这些操作。

### <a name="new-replicas"></a>新副本

为新的 Azure Database for MariaDB 服务器创建了一个只读的副本。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置

副本是使用与主服务器相同的服务器配置创建的。 创建一个副本之后，可以独立地更改多个设置从主服务器： 计算代、 vcore 数、 存储、 备份保留期和 MariaDB 引擎版本。 定价层也可以独立更改，但“基本”层除外。

> [!IMPORTANT]
> 将主服务器的配置更新为新值之前，请将副本配置更新为与这些新值相等或更大的值。 此操作可确保副本与主服务器发生的任何更改保持同步。

### <a name="stopped-replicas"></a>停止的副本

如果停止主服务器与只读副本之间的复制，已停止的副本将成为可接受读取和写入的独立服务器。 独立服务器不能再次成为副本。

### <a name="deleted-master-and-standalone-servers"></a>删除的主服务器和独立服务器

删除主服务器后，将对所有只读副本停止复制。 这些副本服务器将成为独立服务器。 将删除主服务器本身。

### <a name="user-accounts"></a>用户帐户

主服务器上的用户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

### <a name="server-parameters"></a>服务器参数

为了防止数据不同步并避免潜在的数据丢失或损坏，使用读取副本时，会锁定某些服务器参数以防止其更新。

将在主服务器和副本服务器上锁定以下的服务器参数：
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

将在副本服务器上锁定 [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) 参数。

### <a name="other"></a>其他

- 不支持创建副本服务器的副本。
- 内存中的表可能会导致副本服务器变得不同步。这是 MariaDB 复制技术的限制。
- 确保主服务器表具有主键。 缺少主键可能会导致主服务器与副本服务器之间的复制延迟。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户创建和管理只读副本](howto-read-replicas-portal.md)
