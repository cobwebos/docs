---
title: 如何为 Azure Redis 缓存配置异地复制 | Microsoft Docs
description: 了解如何跨地理区域复制 Azure Redis 缓存实例。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552289"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>如何为 Azure Redis 缓存配置异地复制

“异地复制”提供一种用于链接两个高级层 Azure Redis 缓存实例的机制。 一个缓存选作主链接缓存，另一个缓存指定为辅助链接缓存。 辅助链接缓存将变为只读，写入主缓存的数据将复制到辅助链接缓存。 此功能可用于跨 Azure 区域复制缓存。 本文提供了为高级层 Azure Redis 缓存实例配置异地复制的指南。

## <a name="geo-replication-prerequisites"></a>异地复制先决条件

若要在两个缓存之间配置异地复制，必须满足以下先决条件：

- 这两个缓存是[高级层](cache-premium-tier-intro.md)缓存。
- 这两个缓存在同一 Azure 订阅中。
- 辅助链接缓存的大小等于或大于主链接缓存的大小。
- 这两个缓存都已创建且处于运行状态。

异地复制不支持某些功能：

- 异地复制不支持持久性。
- 如果这两个缓存都启用了群集功能并且具有相同数目的分片，则支持群集。
- 支持同一 VNET 中的缓存。
- 也支持不同 VNET 中的缓存，但需要注意一些问题。 有关详细信息，请参阅[当缓存位于 VNET 中时是否可以使用异地复制？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)。

完成异地复制配置后，链接缓存对会有以下限制：

- 辅助链接缓存为只读状态；这意味着只能从中读取，但不能向其写入任何数据。 
- 添加链接前辅助链接缓存中的任何数据都会被删除。 但如果以后删除了异地复制，复制的数据则会保留在辅助链接缓存中。
- 链接缓存时无法[缩放](cache-how-to-scale.md)任一缓存。
- 如果缓存已启用群集功能，则无法[更改分片数目](cache-how-to-premium-clustering.md)。
- 无法在任一缓存上启用暂存。
- 可以从任一缓存[导出](cache-how-to-import-export-data.md#export)。
- 无法[导入](cache-how-to-import-export-data.md#import)到辅助链接缓存。
- 只有在取消链接缓存之后，才可以删除任一链接缓存或包含它们的资源组。 有关详细信息，请参阅[尝试删除链接缓存时为何操作会失败？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果缓存位于不同的区域，网络传出费用将适用于在区域之间移动的数据。 有关详细信息，请参阅[跨 Azure 区域复制数据的费用是多少？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 主要和辅助链接缓存之间不会发生自动故障转移。 有关如何故障转移客户端应用程序的详细信息，请参阅[如何故障转移到辅助链接缓存？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>添加异地复制链接

1. 若要将两个缓存链接到一起以进行异地复制，请先在要用作主链接缓存的缓存的“资源”菜单中单击“异地复制”。 接下来，在“异地复制”边栏选项卡中单击“添加缓存复制链接”。

    ![添加链接](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 在“兼容的缓存”列表中，单击所需辅助缓存的名称。 如果列表中未显示辅助缓存，请确认是否符合辅助缓存的[异地复制先决条件](#geo-replication-prerequisites)。 若要按区域筛选缓存，请在地图中单击相应的区域，以便仅显示“兼容的缓存”列表中的缓存。

    ![异地复制兼容缓存](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    还可以使用上下文菜单启动链接过程或查看辅助缓存的详细信息。

    ![异地复制上下文菜单](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 单击“链接”将两个缓存链接在一起并开始复制过程。

    ![链接缓存](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 可以在“异地复制”边栏选项卡上查看复制过程的进度。

    ![链接状态](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    还可以在主缓存和辅助缓存的“概述”边栏选项卡上查看链接状态。

    ![缓存状态](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    复制过程完成后，“链接状态”改为“成功”。

    ![缓存状态](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    在链接过程中，主链接缓存仍然可用。 在链接过程完成之前，辅助链接缓存将不可用。

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

- 链接时，主链接缓存自始至终保持可用。
- 链接时，在链接过程完成之前，辅助链接缓存将不可用。
- 取消链接时，这两个缓存自始至终保持可用。

### <a name="can-i-link-more-than-two-caches-together"></a>是否可以链接两个以上的缓存？

不可以，只能将两个缓存链接到一起。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>是否可以链接来自不同 Azure 订阅的两个缓存？

不可以，这两个缓存必须在同一 Azure 订阅中。

### <a name="can-i-link-two-caches-with-different-sizes"></a>是否可以链接不同大小的两个缓存？

可以，但辅助链接缓存必须大于主链接缓存。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>是否可以在启用群集时使用异地复制？

可以，但两个缓存的分片数必须相同。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>当缓存位于 VNET 中时是否可以使用异地复制？

可以，支持对 VNET 中的缓存进行异地复制，但需要注意以下问题：

- 支持在同一 VNET 中的缓存间进行异地复制。
- 也支持在不同 VNET 中的缓存之间进行异地复制。
  - 如果 VNET 位于同一区域，则可以使用 [VNET 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或 [VPN 网关 VNET 到 VNET 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)来连接 VNET。
  - 如果 VNET 位于不同的区域，则不支持使用 VNET 对等互连进行异地复制，因为基本内部负载均衡器存在约束。 有关 VNET 对等互连约束的详细信息，请参阅[虚拟网络 - 对等互连 - 要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)。 建议的解决方法是使用 VPN 网关 VNET 到 VNET 连接。

使用[此 Azure 模板](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)可以快速将两个异地复制的缓存部署到通过 VPN 网关 VNET 到 VNET 连接进行连接的 VNET 中。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>什么是 Redis 异地复制的复制计划？

复制是持续异步进行的，而不是按特定的计划进行。 针对主缓存的所有写入会即时异步复制到辅助缓存。

### <a name="how-long-does-geo-replication-replication-take"></a>异地复制需要多长时间？

复制是增量、异步且持续的，所需时间与区域间的延迟并无太大区别。 在某些情况下，辅助缓存可能需要对主缓存中的数据进行完全同步。 在这种情况下，复制时间取决于多个因素，例如：主缓存上的负载、可用网络带宽和区域间的延迟。 我们发现，在一个异地复制对之间完整复制 53 GB 的内容可能需要 5-10 分钟时间。

### <a name="is-the-replication-recovery-point-guaranteed"></a>复制恢复点是否受保证？

对于异地复制模式下的缓存，会禁用持久性。 如果取消链接异地复制对（例如，客户发起了故障转移），则辅助缓存会保留在该时间点之前的同步数据。 在此类情况下，不提供恢复点保证。

若要获取某个恢复点，请从任一缓存[导出](cache-how-to-import-export-data.md#export)。 以后可以[导入](cache-how-to-import-export-data.md#import)到主链接缓存。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>是否可以使用 PowerShell 或 Azure CLI管理异地复制？

是的，可以使用 Azure 门户、PowerShell 或 Azure CLI 管理异地复制。 有关详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache)或 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 区域复制数据的费用是多少？

使用异地复制时，主链接缓存中的数据会被复制到辅助链接缓存中。 如果两个链接缓存位于同一区域，则数据传输不会产生费用。 如果两个链接缓存位于不同的区域，则数据传输费用是跨任一区域移动数据所产生的网络传出费用。 有关详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>尝试删除链接缓存时为何操作会失败？

在删除异地复制链接之前，无法删除已链接的异地复制缓存及其资源组。 如果尝试删除包含一个或两个链接缓存的资源组，则会删除该资源组中的其他资源，但该资源组保持 `deleting` 状态，而资源组中的任意链接缓存则保持 `running` 状态。 若要完全删除资源组及其包含的链接缓存，请根据[删除异地复制链接](#remove-a-geo-replication-link)中所述取消链接这些缓存。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>应为辅助链接缓存选择哪个区域？

一般而言，建议将缓存放置在应用程序所在的同一 Azure 区域，便于应用程序访问。 对于使用单独主要区域和故障回复区域的应用程序，建议将主缓存和辅助缓存放置在这些区域。 有关配对区域的详细信息，请参阅[最佳做法 – Azure 配对区域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>辅助链接缓存如何进行故障转移？

异地复制的缓存不支持跨 Azure 区域的自动故障转移。 在灾难恢复方案中，客户应该在其备份区域中以协调的方式启动整个应用程序堆栈。 让单个应用程序组件自行决定何时切换到其备份区域可能会给性能造成负面影响。 Redis 的主要优势之一是，它是一个延迟极低的存储。 如果客户的主要应用程序与其缓存位于不同的区域，则增大的往返时间可能会对性能产生显著的影响。 因此，我们应该避免自动故障转移，否则会造成暂时性的可用性问题。

若要启动客户发起的故障转移，请先取消链接缓存。 然后将 Redis 客户端更改为使用（以前链接的）辅助缓存的连接终结点。 取消链接两个缓存后，辅助缓存将再次成为常规的读取写入缓存，并直接从 Redis 客户端接受请求。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Redis 缓存高级层](cache-premium-tier-intro.md)的详细信息。
