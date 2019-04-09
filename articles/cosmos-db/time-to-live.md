---
title: 利用生存时间使 Azure Cosmos DB 中的数据过期
description: 通过 TTL 功能，Microsoft Azure Cosmos DB 能够在一段时间后将文档自动从系统清除。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271265"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>生存时间 (TTL Azure Cosmos DB 中) 

与**生存时间**或 TTL、 Azure Cosmos DB 提供了在一段时间后从容器中自动删除项的功能。 默认情况下，可以在容器级别设置生存时间，并基于每个项替代该值。 在容器或项级别设置 TTL 后，Azure Cosmos DB 会在一段时间（自上次修改项的时间开始算起）后自动删除这些项。 配置的生存时间值以秒为单位。 当你配置 TTL 时，系统会自动删除基于 TTL 值，而无需显式颁发的客户端应用程序删除操作的过期的项。

## <a name="time-to-live-for-containers-and-items"></a>容器和项的生存时间

生存时间值被设置为秒，并将其解释为项目的上次修改时间的增量。 可以针对容器或容器中的项设置生存时间：

1. **容器的生存时间**（使用 `DefaultTimeToLive` 设置）：

   - 如果缺失（或设置为 null），则项不会自动过期。

   - 如果存在且值设置为"-1"，它等于无穷大，并且项不会到期默认情况下。

   - 如果存在且值设置为某个数字 *"n"* – 项目将会过期 *"n"* 秒后其上次修改时间。

2. **项的生存时间**（使用 `ttl` 设置）：

   - 仅当父容器的 `DefaultTimeToLive` 存在且不是设置为 null 时，此属性才适用。

   - 如果存在，它将替代父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>生存时间配置

* 如果将 TTL 设置为 *"n"* 容器，然后在该容器中的项将过期后*n*秒。  如果没有在同一容器中有其自己的时间来上线后，将设置为-1 （表示它们不会过期） 的项，或如果某些项已重写设置具有不同数量的生存时间，这些项目的过期时间根据其自己配置的 TTL 值。 

* 如果未针对某个容器设置 TTL，则此容器中的项的生存时间不起作用。 

* 如果某个容器的 TTL 设置为 -1，则此容器中生存时间设置为 n 的项将在 n 秒后过期，剩余的项不会过期。 

基于 TTL 删除项是免费的。 TTL 过期后删除项不会产生额外的费用（即，不会消耗额外的 RU）。

## <a name="next-steps"></a>后续步骤

通过以下文章了解如何配置生存时间：

* [如何配置生存时间](how-to-time-to-live.md)
