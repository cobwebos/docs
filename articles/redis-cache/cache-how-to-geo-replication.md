---
title: 如何为 Azure Redis 缓存配置异地复制功能 | Microsoft Docs
description: 了解如何跨地理区域复制 Azure Redis 缓存实例。
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911364"
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>如何为 Azure Redis 缓存配置异地复制功能

异地复制提供一种用于链接两个高级层 Azure Redis 缓存实例的机制。 一个缓存指定为主链接缓存，另一个缓存指定为辅助链接缓存。 辅助链接缓存将变为只读，写入主缓存的数据将复制到辅助链接缓存。 此功能可用于跨 Azure 区域复制缓存。 本文提供了为高级层 Azure Redis 缓存实例配置异地复制的指南。

## <a name="geo-replication-prerequisites"></a>异地复制先决条件

若要在两个缓存之间配置异地复制，必须满足以下先决条件：

- 这两个缓存必须是[高级层](cache-premium-tier-intro.md)缓存。
- 这两个缓存必须在同一 Azure 订阅中。
- 辅助链接缓存必须是相同的定价层或比主链接缓存更高的定价层。
- 如果主链接缓存已启用群集，辅助链接缓存必须启用分片数量与主链接缓存相同的群集。
- 两个缓存都必须已创建且都处于运行状态。
- 任一缓存都不能启用暂留。
- 支持在同一 VNET 中的缓存间进行异地复制。 也支持不同 VNET 中的缓存间异地复制，只要两个 VNET 都以这样一种方式进行配置，即最终 VNET 中的资源可以通过 TCP 连接到达彼此。

完成异地复制配置后，链接缓存对会有以下限制：

- 辅助链接缓存为只读状态；这意味着只能从中读取，但不能向其写入任何数据。 
- 添加链接前辅助链接缓存中的任何数据都会被删除。 但如果随后删除了异地复制，复制的数据则会保留在辅助链接缓存中。
- 如果缓存启用了群集，则无法在任一缓存上启动[缩放操作](cache-how-to-scale.md)或[更改分片数](cache-how-to-premium-clustering.md)。
- 无法在任一缓存上启用暂存。
- 可通过任一缓存使用[导出](cache-how-to-import-export-data.md#export)，但只能[导入](cache-how-to-import-export-data.md#import)到主链接缓存。
- 除非删除异地复制链接，否则无法删除任一链接缓存或包含它们的资源组。 有关详细信息，请参阅[尝试删除链接缓存时为何操作会失败？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果两个缓存位于不同的区域中，当数据跨地区复制到辅助链接缓存时，将收取网络流出量费用。 有关详细信息，请参阅[跨 Azure 区域复制数据的费用是多少？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 如果主缓存（及其副本）停止运行，则没有针对辅助链接缓存的自动故障转移。 若要故障转移客户端应用程序，建议手动删除异地复制链接并将客户端应用程序指向曾是辅助链接缓存的缓存。 有关详细信息，请参阅[辅助链接缓存如何进行故障转移？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>添加异地复制链接

1. 若要将两个高级缓存链接在一起进行异地复制，请从作为主链接缓存的缓存“资源”菜单，单击“异地复制”，再从“异地复制”边栏选项卡，单击“添加缓存复制链接”。

    ![添加链接](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 从“兼容缓存”列表，单击所需的辅助缓存的名称。 如果列表中未显示所需缓存，请验证是否已满足所需辅助缓存的[异地复制先决条件](#geo-replication-prerequisites)。 若要按区域筛选缓存，单击地图中的所需区域，只显示“兼容缓存”列表中的缓存。

    ![异地复制兼容缓存](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    还可使用上下文菜单启动链接过程或查看辅助缓存的详细信息。

    ![异地复制上下文菜单](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 单击“链接”将两个缓存链接在一起并开始复制过程。

    ![链接缓存](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 可以在“异地复制”边栏选项卡上查看复制过程的进度。

    ![链接状态](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    还可以在主缓存和辅助缓存的“概述”边栏选项卡上查看链接状态。

    ![缓存状态](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    复制过程完成后，“链接状态”改为“成功”。

    ![缓存状态](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    链接过程中，主链接缓存仍然可用，但辅助链接缓存将不可用，直到链接过程完成为止。

## <a name="remove-a-geo-replication-link"></a>删除异地复制链接

1. 若要删除两个缓存之间的链接并停止异地复制，请在“异地复制”边栏选项卡中，单击“取消链接缓存”。
    
    ![取消链接缓存](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    取消链接过程完成后，辅助缓存可用于读取和写入。

>[!NOTE]
>在删除异地复制链接后，从主链接缓存复制的数据保留在辅助缓存中。
>
>

## <a name="geo-replication-faq"></a>异地复制常见问题解答

- [是否可以通过标准层或基本层缓存使用异地复制？](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [在链接或取消链接过程中是否可以使用缓存？](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [是否可以链接两个以上的缓存？](#can-i-link-more-than-two-caches-together)
- [是否可以链接来自不同 Azure 订阅的两个缓存？](#can-i-link-two-caches-from-different-azure-subscriptions)
- [是否可以链接不同大小的两个缓存？](#can-i-link-two-caches-with-different-sizes)
- [是否可以在启用群集时使用异地复制？](#can-i-use-geo-replication-with-clustering-enabled)
- [当缓存位于 VNET 中时是否可以使用异地复制？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [什么是 Redis 异地复制的复制计划？](#what-is-the-replication-schedule-for-redis-geo-replication)
- [异地复制需要多长时间？](#how-long-does-geo-replication-replication-take)
- [复制恢复点是否受保证？](#is-the-replication-recovery-point-guaranteed)
- [是否可以使用 PowerShell 或 Azure CLI 管理异地复制？](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [跨 Azure 区域复制数据的费用是多少？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [尝试删除链接缓存时为何操作会失败？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [应为辅助链接缓存选择哪个区域？](#what-region-should-i-use-for-my-secondary-linked-cache)
- [辅助链接缓存如何进行故障转移？](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>是否可以通过标准层或基本层缓存使用异地复制？

不可以，异地复制仅适用于高级层缓存。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>在链接或取消链接过程中是否可以使用缓存？

- 链接两个缓存进行异地复制时，主链接缓存仍然可用，但辅助链接缓存将不可用，直到链接过程完成为止。
- 当删除两个缓存间的异地复制链接后，两个缓存仍然可用。

### <a name="can-i-link-more-than-two-caches-together"></a>是否可以链接两个以上的缓存？

不可以，使用异地复制时，只能将两个缓存链接到一起。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>是否可以链接来自不同 Azure 订阅的两个缓存？

不可以，这两个缓存必须在同一 Azure 订阅中。

### <a name="can-i-link-two-caches-with-different-sizes"></a>是否可以链接不同大小的两个缓存？

可以，但辅助链接缓存必须大于主链接缓存。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>是否可以在启用群集时使用异地复制？

可以，但两个缓存的分片数必须相同。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>当缓存位于 VNET 中时是否可以使用异地复制？

可以，支持对 VNET 中的缓存进行异地复制。 

- 支持在同一 VNET 中的缓存间进行异地复制。
- 也支持不同 VNET 中的缓存间异地复制，只要两个 VNET 都以这样一种方式进行配置，即最终 VNET 中的资源可以通过 TCP 连接到达彼此。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>什么是 Redis 异地复制的复制计划？

复制不按特定计划进行，而是以连续和异步方式进行，即 对主缓存进行的所有写入都会即时地异步复制到辅助缓存。

### <a name="how-long-does-geo-replication-replication-take"></a>异地复制需要多长时间？

复制以递增、异步和连续方式进行，所用时间通常不会与区域间的延迟相差太多。 在特定情况下的特定时间，辅助缓存可能需要从主缓存执行数据的完全同步。 在这种情况下，复制时间取决于多种因素，如：主缓存上的负载、缓存计算机上的可用带宽、区域间延迟等。例如，基于某种测试，我们发现美国东部和美国西部区域中完整 53 GB 异地复制对的复制时间可能是介于 5 到 10 分钟之间的任何值。

### <a name="is-the-replication-recovery-point-guaranteed"></a>复制恢复点是否受保证？

当前，对于异地复制模式下的缓存，持久性和导入/导出功能处于禁用状态。 因此，在客户启动故障转移情况下，或在异地复制对之间的复制链接中断的情况下，辅助缓存会保留到该时间点为止从主缓存同步的内存中数据。 在这种情况下不提供任何恢复点保证。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>是否可以使用 PowerShell 或 Azure CLI管理异地复制？

当前只能使用 Azure 门户管理异地复制。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 区域复制数据的费用是多少？

使用异地复制时，主链接缓存中的数据会被复制到辅助链接缓存中。 如果两个链接缓存位于同一 Azure 区域，则此数据传输不产生任何费用。 如果两个链接缓存位于不同 Azure 区域，则异地复制数据传输费用就是将该数据复制到其他 Azure 区域的带宽成本。 有关详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>尝试删除链接缓存时为何操作会失败？

两个缓存链接到一起时，除非删除异地复制链接，否则无法删除任一链接缓存或包含它们的资源组。 如果尝试删除包含一个或两个链接缓存的资源组，则会删除该资源组中的其他资源，但该资源组保持 `deleting` 状态，而资源组中的任意链接缓存则保持 `running` 状态。 若要删除资源组及其链接缓存，请按照[删除异地复制链接](#remove-a-geo-replication-link)中所述断开异地复制链接。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>应为辅助链接缓存选择哪个区域？

一般而言，建议将缓存保存在应用程序所在的相同 Azure 区域，便于应用程序访问。 如果应用程序有主区域和备用区域，那么主缓存和辅助缓存应保存在这些相同区域中。 有关配对区域的详细信息，请参阅[最佳做法 – Azure 配对区域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>辅助链接缓存如何进行故障转移？

在异地复制的初始版本中，Azure Redis 缓存不支持跨 Azure 区域自动故障转移。 异地复制主要用于灾难恢复方案。 在灾难恢复方案中，客户需要在备份区域中以协同的方式提供完整的应用程序堆叠，而不是让独立的应用程序组件自行决定切换到其备份区域的时间。 这与 Redis 尤其相关。 Redis 的主要优点之一是，它是一个低延迟存储。 如果应用程序使用的 Redis 故障转移到不同的 Azure 区域，但计算机层不转移，增加的往返时间可能会对性能产生显著影响。 因此，我们希望能够避免因暂时可用性问题而进行的 Redis 自动故障转移。

目前，若要启动故障转移，需要删除 Azure 门户中的异地复制链接，然后将 Redis 客户端中的连接终结点从主链接缓存更改为（以前链接的）辅助缓存。 当两个缓存都解除关联时，副本再次变为常规的读取写入缓存并直接从 Redis 客户端接受请求。


## <a name="next-steps"></a>后续步骤

了解有关 [Azure Redis 缓存高级层](cache-premium-tier-intro.md)的更多信息。

