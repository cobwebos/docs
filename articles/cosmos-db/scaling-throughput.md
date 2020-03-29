---
title: 在 Azure Cosmos DB 中缩放吞吐量
description: 本文介绍 Azure Cosmos DB 如何跨预配 Azure Cosmos 帐户的不同区域缩放吞吐量。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873499"
---
# <a name="globally-scale-provisioned-throughput"></a>全局缩放预配的吞吐量 

在 Azure Cosmos DB 中，预配吞吐量以“请求单位数/秒 (RU/s 或复数形式 RUs)”表示。 RU 度量针对 Cosmos 容器执行的读取和写入操作的成本，如下图所示：

![请求单位](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

可以针对 Cosmos 容器或 Cosmos 数据库预配 RU。 针对容器预配的 RU 专门适用于针对该容器执行的操作。 针对数据库预配的 RU 在该数据库中的所有容器之间共享（具有专用分配 RU 的任何容器除外）

若要弹性缩放预配的吞吐量，随时可以增大或减小预配的 RU/秒。 有关详细信息，请参阅“[如何预配吞吐量](set-throughput.md)和弹性缩放 Cosmos 容器与数据库”。 对于全局缩放吞吐量，您可以随时从 Cosmos 帐户添加或删除区域。 有关详细信息，请参阅[在数据库帐户中添加/删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在许多情况下，将多个区域与 Cosmos 帐户关联非常重要 - 实现全球的低延迟和[高可用性](high-availability.md)。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>预配吞吐量如何跨区域分布

如果在 Cosmos 容器（或数据库）上预配 *"R"R，Cosmos* DB 可确保与 Cosmos 帐户关联的*每个*区域都有 *"R"R。* 每次向帐户添加新区域时，Cosmos DB 都会在新添加的区域中自动提供 *"R"R。* 对 Cosmos 容器执行的操作保证在每个区域获得 *"R"R。* 无法有选择地将 RU 分配给特定区域。 针对 Cosmos 容器（或数据库）预配的 RU 是在与 Cosmos 帐户关联的所有区域中预配的。

假设 Cosmos 容器配置了 *"R"R，* 并且存在与 Cosmos 帐户关联的 *"N"* 区域，则：

- 如果 Cosmos 帐户配置了单个写入区域，则容器上全局可用的总 R 机数 = *R* x *N*。

- 如果 Cosmos 帐户配置了多个写入区域，则容器上全局可用的总 R 数将 = *R* x *（N*+1）。 额外的*RR*自动预配，以处理跨区域的更新冲突和反熵流量。

所选的[一致性模型](consistency-levels.md)也会影响吞吐量。 与更强的一致性级别（例如，“有限过期”** 或“强”** 一致性）相比，更宽松的一致性级别（例如“会话”**、“一致前缀”** 和“最终”** 一致性）可以获得约 2 倍的读取吞吐量。

## <a name="next-steps"></a>后续步骤

接下来，可以了解如何在容器或数据库上配置吞吐量：

* [获取和设置容器与数据库的吞吐量](set-throughput.md) 

