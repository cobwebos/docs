---
title: Azure Cache for Redis 管理常见问题解答
description: 了解帮助你管理用于 Redis 的 Azure 缓存的常见问题解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 5c5c7a5adae9891f764f714d1700c6024376de02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88205393"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Azure Cache for Redis 管理常见问题解答
本文提供关于如何管理用于 Redis 的 Azure 缓存的常见问题解答。

## <a name="common-questions-and-answers"></a>常见问题和解答
本部分包含以下常见问题解答：

* [何时应启用非 TLS/SSL 端口来连接 Redis？](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [生产的一些最佳做法是什么？](#what-are-some-production-best-practices)
* [使用常见 Redis 命令时要注意哪些问题？](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [如何制定基准和测试缓存的性能？](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [有关线程池增长的重要详细信息](#important-details-about-threadpool-growth)
* [启用服务器 GC，以便在使用 StackExchange.Redis 时在客户端上获取更多吞吐量](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [围绕连接的性能注意事项](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>何时应启用非 TLS/SSL 端口来连接 Redis？
Redis 服务器本身不支持 TLS，但 Azure Cache for Redis 可提供此支持。 如果要连接到 Azure Cache for Redis 并且客户端支持 TLS（如 StackExchange.Redis），则应使用 TLS。

>[!NOTE]
>默认情况下，对于新的 Azure Cache for Redis 实例，非 TLS 端口处于禁用状态。 如果客户端不支持 TLS，则必须根据[在 Azure Cache for Redis 中配置缓存](cache-configure.md)一文的[访问端口](cache-configure.md#access-ports)部分中的说明启用非 TLS 端口。
>
>

`redis-cli` 等 Redis 工具对 TLS 端口不起作用，但是，可以根据 [Announcing ASP.NET Session State Provider for Redis Preview Release](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)（适用于 Redis 预览版的 ASP.NET 会话状态提供程序通告）博客文章中的说明，使用 `stunnel` 等实用程序安全地将这些工具连接到 TLS 端口。

有关下载 Redis 工具的说明，请参阅[如何运行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands)部分。

### <a name="what-are-some-production-best-practices"></a>生产的一些最佳做法是什么？
* [StackExchange.Redis 最佳做法](#stackexchangeredis-best-practices)
* [配置和概念](#configuration-and-concepts)
* [性能测试](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis 最佳做法
* 将 `AbortConnect` 设置为 false，并使 ConnectionMultiplexer 自动重新连接。 [请参阅此处了解详细信息](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)。
* 重复使用 ConnectionMultiplexer - 不要为每个请求创建一个新的 ConnectionMultiplexe。 建议使用[此处所示](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)的 `Lazy<ConnectionMultiplexer>` 模式。
* 具有较小值的 Redis 工作性能最佳，因此请考虑将较大数据分成多个密钥。 [本次讨论的 Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) 为 100 kb，属于大型数据。 阅读[本文](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size)了解较大值可能引起的问题示例。
* 配置 [ThreadPool 设置](#important-details-about-threadpool-growth) ，以免超时。
* 将默认 connectTimeout 至少设置为 5 秒。 出现网络故障时，此时间间隔会给 StackExchange.Redis 足够的时间来重新建立连接。
* 注意与正在运行的不同操作相关的性能成本。 例如，`KEYS` 命令是 O(n) 操作，应当避免。 [redis.io](https://redis.io/commands/) 站点具有关于其支持的每个操作的时间复杂性的详细信息。 单击每个命令以查看每个操作的复杂程度。

#### <a name="configuration-and-concepts"></a>配置和概念
* 针对生产系统使用标准层或高级层。 基本层是没有数据复制和 SLA 的单节点系统。 此外，使用至少一个 C1 缓存。 C0 缓存通常用于简单的开发/测试方案。
* 请记住，Redis 是 **内存中** 数据存储区。 阅读[本文](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)，了解可能出现数据丢失的情况。
* 在开发系统时让它可以处理[由于修补和故障转移](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)出现的连接故障。

#### <a name="performance-testing"></a>性能测试
* 使用 `redis-benchmark.exe` 启动以在编写性能测试前感受可能的吞吐量。 因为 `redis-benchmark` 不支持 TLS，因此，在运行测试之前必须[通过 Azure 门户启用非 TLS 端口](cache-configure.md#access-ports)。 例如，请参阅 [如何制定基准和测试缓存的性能？](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* 用于测试的客户端 VM 应与 Azure Redis 缓存实例位于同一区域。
* 建议为客户端使用 Dv2 VM 系列，因为该系列具有更好的硬件，会提供最佳的结果。
* 确保选择的客户端 VM 至少与正在测试的缓存拥有相同的计算和带宽容量。
* 如果是在 Windows 设备上操作，请在客户端计算机上启用 VRSS。 [请参阅此处了解详细信息](https://technet.microsoft.com/library/dn383582.aspx)。
* 高级层 Redis 实例具有更好的网络延迟和吞吐量，因为它们是在 CPU 和网络两方面都更好的硬件上运行的。

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>使用常见 Redis 命令时要注意哪些问题？

* 对于某些需要较长时间才能完成的 Redis 命令，在未完全了解这些命令造成的影响的情况下，应避免运行这些命令。 例如，不要在生产环境中运行 [KEYS](https://redis.io/commands/keys) 命令。 它可能需要很长时间才能返回，具体时间取决于键数。 Redis 是单线程服务器，每次只能处理一个命令。 如果在 KEYS 后面发出了其他命令，则这些命令只会在处理完 KEYS 命令后才会得到处理。 [redis.io 站点](https://redis.io/commands/) 具有关于其支持的每个操作的时间复杂性的详细信息。 单击每个命令以查看每个操作的复杂程度。
* 键大小 - 应使用小键/值还是大键/值？ 这取决于方案。 如果方案需要较大的键，则可调整 ConnectionTimeout，然后重试值并调整重试逻辑。 从 Redis 服务器的角度来看，值越小，性能就越好。
* 这些考量并不意味着不能在 Redis 中存储较大值，只是要注意以下事项。 延迟会提高。 如果有一个较大的数据集和一个较小的数据集，则可以使用多个 ConnectionMultiplexer 实例，并根据 [StackExchange.Redis 配置选项有什么作用](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) 部分中所述，为每个实例配置一组不同的超时和重试值。

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>如何进行基准检验和测试缓存的性能？
* [启用缓存诊断](cache-how-to-monitor.md#enable-cache-diagnostics)，以便可以[监视](cache-how-to-monitor.md)缓存的运行状况。 可以在 Azure 门户中查看指标，也可以使用所选的工具[下载和查看](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)这些指标。
* 可以使用 redis-benchmark.exe 对 Redis 服务器进行负载测试。
* 确保负载测试客户端和 Azure Redis 缓存位于同一区域。
* 使用 redis-cli.exe，并使用 INFO 命令监视缓存。
* 如果负载导致出现大量内存碎片，则你应该扩展为更大的缓存大小。
* 有关下载 Redis 工具的说明，请参阅[如何运行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands)部分。

下面的命令提供了有关如何使用 redis-benchmark.exe 的示例。 为获得准确的结果，请从与缓存位于同一区域的 VM 运行这些命令。

* 使用 1 k 有效负载测试管道 SET 请求

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* 使用 1 k 有效负载测试管道 GET 请求。

>[!NOTE]
> 首先运行上面显示的 SET 测试以填充缓存
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>有关线程池增长的重要详细信息
CLR 线程池具有两种类型的线程 -“辅助角色”和“I/O 完成端口”(IOCP) 线程。

* 对于诸如处理 `Task.Run(…)` 或 `ThreadPool.QueueUserWorkItem(…)` 方法这类事务，请使用辅助角色线程。 需要在后台线程上进行工作时，CLR 中的各种组件也会使用这些线程。
* 进行异步 IO（例如从网络进行读取）时，使用 IOCP 线程。

线程池按需提供新的辅助角色线程或 I/O 完成线程（没有任何限制），直到它达到每种线程类型的“最小值”设置。 默认情况下，最小线程数设置为系统上的处理器数。

一旦现有（忙碌）线程数达到“最小”线程数，ThreadPool 便会将插入新线程的速率限制为每 500 毫秒一个线程。 通常情况下，如果系统中出现需要 IOCP 线程的突发工作，则它会快速处理该工作。 但是，如果突发工作多于配置的“最小值”设置，则在处理某些工作时会出现一定的延迟，因为 ThreadPool 会等待发生以下两种情况之一。

* 一个现有线程释放，以便处理工作。
* 在 500 毫秒内没有任何现有线程释放，因此会创建一个新线程。

基本上，这意味着忙碌线程数大于最小线程数，在应用程序处理网络流量之前可能需要付出 500 毫秒延迟。 此外请务必注意，当现有线程保持空闲状态的时间超过 15 秒（基于我记得的内容）时，会清理它，并且这种增长和收缩的循环可能会重复。

如果我们考虑一个来自 StackExchange.Redis（内部版本 1.0.450 或更高版本）的示例错误消息，会看到它现在会打印 ThreadPool 统计信息（请参阅下面的 IOCP 和辅助角色详细信息）。

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

在上面的示例中，可以看到对于 IOCP 线程有 6 个忙碌线程，而系统配置为允许最少 4 个线程。 在这种情况下，客户端可能会遇到两个 500 毫秒延迟，因为 6 > 4。

请注意，如果 IOCP 或辅助角色线程受到限制，则 StackExchange.Redis 可以会超时。

#### <a name="recommendation"></a>建议

考虑到此信息，我们强烈建议客户将 IOCP 和辅助角色线程的最小配置值设置为大于默认值。 我们无法提供有关此值应是多少的通用指导，因为一个应用程序的合适值对于另一个应用程序可能会太高或太低。 此设置还可能会影响复杂应用程序其他部分的性能，因此每个客户需要按照其特定需求来微调此设置。 开始时设置为 200 或 300 会比较好，随后可进行测试并根据需要进行调整。

如何配置此设置：

* 建议使用 `global.asax.cs` 中的 [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) 方法，以编程方式更改此设置。 例如：

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > 此 方法指定的值是全局设置，将影响整个 AppDomain。 例如，如果已有 4 核计算机，并想要在运行时将 *minWorkerThreads* 和 *minIoThreads* 设置为 50（每个 CPU），可使用 **ThreadPool.SetMinThreads(200, 200)** 。

* 也可指定最小线程数设置，方法是在 `Machine.config`（通常位于 `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`）中的 `<processModel>` 配置元素下使用 [*minIoThreads* 或 *minWorkerThreads* 配置设置](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx)。 **通常不建议以这种方式设置最小线程数，因为它是系统范围的设置。**

  > [!NOTE]
  > 此配置元素中指定的值是*按核心*设置。 例如，如果使用 4 核计算机，并且希望 *minIoThreads* 设置在运行时为 200，则使用 `<processModel minIoThreads="50"/>`。
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>启用服务器 GC，以便在使用 StackExchange.Redis 时在客户端上获取更多吞吐量
启用服务器 GC 可以在使用 StackExchange.Redis 时优化客户端并提供更好的性能和吞吐量。 有关服务器 GC 以及如何启用它的详细信息，请参阅以下文章：

* [若要启用服务器 GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [垃圾回收基础](/dotnet/standard/garbage-collection/fundamentals)
* [垃圾回收和性能](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>围绕连接的性能注意事项

每个定价层都有不同的客户端连接、内存和带宽的限制。 虽然每个缓存大小*最多*允许一定数量的连接，但与 Redis 的每个连接都具有其关联的开销。 此类开销的一个示例是，由于 TLS/SSL 加密而导致的 CPU 和内存使用。 给定缓存大小的最大连接限制假定轻负载缓存。 如果连接开销的负载*和*客户端操作的负载超出了系统容量，那么即使未超出当前缓存大小的连接限制，缓存也可能会遇到容量问题。

有关每个层的不同连接限制的详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。 有关连接和其他默认配置的详细信息，请参阅[默认 Redis 服务器配置](cache-configure.md#default-redis-server-configuration)。

## <a name="next-steps"></a>后续步骤

了解其他 [Azure Cache for Redis 常见问题解答](cache-faq.md)。
