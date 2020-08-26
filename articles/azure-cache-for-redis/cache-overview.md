---
title: 什么是 Azure Redis 缓存？
description: 了解 Azure Cache for Redis，以便启用缓存端、内容缓存、用户会话缓存、作业和消息队列以及分布式事务。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 3751560125ea8ac6cc00ed63521bff30b751e688
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009590"
---
# <a name="azure-cache-for-redis"></a>用于 Redis 的 Azure 缓存
Azure Cache for Redis 基于开源软件 [Redis](https://redis.io/) 提供内存中数据存储。 Redis 极大地提高了在后端数据存储上使用的应用程序的性能和可伸缩性。 它将经常访问的数据保留在可快速读写的服务器内存中，从而能够处理大量应用程序请求。 Redis 为新式应用程序带来了关键的低延迟、高吞吐量数据存储解决方案。

Azure Cache for Redis 以托管服务的形式提供 Redis。 它提供安全的专用 Redis 服务器实例，且完全兼容 Redis API。 该服务由 Microsoft 管理并在 Azure 中托管，可供 Azure 内外的任何应用程序访问。

Azure Cache for Redis 可用作分布式数据或内容缓存、会话存储和消息中转站等。 它可单独部署，也可与其他 Azure 数据库服务（如 Azure SQL 或 Cosmos DB）一起部署。

## <a name="key-scenarios"></a>关键方案
Azure Cache for Redis 通过支持常见的应用程序体系结构模式来提高应用程序性能。 部分最常用的模式包括：

| 模式      | 说明                                        |
| ------------ | -------------------------------------------------- |
| [数据缓存](cache-web-app-cache-aside-leaderboard.md) | 数据库通常很大，无法直接加载到缓存中。 通常是使用[缓存端](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)模式，只在需要时才将数据加载到缓存中。 系统对数据进行更改时，还会同时更新缓存，然后将缓存分发给其他客户端。 另外，系统还可以设置数据的过期时间，或者使用逐出策略触发对缓存的数据更新。|
| [内容缓存](cache-aspnet-output-cache-provider.md) | 许多网页都是基于使用静态内容（例如页眉、页脚、横幅）的模板生成的。 这些静态项不应经常更改。 与后端数据存储相比，使用内存中缓存可以快速地访问静态内容。 此模式降低了处理时间和服务器负载，使 Web 服务器能够更快地做出响应。 这可减少处理负载所需的服务器数量。 Azure Cache for Redis 提供 Redis 输出缓存提供程序，支持对 ASP.NET 使用此模式。|
| [会话存储](cache-aspnet-session-state-provider.md) | 此模式通常用于购物车和其他用户历史记录数据。Web 应用程序可能需要将此类信息与用户 Cookie 相关联。 在 Cookie 中存储过多内容可能会对性能造成负面影响，因为 Cookie 会变大，并且每次请求都需要传递和验证 Cookie。 常用解决方案是使用 Cookie 作为键来查询数据库中的数据。 使用内存中缓存（例如 Azure Redis 缓存）将信息与用户关联在速度上要比与整个关系数据库交互快得多。 |
| 作业和消息队列 | 当需要花费时间来执行与请求相关联的操作时，应用程序通常将任务添加到队列中。 长时间运行的操作将排队等待按顺序处理，通常由另一服务器进行处理。  这种将工作推迟的方法称为任务队列。 Azure Cache for Redis 提供了一个分布式队列，用以在应用程序中启用此模式。|
| 分布式事务 | 有时候，应用程序需要将针对后端数据存储的一系列命令作为单个原子操作来执行。 所有命令都必须成功，否则，所有命令都必须回退到初始状态。 Azure Cache for Redis 支持将一批命令作为单个[事务](https://redis.io/topics/transactions)来执行。 |

## <a name="service-tiers"></a>服务层
Azure Redis 缓存在下述层中提供：

| 层 | 说明 |
|---|---|
基本 | 单节点缓存。 此层级支持多个内存大小（250 MB - 53 GB），适用于开发/测试和非关键工作负荷。 基本层没有服务级别协议 (SLA)。 |
| Standard | 在 Azure 托管的双节点（主/副本）配置中提供复制的缓存，并提供高可用性 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)。 |
| 高级 | 高级层是面向企业的层。 高级层缓存支持更多的功能，吞吐量更高，延迟更低。 高级层中的缓存部署在更强大的硬件上，其性能优于基本层或标准层。 这种优势意味着，在缓存大小相同的情况下，高级层的吞吐量大于标准层。 |

### <a name="feature-comparison"></a>功能比较
[Azure Cache for Redis 定价](https://azure.microsoft.com/pricing/details/cache/)提供了每个层的详细比较。 下表介绍了每个层支持的部分功能：

| 功能说明 | 高级 | Standard | 基本 |
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

### <a name="choosing-the-right-tier"></a>选择正确的层
选择 Azure Cache for Redis 层时，应考虑以下事项。

* **内存**：基本级别和标准级别提供 250 MB - 53 GB。 “高级”层级提供高达 1.2 TB（作为群集）或 120 GB（非群集）。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。
* **网络性能**：如果工作负荷需要较高的吞吐量，则可使用高级层，该层可提供比标准层或基本层更高的带宽。 另外，在每个层中，缓存大小越大，带宽越高，因为是由基础 VM 托管缓存。 有关详细信息，请参阅 [Azure Cache for Redis 性能](cache-planning-faq.md#azure-cache-for-redis-performance)。
* **吞吐量**：高级级别提供的可用吞吐量最大。 如果缓存服务器或客户端达到带宽限制，客户端可能会出现超时。 有关详细信息，请参阅下表。
* **高可用性**：Azure Cache for Redis 保证标准/高级缓存的可用性符合我们的 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)。 SLA 仅涉及与缓存终结点的连接。 SLA 不涉及对数据丢失的防护。 我们建议使用高级层中的 Redis 数据暂留功能来增加灵活性，防止数据丢失。
* **Redis 数据持久性**：高级层允许将缓存数据暂留在 Azure 存储帐户中。 在基本/标准缓存中，所有数据只存储在内存中。 底层基础结构问题可能会导致潜在的数据丢失。 我们建议使用高级层中的 Redis 数据暂留功能来增加灵活性，防止数据丢失。 Azure Cache for Redis 提供可在 Redis 暂留中使用的 RDB 和 AOF（预览版）选项。 有关详细信息，请参阅[如何为高级 Azure Redis 缓存配置持久性](cache-how-to-premium-persistence.md)。
* **Redis 群集**：要创建大于 120 GB 的缓存，或要将数据通过分片的方式分散到多个 Redis 节点中，可以使用在高级层级中包含的 Redis 群集功能。 每个节点都包含一个主/副缓存对，目的是提高可用性。 有关详细信息，请参阅[如何为高级 Azure Redis 缓存配置群集功能](cache-how-to-premium-clustering.md)。
* **增强的安全性和网络隔离**：Azure 虚拟网络 (VNET) 部署为 Azure Redis 缓存提供增强的安全性和隔离性，并提供子网、访问控制策略以及其他进一步限制访问的功能。 有关详细信息，请参阅 [如何为高级 Azure Redis 缓存配置虚拟网络支持](cache-how-to-premium-vnet.md)。
* **配置 Redis**：在标准级别和高级级别，都可以针对 Keyspace 通知来配置 Redis。
* **客户端连接的最大数量**：高级级别提供的可以连接到 Redis 的客户端数量是最大的，缓存大小越大，连接数量越大。 群集不会增加可用于群集缓存的连接数。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。
* **针对 Redis 服务器的专用核心**：高级级别的所有缓存大小都有针对 Redis 的专用核心。 在基本层/标准层中，C1 以上（含）大小均有针对 Redis 服务器的专用核心。
* **单线程处理**：根据设计，Redis 只使用一个线程进行命令处理。 Azure Cache for Redis 还利用其它核心进行 I/O 处理。 拥有更多的内核可能不会产生线性缩放，但可提高吞吐量性能。 而且，较大 VM 的带宽限制通常比较小 VM 的更高。 这有助于避免网络饱和，从而避免应用程序超时。
* **性能改进**：相较于基本层或标准层，高级层中的缓存部署在处理器速度更快且性能更高的硬件上。 高级级别缓存的吞吐量更高，延迟更低。 有关详细信息，请参阅 [Azure Cache for Redis 性能](cache-planning-faq.md#azure-cache-for-redis-performance)

可以在缓存创建后将其提升到更高的层级。 不允许降到更低的层。 有关缩放的分步说明，请参阅[如何缩放 Azure Redis 缓存](cache-how-to-scale.md)和[如何自动执行缩放操作](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

## <a name="next-steps"></a>后续步骤
* [ASP.NET Web 应用快速入门](cache-web-app-howto.md)创建简单的使用 Azure Redis 缓存的 ASP.NET Web 应用。
* [.NET 快速入门](cache-dotnet-how-to-use-azure-redis-cache.md)创建使用 Azure Redis 缓存的 .NET 应用。
* [.NET Core 快速入门](cache-dotnet-core-quickstart.md)创建使用 Azure Redis 缓存的 .NET Core 应用。
* [Node.js 快速入门](cache-nodejs-get-started.md)创建简单的使用 Azure Redis 缓存的 Node.js 应用。
* [Java 快速入门](cache-java-get-started.md)创建使用 Azure Redis 缓存的简单 Java 应用。
* [Python 快速入门](cache-python-get-started.md)创建使用 Azure Redis 缓存的简单 Python 应用。
