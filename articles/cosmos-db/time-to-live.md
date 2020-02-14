---
title: 利用生存时间使 Azure Cosmos DB 中的数据过期
description: 通过 TTL 功能，Microsoft Azure Cosmos DB 能够在一段时间后将文档自动从系统清除。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188721"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB 中的生存时间 (TTL) 

使用**生存时间**或 TTL，Azure Cosmos DB 提供在特定时间段后从容器自动删除项的功能。 默认情况下，可以在容器级别设置生存时间，并基于每个项替代该值。 在容器或项级别设置 TTL 后，Azure Cosmos DB 会在一段时间（自上次修改项的时间开始算起）后自动删除这些项。 配置的生存时间值以秒为单位。 配置 TTL 后，系统会基于 TTL 值自动删除已过期的项，不需要客户端应用程序显式发出的 delete 操作。

删除过期项是一项后台任务，该任务[使用请求单元，而](request-units.md)该任务是用户请求尚未使用的请求单位。 即使在 TTL 过期后，如果容器使用请求重载，且没有足够的 RU 可用，数据删除将会延迟。 如果有足够的 ru 可用于执行删除操作，则会删除数据。 尽管数据删除延迟，但在 TTL 到期后，任何查询（通过任何 API）都不会返回数据。

## <a name="time-to-live-for-containers-and-items"></a>容器和项的生存时间

生存时间值是以秒为单位设置的，解释为自上次修改项的时间的增量。 可以针对容器或容器中的项设置生存时间：

1. **容器的生存时间**（使用 `DefaultTimeToLive` 设置）：

   - 如果缺失（或设置为 null），则项不会自动过期。

   - 如果存在并且值设置为 "-1"，则它等于无限大，并且默认情况下不会过期。

   - 如果存在并且值设置为 *"n"* ，则项在其上次修改时间之后将在 *"n"* 秒后过期。

2. **项的生存时间**（使用 `ttl` 设置）：

   - 仅当父容器的 `DefaultTimeToLive` 存在且不是设置为 null 时，此属性才适用。

   - 如果存在，它将替代父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>生存时间配置

* 如果在容器上将 TTL 设置为 *"n"* ，则该容器中的项将在*n*秒后过期。  如果同一容器中的项有自身的生存时间且 TTL 设置为 -1（表示不会过期），或者某些项使用不同的数字替代了生存时间设置，则这些项会根据其自己的已配置 TTL 值过期。 

* 如果未针对某个容器设置 TTL，则此容器中的项的生存时间不起作用。 

* 如果某个容器的 TTL 设置为 -1，则此容器中生存时间设置为 n 的项将在 n 秒后过期，剩余的项不会过期。

## <a name="examples"></a>示例

本部分演示分配给容器和项的实时值与不同时间的一些示例：

### <a name="example-1"></a>示例 1

容器上的 TTL 设置为 null （DefaultTimeToLive = null）

|项上的 TTL| 结果|
|---|---|
|ttl = null|    TTL 处于禁用状态。 该项永不过期（默认值）。|
|ttl =-1   |TTL 处于禁用状态。 该项将永不过期。|
|ttl = 2000 |TTL 处于禁用状态。 该项将永不过期。|


### <a name="example-2"></a>示例 2

容器上的 TTL 设置为-1 （DefaultTimeToLive =-1）

|项上的 TTL| 结果|
|---|---|
|ttl = null |TTL 已启用。 该项永不过期（默认值）。|
|ttl =-1   |TTL 已启用。 该项将永不过期。|
|ttl = 2000 |TTL 已启用。 此项将在2000秒后过期。|


### <a name="example-3"></a>示例 3

容器上的 TTL 设置为1000（DefaultTimeToLive = 1000）

|项上的 TTL| 结果|
|---|---|
|ttl = null|    TTL 已启用。 此项将在1000秒后过期（默认值）。|
|ttl =-1   |TTL 已启用。 该项将永不过期。|
|ttl = 2000 |TTL 已启用。 此项将在2000秒后过期。|

## <a name="next-steps"></a>后续步骤

通过以下文章了解如何配置生存时间：

* [如何配置生存时间](how-to-time-to-live.md)
