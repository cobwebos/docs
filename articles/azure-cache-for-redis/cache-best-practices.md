---
title: 适用于 Redis 的 Azure 缓存的最佳实践
description: 通过遵循这些最佳做法, 了解如何有效地使用 Azure Cache for Redis。
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
ms.openlocfilehash: 6ac4722c1253f97bfb8c232202e24a923c027edf
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018827"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>适用于 Redis 的 Azure 缓存的最佳实践 
通过遵循这些最佳做法, 你可以最大程度地利用 Azure Cache for Redis 实例的性能和经济高效的使用。

## <a name="configuration-and-concepts"></a>配置和概念
 * **为生产系统使用标准层或高级层。**  基本层是没有数据复制且没有 SLA 的单节点系统。 此外，使用至少一个 C1 缓存。  C0 缓存适用于简单的开发/测试方案, 因为它们具有共享的 CPU 内核、小内存, 并且容易出现 "干扰邻居" 问题。

 * **请记住, Redis 是内存中数据存储区。**  [本文](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)概述了可能发生数据丢失的一些情况。

 * **开发您的系统, 使其能够处理连接信号**[由于修补和故障转移](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)。

 * **配置[maxmemory-reserved 设置](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)以提高**内存压力条件下的系统响应能力。  此设置对于编写繁重的工作负荷或在 Redis 中存储较大的值 (100 KB 或更多) 尤其重要。  我建议从缓存大小的 10% 开始, 如果有写入繁重的负载, 则增加。 请参阅选择值时的[一些注意事项](cache-how-to-troubleshoot.md#considerations-for-memory-reservations)。

 * **Redis 最适用于较小的值**, 因此请考虑将更大的数据将到多个键。  [本 Redis 讨论](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)中列出了一些注意事项, 你应该仔细考虑这些注意事项。  阅读[此文章](cache-how-to-troubleshoot.md#large-requestresponse-size)以了解较大值可能引起的问题示例。

 * **在同一区域中查找缓存实例和应用程序。**  连接到不同区域中的缓存可能会显著增加延迟并降低可靠性。  尽管可以从 Azure 外部进行连接, 但在*使用 Redis 作为缓存*时, 建议不要使用此方法。  如果使用 Redis 作为键/值存储, 则延迟可能不是主要考虑因素。 

 * **重复使用连接**-创建新连接代价高昂并增加延迟, 因此尽量重复使用连接。 如果选择创建新的连接, 请确保在释放旧连接之前将其关闭 (即使是在 .NET 或 Java 等托管内存语言中)。

 * **将客户端库配置为使用至少15秒的*连接超时*** , 使系统在较高 CPU 条件下进行连接。  小型连接超时值不保证在该时间范围内建立连接。  如果出现错误 (高客户端 CPU、高服务器 CPU 等), 则较短的连接超时值将导致连接尝试失败。 此行为通常会使问题更糟。  通过强制系统重新启动尝试重新连接的进程, 而不是帮助, 更短的超时加剧问题, 这可能导致*连接 > 故障 > 重试*循环。 我们通常建议你在15秒或更长时间内保持连接超时。 在15或20秒后, 最好让连接尝试成功, 而不是将其快速地重试。 这种重试循环可能会导致你的服务中断持续时间比你让系统初始时间更长的时间。  
     > [!NOTE]
     > 本指南特定于*连接尝试*, 并且与你愿意等待*操作*(如 GET 或 SET 完成) 无关。
 

 * **避免消耗大量的命令**-某些 redis 操作 (如[KEYS 命令](https://redis.io/commands/keys))*非常*昂贵, 应避免这样做。  有关详细信息, 请参阅有关[昂贵命令的一些注意事项](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>内存管理
你可能需要考虑一些与你的 Redis 服务器实例中的内存使用相关的问题。  下面是几个例子:

 * **选择适用于你的应用程序的[逐出策略](https://redis.io/topics/lru-cache)。**  Azure Redis 的默认策略是不*稳定的*, 这意味着只有设置了 TTL 值的密钥才有资格逐出。  如果没有任何密钥具有 TTL 值, 则系统不会逐出任何密钥。  如果希望系统在内存压力下允许任何密钥被逐出, 则可能需要考虑*allkeys-lru-lru*策略。

 * **设置密钥的过期值。**  这会提前删除密钥, 而不会等待内存不足。  如果由于内存压力而导致逐出, 则可能会导致服务器上出现额外负载。  有关详细信息, 请参阅 "[过期](https://redis.io/commands/expire)" 和 " [ExpireAt](https://redis.io/commands/expireat) " 命令的文档。
 
## <a name="client-library-specific-guidance"></a>特定于客户端库的指南
 * [Stackexchange.redis. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-我应该使用哪种客户端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [番茄酱 (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 会话状态提供程序](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>何时可以安全地重试？
遗憾的是, 没有任何简单的答案。  每个应用程序都需要确定哪些操作可以重试, 哪些不能重试。  每个操作都有不同的要求和密钥间依赖关系。  下面是你可能需要考虑的一些事项:

 * 即使 Redis 已成功运行您要求其运行的命令, 也可以获取客户端错误。  例如：
     - 超时是一种客户端概念。  如果操作到达服务器, 服务器将运行该命令, 即使客户端放弃等待。  
     - 当套接字连接上出现错误时, 不能知道操作是否确实在服务器上运行。  例如, 在服务器处理请求后, 但在客户端接收响应之前, 会发生连接错误。
 *  如果意外运行相同的操作两次, 应用程序将如何响应？  例如, 如果我递增一个整数两次, 而不是一次, 该怎么办？  我的应用程序是否从多个位置写入同一密钥？  如果重试逻辑覆盖了应用的其他部分所设置的值, 会怎么样？

如果要测试代码在出现错误情况下的工作方式, 请考虑使用[重新启动功能](cache-administration.md#reboot)。 这使你可以了解连接信号如何影响你的应用程序。

## <a name="performance-testing"></a>性能测试
 * 在编写自己的性能测试之前, 请先**使用`redis-benchmark.exe`** 来了解可能的吞吐量/延迟。  Redis-基准文档可在[此处找到](https://redis.io/topics/benchmarks)。  请注意, redis 不支持 SSL, 因此在运行测试之前, 必须[通过门户启用非 SSL 端口](cache-configure.md#access-ports)。  [可在此处找到 windows 兼容版本的 redis-benchmark.exe](https://github.com/MSOpenTech/redis/releases)
 * 用于测试的客户端 VM 应与 Redis 缓存实例**位于同一区域**。
 * 建议为客户端**使用 DV2 VM 系列**, 因为它们具有更好的硬件, 会提供最佳的结果。
 * 请确保使用的客户端 VM 的*计算和带宽至少与*要测试的缓存数量相同。 
 * 如果在 Windows 上, 请在客户端计算机上**启用 VRSS** 。  [请参阅此处了解详细信息](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)。  Powershell 脚本示例:
     >Set-executionpolicy 无限制启用-Get-netadapterrss (Get-netadapter)。路径名 
     
 * **请考虑使用高级层 Redis 实例**。  由于这些缓存大小在更好的硬件上运行了 CPU 和网络, 因此会获得更好的网络延迟和吞吐量。
 
     > [!NOTE]
     > 我们观察到的性能结果[发布在此处](cache-faq.md#azure-cache-for-redis-performance)供您参考。   另外, 请注意, SSL/TLS 会增加一些开销, 因此如果使用传输加密, 可能会获得不同的延迟和/或吞吐量。
 
### <a name="redis-benchmark-examples"></a>Redis-基准测试示例
**预测试安装程序**:这会在下面列出的延迟和吞吐量测试命令所需的数据的情况下准备缓存实例。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**测试延迟**:这将使用1k 负载测试 GET 请求。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**测试吞吐量:** 这会将管道 GET 请求用于1k 负载。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
