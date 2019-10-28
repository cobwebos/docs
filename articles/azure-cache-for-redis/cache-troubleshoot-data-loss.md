---
title: 排查 Azure Cache for Redis 数据丢失问题 |Microsoft Docs
description: 了解如何解决 Redis 的 Azure 缓存的数据丢失问题
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 523f4a302eb1f4679eb34bc959efc895fa5408ec
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821040"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>排查 Azure Cache for Redis 数据丢失问题

本部分讨论如何诊断 Azure Cache for Redis 中可能发生的实际或数据丢失。

- [键部分丢失](#partial-loss-of-keys)
- [密钥的主要或完全丢失](#major-or-complete-loss-of-keys)

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 有关详细信息和说明，请参阅[其他信息](#additional-information)部分的文章。
>

## <a name="partial-loss-of-keys"></a>键部分丢失

一旦将密钥存储在内存中，Redis 不会随机删除密钥。 但是，它将删除密钥，以响应过期或逐出策略以及显式密钥删除命令。 此外，在 Redis 高级或标准 Azure 缓存中写入主节点的密钥可能无法立即在副本上使用。 以异步和非阻塞方式将数据从主副本复制到副本。

如果发现缓存中的密钥已消失，可以检查以下各项以查看原因：

| 原因 | 描述 |
|---|---|
| [密钥过期](#key-expiration) | 由于已设置了超时，密钥被删除 |
| [密钥逐出](#key-eviction) | 在内存压力下删除密钥 |
| [删除密钥](#key-deletion) | 显式 delete 命令将删除密钥 |
| [异步复制](#async-replication) | 由于数据复制延迟，副本上的密钥不可用 |

### <a name="key-expiration"></a>密钥过期

如果分配了一个超时值并且该时间段已过去，Redis 将自动删除该密钥。 可以在[过期](http://redis.io/commands/expire)命令文档中找到有关 Redis 密钥过期的更多详细信息。 还可以通过[set](http://redis.io/commands/set)、 [SETEX](https://redis.io/commands/setex)、 [GETSET](https://redis.io/commands/getset)和其他 \*STORE 命令设置超时值。

可以使用[INFO](http://redis.io/commands/info)命令获取过期的密钥数。 "*统计*信息" 部分显示过期密钥的总数。 *密钥空间*部分提供有关具有超时和平均超时值的键的数目的附加信息。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

此外，您还可以查看缓存的诊断指标，以确定该键是否丢失以及过期密钥中是否存在峰值。 请参阅[附录](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)，了解有关使用密钥空间通知或监视器来调试这些类型的问题的信息。

### <a name="key-eviction"></a>密钥逐出

Redis 需要内存空间来存储数据。 必要时，它将清除密钥以释放可用内存。 当[INFO](http://redis.io/commands/info)命令中的**used_memory**或**used_memory_rss**值接近配置的**maxmemory**设置时，Redis 将根据[缓存策略](http://redis.io/topics/lru-cache)开始从内存中逐出密钥。

可以使用[INFO](http://redis.io/commands/info)命令监视已逐出的密钥数。

```
# Stats

evicted_keys:13224
```

此外，您还可以查看缓存的诊断指标，以确定该键是否丢失以及逐出密钥中是否存在峰值。 请参阅[附录](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)，了解有关使用密钥空间通知或监视器来调试这些类型的问题的信息。

### <a name="key-deletion"></a>删除密钥

Redis 客户端可以发出[DEL](http://redis.io/commands/del)或[HDEL](http://redis.io/commands/hdel)命令，以显式删除 Redis 中的密钥。 您可以使用[INFO](http://redis.io/commands/info)命令跟踪删除操作的数量。 如果已调用 DEL 或 HDEL 命令，则它们将在*Commandstats*节中列出。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>异步复制

标准或高级层中的 Redis 的任何 Azure 缓存都配置有一个主节点和至少一个副本。 使用后台进程以异步方式将数据从主副本复制到副本。 [Redis.io](http://redis.io/topics/replication)网站介绍了 redis 数据复制的一般工作方式。 对于客户端频繁写入 Redis 的情况，可能会发生部分数据丢失，这是因为确保此复制是瞬时的。 例如，如果主节点在客户端向其写入密钥_后_出现，但在后台进程有机会将此密钥发送到副本_之前_，则当副本接管为新主副本时，该键会丢失。

## <a name="major-or-complete-loss-of-keys"></a>密钥的主要或完全丢失

如果你发现你的缓存中的大部分或全部键都已消失，可以检查以下内容以了解原因：

| 原因 | 描述 |
|---|---|
| [密钥刷新](#key-flushing) | 已手动清除密钥 |
| [不正确的数据库选择](#incorrect-database-selection) | Redis 设置为使用非默认数据库 |
| [Redis 实例失败](#redis-instance-failure) | 显式 delete 命令将删除密钥 |

### <a name="key-flushing"></a>密钥刷新

客户端可以调用[FLUSHDB](http://redis.io/commands/flushdb)命令来删除**单个**数据库中的所有键，或使用[FLUSHALL](http://redis.io/commands/flushall)删除 Redis 缓存中**所有**数据库的所有键。 您可以使用[INFO](http://redis.io/commands/info)命令确定是否已刷新了键。 它将显示在*Commandstats*节中是否调用了任一 FLUSH 命令。

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>不正确的数据库选择

默认情况下，用于 Redis 的 Azure 缓存使用**db0**数据库。 如果切换到其他数据库（例如 db1），并尝试从该数据库中读取密钥，Redis 将不会在此处找到这些密钥，因为每个数据库都是一个逻辑上独立的单元，并保存了不同的数据集。 使用 "[选择](http://redis.io/commands/select)" 命令可使用其他可用数据库，并在每个数据库中查找密钥。

### <a name="redis-instance-failure"></a>Redis 实例失败

Redis 是内存中数据存储区。 数据保存在托管 Redis 的物理机或虚拟机上。 基本层中的 Redis 实例的 Azure Cache 仅在单个虚拟机（VM）上运行。 如果该 VM 关闭，则您在缓存中存储的所有数据都将丢失。 标准层和高级层中的缓存通过在复制的配置中使用两个 Vm 来提供更高的灵活性，防止数据丢失。 此类缓存中的主节点发生故障时，副本节点会自动处理数据。 这些 Vm 位于不同的容错域和更新域中，以最大程度地降低两者同时变为不可用的几率。 但在发生主要数据中心服务中断的情况下，Vm 仍可同时出现故障。 在这种罕见情况下，你的数据将会丢失。

你应考虑使用[Redis 数据持久性](http://redis.io/topics/persistence)和[异地复制](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)来改善对数据的保护，使其不受这些基础结构故障的影响。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
