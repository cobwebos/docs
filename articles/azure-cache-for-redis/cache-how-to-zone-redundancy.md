---
title: '为 Redis (预览版的 Azure Cache 启用区域冗余) '
description: 了解如何为高级层 Azure 缓存的 Redis 实例设置区域冗余
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 3c396d6d5b9da9a48e0d68a2d7d49561d6f688de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342675"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>为 Redis (预览版的 Azure Cache 启用区域冗余) 
本文介绍如何使用 Azure 门户配置区域冗余的 Azure 缓存实例。

适用于 Redis 标准和高级层的 Azure 缓存通过在两个专用虚拟机 (Vm) 上托管每个缓存来提供内置冗余。 即使这些 Vm 位于不同的 [Azure 容错域和更新域](/azure/virtual-machines/windows/manage-availability) 中，并且具有高可用性，它们也容易受到数据中心级故障的影响。 适用于 Redis 的 Azure 缓存还支持其高级层中的区域冗余。 区域冗余缓存在跨多个 [可用性区域](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures)的 vm 上运行。 它提供更高的复原能力和可用性。

> [!IMPORTANT]
> 此预览版在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款。](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>先决条件
* Azure 订阅- [免费创建一个](https://azure.microsoft.com/free/)

> [!NOTE]
> 此功能目前处于预览阶段，如果你感兴趣， [请](mailto:azurecache@microsoft.com) 联系我们。
>

## <a name="create-a-cache"></a>创建缓存
若要创建缓存，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com) ，然后选择 " **创建资源**"。
  
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="为 Redis 选择 "Azure 缓存"。":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
    | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **定价层** | 下拉菜单，然后选择 " [高级" 层](https://azure.microsoft.com/pricing/details/cache/) 缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
    | **副本计数** | 滑动以选择副本数。 | 默认值为 1。 |
    | **可用性区域** | 下拉，并选择要使用的区域。 | 缓存的 Vm 会尽可能均匀地分布在所选区域中。 例如，如果缓存有三个副本并使用两个区域，则每个区域中将有两个 Vm。 |
   
1. 选择高级层缓存后，将询问你是否启用 Redis 群集。 使 **群集** 保持为 *禁用状态*。 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="配置 Redis 群集。":::

    > [!NOTE]
    > 区域冗余支持仅适用于非群集和非异地复制的缓存。 此外，它不支持专用链接、缩放、数据暂留或导入/导出。
    >

1. 单击“创建”。 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="创建用于 Redis 的 Azure 缓存。":::
   
    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。

    > [!NOTE]
    > 创建缓存后，无法更改可用性区域。
    >

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
