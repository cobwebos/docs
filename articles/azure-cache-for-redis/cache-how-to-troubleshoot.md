---
title: 如何排查 Azure Redis 缓存问题 | Microsoft Docs
description: 了解如何解决 Azure Redis 缓存的常见问题。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830001"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>如何排查 Azure Redis 缓存问题

本文可帮助你排查在连接 Azure Redis 缓存实例时可能会遇到的各种问题。

- [客户端故障排除](#client-side-troubleshooting)可帮助你识别和解决在应用程序中连接到缓存时遇到的问题。
- [服务器端故障排除](#server-side-troubleshooting)可帮助你识别和解决 Azure Redis 缓存端发生的问题。
- [数据丢失故障排除](#data-loss-troubleshooting)可帮助你识别和解决在缓存中找不到预期键的事件。
- [StackExchange.Redis 超时异常](#stackexchangeredis-timeout-exceptions)提供有关排查 StackExchange.Redis 库问题的具体指导。

> [!NOTE]
> 本指南中的多个故障排除步骤包括了运行 Redis 命令和监视各种性能指标的说明。 有关详细信息和说明，请参阅[其他信息](#additional-information)部分的文章。
>
>

## <a name="client-side-troubleshooting"></a>客户端故障排除

此部分讨论如何排查因客户端应用程序出现状况而发生的问题。

- [客户端内存压力](#memory-pressure-on-the-client)
- [流量激增](#burst-of-traffic)
- [客户端 CPU 使用率过高](#high-client-cpu-usage)
- [超出客户端带宽](#client-side-bandwidth-exceeded)
- [请求/响应大小过大](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>客户端内存压力

客户端计算机上出现的内存压力会导致各种性能问题，这些问题可能会延迟对缓存所发出的响应的处理。 出现内存压力时，系统可能会将数据分页到磁盘。 此分页错误导致系统性能显著下降。

检测客户端上的内存压力：

- 监视计算机上的内存用量，确保所用内存未超过可用内存。
- 监视客户端的 `Page Faults/Sec` 性能计数器。 在正常运行期间，大多数系统会出现某些页面错误。 如果页面错误中存在与请求超时相关的峰值，可能表示出现了内存压力。

可通过多种方式缓解客户端上较高的内存压力：

- 深入分析内存使用模式，并减少客户端上的内存消耗量。
- 将客户端 VM 升级到可提供更多内存的更大大小。

### <a name="burst-of-traffic"></a>流量激增

流量激增时，如果 `ThreadPool` 设置不佳，则可能导致对 Redis 服务器已发送但尚未在客户端上使用的数据的处理出现延迟。

使用[示例 `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) 监视 `ThreadPool` 统计信息在不同时间的变化。 可以使用 StackExchange.Redis 发出的 `TimeoutException` 消息（如下所示）做进一步的调查：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在上面的异常中，有几个需要注意的问题：

- 请注意，在 `IOCP` 部分和 `WORKER` 部分，`Busy` 值大于 `Min` 值。 这种差异意味着 `ThreadPool` 设置需要调整。
- 也可参看 `in: 64221`。 此值表示客户端的内核套接字层收到了 64,211 字节，但应用程序尚未读取这些字节。 这种差异通常意味着，应用程序（例如 StackExchange.Redis）从网络读取数据的速度没有服务器向你发送数据的速度快。

可以[配置 `ThreadPool` 设置](https://gist.github.com/JonCole/e65411214030f0d823cb)，确保线程池在流量激增的情况下快速扩展。

### <a name="high-client-cpu-usage"></a>客户端 CPU 使用率过高

客户端 CPU 使用率偏高表示系统跟不上所要求执行的工作的进度。 即使缓存发送响应的速度很快，客户端也可能无法及时处理该响应。

使用 Azure 门户中提供的指标或者通过计算机上的性能计数器监视客户端的系统范围的 CPU 使用率。 请注意不要监视进程 CPU，因为即使单个进程的 CPU 使用率较低，但系统范围的 CPU 使用率也可能很高。 注意与超时相对应的 CPU 使用率峰值。 CPU 使用率较高可能还会导致 `TimeoutException` 错误消息中出现较大的 `in: XXX` 值，如[流量激增](#burst-of-traffic)部分所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 及更高版本在 `TimeoutException` 错误消息中包括了 `local-cpu` 指标。 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
>
>

缓解客户端 CPU 使用率较高的问题：

- 调查出现 CPU 峰值的原因。
- 将客户端升级到可提供更多 CPU 容量的更大 VM 大小。

### <a name="client-side-bandwidth-exceeded"></a>超出客户端带宽

不同的客户端计算机体系结构对于可提供的网络带宽存在不同的限制。 如果客户端超出可用带宽，则客户端的数据处理速度将赶不上服务器的数据发送速度。 这种情况下会导致超时。

使用 [示例 `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)监视带宽使用率在不同时间的变化。 在对权限有限制的某些环境（例如 Azure 网站）中，此代码可能无法成功运行。

若要缓解此问题，请减少网络带宽消耗，或者将客户端 VM 大小提高到可以提供更大网络容量的大小。

### <a name="large-requestresponse-size"></a>请求/响应大小过大

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
    - 请参阅博文[redis 的理想值大小范围是什么？为 100 KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)有关原因的详细信息我们建议使用较小的值。
1. 增大 VM 的大小以获得更高的带宽能力
    - 提高客户端或服务器 VM 上的带宽可以缩短较大响应的数据传输时间。
    - 将两台计算机上的网络用量与当前 VM 大小的限制进行比较。 只提高服务器上的带宽，或者只提高客户端上的带宽，都不足以解决问题。
1. 增加应用程序使用的连接对象数。
    - 使用轮询方法通过不同的连接对象发出请求。

## <a name="server-side-troubleshooting"></a>服务器端故障排除

此部分讨论如何排查因缓存服务器出现状况而发生的问题。

- [服务器上的内存压力](#memory-pressure-on-the-server)
- [CPU 使用率/服务器负载过高](#high-cpu-usage--server-load)
- [超出服务器端带宽](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>服务器上的内存压力

服务器端的内存压力会导致各种性能问题，从而延缓对请求的处理。 出现内存压力时，系统可能会将数据分页到磁盘。 此分页错误导致系统性能显著下降。 这种内存压力可能有多个原因：

- 缓存中填充的数据即将达到其最大容量。
- Redis 出现大量内存碎片。 这种碎片往往是存储大型对象造成的，因为 Redis 已针对小型对象进行优化。

Redis 通过 [INFO](https://redis.io/commands/info) 命令公开以下两项统计信息来帮助你识别此问题：“used_memory”和“used_memory_rss”。 可以使用门户[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

可以通过多种可能的更改来帮助确保内存用量正常：

- [配置内存策略](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，对密钥设置过期时间。 如果存在内存碎片，则此策略可能还不足够。
- [配置 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，该值应足够大，可以抵消内存碎片造成的影响。 有关详细信息，请参阅下面附加的[有关内存预留的注意事项](#considerations-for-memory-reservations)。
- 将大型缓存对象分解成小型相关对象。
- 基于指标（例如已用内存）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更多内存容量的更大缓存大小。

#### <a name="considerations-for-memory-reservations"></a>有关内存预留的注意事项

更新内存预留值（例如 maxmemory-reserved）可能会影响缓存性能。 假设某个 53-GB 的缓存中填充了 49-GB 的数据。 将预留值更改为 8 GB 会将系统的最大可用内存降低到 45 GB。 如果 _used_memory_ 或 _used_memory_rss_ 值高于 45 GB，则系统可能会不断逐出数据，直到 _used_memory_ 和 _used_memory_rss_ 均低于 45 GB。 逐出可能会增加服务器负载和内存碎片。

### <a name="high-cpu-usage--server-load"></a>CPU 使用率/服务器负载过高

服务器负载或 CPU 使用率偏高意味着服务器无法及时处理请求。 服务器可能会减慢响应速度，且无法跟上请求速率。

[监视指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)，例如 CPU 或服务器负载。 注意与超时相对应的 CPU 使用率峰值。

可以通过做出几项更改来缓解较高的服务器负载：

- 调查哪些因素导致出现 CPU 峰值，例如，正在运行[资源密集型的命令](#expensive-commands)，或者较高的内存压力导致出现页面错误。
- 基于指标（例如 CPU 或服务器负载）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更多 CPU 容量的更大缓存大小。

#### <a name="expensive-commands"></a>资源密集型的命令

并非所有 Redis 命令都是以相同的方式创建的 - 有些命令的开销高于其他命令。 [Redis 命令文档](https://redis.io/commands)介绍了每个命令的时间复杂性。 建议查看针对缓存运行的命令，以了解这些命令对性能造成的影响。 例如，我们经常使用 [KEYS](https://redis.io/commands/keys) 命令，但事先并不知道它是一个 O(N) 操作。 可以使用 [SCAN](https://redis.io/commands/scan) 来避免 KEYS，以降低 CPU 峰值。

使用 [SLOWLOG](https://redis.io/commands/slowlog) 命令可以测量正在对服务器执行的命令的开销。

### <a name="server-side-bandwidth-exceeded"></a>超出服务器端带宽

不同的缓存大小具有不同的网络带宽容量。 如果服务器超出可用带宽，则数据无法快速发送到客户端。 客户端请求可能会超时，因为服务器无法以足够快的速度将数据推送到客户端。

可以使用“缓存读取”和“缓存写入”指标来查看使用的服务器端带宽量。 可以在门户中[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

缓解网络带宽用量即将达到最大容量的情况：

- 更改客户端调用行为，以降低网络需求。
- 基于指标（例如缓存读取或缓存写入）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更高网络带宽容量的更大缓存大小。

## <a name="data-loss-troubleshooting"></a>数据丢失故障排除

我本以为某些数据会出现在我的 Azure Redis 缓存实例中，但这些数据似乎并不在那里。

请参阅 [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)（我在 Redis 中的数据发生了什么情况？），了解可能的原因和解决方法。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 超时异常

StackExchange.Redis 使用名为 `synctimeout` 的配置设置进行同步操作，该设置的默认值为 1000 毫秒。 如果同步调用未在此时间内完成，StackExchange.Redis 客户端会引发类似于以下示例的超时错误：

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

此错误消息中包含的指标可以指出问题的原因和可能的解决方法。 下表包含有关错误消息指标的详细信息。

| 错误消息指标 | 详细信息 |
| --- | --- |
| inst |在上一个时间切片中：已发出了 0 个命令 |
| mgr |套接字管理器正在执行 `socket.select`，即，它正在请求 OS 指示一个需要执行某种操作的套接字。 读取器并未主动从网络读取数据，因为它认为不需执行任何操作 |
| 队列 |总共有 73 个正在进行的操作 |
| qu |正在进行的操作中，有 6 个操作位于未发送队列中，而尚未写入到出站网络 |
| qs |正在进行的操作中，有 67 个操作已发送给服务器，但尚未得到响应。 响应可能为 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |正在进行的操作中，有 0 个操作已看到回复，但尚未标记为完成，因为它们正在完成循环中等待 |
| wr |存在活动的写入器（这意味着系统不会忽略这 6 个尚未发送的请求）字节/活动写入器 |
| 位于 |没有活动的读取器，NIC 字节/活动读取器上没有可供读取的字节 |

### <a name="steps-to-investigate"></a>调查步骤

1. 作为最佳做法，在使用 StackExchange.Redis 客户端时，请确保遵循以下模式进行连接。

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    有关详细信息，请参阅[使用 StackExchange.Redis 连接到缓存](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)。

1. 确保服务器和客户端应用程序位于 Azure 中的同一区域。 例如，您可能会得到超时如果缓存位于美国东部但客户端位于美国西部，而且请求未在内完成`synctimeout`间隔，或出现在调试从本地开发计算机时。 

    强烈建议将缓存和客户端置于同一 Azure 区域。 如果方案包括跨区域调用，则应将 `synctimeout` 时间间隔设置为比默认的 1000 毫秒时间间隔更高的值，方法是在连接字符串中增加一个 `synctimeout` 属性。 以下示例演示了 Azure Redis 缓存提供的 StackExchange.Redis 连接字符串代码片段，其中的 `synctimeout` 为 2000 毫秒。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
1. 如果请求受服务器或客户端上的带宽限制的约束，则需要更长的时间才能完成，因此可能会导致超时。 若要了解超时是否是服务器上的网络带宽造成的，请参阅[超出服务器端带宽](#server-side-bandwidth-exceeded)。 若要了解超时是否是客户端网络带宽造成的，请参阅[超出客户端带宽](#client-side-bandwidth-exceeded)。
1. 操作是否占用了服务器或客户端上的大量 CPU？

   - 检查是否受到了客户端上的 CPU 消耗量的约束。 较高的 CPU 可能会导致请求无法在 `synctimeout` 时间间隔内得到处理，从而导致请求超时。改用更大型客户端或者将负载分散也许有助于控制这种情况。
   - 监视 `CPU` [缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)，检查是否受到了服务器上的 CPU 消耗量的约束。 如果请求传入时 Redis 受到 CPU 消耗量的约束，则可能会导致这些请求超时。为了解决此问题，可以将负载分散到高级缓存的多个分片中，也可以升级缓存大小或定价层。 有关详细信息，请参阅[超出服务器端带宽](#server-side-bandwidth-exceeded)。
1. 是否存在需要在服务器上进行长时间处理的命令？ 在 Redis 服务器上花费很长时间处理请求的命令可能会导致超时。 有关长时间运行的命令的详细信息，请参阅[资源密集型的命令](#expensive-commands)。 可以使用 redis-cli 客户端或 [Redis 控制台](cache-configure.md#redis-console)连接到 Azure Redis 缓存实例。 然后，运行 [SLOWLOG](https://redis.io/commands/slowlog) 命令查看是否存在比预期速度更慢的请求。 Redis 服务器和 StackExchange.Redis 适合处理多个小型请求，而不适合处理寥寥数个大型请求。 将数据拆分成更小的块可能会解决问题。

    有关使用 redis-cli 和 stunnel 连接到缓存 SSL 终结点的信息，请参阅博客文章：[Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)（宣布推出适用于 Redis 的 ASP.NET 会话状态提供程序预览版）。
1. Redis 服务器负载过高可能会导致超时。 可以通过监视 `Redis Server Load` [缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)来监视服务器负载。 服务器负载值为 100（最大值）表示 Redis 服务器正忙于处理请求，没有空闲时间。 若要查看某些请求是否占用了服务器的全部处理能力，请按上一段中的说明运行 SlowLog 命令。 有关详细信息，请参阅“CPU 使用率/服务器负载过高”。
1. 客户端上是否存在其他可能导致网络故障的事件？ 常见事件包括：增加或减少客户端实例的数目、部署新的客户端版本，或启用自动缩放。 我们在测试中发现，自动缩放或扩展/缩减可能会导致出站网络连接断开几秒。 StackExchange.Redis 代码可以灵活应对此类事件，并且会重新连接。 重新连接时，队列中的所有请求可能超时。
1. 在向缓存发出多个小型请求之前，是否存在导致超时的大型请求？ 错误消息中的参数 `qs` 会告知，有多少个请求已从客户端发送到服务器，但尚未处理响应。 此值可能会持续增加，因为 StackExchange.Redis 使用单个 TCP 连接，一次只能读取一个响应。 即使第一个操作超时，也不会阻止与服务器之间来回发送数据。 在完成大型请求之前，系统会阻止其他请求，从而可能导致超时。 降低超时概率的一种解决方案是确保缓存对于工作负荷来说足够大，并将大的值拆分成较小的块。 另一种可能的解决方案是使用客户端中的 `ConnectionMultiplexer` 对象池，在发送新请求时选择负载最小的 `ConnectionMultiplexer`。 通过多个连接对象进行加载应该可以防止单次超时导致其他请求也发生超时。
1. 如果使用 `RedisSessionStateProvider`，请确保正确设置重试超时。 `retryTimeoutInMilliseconds` 应高于 `operationTimeoutInMilliseconds`，否则无法执行重试。 在下面的示例中，`retryTimeoutInMilliseconds` 设置为 3000。 有关详细信息，请参阅 [Azure Redis 缓存的 ASP.NET 会话状态提供程序](cache-aspnet-session-state-provider.md)和 [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)（如何使用会话状态提供程序和输出缓存提供程序的配置参数）。

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. 通过[监视](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，了解 Azure Redis 缓存服务器上的内存使用情况。 如果实施了逐出策略，则当 `Used_Memory` 达到缓存大小时，Redis 就会开始逐出密钥。 理想情况下，`Used memory` 应只稍高于 `Used Memory RSS`。 差异过大意味着出现内存碎片（内部或外部）。 如果 `Used Memory RSS` 小于 `Used Memory`，则意味着操作系统已更换部分缓存内存。 如果发生这种情况，则会出现明显的延迟。 由于 Redis 无法控制如何将其分配映射到内存页，`Used Memory RSS` 偏高通常是内存用量出现高峰而造成的。 当 Redis 服务器释放内存时，分配器会回收该内存，但不一定会将内存重新分配到系统。 `Used Memory` 值与操作系统所报告的内存消耗量可能存在差异。 内存可能已由 Redis 使用并释放，但尚未重新分配到系统。 为了帮助缓解内存问题，可执行以下步骤：

   - 将缓存升级到更大的大小，以免系统中的内存限制导致运行受阻。
   - 对密钥设置过期时间，以便主动逐出过旧的值。
   - 监视 `used_memory_rss` 缓存指标。 当此值接近缓存大小时，可能会开始出现性能问题。 将数据分配到多个分片（如果使用高级缓存），或者升级到更大的缓存大小。

   有关详细信息，请参阅[服务器上的内存压力](#memory-pressure-on-the-server)。

## <a name="additional-information"></a>其他信息

- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何制定基准和测试缓存性能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
