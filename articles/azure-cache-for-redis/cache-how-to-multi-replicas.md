---
title: '将副本添加到 Azure Cache for Redis (预览版) '
description: 了解如何向高级层 Azure Cache for Redis 实例添加更多副本
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91566782"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>将副本添加到 Azure Cache for Redis (预览版) 
本文介绍如何使用 Azure 门户设置包含附加副本的 Azure 缓存实例。

适用于 Redis 标准和高级层的 Azure 缓存通过在两个专用虚拟机 (Vm) 上托管每个缓存来提供冗余。 这些 Vm 配置为主和副本。 当主 VM 变得不可用时，副本将检测到，并将其作为新的主副本自动接管。 你现在可以将高级缓存中的副本数增加到3个，为你提供了总共四个虚拟机来支持缓存。 具有多个副本将导致比单个副本提供的复原更高。

> [!IMPORTANT]
> 此预览版在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款。](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>必备条件
* Azure 订阅- [免费创建一个](https://azure.microsoft.com/free/)

> [!NOTE]
> 此功能目前处于预览阶段，如果你感兴趣， [请](mailto:azurecache@microsoft.com) 联系我们。
>

## <a name="create-a-cache"></a>创建缓存
若要创建缓存，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com) ，然后选择 " **创建资源**"。
  
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis。&quot;:::
   
1. 在 &quot; **基本** 信息&quot; 页上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择资源组，或选择 &quot; **新建&quot; 并输入** 新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择 &quot; [高级&quot; 层](https://azure.microsoft.com/pricing/details/cache/) 缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在 &quot; **高级** &quot; 页上，选择 " **副本计数**"。
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="选择 Azure Cache for Redis。&quot;:::
   
1. 在 &quot; **基本** 信息&quot; 页上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择资源组，或选择 &quot; **新建&quot; 并输入** 新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择 &quot; [高级&quot; 层](https://azure.microsoft.com/pricing/details/cache/) 缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在 &quot; **高级** &quot; 页上，选择 ":::

1. 将其他选项保留为其默认设置。 

    > [!NOTE]
    > 多副本支持仅适用于当前非群集缓存。
    >

1. 单击“创建”。
   
    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。

    > [!NOTE]
    > 缓存中的副本数在创建后无法更改。
    >

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
