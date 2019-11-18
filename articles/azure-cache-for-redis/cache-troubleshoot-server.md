---
title: 排查 Azure Cache for Redis 服务器端问题
description: 了解如何解决 Redis 的 Azure 缓存的常见服务器端问题
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: 22cb4beb3411c617882972e1b91c5f538019fae4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122570"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>排查 Azure Cache for Redis 服务器端问题

本部分讨论如何排查由于 Azure Cache for Redis 或托管它的虚拟机上出现的问题。

- [Redis 服务器上的内存压力](#memory-pressure-on-redis-server)
- [CPU 使用率或服务器负载过高](#high-cpu-usage-or-server-load)
- [长时间运行的命令](#long-running-commands)
- [服务器端带宽限制](#server-side-bandwidth-limitation)

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 有关详细信息和说明，请参阅[其他信息](#additional-information)部分的文章。
>

## <a name="memory-pressure-on-redis-server"></a>Redis 服务器上的内存压力

服务器端的内存压力会导致各种性能问题，从而延缓对请求的处理。 出现内存压力时，系统可能会将数据分页到磁盘。 此分页错误导致系统性能显著下降。 这种内存压力可能有多个原因：

- 缓存中填充的数据即将达到其最大容量。
- Redis 出现大量内存碎片。 这种碎片往往是存储大型对象造成的，因为 Redis 已针对小型对象进行优化。

Redis 通过 [INFO](https://redis.io/commands/info) 命令公开以下两项统计信息来帮助你识别此问题：“used_memory”和“used_memory_rss”。 可以使用门户[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

可以通过多种可能的更改来帮助确保内存用量正常：

- [配置内存策略](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，对密钥设置过期时间。 如果存在内存碎片，则此策略可能还不足够。
- [配置 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，该值应足够大，可以抵消内存碎片造成的影响。
- 将大型缓存对象分解成小型相关对象。
- 基于指标（例如已用内存）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更多内存容量的更大缓存大小。

## <a name="high-cpu-usage-or-server-load"></a>CPU 使用率或服务器负载过高

服务器负载或 CPU 使用率偏高意味着服务器无法及时处理请求。 服务器可能会减慢响应速度，且无法跟上请求速率。

[监视指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)，例如 CPU 或服务器负载。 注意与超时相对应的 CPU 使用率峰值。

可以通过做出几项更改来缓解较高的服务器负载：

- 调查导致 CPU 峰值的原因，如下面所述的[长时间运行命令](#long-running-commands)，或由于内存不足导致的页错误。
- 基于指标（例如 CPU 或服务器负载）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更多 CPU 容量的更大缓存大小。

## <a name="long-running-commands"></a>长时间运行的命令

某些 Redis 命令比其他命令更昂贵。 [Redis 命令文档](https://redis.io/commands)介绍了每个命令的时间复杂性。 由于 Redis 命令处理是单线程的，因此，运行时间的命令会阻止其后的所有其他命令。 你应该查看正在向 Redis 服务器发出的命令，以了解其对性能的影响。 例如，我们经常使用 [KEYS](https://redis.io/commands/keys) 命令，但事先并不知道它是一个 O(N) 操作。 可以使用 [SCAN](https://redis.io/commands/scan) 来避免 KEYS，以降低 CPU 峰值。

使用 [SLOWLOG](https://redis.io/commands/slowlog) 命令可以测量正在对服务器执行的命令的开销。

## <a name="server-side-bandwidth-limitation"></a>服务器端带宽限制

不同的缓存大小具有不同的网络带宽容量。 如果服务器超出可用带宽，则数据无法快速发送到客户端。 客户端请求可能会超时，因为服务器无法以足够快的速度将数据推送到客户端。

可以使用“缓存读取”和“缓存写入”指标来查看使用的服务器端带宽量。 可以在门户中[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

缓解网络带宽用量即将达到最大容量的情况：

- 更改客户端调用行为，以降低网络需求。
- 基于指标（例如缓存读取或缓存写入）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更高网络带宽容量的更大缓存大小。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 客户端问题](cache-troubleshoot-client.md)
- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何制定基准和测试缓存性能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
