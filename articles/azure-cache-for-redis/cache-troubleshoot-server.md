---
title: 排查 Azure Cache for Redis 服务器端问题
description: 了解如何解决用于 Redis 的 Azure 缓存的常见服务器端问题，例如内存压力、高 CPU、长时间运行的命令或带宽限制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372189"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>排查 Azure Cache for Redis 服务器端问题

本部分讨论如何排查由于 Azure Cache for Redis 或托管它的虚拟机上出现的问题。

- [Redis 服务器上的内存压力](#memory-pressure-on-redis-server)
- [CPU 使用率或服务器负载过高](#high-cpu-usage-or-server-load)
- [长时间运行命令](#long-running-commands)
- [服务器端带宽限制](#server-side-bandwidth-limitation)

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 有关详细信息和说明，请参阅[其他信息](#additional-information)部分的文章。
>

## <a name="memory-pressure-on-redis-server"></a>Redis 服务器上的内存压力

服务器端的内存压力会导致各种性能问题，从而延缓对请求的处理。 当内存压力命中时，系统可能会将数据分页到磁盘。 此分页错误导致系统性能显著下降。 这种内存压力可能有多个原因：

- 缓存中填充的数据接近其最大容量。
- Redis 看到的内存碎片较高。 此碎片最常见的原因是存储大型对象，因为 Redis 是针对小型对象进行优化的。

Redis 通过 INFO 命令公开两个统计信息，这些统计[信息](https://redis.io/commands/info)可帮助你确定此问题： "used_memory" 和 "used_memory_rss"。 可以使用门户[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

可以进行几项可能的更改，以确保内存使用率正常：

- [配置内存策略](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，对密钥设置过期时间。 如果有碎片，此策略可能不足以满足需要。
- [配置 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，该值应足够大，可以抵消内存碎片造成的影响。
- 将大型缓存对象分解成小型相关对象。
- [创建](cache-how-to-monitor.md#alerts)有关指标的警报，如使用的内存，在提前通知潜在影响。
- [缩放](cache-how-to-scale.md)到更大的缓存大小，具有更多内存容量。

## <a name="high-cpu-usage-or-server-load"></a>CPU 使用率或服务器负载过高

服务器负载过高或 CPU 使用意味着服务器无法及时处理请求。 服务器响应速度太慢，无法跟上请求速率。

[监视](cache-how-to-monitor.md#view-metrics-with-azure-monitor)CPU 或服务器负载等指标。 注意与超时相对应的 CPU 使用率峰值。

可以进行几项更改，以缓解高服务器负载：

- 调查导致 CPU 峰值的原因，如下面所述的[长时间运行命令](#long-running-commands)，或由于内存不足导致的页错误。
- [创建](cache-how-to-monitor.md#alerts)有关 CPU 或服务器负载等指标的警报，以提前通知潜在影响。
- [缩放](cache-how-to-scale.md)到更大的缓存大小，具有更多的 CPU 容量。

## <a name="long-running-commands"></a>长时间运行的命令

某些 Redis 命令比其他命令更昂贵。 [Redis 命令文档](https://redis.io/commands)显示每个命令的时间复杂性。 由于 Redis 命令处理是单线程的，因此，运行时间的命令会阻止其后的所有其他命令。 你应该查看正在向 Redis 服务器发出的命令，以了解其对性能的影响。 例如，通常使用[KEYS](https://redis.io/commands/keys)命令，而无需知道它是 O （N）操作。 可以通过使用[扫描](https://redis.io/commands/scan)来减少 CPU 高峰。

使用[SLOWLOG](https://redis.io/commands/slowlog)命令，可以度量对服务器执行的昂贵命令。

## <a name="server-side-bandwidth-limitation"></a>服务器端带宽限制

不同的缓存大小具有不同的网络带宽容量。 如果服务器超出可用带宽，则数据不会迅速发送到客户端。 由于服务器无法将数据推送到客户端，因此客户端请求可能会超时。

可以使用 "缓存读取" 和 "缓存写入" 指标来查看正在使用的服务器端带宽量。 可以在门户中[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

缓解网络带宽使用量接近最大容量的情况：

- 更改客户端调用行为以减少网络需求。
- 针对诸如缓存读取或缓存写入等指标[创建警报](cache-how-to-monitor.md#alerts)，以提前通知潜在影响。
- [缩放](cache-how-to-scale.md)到更大的缓存大小，具有更多网络带宽容量。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 客户端问题](cache-troubleshoot-client.md)
- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何制定基准和测试缓存性能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
