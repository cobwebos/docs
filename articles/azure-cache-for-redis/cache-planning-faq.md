---
title: Azure Cache for Redis 规划常见问题解答
description: 了解帮助你计划 Azure Redis 缓存的常见问题的解答
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 13ba529dd3067ae16167f0d9c14c8f72b982f52c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010809"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Azure Cache for Redis 规划常见问题解答

本文提供有关如何计划 Azure Cache for Redis 的常见问题的解答。

## <a name="common-questions-and-answers"></a>常见问题和解答
本部分包含以下常见问题解答：

* [Azure Redis 缓存性能](#azure-cache-for-redis-performance)
* [我应该将缓存放在哪个区域？](#in-what-region-should-i-locate-my-cache)
* [我的缓存数据位于何处？](#where-do-my-cached-data-reside)
* [Azure Redis 缓存如何计费？](#how-am-i-billed-for-azure-cache-for-redis)
* [是否可以将 Azure Cache 用于 Redis 与 Azure 政府云、Azure 中国世纪互联云或 Microsoft Azure 德国？](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure Redis 缓存性能
下表显示了在 IaaS VM 中使用 `redis-benchmark.exe` 针对 Azure Redis 缓存终结点测试各种大小的标准缓存和高级缓存时，所观测到的最大带宽值。 对于 TLS 吞吐量，请将 redis 基准用于 stunnel 以连接到 Azure Cache for Redis 终结点。

>[!NOTE] 
>这些值是没有保证的，并且我们不针对这些数字提供 SLA，但它们反映了典型的情况。 应该对自己的应用程序进行负载测试，以确定适合应用程序的缓存大小。
>因为我们会定期发布更新结果，这些数字可能会更改。
>

我们可以从此表得出以下结论：

* 在缓存大小相同的情况下，高级层中的缓存吞吐量要高于标准层中的缓存吞吐量。 例如，使用 6 GB 缓存，P1 的吞吐量为 180,000 请求/秒 (RPS)，而 C3 的吞吐量为 100,000 RPS。
* 启用 Redis 群集功能时，增加群集中分片（节点）的数量会导致吞吐量线性提高。 例如，若创建一个包含 10 个分片的 P4 群集，则可用吞吐量为 400,000 * 10 = 4 百万 RPS。
* 如果增加密钥大小，则高级层的吞吐量要高于标准层。

| 定价层 | 大小 | CPU 核心数 | 可用带宽 | 1 KB 值大小 | 1 KB 值大小 |
| --- | --- | --- | --- | --- | --- |
| **标准缓存大小** | | |**兆位/秒（Mb/秒）/兆字节/秒（MB/秒）** |**非 SSL 请求数/秒 (RPS)** |**SSL 请求数/秒 (RPS)** |
| C0 | 250 MB | 共享 | 100/12.5  |  15,000 |   7,500 |
| C1 |   1 GB | 1      | 500/62.5  |  38,000 |  20,720 |
| C2 | 2.5 GB | 2      | 500/62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000/125  | 100,000 |  90,000 |
| C4 |  13 GB | 2      | 500/62.5  |  60,000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **高级缓存大小** | |**每个分片的 CPU 核心数** | **兆位/秒（Mb/秒）/兆字节/秒（MB/秒）** |**每分片非 SSL 请求数/秒 (RPS)** |**每分片 SSL 请求数/秒 (RPS)** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

有关设置 stunnel 或下载 Redis 工具（例如 `redis-benchmark.exe`）的说明，请参阅[如何运行 Redis 命令？](cache-development-faq.md#how-can-i-run-redis-commands)。

### <a name="in-what-region-should-i-locate-my-cache"></a>应该将缓存放在哪个区域？
为了获得最佳性能并最大程度地降低延迟，请在缓存客户端应用程序所在的区域放置 Azure Redis 缓存。

### <a name="where-do-my-cached-data-reside"></a>我的缓存数据位于何处？
Azure Cache for Redis 将应用程序数据存储在托管缓存的 VM 的 RAM 中，具体取决于层级。 数据全部位于默认选择的 Azure 区域中。 在两种情况下，数据可能会离开某个区域：
* 在缓存上启用持久性后，Azure Cache for Redis 会将数据备份到你拥有的 Azure 存储帐户中。 如果提供的存储帐户恰好位于另一个区域，则数据的副本将存储在该区域。
* 如果设置了异地复制，并且辅助缓存位于其他区域（通常是这种情况），则数据会复制到该区域。

需要显式配置 Azure Cache for Redis 才能使用这些功能。 你还可以完全控制存储帐户或辅助缓存所在的区域。

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Azure Redis 缓存如何计费？
Azure Redis 缓存的定价在[此处](https://azure.microsoft.com/pricing/details/cache/)。 定价页列出每小时和每月费率。 缓存按分钟计费，从创建缓存时开始，到删除缓存时为止。 没有提供用于停止或暂停缓存的计费选项。

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>是否可以将 Azure Cache 用于 Redis 与 Azure 政府云、Azure 中国世纪互联云或 Microsoft Azure 德国？
可以，Azure Cache for Redis 可用于 Azure 政府云、Azure 中国世纪互联云和 Microsoft Azure 德国。 与 Azure 公有云相比，这些云中用于访问和管理 Azure Redis 缓存的 URL 有所不同。

| 云   | Redis 的 Dns 后缀            |
|---------|---------------------------------|
| 公共  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| 德国 | *.redis.cache.cloudapi.de       |
| 中国   | *.redis.cache.chinacloudapi.cn  |

若要深入了解通过其他云使用 Azure Redis 缓存的注意事项，请参阅以下链接。

- [Azure 政府数据库 - Azure Redis 缓存](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure 中国世纪互联云 - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

有关通过 PowerShell 在 Azure 政府云、Azure 中国世纪互联云和 Microsoft Azure 德国中使用 Azure Cache for Redis 的信息，请参阅[如何连接到其他云 - Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)。

## <a name="next-steps"></a>后续步骤

了解其他 [Azure Cache for Redis 常见问题解答](cache-faq.md)。
