---
title: 在 Azure Cosmos DB 中缩放吞吐量
description: 本文介绍 Azure Cosmos DB 如何在预配 Azure Cosmos 帐户的不同区域之间缩放吞吐量。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873499"
---
# <a name="globally-scale-provisioned-throughput"></a>全局缩放预配的吞吐量 

在 Azure Cosmos DB 中，预配的吞吐量以请求单位/秒（RU/s 或复数形式的 RU）表示。 RU 度量针对 Cosmos 容器执行的读取和写入操作的成本，如下图所示：

![请求单位](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

可以针对 Cosmos 容器或 Cosmos 数据库预配 RU。 在容器上预配的 ru 仅适用于在该容器上执行的操作。 针对数据库预配的 RU 在该数据库中的所有容器之间共享（具有专用分配 RU 的任何容器除外）

对于弹性缩放预配吞吐量，随时可以增加或减少预配的 RU/s。 有关详细信息，请参阅[如何预配吞吐量](set-throughput.md)和弹性 scale Cosmos 容器和数据库。 为实现全局缩放，你可以随时在 Cosmos 帐户中添加或删除区域。 有关详细信息，请参阅[在数据库帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在许多情况下，将多个区域与 Cosmos 帐户相关联非常重要，在世界各地实现低延迟和[高可用性](high-availability.md)。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>预配吞吐量如何跨区域分布

如果在 Cosmos 容器（或数据库）上设置 *"r"* ru，则 Cosmos DB 确保 *"r"* ru 在与 Cosmos 帐户关联的*每个*区域中可用。 每次向帐户添加新区域时，Cosmos DB 会自动在新添加的区域中设置 *"R"* ru。 可以保证针对 Cosmos 容器执行的操作获取每个区域中*的 "R"* 个 ru。 无法有选择地将 RU 分配给特定区域。 在 Cosmos 容器（或数据库）上预配的 RUs 在与 Cosmos 帐户关联的所有区域中预配。

假设使用 *"R"* ru 配置了 Cosmos 容器，并且存在与 Cosmos 帐户关联的 *"N"* 区域，则：

- 如果使用单个写入区域配置了 Cosmos 帐户，则容器中全局可用的总 ru 数 = *R* x *N*。

- 如果 Cosmos 帐户配置有多个写入区域，则容器上全局可用的总 ru 数 = *R* x （*N*+ 1）。 将自动预配其他*R* ru，以处理跨区域的更新冲突和防熵流量。

你选择的[一致性模型](consistency-levels.md)还会影响吞吐量。 对于更宽松的一致性级别（例如，*会话*、*一致前缀*和*最终*一致性），与更强的一致性级别（例如，*有限过期*或*强*一致性）相比，可以获得大约2倍的读取吞吐量。

## <a name="next-steps"></a>后续步骤

接下来，可以了解如何在容器或数据库上配置吞吐量：

* [获取和设置容器与数据库的吞吐量](set-throughput.md) 

