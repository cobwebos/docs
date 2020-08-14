---
title: 适用于 Redis 开发的 Azure 缓存常见问题解答
description: 了解可帮助你为 Redis 的 Azure 缓存进行开发的常见问题的解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: ef85b6f9e4595e7b4ff367da415fad777de68679
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211312"
---
# <a name="azure-cache-for-redis-development-faqs"></a>适用于 Redis 开发的 Azure 缓存常见问题解答

本文提供了有关如何针对 Redis 进行 Azure 缓存开发的常见问题的解答。

## <a name="common-questions-and-answers"></a>常见问题和解答
本部分包含以下 Faq：

* [如何开始使用 Azure Redis 缓存？](#how-can-i-get-started-with-azure-cache-for-redis)
* [StackExchange.Redis 配置选项有什么作用？](#what-do-the-stackexchangeredis-configuration-options-do)
* [可以使用哪些 Azure Redis 缓存客户端？](#what-azure-cache-for-redis-clients-can-i-use)
* [Azure Redis 缓存是否有本地模拟器？](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [如何运行 Redis 命令？](#how-can-i-run-redis-commands)
* [为什么 Redis 的 Azure 缓存没有 MSDN 类库参考？](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [是否可将 Azure Redis 缓存用作 PHP 会话缓存？](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [什么是 Redis 数据库？](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>如何开始使用 Azure Redis 缓存？
有几种开始使用 Azure Redis 缓存的方法。

* 用户可以查看适用于 [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)、[ASP.NET](cache-web-app-howto.md)、[Java](cache-java-get-started.md)、[Node.js](cache-nodejs-get-started.md) 和 [Python](cache-python-get-started.md) 的教程之一。
* 用户可以观看[如何使用 Microsoft Azure Redis 缓存生成高性能应用](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)。
* 还可以查看与项目的开发语言匹配的面向客户的客户文档，了解如何使用 Redis。 许多 Redis 客户端都可用于 Azure Redis 缓存。 有关 Redis 客户端列表，请参阅 [https://redis.io/clients](https://redis.io/clients)。

如果还没有 Azure 帐户，则可以：

* [免费注册 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 获取可用来尝试付费版 Azure 服务的信用额度。 即使在信用额度用完之后，也可以保留帐户和使用免费的 Azure 服务和功能。
* [激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 MSDN 订阅每月提供可用来试用付费版 Azure 服务的信用额度。

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange.Redis 配置选项有什么作用？
StackExchange.Redis 有很多选项。 本部分介绍一些常用设置。 有关 StackExchange.Redis 选项的详细详细，请参阅 [StackExchange.Redis 配置](https://stackexchange.github.io/StackExchange.Redis/Configuration)。

| 配置选项 | 说明 | 建议 |
| --- | --- | --- |
| AbortOnConnectFail |如果设置为 true，则发生网络故障后不会重新建立连接。 |设置为 false，让 StackExchange.Redis 自动重新连接。 |
| ConnectRetry |初始连接期间重试连接的次数。 |请参阅下面的注释寻求指导。 |
| ConnectTimeout |连接操作的超时，以毫秒为单位。 |请参阅下面的注释寻求指导。 |

通常情况下，使用客户端的默认值便已足够。 可以根据工作负荷微调选项。

* **重试**
  * 对于 ConnectRetry 和 ConnectTimeout，一般指导原则是快速失败并重试。 该指导原则取决于工作负荷，以及客户端发出 Redis 命令和接收响应平均花费的时间。
  * 让 StackExchange.Redis 自动重新连接，而不是检查连接状态，并由用户自己重新连接。 **避免使用 ConnectionMultiplexer.IsConnected 属性**。
  * 雪球效应 - 有时，可能会遇到这样的问题：不断地重试解决，但重试不断累积而永远无法恢复。 如果发生雪球效应，应该根据 Microsoft 模式和实践小组发布的[一般重试指导原则](../best-practices-retry-general.md)中所述，考虑使用指数退让重试算法。
  
* **超时值**
  * 根据工作负荷相应地设置值。 如果要存储较大值，应将超时设置为较大值。
  * 将 `AbortOnConnectFail` 设置为 false，让 StackExchange.Redis 重新连接。
  * 使用应用程序的单个 ConnectionMultiplexer 实例。 可以使用 LazyConnection 创建 Connection 属性返回的单个实例，如[使用 ConnectionMultiplexer 类连接到缓存](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)中所示。
  * 将 `ConnectionMultiplexer.ClientName` 属性设置为应用程序实例的唯一名称以进行诊断。
  * 对自定义工作负载使用多个 `ConnectionMultiplexer` 实例。
      * 如果应用程序中的负载不同，可以遵循此模型。 例如：
      * 可以使用一个多路复用器来处理大键。
      * 可以使用一个多路复用器来处理小键。
      * 可为连接超时设置不同的值，并为使用的每个 ConnectionMultiplexer 设置重试逻辑。
      * 在每个多路复用器上设置 `ClientName` 属性以帮助进行诊断。
      * 该指导原则可以更好地改进每个 `ConnectionMultiplexer`的延迟。

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>可以使用哪些 Azure Redis 缓存客户端？
Redis 的一大优势是有许多客户端，支持许多不同的开发语言。 如需最新的客户端列表，请参阅 [Redis 客户端](https://redis.io/clients)。 有关介绍多种不同语言和客户端的教程，请参阅[如何使用 Azure Redis 缓存](cache-dotnet-how-to-use-azure-redis-cache.md)以及它在内容列表中的同级文章。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Azure Redis 缓存是否有本地模拟器？
Azure Redis 缓存没有本地模拟器，但可以在本地计算机上从 [Redis 命令行工具](https://github.com/MSOpenTech/redis/releases/)运行 MSOpenTech 版本的 redis-server.exe 并连接到它，以获得与本地缓存模拟器相似的体验，如以下示例所示：

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

如果需要，可以选择配置 [redis.conf](https://redis.io/topics/config) 文件，以更好地匹配联机 Azure Redis 缓存的[默认缓存设置](cache-configure.md#default-redis-server-configuration)。

### <a name="how-can-i-run-redis-commands"></a>如何运行 Redis 命令？
可以使用 [Redis 命令](https://redis.io/commands#)中列出的任何命令，但 [Azure Redis 缓存中不支持的 Redis 命令](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)所列的命令除外。 可以使用多个选项来运行 Redis 命令。

* 如果有标准或高级缓存，可以使用 [Redis 控制台](cache-configure.md#redis-console)运行 Redis 命令。 Redis 控制台提供了一种在 Azure 门户中安全地运行 Redis 命令的方式。
* 还可以使用 Redis 命令行工具。 若要使用这些工具，请执行以下步骤：
* 下载 [Redis 命令行工具](https://github.com/MSOpenTech/redis/releases/)。
* 使用 `redis-cli.exe`连接到缓存。 使用 -h 开关传入缓存终结点，使用 -a 传入密钥，如以下示例中所示：
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis 命令行工具不适用于 TLS 端口，但你可以按照[如何将 Redis 命令行工具用于 Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) 一文中的说明，使用 `stunnel` 等实用程序将工具安全地连接到 TLS 端口。
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>为什么 Redis 的 Azure 缓存没有 MSDN 类库参考？
Redis Microsoft Azure 缓存基于流行的开源内存中数据存储 Redis。 它可以通过各种 [Redis 客户端](https://redis.io/clients) 进行访问，这些客户端适用于许多编程语言。 每个客户端有自身的 API，用于通过 [Redis 命令](https://redis.io/commands)调用 Azure Redis 缓存实例。

由于客户端各不相同，因此 MSDN 上未提供统一的类引用，每个客户端都有自己的参考文档。 除了参考文档以外，还可以参阅多个教程，这些教程介绍了如何通过不同的语言和缓存客户端来开始使用 Azure Redis 缓存。 若要访问这些教程，请参阅[如何使用 Azure Redis 缓存](cache-dotnet-how-to-use-azure-redis-cache.md)以及它在内容列表中的同级文章。

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>是否可将 Azure Redis 缓存用作 PHP 会话缓存？
可以。若要使用 Azure Redis 缓存作为 PHP 会话缓存，请在 `session.save_path` 中指定 Azure Redis 缓存实例的连接字符串。

> [!IMPORTANT]
> 使用 Azure Redis 缓存作为 PHP 会话缓存时，必须对用于连接到缓存的安全密钥进行 URL 编码，如以下示例所示：
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> 如果未对密钥进行 URL 编码，可能会收到类似于以下消息的异常： `Failed to parse session.save_path`
>

有关在 PhpRedis 客户端中使用 Azure Redis 缓存作为 PHP 会话缓存的详细信息，请参阅 [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler)（PHP 会话处理程序）。

### <a name="what-are-redis-databases"></a>什么是 Redis 数据库？

Redis 数据库就是同一 Redis 实例中的数据的逻辑隔离。 缓存内存在所有数据库之间共享，给定数据库的实际内存消耗取决于该数据库中存储的键/值。 例如，C6 缓存的内存为 53 GB，P5 的内存为 120 GB。 可以选择将全部 53 GB/120 GB 放置在一个数据库中，也可以将其分配到多个数据库中。 

> [!NOTE]
> 使用启用了群集功能的高级 Azure Redis 缓存时，仅数据库 0 可用。 此限制是固有的 Redis 限制，并不特定于 Azure Redis 缓存。 有关详细信息，请参阅 [是否需要对客户端应用程序进行更改才能使用群集功能？](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)。
> 
> 

## <a name="next-steps"></a>后续步骤

了解 [适用于 Redis 的其他 Azure 缓存的常见问题](cache-faq.md)。
