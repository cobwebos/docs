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
ms.openlocfilehash: 838fc1da3e167d1df04fbb36a2fea33b8ac248a4
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482600"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>如何排查 Azure Redis 缓存问题

本文介绍如何对不同类别的连接使用 Azure 缓存的 Redis 实例时可能遇到的问题进行故障排除。

- [客户端故障排除](#client-side-troubleshooting)帮助识别和解决应用程序连接到缓存中的问题。
- [服务器端故障排除](#server-side-troubleshooting)帮助识别和解决 Azure 缓存的 Redis 端发生的问题。
- [数据丢失故障排除](#data-loss-troubleshooting)帮助识别和解决的事件在预期但未在缓存中找到密钥。
- [StackExchange.Redis 超时异常](#stackexchangeredis-timeout-exceptions)使用 StackExchange.Redis 库问题的故障排除提供的具体指南。

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

客户端计算机上的内存压力会导致所有类型的可以延迟的缓存的响应进行处理的性能问题。 当遇到内存压力时，系统可能页数据保存到磁盘上。 此分页错误导致系统性能显著下降。

若要检测客户端上的内存压力：

- 监视以确保它不会超过可用内存的计算机上的内存使用情况。
- 监视客户端的`Page Faults/Sec`性能计数器。 正常操作期间，大多数系统中出现某些分页错误。 使用请求超时值相对应的页面错误中的峰值可以表明内存不足的情况。

可以通过多种方法缓解客户端上的高内存压力：

- 深入分析以减少在客户端上的内存消耗对内存使用模式。
- 将客户端 VM 升级到具有更多内存更大的大小。

### <a name="burst-of-traffic"></a>流量激增

流量激增时，如果 `ThreadPool` 设置不佳，则可能导致对 Redis 服务器已发送但尚未在客户端上使用的数据的处理出现延迟。

监视器如何你`ThreadPool`统计信息更改时使用高于[举例`ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)。 可以使用`TimeoutException`stackexchange.redis 发出的消息如下进行进一步调查：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在前面的异常，有几个有趣的问题：

- 请注意，在 `IOCP` 部分和 `WORKER` 部分，`Busy` 值大于 `Min` 值。 这种差别意味着你`ThreadPool`设置需要调整。
- 也可参看 `in: 64221`。 此值指示，已在客户端的内核套接字层收到了 64211 字节，但还没有已读取的应用程序。 这通常意味着，应用程序 (例如 StackExchange.Redis) 读取没有数据从网络快速服务器在向您发送它。

你可以[配置您`ThreadPool`设置](https://gist.github.com/JonCole/e65411214030f0d823cb)以确保您的线程池下快速增加流量激增的情况。

### <a name="high-client-cpu-usage"></a>客户端 CPU 使用率过高

客户端过高 CPU 使用率指示系统无法跟上已要求其执行操作的工作。 即使缓存快速发送响应，客户端可能无法处理及时响应。

监视客户端的整个系统的 CPU 使用情况使用可用的指标，在 Azure 门户中或通过在计算机上的性能计数器。 请注意，不要监视*进程*CPU 因为单个进程可以具有较低的 CPU 使用率，但整个系统的 CPU 可能很高。 注意与超时相对应的 CPU 使用率峰值。 Cpu 使用率较高可能会也可能导致高`in: XXX`中的值以`TimeoutException`错误消息中所述[流量激增](#burst-of-traffic)部分。

> [!NOTE]
> StackExchange.Redis 1.1.603 及更高版本在 `TimeoutException` 错误消息中包括了 `local-cpu` 指标。 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
>
>

若要缓解客户端的高 CPU 使用率：

- 调查导致 CPU 峰值的原因。
- 将客户端升级为具有更多的 CPU 容量更大的 VM 大小。

### <a name="client-side-bandwidth-exceeded"></a>超出客户端带宽

不同的客户端计算机体系结构对于可提供的网络带宽存在不同的限制。 如果客户端通过重载网络容量超出可用带宽，则数据不被处理客户端上快速服务器发送。 这种情况下会导致超时。

监视带宽使用情况如何更改时间使用相比[举例`BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)。 在对权限有限制的某些环境（例如 Azure 网站）中，此代码可能无法成功运行。

若要缓解问题，请减少网络带宽消耗或增加客户端到一个具有更多的网络容量的 VM 大小。

### <a name="large-requestresponse-size"></a>请求/响应大小过大

请求/响应过大可能导致超时。 例如，假设您的客户端上配置的超时值为 1 秒。 你的应用程序（使用相同的物理网络连接）的同时请求两个键 （例如，A 和 B）。 大多数客户端支持请求"管道"，A 和 B 这两个请求发送到何处： 一个接一个而无需等待其响应。 服务器会按相同顺序将响应发送回来。 如果响应 A 过大，则可能会消耗掉大部分的更高版本的请求超时。

在以下示例中，服务器可以快速发送请求 A 和 B。 在服务器开始快速发送响应 A 和 B。 由于数据传输需要时间，B 必须等待后响应 A 的响应超时即使服务器响应速度很快。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此请求/响应很难度量值。 无法检测客户端代码来跟踪大型请求和响应。

大型响应大小的解决方法不同的但包括：

1. 优化应用程序进行大量的小型值，而不是几个较大值。
    - 首选解决方案是将数据分解成较小的相关值。
    - 请参阅博文[redis 的理想值大小范围是什么？为 100 KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)有关原因的详细信息我们建议使用较小的值。
1. 增加 VM 以提高带宽能力的大小
    - 客户端或服务器 VM 上的更多带宽可能会减少大型响应的数据传输时间。
    - 比较当前的网络使用情况，你当前的 VM 大小的限制到两台计算机上。 仅在服务器上或仅在客户端上的更多带宽可能不够。
1. 增加应用程序使用的连接对象的数目。
    - 使用轮循机制方法来通过不同的连接对象发出请求。

## <a name="server-side-troubleshooting"></a>服务器端故障排除

此部分讨论如何排查因缓存服务器出现状况而发生的问题。

- [服务器上的内存压力](#memory-pressure-on-the-server)
- [CPU 使用率/服务器负载过高](#high-cpu-usage--server-load)
- [超出服务器端带宽](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>服务器上的内存压力

服务器端的内存压力会导致各种性能问题，从而延缓对请求的处理。 当遇到内存压力时，系统可能页数据保存到磁盘上。 此分页错误导致系统性能显著下降。 这种内存压力可能有多个原因：

- 缓存使用接近其最大容量的数据进行填充。
- Redis 出现大量内存碎片。 通常由由于 Redis 适用于较小的对象存储了大型对象导致此碎片。

Redis 公开了两个统计信息通过[信息](https://redis.io/commands/info)命令可帮助你确定此问题:"used_memory"和"used_memory_rss"。 你可以[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)使用门户。

有几个可能的更改可帮助保持正常的内存使用情况：

- [配置内存策略](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，对密钥设置过期时间。 此策略可能还不够，如果存在内存碎片。
- [配置 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)，该值应足够大，可以抵消内存碎片造成的影响。 有关详细信息，请参阅其他[注意事项内存预留](#considerations-for-memory-reservations)下面。
- 将大型缓存对象分解成小型相关对象。
- [创建警报](cache-how-to-monitor.md#alerts)等指标上使用的内存有关的潜在影响早期通知。
- [缩放](cache-how-to-scale.md)到具有更多的内存容量更大的缓存大小。

#### <a name="considerations-for-memory-reservations"></a>有关内存预留的注意事项

更新内存保留值，如 maxmemory 保留，可能会影响缓存性能。 假设已有 49 GB 数据填充的 53 GB 缓存。 保留值更改为 8 GB 删除到 45 GB 的系统的最大可用内存。 如果_used_memory_或_used_memory_rss_值高于 45 GB，系统可能会逐出数据，直到_used_memory_和_used_memory_rss_均低于 45 GB。 逐出可能会增加服务器负载和内存碎片。

### <a name="high-cpu-usage--server-load"></a>CPU 使用率/服务器负载过高

服务器负载过重或 CPU 使用率意味着服务器无法处理请求中及时。 服务器可能是响应速度变慢，而且无法跟上请求率。

[监视指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)如 CPU 或服务器负载。 注意与超时相对应的 CPU 使用率峰值。

有几个您可以进行减少服务器负载过重的更改：

- 调查导致 CPU 峰值，如正在运行的原因[占用内存的命令](#expensive-commands)或页错误由于高内存压力。
- [创建警报](cache-how-to-monitor.md#alerts)上 CPU 或服务器有关的潜在影响早期通知的负载等指标。
- [缩放](cache-how-to-scale.md)到具有更多的 CPU 容量更大的缓存大小。

#### <a name="expensive-commands"></a>占用内存的命令

并非所有 Redis 命令都是相同的-一些比其他运行开销更大。 [的 Redis 命令文档](https://redis.io/commands)显示每个命令的时间复杂性。 建议您查看以了解这些命令的性能影响在缓存上运行的命令。 例如，[密钥](https://redis.io/commands/keys)命令通常使用而不必知道它 o （n） 操作。 您可以通过避免使用键[扫描](https://redis.io/commands/scan)以减少 CPU 峰值。

使用[SLOWLOG](https://redis.io/commands/slowlog)命令时，可以测量正在对服务器执行占用内存的命令。

### <a name="server-side-bandwidth-exceeded"></a>超出服务器端带宽

不同的缓存大小都有不同的网络带宽容量。 如果服务器超出可用带宽，以快速对客户端中就不会发送数据。 客户端请求可能超时，因为服务器不能将数据推送到客户端速度不够快。

"缓存读取"和"缓存写入"度量值可用于查看正在使用服务器端带宽。 你可以[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)在门户中。

若要缓解情况下，网络带宽使用情况时，关闭到最大容量：

- 更改客户端调用行为，以便减少网络需求。
- [创建警报](cache-how-to-monitor.md#alerts)缓存读取或缓存写入有关潜在影响早期通知等指标。
- [缩放](cache-how-to-scale.md)到具有更多网络带宽容量更大的缓存大小。

## <a name="data-loss-troubleshooting"></a>数据丢失故障排除

我本以为某些数据会出现在我的 Azure Redis 缓存实例中，但这些数据似乎并不在那里。

请参阅 [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)（我在 Redis 中的数据发生了什么情况？），了解可能的原因和解决方法。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 超时异常

StackExchange.Redis 使用的配置设置命名`synctimeout`为默认值为 1000 毫秒的同步操作。 如果同步调用未在此时间内完成，StackExchange.Redis 客户端会引发类似于下面的示例的超时错误：

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

此错误消息中包含的指标可以指出问题的原因和可能的解决方法。 下表包含有关错误消息指标的详细信息。

| 错误消息指标 | 详细信息 |
| --- | --- |
| inst |在上一个时间切片中：已发出了 0 个命令 |
| mgr |执行操作的套接字管理器`socket.select`，这意味着它在请求 OS 指示需要执行某些操作的套接字。 读取器当前不从网络读取，因为它不认为不执行任何操作 |
| 队列 |总共有 73 个正在进行的操作 |
| qu |正在进行中操作的第 6 未发送队列中和尚未尚未写入到出站网络 |
| qs |67 个正在进行中操作已发送到服务器，但响应尚不可用。 响应可能为 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |正在进行中操作的 0 已经有回复，但尚未尚未标记为完成，因为它们正在等待完成循环 |
| wr |没有活动的写入器 （即 6 发送的请求不会被忽略） 字节/activewriters |
| 位于 |没有活动的读取器，NIC 字节/活动读取器上没有可供读取的字节 |

### <a name="steps-to-investigate"></a>调查步骤

1. 作为最佳做法，请确保使用以下模式时使用 StackExchange.Redis 客户端连接。

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

1. 请确保你的服务器和客户端应用程序是在 Azure 中的同一区域中。 例如，您可能会得到超时如果缓存位于美国东部但客户端位于美国西部，而且请求未在内完成`synctimeout`间隔，或出现在调试从本地开发计算机时。 

    强烈建议将缓存和客户端置于同一 Azure 区域。 如果方案包括跨区域调用，则应将 `synctimeout` 时间间隔设置为比默认的 1000 毫秒时间间隔更高的值，方法是在连接字符串中增加一个 `synctimeout` 属性。 下面的示例演示连接字符串的代码片段用于提供有关使用 Redis Azure 缓存的 StackExchange.Redis`synctimeout`为 2000 毫秒。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. 确保使用最新版本的 [StackExchange.Redis NuGet 包](https://www.nuget.org/packages/StackExchange.Redis/)。 我们会不断对代码中的 Bug 进行修正，以便更好地应对超时情况。因此，请务必使用最新的版本。
1. 如果你的请求服务器或客户端上的带宽限制的绑定，时间才能完成，并可能会导致超时。 要了解超时是否是由于服务器上的网络带宽，请参阅[超出服务器端带宽](#server-side-bandwidth-exceeded)。 要了解超时是否是由于客户端网络带宽，请参阅[超出客户端的带宽](#client-side-bandwidth-exceeded)。
1. 操作是否占用了服务器或客户端上的大量 CPU？

   - 看是否你要获取大量 CPU 在您的客户端上。 Cpu 使用率较高可能会导致请求中处理`synctimeout`间隔和超时的请求的原因。改用更大型客户端或者将负载分散也许有助于控制这种情况。
   - 检查是否可将 CPU 绑定在服务器上通过监视`CPU`[缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)。 如果请求传入时 Redis 是受到 CPU 的约束可能会导致这些请求超时。为了解决此问题，可以将负载分散到高级缓存的多个分片中，也可以升级缓存大小或定价层。 有关详细信息，请参阅[超出服务器端带宽](#server-side-bandwidth-exceeded)。
1. 是否存在需要在服务器上进行长时间处理的命令？ 需要长时间才能处理 redis 服务器上的长时间运行命令可能会导致超时。 有关长时间运行命令的详细信息，请参阅[占用内存的命令](#expensive-commands)。 可以连接到 Azure 缓存的 Redis 实例使用 redis-cli 客户端或[Redis 控制台](cache-configure.md#redis-console)。 然后，运行[SLOWLOG](https://redis.io/commands/slowlog)命令以查看是否存在请求慢于预期。 Redis 服务器和 StackExchange.Redis 适合处理多个小型请求，而不适合处理寥寥数个大型请求。 将数据拆分成更小的块可能会解决问题。

    有关连接到你的缓存的 SSL 终结点使用 redis-cli 和 stunnel 的信息，请参阅博客文章[于 Redis 预览版宣布推出 ASP.NET 会话状态提供程序](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)。
1. Redis 服务器负载过高可能会导致超时。 可以通过监视 `Redis Server Load` [缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)来监视服务器负载。 服务器负载值为 100（最大值）表示 Redis 服务器正忙于处理请求，没有空闲时间。 若要查看某些请求是否占用了服务器的全部处理能力，请按上一段中的说明运行 SlowLog 命令。 有关详细信息，请参阅“CPU 使用率/服务器负载过高”。
1. 客户端上是否存在其他可能导致网络故障的事件？ 常见的事件包括： 客户端实例数向上扩展或向下，部署新版本的客户端或启用了自动缩放。 在我们的测试，我们发现自动缩放或缩放向上/向下可能导致出站网络连接，以几秒钟后会丢失。 StackExchange.Redis 代码可以灵活应对此类事件，并且会重新连接。 时重新连接后，在队列中的任何请求会超时。
1. 是否存在到缓存超时前几个小型请求的大型请求？ 参数`qs`中错误消息，告知你多少请求从客户端发送到服务器，但尚未处理响应。 此值可能会持续增加，因为 StackExchange.Redis 使用单个 TCP 连接，一次只能读取一个响应。 即使第一个操作已超时，它不会阻止发送到或从服务器的更多数据。 将阻止其他请求，直到大型请求已完成，并可能会导致超时。 降低超时概率的一种解决方案是确保缓存对于工作负荷来说足够大，并将大的值拆分成较小的块。 另一种可能的解决方案是使用客户端中的 `ConnectionMultiplexer` 对象池，在发送新请求时选择负载最小的 `ConnectionMultiplexer`。 加载跨多个连接对象应防止单个超时而导致其他请求也超时。
1. 如果您使用的`RedisSessionStateProvider`，确保已正确设置重试超时时间。 `retryTimeoutInMilliseconds` 应高于 `operationTimeoutInMilliseconds`，否则无法执行重试。 在下面的示例中，`retryTimeoutInMilliseconds` 设置为 3000。 有关详细信息，请参阅 [Azure Redis 缓存的 ASP.NET 会话状态提供程序](cache-aspnet-session-state-provider.md)和 [How to use the configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)（如何使用会话状态提供程序和输出缓存提供程序的配置参数）。

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

1. 通过[监视](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，了解 Azure Redis 缓存服务器上的内存使用情况。 如果实施了逐出策略，则当 `Used_Memory` 达到缓存大小时，Redis 就会开始逐出密钥。 理想情况下，`Used memory` 应只稍高于 `Used Memory RSS`。 差异过大意味着没有内存碎片 （内部或外部）。 如果 `Used Memory RSS` 小于 `Used Memory`，则意味着操作系统已更换部分缓存内存。 如果发生这种情况，则会出现明显的延迟。 因为没有控制如何将其分配映射到内存页，高 Redis。`Used Memory RSS`通常是出现峰值内存使用情况的结果。 当 Redis 服务器释放内存时，分配器会占用内存，但它可能会或可能不提供内存返回给系统。 `Used Memory` 值与操作系统所报告的内存消耗量可能存在差异。 内存可能已使用和发布由 Redis 但并未送回给系统。 若要帮助减少内存问题，可以执行以下步骤：

   - 缓存升级到更大的大小，使你不会在系统上运行和内存限制。
   - 对密钥设置过期时间，以便主动逐出过旧的值。
   - 监视 `used_memory_rss` 缓存指标。 当此值接近缓存大小时，您可能会出现性能问题。 如果要使用高级缓存，或升级到更大的缓存大小在多个分片上分布数据。

   有关详细信息，请参阅[服务器上的内存压力](#memory-pressure-on-the-server)。

## <a name="additional-information"></a>其他信息

- [我应使用哪种 Azure Redis 缓存产品/服务和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何制定基准和测试缓存性能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何运行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
- [如何监视 Azure Redis 缓存](cache-how-to-monitor.md)
