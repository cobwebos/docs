---
title: Azure Database for MySQL 中的只读副本。
description: 本文介绍 Azure Database for MySQL 的只读副本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: e0309ac70d39272bf045e49ca7ddecd258cc1f36
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992048"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的只读副本

使用只读副本功能可将数据从 Azure Database for MySQL 服务器复制到只读服务器。 可将主服务器中的数据复制到最多 5 个副本。 使用 MySQL 引擎的基于本机二进制日志 (binlog) 文件位置的复制技术以异步方式更新副本。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。

> [!IMPORTANT]
> 可以在主服务器所在的区域或所选的任何其他 Azure 区域创建只读副本。 跨区域复制目前为公共预览版。

副本是新的服务器，可以像管理普通的 Azure Database for MySQL 服务器一样对其进行管理。 每个只读副本按照预配计算资源的 vCore 数量以及每月 GB 存储量计费。

如需了解有关 MySQL 复制功能和问题的详细信息，请参阅 [MySQL 复制文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)。

## <a name="when-to-use-a-read-replica"></a>何时使用只读副本

只读副本功能可帮助改善读取密集型工作负荷的性能与规模。 读取工作负载可以与副本服务器隔离，而写入工作负载可以定向到主服务器。

常见方案是让 BI 和分析工作负载将只读副本用作报告的数据源。

由于副本是只读的，它们不能直接缓解主服务器上的写入容量负担。 此功能并非面向写入密集型工作负荷。

只读副本功能使用 MySQL 本机异步复制。 该功能不适用于同步复制方案。 主服务器与副本之间存在明显的延迟。 副本上的数据最终将与主服务器上的数据保持一致。 对于能够适应这种延迟的工作负荷，可以使用此功能。

## <a name="cross-region-replication"></a>跨区域复制
可以在主服务器所在的不同区域中创建读取副本。 跨区域复制有助于进行灾难恢复规划或使数据更接近用户的情况。

> [!IMPORTANT]
> 跨区域复制目前为公共预览版。

可以在任何[Azure Database for MySQL 区域](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)中拥有主服务器。  主服务器可以在其配对区域或通用副本区域中拥有副本。

### <a name="universal-replica-regions"></a>通用副本区域
无论主服务器位于何处, 始终可以在以下任何区域中创建读取副本。 下面是通用副本区域:

澳大利亚东部、澳大利亚东南部、美国中部、东亚、美国东部、美国东部2、日本东部、日本西部、韩国中部、韩国南部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、西欧、美国西部、美国西部2。


### <a name="paired-regions"></a>配对区域
除通用副本区域外, 还可以在主服务器的 Azure 配对区域中创建读取副本。 如果你不知道区域对, 可以从[Azure 配对区域一文](../best-practices-availability-paired-regions.md)了解详细信息。

如果你使用跨区域副本进行灾难恢复计划, 则建议在配对区域中创建副本, 而不是在另一个区域中创建。 配对区域避免同时进行更新, 并划分物理隔离和数据驻留的优先级。  

但是, 有一些限制: 

* 区域可用性：Azure Database for MySQL 在美国西部2、法国中部、阿拉伯联合酋长国北部和德国中部提供。 但是, 它们的配对区域不可用。
    
* 单向对:某些 Azure 区域仅在一个方向上配对。 这些区域包括印度西部、巴西南部和 US Gov 弗吉尼亚州。 
   这意味着印度西部的主服务器可以在印度南部创建副本。 但是, 印度南部的主服务器无法在印度西部创建副本。 这是因为西部印度的次要区域是印度南部地区, 而印度南部的次要区域不是西印度。


## <a name="create-a-replica"></a>创建副本

如果主服务器没有现有的副本服务器，主服务器会先重启，以自行准备复制。

启动“创建副本”工作流时，将创建空白的 Azure Database for MySQL 服务器。 新服务器中填充了主服务器上的数据。 创建时间取决于主服务器上的数据量，以及自上次每周完整备份以来所经历的时间。 具体所需时间从几分钟到几小时不等。

每个副本都启用了存储[自动增长](concepts-pricing-tiers.md#storage-auto-grow)。 自动增长功能允许副本与复制到它的数据保持同步，并防止由于存储空间不足错误而导致的复制中断。

了解如何[在 Azure 门户中创建只读副本](howto-read-replicas-portal.md)。

## <a name="connect-to-a-replica"></a>连接到副本

创建副本时，该副本不会继承主服务器的防火墙规则或 VNet 服务终结点。 必须单独为副本设置这些规则。

副本从主服务器继承其管理员帐户。 主服务器上的所有用户帐户将复制到只读副本。 只能使用主服务器上可用的用户帐户连接到只读副本。

可以使用主机名和有效的用户帐户连接到副本，就像在常规的 Azure Database for MySQL 服务器上连接一样。 对于名称为 **myreplica**、管理员用户名为 **myadmin** 的服务器，可以使用 mysql CLI 连接到副本：

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

在提示符下，输入用户帐户的密码。

## <a name="monitor-replication"></a>监视复制

Azure Database for MySQL 在 Azure Monitor 中提供“复制滞后时间(秒)”指标。 此指标仅适用于副本。

此指标是使用 MySQL 的 `SHOW SLAVE STATUS` 命令中提供的 `seconds_behind_master` 指标计算的。

请设置警报，以便在复制滞后时间达到工作负荷不可接受的值时收到通知。

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

只读副本创建为新的 Azure Database for MySQL 服务器。 无法将现有的服务器设为副本。 无法创建另一个只读副本的副本。

### <a name="replica-configuration"></a>副本配置

副本是使用与主服务器相同的服务器配置创建的。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储、备份保留期和 MySQL 引擎版本。 定价层也可以独立更改，但“基本”层除外。

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
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

将在副本服务器上锁定 [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 参数。 

### <a name="other"></a>其他

- 不支持全局事务标识符 (GTID)。
- 不支持创建副本服务器的副本。
- 内存中的表可能会导致副本服务器变得不同步。这是 MySQL 复制技术的限制。 有关详细信息，请阅读 [MySQL 参考文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)中的更多信息。
- 确保主服务器表具有主键。 缺少主键可能会导致主服务器与副本服务器之间的复制延迟。
- 查看 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)中 MySQL 复制限制的完整列表

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure 门户创建和管理只读副本](howto-read-replicas-portal.md)
- 了解如何[使用 Azure CLI 创建和管理只读副本](howto-read-replicas-cli.md)
