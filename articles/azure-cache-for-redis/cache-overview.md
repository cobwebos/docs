---
title: 什么是 Azure Cache for Redis？
description: 了解 Azure Cache for Redis，以便启用缓存端、内容缓存、用户会话缓存、作业和消息队列以及分布式事务。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126364"
---
# <a name="azure-cache-for-redis-description"></a>Azure Cache for Redis 说明

Azure Cache for Redis 基于开源软件 [Redis](https://redis.io/) 提供内存中数据存储。 当用作缓存时，Redis 可以提高严重依赖于后端数据存储的系统的性能和可伸缩性。 提高性能的方式是将经常访问的数据复制到靠近应用程序的快速存储。 使用 Azure Cache for Redis 时，此快速存储位于内存中，而不是通过数据库从磁盘加载。

Azure Cache for Redis 也可用作内存中数据结构存储、分布式非关系数据库以及消息代理。 若要提高应用程序性能，可充分利用 Redis 引擎低延迟、高吞吐量的性能特点。

Azure Cache for Redis 提供了安全的专用 Redis 缓存。 Azure Redis 缓存由 Microsoft 进行管理并承载在 Azure 中，可供 Azure 内外的任何应用程序访问。

## <a name="using-azure-cache-for-redis"></a>使用 Azure Redis 缓存

Azure Cache for Redis 通过支持常见的应用程序体系结构模式来提高应用程序性能。 部分最常用的模式包括：

| 模式      | 说明                                        |
| ------------ | -------------------------------------------------- |
| [缓存端](cache-web-app-cache-aside-leaderboard.md) | 数据库通常很大，无法直接加载到缓存中。 通常是使用[缓存端](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)模式，只在需要时才将数据加载到缓存中。 系统对数据进行更改时，还会同时更新缓存，然后将缓存分发给其他客户端。 另外，系统还可以设置数据的过期时间，或者使用逐出策略触发对缓存的数据更新。|
| [内容缓存](cache-aspnet-output-cache-provider.md) | 许多网页都是基于使用静态内容（例如页眉、页脚、横幅）的模板生成的。 这些静态项不应经常更改。 与后端数据存储相比，使用内存中缓存可以快速地访问静态内容。 此模式降低了处理时间和服务器负载，使 Web 服务器能够更快地做出响应。 这可减少处理负载所需的服务器数量。 Azure Cache for Redis 提供 Redis 输出缓存提供程序，支持对 ASP.NET 使用此模式。|
| [用户会话缓存](cache-aspnet-session-state-provider.md) | 此模式通常用于购物车和其他用户历史记录数据。Web 应用程序可能需要将此类信息与用户 Cookie 相关联。 在 Cookie 中存储过多内容可能会对性能造成负面影响，因为 Cookie 会变大，并且每次请求都需要传递和验证 Cookie。 常用解决方案是使用 Cookie 作为键来查询数据库中的数据。 使用内存中缓存（例如 Azure Redis 缓存）将信息与用户关联在速度上要比与整个关系数据库交互快得多。 |
| 作业和消息队列 | 当需要花费时间来执行与请求相关联的操作时，应用程序通常将任务添加到队列中。 长时间运行的操作将排队等待按顺序处理，通常由另一服务器进行处理。  这种将工作推迟的方法称为任务队列。 Azure Cache for Redis 提供了一个分布式队列，用以在应用程序中启用此模式。|
| 分布式事务 | 有时候，应用程序需要将针对后端数据存储的一系列命令作为单个原子操作来执行。 所有命令都必须成功，否则，所有命令都必须回退到初始状态。 Azure Cache for Redis 支持将一批命令作为单个[事务](https://redis.io/topics/transactions)来执行。 |

## <a name="azure-cache-for-redis-offerings"></a>Azure Redis 缓存的产品/服务

Azure Redis 缓存在下述层中提供：

| 层级 | 说明 |
|---|---|
基本 | 单节点缓存。 此层级支持多个内存大小（250 MB - 53 GB），适用于开发/测试和非关键工作负荷。 基本层没有服务级别协议 (SLA) |
| 标准 | 在 Azure 托管的双节点（主/辅）配置中提供复制的缓存，并提供高可用性 SLA (99.9%) |
| 高级 | 高级层是面向企业的层。 高级层缓存支持更多的功能，吞吐量更高，延迟更低。 高级层中的缓存部署在更强大的硬件上，其性能优于基本层或标准层。 这种优势意味着，在缓存大小相同的情况下，高级层的吞吐量大于标准层。 |

> [!TIP]
> 有关高级缓存大小、吞吐量和带宽的详细信息，请参阅 [Azure Redis 缓存常见问题解答](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)。
>

可以在缓存创建后将其提升到更高的层级。 不允许降到更低的层。 有关缩放的分步说明，请参阅[如何缩放 Azure Redis 缓存](cache-how-to-scale.md)和[如何自动执行缩放操作](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

### <a name="feature-comparison"></a>功能比较

[Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)页对每个层进行了详细比较。 下表介绍了每个层支持的部分功能：

| 功能说明 | 高级 | 标准 | 基本 |
| ------------------- | :-----: | :------: | :---: |
| [服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Redis 数据持久性](cache-how-to-premium-persistence.md) |✔|-|-|
| [Redis 群集](cache-how-to-premium-clustering.md) |✔|-|-|
| [通过防火墙规则确保安全性](cache-configure.md#firewall) |✔|✔|✔|
| 传输中加密 |✔|✔|✔|
| [通过 VNet 增强安全性和隔离性](cache-how-to-premium-vnet.md) |✔|-|-|
| [导入/导出](cache-how-to-import-export-data.md) |✔|-|-|
| [计划的更新](cache-administration.md#schedule-updates) |✔|✔|✔|
| [异地复制](cache-how-to-geo-replication.md) |✔|-|-|
| [重新启动](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>后续步骤

* [ASP.NET Web 应用快速入门](cache-web-app-howto.md)创建简单的使用 Azure Redis 缓存的 ASP.NET Web 应用。
* [.NET 快速入门](cache-dotnet-how-to-use-azure-redis-cache.md)创建使用 Azure Redis 缓存的 .NET 应用。
* [.NET Core 快速入门](cache-dotnet-core-quickstart.md)创建使用 Azure Redis 缓存的 .NET Core 应用。
* [Node.js 快速入门](cache-nodejs-get-started.md)创建简单的使用 Azure Redis 缓存的 Node.js 应用。
* [Java 快速入门](cache-java-get-started.md)创建使用 Azure Redis 缓存的简单 Java 应用。
* [Python 快速入门](cache-python-get-started.md)创建使用 Azure Redis 缓存的简单 Python 应用。
