---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553317"
---
| 资源 | 限制 |
| --- | --- |
| 缓存大小 |530 GB |
| 数据库 |64 |
| 最大连接的客户端 |40,000 |
| Azure 缓存的 Redis 副本，以实现高可用性 |第 |
| 启用群集的高级缓存中的分片数 |10 |

每个定价层的 Azure Redis 缓存限制和大小都不相同。 若要查看定价层及其关联的大小，请参阅[Azure 缓存的 Redis 定价](https://azure.microsoft.com/pricing/details/cache/)。

有关 Azure Redis 缓存配置限制的详细信息，请参阅[默认 Redis 服务器配置](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration)。

由于 Azure Redis 缓存实例的配置和管理是由 Microsoft 执行的，因此 Azure Redis 缓存并非支持所有 Redis 命令。 有关详细信息，请参阅 [Azure Redis 缓存不支持的 Redis 命令](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis)。

