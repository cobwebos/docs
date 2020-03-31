---
title: 排查 Azure Cache for Redis 中的数据丢失问题
description: 了解如何使用 Azure Cache for Redis 解决数据丢失问题，例如密钥部分丢失、密钥过期或密钥完全丢失。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530895"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>排查 Azure Cache for Redis 中的数据丢失问题

本文探讨如何诊断 Azure Cache for Redis 中实际发生的，或预计可能发生的数据丢失问题。

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 有关详细信息和说明，请参阅[其他信息](#additional-information)部分的文章。
>

## <a name="partial-loss-of-keys"></a>密钥部分丢失

将密钥存储在内存中后，Azure Cache for Redis 不会随机删除密钥。 但是，在响应过期或逐出策略以及显式密钥删除命令时，它确实会删除密钥。 此外，在高级或标准版 Azure Cache for Redis 实例中写入到主节点的密钥可能不会立即在副本中出现。 数据将以异步的非阻塞方式从主节点复制到副本。

如果你发现密钥在缓存中消失，请检查以下可能原因：

| 原因 | 描述 |
|---|---|
| [密钥到期时间](#key-expiration) | 密钥因设置了超时而被删除。 |
| [密钥逐出](#key-eviction) | 在内存压力较大的情况下删除了密钥。 |
| [密钥删除](#key-deletion) | 显式删除命令删除了密钥。 |
| [异步复制](#async-replication) | 由于数据复制延迟，副本上未提供密钥。 |

### <a name="key-expiration"></a>密钥到期时间

如果为密钥分配了超时，而该期限已过，则 Azure Cache for Redis 会自动删除该密钥。 有关 Redis 密钥过期的详细信息，请参阅 [EXPIRE](https://redis.io/commands/expire) 命令文档。 还可以使用 [SET](https://redis.io/commands/set)、[SETEX](https://redis.io/commands/setex)、[GETSET](https://redis.io/commands/getset) 和其他 **\*STORE** 命令来设置超时值。

若要获取有关已过期密钥数的统计信息，请使用 [INFO](https://redis.io/commands/info) 命令。 `Stats` 部分显示已过期密钥的总数。 `Keyspace` 部分提供有关设置了超时的密钥数以及平均超时值的详细信息。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

此外，可以查看缓存的诊断指标，确定密钥丢失的时间以及已过期密钥的高峰之间是否存在某种关联。 有关如何使用密钥空间通知或 **MONITOR** 调试此类问题的信息，请参阅[调试 Redis 密钥空间缺失](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)附录。

### <a name="key-eviction"></a>密钥逐出

Azure Cache for Redis 需要使用内存空间来存储数据。 在必要时，它将清除密钥以释放可用内存。 如果 [INFO](https://redis.io/commands/info) 命令中的 **used_memory** 或 **used_memory_rss** 值即将达到配置的 **maxmemory** 设置，Azure Cache for Redis 将会根据[缓存策略](https://redis.io/topics/lru-cache)从内存中开始逐出密钥。

可以使用 [INFO](https://redis.io/commands/info) 命令来监视逐出的密钥数。

```
# Stats

evicted_keys:13224
```

此外，可以查看缓存的诊断指标，确定密钥丢失的时间以及已逐出密钥的高峰之间是否存在某种关联。 有关如何使用密钥空间通知或 **MONITOR** 调试此类问题的信息，请参阅[调试 Redis 密钥空间缺失](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)附录。

### <a name="key-deletion"></a>密钥删除

Redis 客户端可以发出 [DEL](https://redis.io/commands/del) 或 [HDEL](https://redis.io/commands/hdel) 命令来显式删除 Azure Cache for Redis 中的密钥。 可以使用 [INFO](https://redis.io/commands/info) 命令来跟踪删除操作数目。 如果已调用 **DEL** 或 **HDEL** 命令，它们将列在 `Commandstats` 部分。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>异步复制

标准或高级层中的任何 Azure Cache for Redis 实例都配置有一个主节点和至少一个副本。 数据将通过一个后台进程以异步方式从主节点复制到副本。 [redis.io](https://redis.io/topics/replication) 网站概括性地介绍了 Redis 数据复制的工作原理。 如果客户端频繁写入 Redis，可能会发生部分数据丢失，因为需要保证这种复制在瞬间完成。 例如，如果在客户端向主节点写入密钥之后、后台进程有机会将此密钥发送到副本之前主节点关闭，那么，在副本接管为新的主节点时，密钥就会丢失。****

## <a name="major-or-complete-loss-of-keys"></a>密钥严重丢失或完全丢失

如果大部分或所有密钥在缓存中消失，请检查以下可能原因：

| 原因 | 描述 |
|---|---|
| [密钥刷新](#key-flushing) | 已手动清除密钥。 |
| [选择了错误的数据库](#incorrect-database-selection) | Azure Cache for Redis 设置为使用非默认数据库。 |
| [Redis 实例故障](#redis-instance-failure) | Redis 服务器不可用。 |

### <a name="key-flushing"></a>密钥刷新

客户端可以调用 [FLUSHDB](https://redis.io/commands/flushdb) 命令来删除*单个*数据库中的所有密钥，或调用 [FLUSHALL](https://redis.io/commands/flushall) 来删除 Redis 缓存中*所有*数据库中的所有密钥。 若要确定密钥是否已刷新，请使用 [INFO](https://redis.io/commands/info) 命令。 `Commandstats` 部分显示是否调用了 **FLUSH** 命令：

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>选择了错误的数据库

Azure Cache for Redis 默认使用 **db0** 数据库。 如果切换到其他数据库（例如 **db1**），并尝试从该数据库读取密钥，则 Azure Cache for Redis 在其中将找不到这些密钥。 每个数据库都是一个逻辑上独立的单元，其中保存了不同的数据集。 使用 [SELECT](https://redis.io/commands/select) 命令来选择其他可用数据库，并在其中每个数据库中查找密钥。

### <a name="redis-instance-failure"></a>Redis 实例故障

Redis 是内存中数据存储。 数据保存在托管 Redis 缓存的物理机或虚拟机上。 基本层中的 Azure Cache for Redis 实例只在单个虚拟机 (VM) 上运行。 如果该 VM 关闭，则缓存中存储的所有数据都会丢失。 

标准层和高级层中的缓存在复制的配置中使用两个 VM，能够以更高的弹性防范数据丢失。 当此类缓存中的主节点发生故障时，副本节点将会接管工作并自动提供数据。 这些 VM 位于独立的容错域和更新域中，以最大程度地减少主节点和副本同时发生故障的几率。 但是，如果发生严重的数据中心服务中断，这些 VM 仍可能会同时发生故障。 此时，数据将会丢失，但这种情况非常罕见。

考虑使用 [Redis 数据持久性](https://redis.io/topics/persistence)和[异地复制](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)来改善数据保护，防范此类基础结构故障。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
