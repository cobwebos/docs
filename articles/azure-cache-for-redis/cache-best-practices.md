---
title: Azure Redis 缓存的最佳做法
description: 了解如何使用 Azure 缓存为 Redis 有效地遵循以下最佳做法。
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452457"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Azure Redis 缓存的最佳做法 
通过遵循这些最佳做法，可帮助最大程度提高性能和经济高效利用 Azure 缓存的 Redis 实例。

## <a name="configuration-and-concepts"></a>配置和概念
 * **为生产系统使用标准或高级层。**  基本层是有没有数据复制和没有 SLA 的单个节点系统。 此外，使用至少一个 C1 缓存。  C0 缓存是适用于简单开发/测试方案，因为它们具有共享的 CPU 内核，很少的内存，可能有"相邻干扰"问题。

 * **请记住，Redis 是一个内存中数据存储区。**  [这篇文章](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)概述了某些情况下，会发生数据丢失。

 * **开发您的系统，以便它可以处理连接故障**[由于修补和故障转移](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)。

 * **配置你[maxmemory 保留设置](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)来提高系统的响应能力**内存压力的情况下。  此设置是写入密集型工作负荷或如果要在 Redis 中存储较大的值 （100 KB 或更大） 尤其重要。  我会建议你缓存的大小的 10%从开始，然后如果您有大量写入负载增加。 请参阅[一些注意事项](cache-how-to-troubleshoot.md#considerations-for-memory-reservations)时选择的值。

 * **Redis 工作性能最佳具有较小值**，因此请考虑将较大数据分成多个密钥。  在中[本次讨论的 Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)，应仔细考虑，列出了一些注意事项。  阅读[此文章](cache-how-to-troubleshoot.md#large-requestresponse-size)以了解较大值可能引起的问题示例。

 * **在同一区域中找到你的缓存实例和应用程序。**  连接到不同区域中的缓存可以显著增加延迟时间并降低可靠性。  虽然您可以连接从 Azure 外部，但不建议*尤其是在使用 Redis 作为缓存*。  如果将 Redis 用作只是键/值存储，延迟可能不是主要考虑因素。 

 * **重复使用连接**-创建新的连接是昂贵并且会增加延迟时间，因此重复使用尽可能多的连接。 如果您选择创建新的连接，请务必关闭旧连接之前释放 （甚至在托管的内存.NET 或 Java 等语言）。

 * **配置客户端库使用*连接超时*至少 15 秒的**，提供连接的系统时间甚至更高的 CPU 条件下。  小的连接超时值并不能保证在该时间范围内建立的连接。  如果转错误 （客户端过高 CPU、 高的服务器 CPU 等），则短时间的连接超时值将会导致连接尝试失败。 此行为通常更糟糕的情形变得更糟。  而不是帮助，较短的超时担负的强制系统重新启动该进程的尝试重新连接，这可能会导致问题*连接-> 故障-> 重试*循环。 我们通常建议您保留您的连接超时值 15 秒或更高版本。 最好是让您成功 15 或 20 秒后，不是很有其快速失败的连接尝试仅以重试。 重试循环可能会导致持续时间不是如果您只需让系统需要再最初在服务中断。  
     > [!NOTE]
     > 本指南是特定于*连接尝试*，也不愿意等待的时间到相关*操作*如 GET 或 SET 才能完成。
 

 * **避免占用内存的命令**-某些 redis 操作，如[KEYS 命令](https://redis.io/commands/keys)，将*非常*昂贵，应当避免。  有关详细信息，请参阅[占用内存的命令一些注意事项](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>内存管理
有几种方法与您可能想要考虑的 Redis 服务器实例内的内存使用率。  以下是一些：

 * **选择[逐出策略](https://redis.io/topics/lru-cache)适用于你的应用程序。**  Azure Redis 的默认策略是*易失性 lru*，这意味着 ttl 的密钥值设置才有资格的逐出。  如果没有键具有 TTL 值，系统不会逐出所有密钥。  如果您希望系统以允许任意键可在内存压力下如果逐出，则你可能希望考虑*allkeys lru*策略。

 * **设置对密钥的过期值。**  这将删除主动而不是等到遇到内存不足的密钥。  时逐出 does 进入由于内存压力，则可能导致额外的负载，在服务器上。  有关详细信息，请参阅的文档[过期](https://redis.io/commands/expire)并[ExpireAt](https://redis.io/commands/expireat)命令。
 
## <a name="client-library-specific-guidance"></a>客户端库的具体指南
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-应使用哪种客户端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [番茄酱 (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 会话状态提供程序](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>是不安全地重试？
遗憾的是，没有任何简单的答案。  每个应用程序需要确定哪些操作可以重试，哪些不能。  每个操作具有不同的要求和关键间的依赖项。  下面是可以考虑一些事项：

 * 你可以获取客户端的错误，即使 Redis 已成功运行要求要运行该命令。  例如：
     - 超时是客户端的概念。  如果该操作传播到服务器，服务器将运行命令，即使客户端放弃等待。  
     - 套接字连接错误时，不可能知道是否实际上该操作将在服务器上运行。  例如，由服务器，但在客户端收到响应之前处理该请求后，则会发生连接错误。
 *  我的应用程序如何响应如果我意外运行两次相同的操作？  例如，如果增加整数两次而不是只需一次？  将我的应用程序写入到相同的密钥从多个位置？  如果我重试逻辑会覆盖我的应用程序的其他部分所设置的值？

如果你想要测试你的代码是如何工作的错误条件，请考虑使用[重新启动功能](cache-administration.md#reboot)。 这样，您可以查看如何引起的连接故障会影响你的应用程序。

## <a name="performance-testing"></a>性能测试
 * **使用启动`redis-benchmark.exe`** 感受可能的吞吐量/延迟之前在编写性能测试。  Redis 基准文档可以是[以下网页中找到](http://redis.io/topics/benchmarks)。  请注意该 redis 基准不支持 SSL，因此您必须向[启用非 SSL 端口通过门户](cache-configure.md#access-ports)运行测试之前。  [Windows 兼容版本的 benchmark.exe 可在此处找到](https://github.com/MSOpenTech/redis/releases)
 * 客户端用于测试的 VM 应该**同一区域中**与 Redis 缓存实例。
 * **我们建议使用 Dv2 VM 系列**客户端因为它们具有更好的硬件，将提供最佳的结果。
 * 请确保客户端使用的 VM 具有 **至少要计算和带宽*与所测试的缓存。 
 * **启用 VRSS**如果在 Windows 上的客户端计算机上。  [请参阅此处了解详细信息](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)。  Powershell 脚本示例：
     >PowerShell 执行策略不受限制地启用 NetAdapterRSS-名称 (Get-netadapter)。名称 
     
 * **请考虑使用高级层 Redis 实例**。  这些缓存大小将具有更好的网络延迟和吞吐量，因为它们更好的硬件上运行的 CPU 和网络。
 
     > [!NOTE]
     > 我们观察到的性能结果[发布在这里](cache-faq.md#azure-cache-for-redis-performance)供你参考。   此外，请注意，SSL/TLS 会增加一些开销，因此如果您使用的传输加密可能会收到不同的延迟和/或吞吐量。
 
### <a name="redis-benchmark-examples"></a>Redis 基准示例
**测试前设置**:这将使用所需的延迟和吞吐量测试下面列出的命令的数据准备缓存实例。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**若要测试延迟**:这将测试使用 1 k 有效负载的 GET 请求。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**若要测试的吞吐量：** 1 k 有效负载使用管道 GET 请求。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
