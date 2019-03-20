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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991575"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>如何为 Azure Redis 缓存配置异地复制

“异地复制”提供一种用于链接两个高级层 Azure Redis 缓存实例的机制。 一个缓存会选择作为主链接的缓存，和另一个用作辅助链接缓存。 辅助链接缓存将变为只读，写入主缓存的数据将复制到辅助链接缓存。 此功能可用于跨 Azure 区域复制缓存。 本文提供了为高级层 Azure Redis 缓存实例配置异地复制的指南。

## <a name="geo-replication-prerequisites"></a>异地复制先决条件

若要在两个缓存之间配置异地复制，必须满足以下先决条件：

- 这两个缓存都[高级层](cache-premium-tier-intro.md)缓存。
- 这两个缓存位于同一 Azure 订阅。
- 辅助链接的缓存将缓存大小相同或比主链接缓存较大缓存大小。
- 已创建这两个缓存并处于运行状态。

使用异地复制不支持某些功能：

- 使用异地复制不支持暂留。
- 如果这两个缓存启用群集功能，并且具有相同数量的分片，支持群集。
- 支持在同一 VNET 中的缓存。
- 需要注意的问题与支持的不同 Vnet 中的缓存。 请参阅[可以在 VNET 中缓存使用异地复制？](#can-i-use-geo-replication-with-my-caches-in-a-vnet)有关详细信息。

完成异地复制配置后，链接缓存对会有以下限制：

- 辅助链接缓存为只读状态；这意味着只能从中读取，但不能向其写入任何数据。 
- 添加链接前辅助链接缓存中的任何数据都会被删除。 如果异地复制是更高版本但是，删除该复制的数据将保持在辅助链接缓存。
- 您不能[规模](cache-how-to-scale.md)任一缓存时缓存链接。
- 您不能[更改分片数](cache-how-to-premium-clustering.md)如果缓存已启用群集。
- 无法在任一缓存上启用暂存。
- 你可以[导出](cache-how-to-import-export-data.md#export)从任一缓存。
- 您不能[导入](cache-how-to-import-export-data.md#import)到辅助链接缓存。
- 无法删除链接的缓存或包含它们，直到取消链接缓存的资源组。 有关详细信息，请参阅[尝试删除链接缓存时为何操作会失败？](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- 如果缓存位于不同的区域，网络出口费用适用于在区域间移动数据。 有关详细信息，请参阅[跨 Azure 区域复制数据的费用是多少？](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- 主要和辅助链接缓存之间不会发生自动故障转移。 有关详细信息和如何故障转移的客户端应用程序的信息，请参阅[故障转移到辅助链接缓存如何工作？](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>添加异地复制链接

1. 若要将两个缓存链接在一起以便进行异地复制，第一个单击**异地复制**缓存的资源菜单中你想要将主链接缓存。 接下来，单击**添加缓存复制链接**从**异地复制**边栏选项卡。

    ![添加链接](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. 单击从您预期辅助缓存的名称**兼容缓存**列表。 如果辅助缓存未显示在列表中，确认[异地复制先决条件](#geo-replication-prerequisites)满足辅助缓存。 若要筛选的区域的缓存，请单击地图以显示中的这些缓存中的区域**兼容缓存**列表。

    ![异地复制兼容缓存](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    此外可以启动链接过程或通过使用上下文菜单中查看有关辅助缓存的详细信息。

    ![异地复制上下文菜单](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. 单击“链接”将两个缓存链接在一起并开始复制过程。

    ![链接缓存](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. 可以在“异地复制”边栏选项卡上查看复制过程的进度。

    ![链接状态](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    还可以在主缓存和辅助缓存的“概述”边栏选项卡上查看链接状态。

    ![缓存状态](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    复制过程完成后，“链接状态”改为“成功”。

    ![缓存状态](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    在链接过程，主链接的缓存仍然可供使用。 辅助链接的缓存不可用，直到链接过程完成。

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

- 链接时，主链接的缓存仍然可用，而链接过程完成。
- 链接时，辅助链接的缓存不可用，直到链接过程完成。
- 当取消链接，这两个缓存仍然可用，取消链接过程完成时。

### <a name="can-i-link-more-than-two-caches-together"></a>是否可以链接两个以上的缓存？

不可以，你仅可以一起链接两个缓存。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>是否可以链接来自不同 Azure 订阅的两个缓存？

不可以，这两个缓存必须在同一 Azure 订阅中。

### <a name="can-i-link-two-caches-with-different-sizes"></a>是否可以链接不同大小的两个缓存？

可以，但辅助链接缓存必须大于主链接缓存。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>是否可以在启用群集时使用异地复制？

可以，但两个缓存的分片数必须相同。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>当缓存位于 VNET 中时是否可以使用异地复制？

是的但必须注意几点支持 Vnet 中的缓存进行异地复制：

- 支持在同一 VNET 中的缓存间进行异地复制。
- 此外支持不同 Vnet 中的缓存间异地复制。
  - 如果 Vnet 位于同一区域，可以将其连接使用[VNET 对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)或[VPN 网关 VNET 到 VNET 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)。
  - 如果 Vnet 位于不同区域时，异地复制使用 VNET 对等互连不受支持，因为具有基本的内部负载均衡器的约束。 有关 VNET 对等互连约束的详细信息，请参阅[虚拟网络的对等互连的要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)。 建议的解决方案是使用 VNET 到 VNET VPN 网关连接。

使用[此 Azure 模板](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)，可以快速部署到 VNET 连接与 VNET 到 VNET VPN 网关连接的两个异地复制缓存。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>什么是 Redis 异地复制的复制计划？

复制是连续和异步，不会按特定计划。 到主数据库进行的所有写入在瞬间完成并以异步方式都复制到辅助数据库上。

### <a name="how-long-does-geo-replication-replication-take"></a>异地复制需要多长时间？

复制是增量、 异步和连续和跨区域所花费的时间没有从延迟太大区别。 在某些情况下，辅助缓存可能需要从主要执行完全同步的数据。 在这种情况下，复制时间是依赖于多种因素，如： 主缓存、 可用网络带宽和区域间延迟上的负载。 我们发现完整 53 GB 异地复制对的复制时间可能需要 5 到 10 分钟。

### <a name="is-the-replication-recovery-point-guaranteed"></a>复制恢复点是否受保证？

对于异地复制模式中的缓存，持久性处于禁用状态。 如果异地复制对的链接，如客户启动故障转移，辅助链接的缓存将使该点的时间之前其已同步的数据。 在这种情况下不保证任何恢复点。

若要获取恢复点，[导出](cache-how-to-import-export-data.md#export)从任一缓存。 你可以稍后[导入](cache-how-to-import-export-data.md#import)到主链接缓存。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>是否可以使用 PowerShell 或 Azure CLI管理异地复制？

是的可以使用 Azure 门户、 PowerShell 或 Azure CLI 管理异地复制。 有关详细信息，请参阅[PowerShell 文档](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache)或[Azure CLI 文档](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>跨 Azure 区域复制数据的费用是多少？

使用异地复制时，主链接缓存中的数据会被复制到辅助链接缓存中。 如果两个链接的缓存位于同一区域，不用于数据传输收费。 如果两个链接的缓存位于不同区域，数据传输费用就是在任一区域之间移动的数据的网络出口成本。 有关详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>尝试删除链接缓存时为何操作会失败？

链接之前删除异地复制链接时，无法删除异地复制的缓存和其资源组。 如果尝试删除包含一个或两个链接缓存的资源组，则会删除该资源组中的其他资源，但该资源组保持 `deleting` 状态，而资源组中的任意链接缓存则保持 `running` 状态。 若要完全删除资源组及其链接的缓存，请取消缓存链接中所述[删除异地复制链接](#remove-a-geo-replication-link)。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>应为辅助链接缓存选择哪个区域？

一般情况下，建议将缓存在应用程序访问其所在的同一 Azure 区域中存在。 对于使用不同的主要和回退区域的应用程序，建议在这些相同区域中存在的主要和辅助缓存。 有关配对区域的详细信息，请参阅[最佳做法 – Azure 配对区域](../best-practices-availability-paired-regions.md)。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>辅助链接缓存如何进行故障转移？

对于异地复制的缓存不支持跨 Azure 区域的自动故障转移。 在灾难恢复方案中，客户应显示整个应用程序堆栈以协调的方式在其备份区域中。 让单个应用程序组件决定何时切换到他们自己的备份可能会降低性能。 Redis 的主要优点之一是它是一个非常低延迟存储。 如果客户的主应用程序在不同于其缓存的区域中，增加的往返时间会对性能产生显著影响。 出于此原因，我们避免自动故障转移由于暂时可用性问题。

若要启动客户启动的故障转移，首先取消链接缓存。 然后，更改你的 Redis 客户端使用 （以前称为链接） 的辅助缓存的连接终结点。 取消链接两个缓存时，辅助缓存将再次变为常规的读写缓存，并接受直接来自 Redis 客户端的请求。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Redis 缓存高级层](cache-premium-tier-intro.md)的详细信息。
