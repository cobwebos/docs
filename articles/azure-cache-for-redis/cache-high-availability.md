---
title: 适用于 Redis 的 Azure 缓存的高可用性
description: 了解 Azure Cache for Redis 高可用性功能和选项
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: 145be11436eb4d0c4f6b892e5239ccacd838d780
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654108"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>适用于 Redis 的 Azure 缓存的高可用性

适用于 Redis 的 Azure 缓存提供内置的高可用性。 其高可用性体系结构的目标是确保托管的 Redis 实例正常运行，即使在计划内或计划外中断时， (Vm) 的基础虚拟机也会受到影响。 它比在单个 VM 上托管 Redis，提供的速率要大得多。

适用于 Redis 的 Azure Cache 通过使用多个虚拟机（称为 *节点*）来实现高可用性。 它会配置这些节点，以便在协调礼节中进行数据复制和故障转移。 它还会协调维护操作，例如 Redis software 修补。 "标准" 和 "高级" 级别提供各种高可用性选项：

| 选项 | 说明 | 可用性 | Standard | 高级 |
| ------------------- | ------- | ------- | :------: | :---: |
| [标准复制](#standard-replication)| 单个数据中心或可用性区域中双节点复制的配置 (AZ) ，具有自动故障转移 | 99.9% |✔|✔|
| [多个副本](#multiple-replicas) | 具有自动故障转移功能的一个或多个 AZs 中的多节点复制配置 | 99.95% (的区域冗余)  |-|✔|
| [区域冗余](#zone-redundancy) | 跨 AZs 的多节点复制配置，具有自动故障转移 | 99.95% (具有多个副本)  |-|✔|
| [异地复制](#geo-replication) | 两个区域中的链接缓存实例，具有用户控制的故障转移 | 单个区域的 99.9% ()  |-|✔|

## <a name="standard-replication"></a>标准复制

默认情况下，在 "标准" 或 "高级" 层中，用于 Redis 的 Azure 缓存在一对 Redis 服务器上运行。 这两个服务器托管在专用 Vm 上。 开源 Redis 只允许一个服务器处理数据写入请求。 此服务器是 *主* 节点，而另一个 *副本*。 预配服务器节点后，适用于 Redis 的 Azure 缓存可向其分配主角色和副本角色。 主节点通常负责维护写入以及从 Redis 客户端读取请求。 在写入操作时，它会向其内部内存提交新密钥和密钥更新，并立即回复客户端。 它以异步方式将操作转发给副本。

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="数据复制设置":::
   
>[!NOTE]
>通常，Redis 客户端会与 Redis 缓存中的主节点通信，以获取所有读取和写入请求。 某些 Redis 客户端可以配置为读取副本节点。
>
>

如果 Redis 缓存中的主节点不可用，则副本将自动升级为新的主节点。 此过程称为 *故障转移*。 副本将等待足够长的时间，然后才会考虑主节点快速恢复的情况。 发生故障转移时，适用于 Redis 的 Azure Cache 预配新的 VM，并将其作为副本节点加入到缓存中。 副本执行与主副本的完整数据同步，以使其具有其他缓存数据副本。

主节点可以作为计划内维护活动（如 Redis software 或操作系统更新）的一部分。 它还可以因为计划外的事件（如底层硬件、软件或网络故障）而停止工作。 [适用于 Redis 的 Azure 缓存的故障转移和修补](cache-failover.md) 提供有关 Redis 故障转移类型的详细说明。 用于 Redis 的 Azure 缓存将在其生存期内经历许多故障转移。 高可用性体系结构旨在使缓存中的这些更改在客户端上尽可能透明。

## <a name="multiple-replicas"></a>多个副本

>[!NOTE]
>此功能以预览版形式提供。
>
>

适用于 Redis 的 Azure 缓存允许高级层中的其他副本节点。 [多副本缓存](cache-how-to-multi-replicas.md)最多可以配置三个副本节点。 具有更多副本通常会提高复原能力，因为附加的节点会备份主节点。 即使有更多副本，Azure Cache for Redis 实例仍会受到数据中心或 AZ 服务中断的严重影响。 可以通过将多个副本与 [区域冗余](#zone-redundancy)结合使用来提高缓存的可用性。

## <a name="zone-redundancy"></a>区域冗余

>[!NOTE]
>此功能以预览版形式提供。
>
>

适用于 Redis 的 Azure 缓存支持高级层中的区域冗余配置。 [区域冗余缓存](cache-how-to-zone-redundancy.md)可以将其节点置于同一区域中的不同[Azure 可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)上。 它消除了数据中心或 AZ 停机作为单点故障，并提高了缓存的总体可用性。

下图说明了区域冗余配置：

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="数据复制设置":::
   
适用于 Redis 的 Azure 缓存以循环方式将区域中的节点分配给所选的 AZs。 它还确定哪个节点最初将用作主节点。

区域冗余缓存提供自动故障转移。 当当前主节点不可用时，其中一个副本将接管。 如果新的主节点位于不同的 AZ，则您的应用程序可能会遇到更高的缓存响应时间。 AZs 是地理上分隔的。 从 AZ 切换到另一 AZ 会改变应用程序和缓存的托管位置之间的物理距离。 此更改会影响应用程序到缓存的双程网络延迟。 对于大多数应用程序而言，额外滞后时间应在可接受的范围内。 建议你对应用程序进行测试，以确保它可以使用区域冗余缓存良好运行。

## <a name="geo-replication"></a>异地复制

异地复制主要用于灾难恢复。 它使你能够在不同的 Azure 区域中为 Redis 实例配置 Azure 缓存，以备份主缓存。 为[Redis 的 Azure Cache 设置异地复制](cache-how-to-geo-replication.md)提供了有关异地复制工作原理的详细说明。

## <a name="next-steps"></a>后续步骤

详细了解如何为 Redis 高可用性选项配置 Azure Cache。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
* [将副本添加到 Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [为 Redis 的 Azure 缓存启用区域冗余](cache-how-to-zone-redundancy.md)
* [为 Redis 的 Azure 缓存设置异地复制](cache-how-to-geo-replication.md)
