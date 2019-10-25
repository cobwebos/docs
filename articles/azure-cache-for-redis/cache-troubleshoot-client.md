---
title: 排查 Azure Cache for Redis 客户端问题 |Microsoft Docs
description: 了解如何解决 Redis 的 Azure 缓存的常见客户端问题
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
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: 18fb2f7c5a411ff2026437b647be56812b4d2521
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819625"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>排查 Azure Cache for Redis 客户端问题

本部分讨论因应用程序使用的 Redis 客户端上出现的问题而发生的故障排除问题。

- [Redis 客户端的内存压力](#memory-pressure-on-redis-client)
- [流量脉冲](#traffic-burst)
- [客户端 CPU 使用率过高](#high-client-cpu-usage)
- [客户端带宽限制](#client-side-bandwidth-limitation)
- [大请求或响应大小](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 客户端的内存压力

客户端计算机上的内存压力导致各种性能问题，这些问题可能会延迟缓存的响应处理。 当内存压力命中时，系统可能会将数据分页到磁盘。 此分页错误导致系统性能显著下降。

检测客户端的内存压力：

- 监视计算机上的内存使用情况，以确保它不会超过可用内存。
- 监视客户端的 `Page Faults/Sec` 性能计数器。 在正常操作期间，大多数系统都有一些页面错误。 与请求超时相对应的页面错误高峰可能表明内存不足。

可以通过以下几种方式缓解客户端的高内存压力：

- 深入了解内存使用模式，以减少客户端的内存消耗。
- 将客户端 VM 升级到更大的内存量。

## <a name="traffic-burst"></a>流量脉冲

流量激增时，如果 `ThreadPool` 设置不佳，则可能导致对 Redis 服务器已发送但尚未在客户端上使用的数据的处理出现延迟。

使用[`ThreadPoolLogger` 示例](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)监视 `ThreadPool` 统计信息随时间变化的情况。 你可以使用以下 Stackexchange.redis 中的 `TimeoutException` 消息，以便进一步调查：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在上述异常中，有几个有趣的问题：

- 请注意，在 `IOCP` 部分和 `WORKER` 部分，`Busy` 值大于 `Min` 值。 这种差异意味着 `ThreadPool` 设置需要调整。
- 也可参看 `in: 64221`。 此值指示已在客户端内核套接字层接收到64211字节，但应用程序尚未对其进行读取。 这种差异通常意味着，当服务器发送给你的应用程序（例如，Stackexchange.redis）不是从网络读取数据的速度。

可以[配置 `ThreadPool` 设置](https://gist.github.com/JonCole/e65411214030f0d823cb)，确保线程池在突发情况下快速扩展。

## <a name="high-client-cpu-usage"></a>客户端 CPU 使用率过高

高客户端 CPU 使用率表明系统不能跟上系统要求完成的工作。 即使缓存迅速发送响应，客户端也可能无法及时处理响应。

使用 Azure 门户中的指标或计算机上的性能计数器，监视客户端的系统范围的 CPU 使用情况。 请注意不要监视*进程*cpu，因为单个进程的 cpu 使用率较低，但系统范围的 cpu 可能会很高。 注意与超时相对应的 CPU 使用率峰值。 高 CPU 可能还会导致 `TimeoutException` 错误消息中的高 `in: XXX` 值，如[流量](#traffic-burst)激增部分所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 及更高版本在 `TimeoutException` 错误消息中包括了 `local-cpu` 指标。 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
>

降低客户端的高 CPU 使用率：

- 调查导致 CPU 峰值的原因。
- 将客户端升级到具有更多 CPU 容量的更大 VM 大小。

## <a name="client-side-bandwidth-limitation"></a>客户端带宽限制

不同的客户端计算机体系结构对于可提供的网络带宽存在不同的限制。 如果客户端通过重载网络容量超出了可用带宽，则在服务器发送数据时，不会尽快在客户端上处理数据。 这种情况下会导致超时。

使用[示例 `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)来监视带宽使用情况如何变化。 在对权限有限制的某些环境（例如 Azure 网站）中，此代码可能无法成功运行。

若要缓解这种情况，请减少网络带宽消耗，或将客户端 VM 大小增加到具有更多网络容量的客户端

## <a name="large-request-or-response-size"></a>大请求或响应大小

请求/响应过大可能导致超时。 例如，假设你在客户端上配置的超时值为1秒。 你的应用程序（使用相同的物理网络连接）的同时请求两个键 （例如，A 和 B）。 大多数客户端支持 "流水线" 请求，在这种情况下，请求 "A" 和 "B" 同时发送一次，而不等待其响应。 服务器会按相同顺序将响应发送回来。 如果响应 "A" 很大，则可能会为以后的请求消耗大部分超时时间。

在下面的示例中，请求 "A" 和 "B" 迅速发送到服务器。 服务器会立即开始发送响应 "A" 和 "B"。 由于数据传输时间，响应 "B" 必须等待响应 "A" 超时，即使服务器的响应速度快。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此请求/响应很难度量值。 可以通过检测客户端代码来跟踪大型请求和响应。

大响应大小的分辨率多种多样，但包括：

1. 针对大量小值而不是几个较小的值优化应用程序。
    - 首选解决方案是将数据分解成较小的相关值。
    - 请参阅 post[什么是 redis 的理想值大小范围？100 KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)有关为何建议使用较小值的详细信息。
1. 增加 VM 的大小以获取更高的带宽功能
    - 对于更大的响应，客户端或服务器 VM 上的带宽越多，可能会减少数据传输时间。
    - 将两台计算机上的当前网络使用情况与当前 VM 大小的限制进行比较。 仅在服务器上或仅在客户端上的带宽越多。
1. 增加应用程序使用的连接对象的数量。
    - 使用轮循方法对不同的连接对象发出请求。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [如何制定基准和测试缓存性能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
