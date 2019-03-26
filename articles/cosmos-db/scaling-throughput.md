---
title: 在 Azure Cosmos DB 中缩放吞吐量
description: 本文介绍 Azure Cosmos DB 如何弹性缩放吞吐量
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407430"
---
# <a name="globally-scale-provisioned-throughput"></a>全局缩放预配的吞吐量 

在 Azure Cosmos DB 中，预配的吞吐量表示为请求单位/秒 （RU/秒或复数形式的 Ru）。 RU 度量针对 Cosmos 容器执行的读取和写入操作的成本，如下图所示：

![请求单位](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

可以针对 Cosmos 容器或 Cosmos 数据库预配 RU。 在容器上预配的 ru 数是专门为该容器上执行的操作提供。 针对数据库预配的 RU 在该数据库中的所有容器之间共享（具有专用分配 RU 的任何容器除外）

有关弹性缩放预配的吞吐量，可以增加或减少在任何时候预配的 RU/秒。 有关详细信息，请参阅[操作方法预配吞吐量](set-throughput.md)和弹性缩放 Cosmos 容器和数据库。 有关全局缩放吞吐量，可以添加或在任何时候从 Cosmos 帐户删除区域。 有关详细信息，请参阅[在数据库帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 将多个区域与 Cosmos 帐户相关联是重要在许多情况下-可实现低延迟和[高可用性](high-availability.md)世界各地。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>预配吞吐量如何跨区域分布

如果预配 *'R'* Ru Cosmos 容器 （或数据库），Cosmos DB 确保 *'R'* Ru 均位于*每个*与 Cosmos 帐户关联的区域。 每次将新区域添加到你的帐户，Cosmos DB 会自动预配 *'R'* 新添加的区域中的 Ru。 针对 Cosmos 容器执行的操作总是能获得 *'R'* 每个区域中的 Ru。 无法有选择地将 RU 分配给特定区域。 与 Cosmos 帐户关联的所有区域中预配 Cosmos 容器 （或数据库） 上预配的 ru 数。

假设 Cosmos 容器配置有 *'R'* Ru 还有 *'n'* 然后与 Cosmos 帐户相关联的区域：

- 如果使用单个写入区域，在容器上全局可用的总 Ru 配置 Cosmos 帐户 = *R* x *N*。

- 如果 Cosmos 帐户配置了多个写入区域，在容器上全局可用的总 Ru = *R* x (*N*+ 1)。 附加*R* Ru 自动预配的过程更新冲突和防平均信息量流量跨区域。

所选[一致性模型](consistency-levels.md)还会影响吞吐量。 对于相对较为宽松的一致性级别，可以获取约 2 倍的读取吞吐量 (例如，*会话*，*一致前缀*并*最终*一致性) 相比更强的一致性级别 (例如，*有限过期*或*强*一致性)。

## <a name="next-steps"></a>后续步骤

接下来可以学习如何在容器或数据库上配置的吞吐量：

* [获取和设置容器与数据库的吞吐量](set-throughput.md) 

