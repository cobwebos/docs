---
title: 在 Azure Cosmos DB 中缩放吞吐量
description: 本文介绍 Azure Cosmos DB 如何弹性缩放吞吐量
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 7e3f6d053e9466f07e15b0c2c1092fece76c98a4
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160658"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中缩放吞吐量

在 Azure Cosmos DB 中，预配吞吐量以“请求单位数/秒 (RU/s)”表示。 RU 度量针对 Cosmos 容器执行的读取和写入操作的成本，如下图所示：

![请求单位](./media/scale-throughput/figure1.png)

可以针对 Cosmos 容器或 Cosmos 数据库预配 RU。 针对容器预配的 RU 专门适用于针对该容器执行的操作。 针对数据库预配的 RU 在该数据库中的所有容器之间共享（具有专用分配 RU 的任何容器除外）

若要弹性缩放吞吐量，随时可以增大或减小预配的 RU/秒。 有关详细信息，请参阅“[如何预配吞吐量](set-throughput.md)和弹性缩放 Cosmos 容器与数据库”。 若要全局缩放吞吐量，随时可以在 Cosmos 帐户中添加或删除区域。 有关详细信息，请参阅[在数据库帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在许多情况下，将多个区域与 Cosmos 帐户关联非常重要，这样可以在全球实现低延迟和[高可用性](high-availability.md)。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>预配吞吐量如何跨区域分布

如果针对 Cosmos 容器（或数据库）预配了 'R' 个 RU，则 Cosmos DB 可确保 'R' 个 RU 在与 Cosmos 帐户关联的每个区域中都可用。 每次将新区域添加到帐户时，Cosmos DB 都会自动在新添加的区域中预配 'R' 个 RU。 保证针对 Cosmos 容器执行的操作可以在每个区域中获取 'R' 个 RU。 无法有选择地将 RU 分配给特定区域。 针对 Cosmos 容器（或数据库）预配的 RU 是针对与 Cosmos 帐户关联的所有区域预配的。

假设为 Cosmos 容器配置了 'R' 个 RU，并且有 'N' 个区域与 Cosmos 帐户关联，那么：

- 如果为 Cosmos 帐户配置了单个写入区域，则容器中全局可用的 RU 总数 = R x N。

- 如果为 Cosmos 帐户配置了多个写入区域，则容器中全局可用的 RU 总数 = R x (N+1)。 将自动预配附加的 R 个 RU，以处理区域之间的更新冲突和反熵流量。

所选的[一致性模型](consistency-levels.md)也会影响吞吐量。 与有限过期一致性或非常一致性相比，会话、一致前缀和最终一致性的读取吞吐量大约要高出 2 倍。

## <a name="next-steps"></a>后续步骤

接下来，可借助以下文章了解如何配置吞吐量：

* [获取和设置容器与数据库的吞吐量](set-throughput.md) 

