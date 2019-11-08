---
title: 排查 Azure Cache for Redis 的数据丢失问题 |Microsoft Docs
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
ms.openlocfilehash: 8165ce3195c12af52cfee2fff598d9417697f4cb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806560"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>排查 Azure Cache for Redis 的数据丢失问题

本文介绍如何诊断 Azure Cache for Redis 中可能发生的实际或数据丢失。

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 有关详细信息和说明，请参阅[其他信息](#additional-information)部分的文章。
>

## <a name="partial-loss-of-keys"></a>键部分丢失

用于 Redis 的 Azure 缓存在存储到内存中后不会随机删除密钥。 但是，它会删除密钥以响应过期或逐出策略，并删除显式密钥删除命令。 已写入高级或标准 Azure Cache for Redis 实例中的主节点的密钥也可能不会立即在副本上可用。 以异步和非阻塞方式将数据从主副本复制到副本。

如果发现缓存中的密钥已消失，请检查以下可能的原因：

| 原因 | 说明 |
|---|---|
| [密钥过期](#key-expiration) | 由于已设置了超时，因此删除了这些密钥。 |
| [密钥逐出](#key-eviction) | 在内存压力下删除键。 |
| [删除密钥](#key-deletion) | 显式 delete 命令将删除密钥。 |
| [异步复制](#async-replication) | 由于数据复制延迟，密钥在副本上不可用。 |

### <a name="key-expiration"></a>密钥过期

如果为密钥分配了超时并且已超过该时间段，则 Azure Cache for Redis 会自动删除密钥。 有关 Redis 密钥过期的详细信息，请参阅[过期](http://redis.io/commands/expire)命令文档。 还可以通过使用[set](http://redis.io/commands/set)、 [SETEX](https://redis.io/commands/setex)、 [GETSET](https://redis.io/commands/getset)和其他 **\*存储**命令来设置超时值。

若要获取已过期的密钥数的统计信息，请使用[INFO](http://redis.io/commands/info)命令。 "`Stats`" 部分显示过期密钥的总数。 "`Keyspace`" 部分提供了有关超时值和平均超时值的详细信息。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

你还可以查看缓存的诊断指标，以确定密钥何时丢失和过期密钥中是否存在峰值。 有关使用密钥空间通知或**监视器**来调试这些类型的问题的信息，请参阅[调试 Redis 密钥空间未命中](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)的附录。

### <a name="key-eviction"></a>密钥逐出

用于 Redis 的 Azure 缓存需要内存空间来存储数据。 必要时，它会清除密钥以释放可用内存。 当[INFO](http://redis.io/commands/info)命令中的**used_memory**或**used_memory_rss**值接近配置的**Maxmemory**设置时，Azure Cache for Redis 会根据[缓存策略](http://redis.io/topics/lru-cache)开始从内存中逐出密钥。

可以使用[INFO](http://redis.io/commands/info)命令监视逐出密钥的数目：

```
# Stats

evicted_keys:13224
```

你还可以查看缓存的诊断指标，以了解密钥是否丢失以及逐出密钥中是否存在峰值。 有关使用密钥空间通知或**监视器**来调试这些类型的问题的信息，请参阅[调试 Redis 密钥空间未命中](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)的附录。

### <a name="key-deletion"></a>删除密钥

Redis 客户端可以发出[DEL](http://redis.io/commands/del)或[HDEL](http://redis.io/commands/hdel)命令，从 Azure Cache for Redis 显式删除密钥。 您可以使用[INFO](http://redis.io/commands/info)命令跟踪删除操作的数量。 如果已调用**DEL**或**HDEL**命令，它们将列在 `Commandstats` 部分中。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>异步复制

标准或高级层中的 Redis 实例的任何 Azure 缓存都配置有一个主节点和至少一个副本。 使用后台进程以异步方式将数据从主副本复制到副本。 [Redis.io](http://redis.io/topics/replication)网站介绍了 redis 数据复制的一般工作方式。 对于客户端频繁写入 Redis 的情况，可能会发生部分数据丢失，因为这种复制是可保证的。 例如，如果在客户端向其写入密钥*后*主服务器出现故障，但在后台进程有机会将该密钥发送到副本*之前*，则当副本接管作为新的主副本时，该键会丢失。

## <a name="major-or-complete-loss-of-keys"></a>密钥的主要或完全丢失

如果缓存中的大部分或全部键都已消失，请检查以下可能的原因：

| 原因 | 说明 |
|---|---|
| [密钥刷新](#key-flushing) | 已手动清除密钥。 |
| [不正确的数据库选择](#incorrect-database-selection) | 适用于 Redis 的 Azure 缓存设置为使用非默认数据库。 |
| [Redis 实例失败](#redis-instance-failure) | Redis 服务器不可用。 |

### <a name="key-flushing"></a>密钥刷新

客户端可以调用[FLUSHDB](http://redis.io/commands/flushdb)命令来删除*单个*数据库中的所有键，或使用[FLUSHALL](http://redis.io/commands/flushall)删除 Redis 缓存中*所有*数据库的所有键。 若要确定是否已刷新密钥，请使用[INFO](http://redis.io/commands/info)命令。 "`Commandstats`" 部分显示是否调用了两个**FLUSH**命令：

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>不正确的数据库选择

默认情况下，用于 Redis 的 Azure 缓存使用**db0**数据库。 如果切换到其他数据库（例如**db1**）并尝试从中读取密钥，则 Azure Cache for Redis 将不会在那里找到它们。 每个数据库都是一个逻辑上独立的单元，并包含不同的数据集。 使用 "[选择](http://redis.io/commands/select)" 命令可使用其他可用数据库，并在每个数据库中查找密钥。

### <a name="redis-instance-failure"></a>Redis 实例失败

Redis 是内存中数据存储区。 数据保存在托管 Redis 缓存的物理计算机或虚拟机上。 基本层中的 Redis 实例的 Azure Cache 仅在单个虚拟机（VM）上运行。 如果该 VM 关闭，则您在缓存中存储的所有数据都将丢失。 

标准层和高级层中的缓存通过在复制的配置中使用两个 Vm 来提供更高的灵活性，防止数据丢失。 此类缓存中的主节点发生故障时，副本节点会自动处理数据。 这些 Vm 位于不同的域中，用于故障和更新，从而最大限度地降低两者同时变为不可用的几率。 但是，如果发生了重大的数据中心中断，则 Vm 可能仍会出现问题。 在这种罕见情况下，你的数据将会丢失。

请考虑使用[Redis 数据持久性](http://redis.io/topics/persistence)和[异地复制](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)来改善对数据的保护，使其不受这些基础结构故障的影响。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
