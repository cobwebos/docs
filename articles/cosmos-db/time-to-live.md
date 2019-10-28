---
title: 利用生存时间使 Azure Cosmos DB 中的数据过期
description: 通过 TTL 功能，Microsoft Azure Cosmos DB 能够在一段时间后将文档自动从系统清除。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: f66508a4794b8009523cc2820efe0156b4a9e2f6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756844"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB 中的生存时间（TTL） 

使用**生存时间**或 TTL，Azure Cosmos DB 提供在特定时间段后从容器自动删除项的功能。 默认情况下，可以在容器级别设置生存时间，并基于每个项替代该值。 在容器或项级别设置 TTL 后，Azure Cosmos DB 会在一段时间（自上次修改项的时间开始算起）后自动删除这些项。 配置的生存时间值以秒为单位。 配置 TTL 时，系统将根据 TTL 值自动删除过期的项目，而不需要客户端应用程序显式发出的删除操作。

删除过期项是一项后台任务，该任务[使用请求单元，而](request-units.md)该任务是用户请求尚未使用的请求单位。 如果容器负载过大，并且没有请求单元留给维护任务，则可能会延迟过期。

## <a name="time-to-live-for-containers-and-items"></a>容器和项的生存时间

"生存时间" 值以秒为单位进行设置，并将其解释为上次修改项的时间的增量。 可以针对容器或容器中的项设置生存时间：

1. **容器的生存时间**（使用 `DefaultTimeToLive` 设置）：

   - 如果缺失（或设置为 null），则项不会自动过期。

   - 如果存在并且值设置为 "-1"，则它等于无限大，并且默认情况下不会过期。

   - 如果存在并且值设置为 *"n"* ，则项在其上次修改时间之后将在 *"n"* 秒后过期。

2. **项的生存时间**（使用 `ttl` 设置）：

   - 仅当父容器的 `DefaultTimeToLive` 存在且不是设置为 null 时，此属性才适用。

   - 如果存在，它将替代父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>生存时间配置

* 如果在容器上将 TTL 设置为 *"n"* ，则该容器中的项将在*n*秒后过期。  如果同一容器中的项有自己的生存时间，则将设置为-1 （指示它们未过期），或者，如果某些项已将时间设置为不同的值，则这些项将根据其自己配置的 TTL 值过期。 

* 如果未针对某个容器设置 TTL，则此容器中的项的生存时间不起作用。 

* 如果某个容器的 TTL 设置为 -1，则此容器中生存时间设置为 n 的项将在 n 秒后过期，剩余的项不会过期。 

基于 TTL 删除项是免费的。 TTL 过期后删除项不会产生额外的费用（即，不会消耗额外的 RU）。

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

在以下文章中了解如何配置生存时间：

* [如何配置生存时间](how-to-time-to-live.md)
