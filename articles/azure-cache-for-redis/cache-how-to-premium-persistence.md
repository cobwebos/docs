---
title: 配置数据暂留 - 高级 Azure Cache for Redis
description: 了解如何为高级层的 Azure Redis 缓存实例配置和管理数据暂留
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: fbfd384787d35317a4e45c4f91cf8a3ad4ba5a61
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000015"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>如何为高级 Azure Redis 缓存配置数据暂留
本文介绍如何通过 Azure 门户在高级 Azure Cache for Redis 实例中配置持久性。 Azure Redis 缓存具有不同的缓存产品/服务，从而在缓存大小和功能（包括群集、暂留和虚拟网络支持等高级层功能）的选择上具有灵活性。 

## <a name="what-is-data-persistence"></a>什么是数据暂留？
[Redis 暂留](https://redis.io/topics/persistence)可让你保留存储在 Redis 中的数据。 还可以获取快照并备份数据，以便在出现硬件故障时进行加载。 这相对于基本级别或标准级别是一项巨大优势，因为基本级别或标准级别将所有数据存储在内存中，在出现故障的情况下，如果缓存节点停机，则可能导致数据丢失。 

Azure Redis 缓存使用以下模型提供 Redis 暂留：

* **RDB 暂留** - 配置 RDB（Redis 数据库）暂留以后，Azure Redis 缓存按照可配置的备份频率，将 Azure Redis 缓存的快照以 Redis 二进制格式暂留在磁盘上。 如果发生了灾难性事件，导致主缓存和副缓存都无法使用，则会使用最新快照重新构造缓存。 详细了解 RDB 暂留的[优点](https://redis.io/topics/persistence#rdb-advantages)和[缺点](https://redis.io/topics/persistence#rdb-disadvantages)。
* **AOF 暂留** - 配置 AOF（仅追加文件）暂留后，Azure Redis 缓存将每个写入操作保存到日志，此日志每秒至少保存到 Microsoft Azure 存储帐户一次。 如果发生了灾难性事件，导致主缓存和副缓存都无法使用，则会使用存储的写入操作重新构造缓存。 详细了解 AOF 暂留的[优点](https://redis.io/topics/persistence#aof-advantages)和[缺点](https://redis.io/topics/persistence#aof-disadvantages)。

暂留将 Redis 数据写入你拥有和管理的 Azure 存储帐户。 可在缓存创建过程中通过“新建 Azure Redis 缓存”边栏选项卡进行配置，也可以在现有高级缓存的“资源”菜单上配置********。

> [!NOTE]
> 
> 保存数据时，Azure 存储会自动加密数据。 可以使用自己的密钥进行加密。 有关详细信息，请参阅 [Azure Key Vault 中的客户托管密钥](/azure/storage/common/storage-service-encryption)。
> 
> 

1. 若要创建高级缓存，请登录到 [Azure 门户](https://portal.azure.com) ，然后选择 " **创建资源**"。 除了在 Azure 门户中创建缓存以外，也可以使用 Resource Manager 模板、PowerShell 或 Azure CLI 创建。 有关创建 Azure Redis 缓存的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="创建资源。":::
   
2. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="创建资源。" 并配置设置。 
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **首个存储帐户** | 下拉，然后选择存储帐户。 | 此存储帐户必须与缓存处于相同区域，建议选择“高级存储”帐户，因为高级存储的吞吐量较高****。 | 
   | **第一个存储密钥** | 下拉，并选择要使用的**主密钥或****辅助密钥**。 | 如果重新生成了暂留帐户的存储密钥，必须从“存储密钥”**** 下拉列表中重新配置所需密钥。 | 
   | **第二个存储帐户** |  (可选) 下拉，然后选择要使用的 **主密钥** 或 **辅助密钥** 。 | 你可以选择配置其他存储帐户。 如果配置第二个存储帐户，写入副本缓存操作会写入到第二个存储帐户。 | 
   | **第二个存储密钥** |  (可选) 下拉，然后选择要使用的 **主密钥** 或 **辅助密钥** 。 | 如果重新生成了暂留帐户的存储密钥，必须从“存储密钥”**** 下拉列表中重新配置所需密钥。 | 

    启用 AOF 暂留后，写入缓存操作会保存到指定的存储帐户（如果配置了第二个存储帐户，还可指定该帐户）。 如果灾难性故障导致主缓存和副本缓存均无法使用，则使用存储的 AOF 日志重新生成缓存。

10. 选择页面底部的“下一步:标记”选项卡，或者单击“下一步:标记”按钮。

11. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。 

12. 选择“查看 + 创建”。 **** 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

13. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可在 Azure Cache for Redis 的“概述”页面上监视进度 ****  。 如果“状态”显示为“正在运行”，则表示该缓存可供使用。 ****   **** 

## <a name="persistence-faq"></a>保留常见问题
以下列表包含对 Azure Redis 缓存暂留相关常见问题的解答。

* [能否在此前已创建的缓存的基础上启用保留？](#can-i-enable-persistence-on-a-previously-created-cache)
* [是否可同时启用 AOF 暂留和 RDB 暂留？](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [应该选择哪个暂留模型？](#which-persistence-model-should-i-choose)
* [如果我缩放到不同大小并还原了缩放操作之前生成的备份，会发生什么情况？](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB 暂留
* [创建缓存后是否可更改 RDB 备份频率？](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [为何我的 RDB 备份频率为 60 分钟，而两次备份的间隔却超过 60 分钟？](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [进行新备份以后，旧的 RDB 备份会发生什么情况？](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 暂留
* [是否应使用第二个存储帐户？](#when-should-i-use-a-second-storage-account)
* [AOF 暂留是否影响缓存吞吐量、延迟或性能？](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [如何删除第二个存储帐户？](#how-can-i-remove-the-second-storage-account)
* [什么是重写？重写对缓存有何影响？](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [缩放启用 AOF 的缓存时会出现什么情况？](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [AOF 数据如何在存储中进行整理？](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>能否在此前已创建的缓存的基础上启用保留？
是的，可以在创建缓存时或者在现有高级缓存上配置 Redis 持久性。

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>是否可以同时启用 AOF 暂留和 RDB 暂留？

不可以，只能分别启用 RDB 或 AOF，不能同时启用二者。

### <a name="which-persistence-model-should-i-choose"></a>应该选择哪个暂留模型？

AOF 暂留将每个写入保存到日志，与 RDB 暂留相比，这对吞吐量有一些影响，因为 RDB 暂留基于配置的备份间隔保存备份，对性能产生的影响非常小。 如果主要目标是最大程度减少数据损失，并且可应付缓存吞吐量下降，请选择 AOF 暂留。 如果希望在缓存上保持最优吞吐量，但仍希望使用一种数据恢复机制，请选择 RDB 暂留。

* 详细了解 RDB 暂留的[优点](https://redis.io/topics/persistence#rdb-advantages)和[缺点](https://redis.io/topics/persistence#rdb-disadvantages)。
* 详细了解 AOF 暂留的[优点](https://redis.io/topics/persistence#aof-advantages)和[缺点](https://redis.io/topics/persistence#aof-disadvantages)。

有关使用 AOF 暂留时的性能详细信息，请参阅 [AOF 暂留是否影响缓存吞吐量、延迟或性能？](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>如果我缩放到不同大小并还原了缩放操作之前生成的备份，会发生什么情况？

对于 RDB 暂留和 AOF 暂留：

* 如果缩放到更大的大小，则没有任何影响。
* 如果缩放到更小的大小，并且自定义[数据库](cache-configure.md#databases)设置大于新大小的[数据库限制](cache-configure.md#databases)，则不会还原这些数据库中的数据。 有关详细信息，请参阅[在缩放过程中，自定义数据库设置是否会受影响？](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* 如果缩放到更小的大小，并且更小的大小空间不足，无法容纳上次备份的所有数据，则在还原过程中，通常会使用 [allkeys-lru](https://redis.io/topics/lru-cache) 逐出策略逐出密钥。

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>创建缓存后是否可更改 RDB 备份频率？
可以，可在“数据暂留”边栏选项卡上更改 RDB 暂留的备份频率****。 有关说明，请参阅“配置 Redis 暂留”。

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>为何我的 RDB 备份频率为 60 分钟，而两次备份的间隔却超过 60 分钟？
RDB 暂留备份频率间隔在先前备份过程已成功完成后才会开始。 如果备份频率为 60 分钟，而备份过程需要 15 分钟才能成功完成，则在上一次备份开始以后，要再过 75 分钟才会开始下一次备份。

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>进行新备份以后，旧的 RDB 备份会发生什么情况？
除最新备份外的所有 RDB 暂留备份会被自动删除。 这种删除可能不会即刻发生，但旧备份是不会无限期保留下去的。


### <a name="when-should-i-use-a-second-storage-account"></a>是否应使用第二个存储帐户？

如果认为缓存上的设置操作高于预期，则应将第二个存储帐户用于 AOF 暂留。  设置辅助存储帐户有助于确保缓存不会达到存储带宽限制。

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 暂留是否影响缓存吞吐量、延迟或性能？

缓存低于最大负载（CPU 和服务器负载皆低于 90%）时，AOF 暂留对性能产生约 15% - 20% 的影响。 缓存处于这些限制以内时不存在延迟问题。 但是，启用 AOF 时缓存将更快达到这些限制。

### <a name="how-can-i-remove-the-second-storage-account"></a>如何删除第二个存储帐户？

可通过将第二个存储帐户设置为与第一个存储帐户相同的方式来删除 AOF 暂留辅助存储帐户。 对于现有缓存，可从缓存的“资源菜单”访问“数据暂留”边栏选项卡********。 若要禁用 AOF 持久性，请单击 " **禁用**"。

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>什么是重写？重写对缓存有何影响？

在 AOF 文件足够大时，重写会自动处于缓存上的队列中。 重写使用创建当前数据集所需的最小操作集来重设 AOF 文件大小。 重写期间会很快达到性能限制，尤其是在处理大型数据集时。 AOF 文件越大，重写发生频率越低，但是如果发生则会需要较长时间。

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>缩放启用 AOF 的缓存时会出现什么情况？

如果缩放时的 AOF 文件非常大，则缩放操作所花时间应长于预期，因为在缩放结束后才重载文件。

有关缩放的详细信息，请参阅[如果我缩放到不同大小并还原了缩放操作之前生成的备份，会发生什么情况？](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>AOF 数据如何在存储中进行整理？

AOF 文件中存储的数据在每个节点分为多个页 Blob，以便提升将数据保存至存储的性能。 下表介绍每个定价层使用的页 Blob 数量：

| 高级层 | Blob |
|--------------|-------|
| P1           | 4 个/分片    |
| P2           | 8 个/分片    |
| P3           | 16 个/分片   |
| P4           | 20 个/分片   |

启用群集时，缓存中的每个分片具有自己的页 Blob 集，如上表所示。 例如，具有 3 个分片的 P2 缓存在 24 个页 Blob 之间（每个分片 8 个页 Blob，共 3 个分片）分配其 AOF 文件。

重写后，存储中存在 2 个 AOF 文件集。 重写在后台进行，并附加到第一个文件集，而重写期间发送至缓存的设置操作会附加到第二个文件集。 重写期间会暂时存储备份以防发生故障，但是备份在重写结束后会立即删除。


## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
