---
title: 排查 Azure Cache for Redis 客户端问题
description: 了解如何解决 Redis 的 Azure 缓存的常见客户端问题
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: a4fdbe9c0943e77719a9ee9da7dc358696284d99
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122615"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>排查 Azure Cache for Redis 客户端问题

本部分讨论因应用程序使用的 Redis 客户端上出现的问题而发生的故障排除问题。

- [Redis 客户端的内存压力](#memory-pressure-on-redis-client)
- [流量脉冲](#traffic-burst)
- [客户端 CPU 使用率过高](#high-client-cpu-usage)
- [客户端带宽限制](#client-side-bandwidth-limitation)
- [大请求或响应大小](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redis 客户端的内存压力

客户端计算机上出现的内存压力会导致各种性能问题，这些问题可能会延迟对缓存所发出的响应的处理。 出现内存压力时，系统可能会将数据分页到磁盘。 此分页错误导致系统性能显著下降。

检测客户端上的内存压力：

- 监视计算机上的内存用量，确保所用内存未超过可用内存。
- 监视客户端的 `Page Faults/Sec` 性能计数器。 在正常运行期间，大多数系统会出现某些页面错误。 如果页面错误中存在与请求超时相关的峰值，可能表示出现了内存压力。

可通过多种方式缓解客户端上较高的内存压力：

- 深入分析内存使用模式，并减少客户端上的内存消耗量。
- 将客户端 VM 升级到可提供更多内存的更大大小。

## <a name="traffic-burst"></a>流量脉冲

流量激增时，如果 `ThreadPool` 设置不佳，则可能导致对 Redis 服务器已发送但尚未在客户端上使用的数据的处理出现延迟。

使用`ThreadPool`示例 [`ThreadPoolLogger` 监视 ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) 统计信息在不同时间的变化。 可以使用 StackExchange.Redis 发出的 `TimeoutException` 消息（如下所示）做进一步的调查：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在上面的异常中，有几个需要注意的问题：

- 请注意，在 `IOCP` 部分和 `WORKER` 部分，`Busy` 值大于 `Min` 值。 这种差异意味着 `ThreadPool` 设置需要调整。
- 也可参看 `in: 64221`。 此值表示客户端的内核套接字层收到了 64,211 字节，但应用程序尚未读取这些字节。 这种差异通常意味着，应用程序（例如 StackExchange.Redis）从网络读取数据的速度没有服务器向你发送数据的速度快。

可以[配置 `ThreadPool` 设置](https://gist.github.com/JonCole/e65411214030f0d823cb)，确保线程池在流量激增的情况下快速扩展。

## <a name="high-client-cpu-usage"></a>客户端 CPU 使用率过高

客户端 CPU 使用率偏高表示系统跟不上所要求执行的工作的进度。 即使缓存发送响应的速度很快，客户端也可能无法及时处理该响应。

使用 Azure 门户中提供的指标或者通过计算机上的性能计数器监视客户端的系统范围的 CPU 使用率。 请注意不要监视进程 CPU，因为即使单个进程的 CPU 使用率较低，但系统范围的 CPU 使用率也可能很高。 注意与超时相对应的 CPU 使用率峰值。 高 CPU 可能还会导致 `TimeoutException` 错误消息中的高 `in: XXX` 值，如[流量](#traffic-burst)激增部分所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 及更高版本在 `local-cpu` 错误消息中包括了 `TimeoutException` 指标。 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
>

缓解客户端 CPU 使用率较高的问题：

- 调查出现 CPU 峰值的原因。
- 将客户端升级到可提供更多 CPU 容量的更大 VM 大小。

## <a name="client-side-bandwidth-limitation"></a>客户端带宽限制

不同的客户端计算机体系结构对于可提供的网络带宽存在不同的限制。 如果客户端超出可用带宽，则客户端的数据处理速度将赶不上服务器的数据发送速度。 这种情况下会导致超时。

使用 [示例 `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)监视带宽使用率在不同时间的变化。 在对权限有限制的某些环境（例如 Azure 网站）中，此代码可能无法成功运行。

若要缓解此问题，请减少网络带宽消耗，或者将客户端 VM 大小提高到可以提供更大网络容量的大小。

## <a name="large-request-or-response-size"></a>大请求或响应大小

请求/响应过大可能导致超时。 例如，假设你在客户端上配置的超时值为 1 秒。 你的应用程序（使用相同的物理网络连接）的同时请求两个键 （例如，A 和 B）。 大多数客户端支持对请求进行“管道操作”，使得请求“A”和“B”可以逐个发送，而无需等待响应。 服务器会按相同顺序将响应发送回来。 如果响应“A”较大，可能会消耗掉后续请求的大部分超时时间。

在以下示例中，请求“A”和“B”快速发送到服务器。 服务器开始快速发送响应“A”和“B”。 由于数据传输需要时间，即使服务器的响应速度很快，响应“B”也必须等到响应“A”超时。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此请求/响应很难度量值。 可对客户端代码进行检测，以跟踪大型请求和响应。

针对大型响应的解决方法各不相同，但是包括：

1. 优化应用程序以处理大量的小值，而不是处理少量的大值。
    - 首选解决方案是将数据分解成较小的相关值。
    - 请参阅 post[什么是 redis 的理想值大小范围？100 KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)有关为何建议使用较小值的详细信息。
1. 增大 VM 的大小以获得更高的带宽能力
    - 提高客户端或服务器 VM 上的带宽可以缩短较大响应的数据传输时间。
    - 将两台计算机上的网络用量与当前 VM 大小的限制进行比较。 只提高服务器上的带宽，或者只提高客户端上的带宽，都不足以解决问题。
1. 增加应用程序使用的连接对象数。
    - 使用轮询方法通过不同的连接对象发出请求。

## <a name="additional-information"></a>其他信息

- [排查 Azure Cache for Redis 服务器端问题](cache-troubleshoot-server.md)
- [如何制定基准和测试缓存性能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
