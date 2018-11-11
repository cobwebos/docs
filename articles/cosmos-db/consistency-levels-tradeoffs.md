---
title: 各种一致性级别的可用性和性能利弊 | Microsoft Docs
description: Azure Cosmos DB 中各种一致性级别的可用性和性能利弊。
keywords: 一致性, 性能, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 061a7c223d0e1c6fa7384d7defe9f84f470236ce
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243822"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels"></a>各种一致性级别的可用性和性能利弊

依赖于高可用性、低延迟或两者的复制的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 Azure Cosmos DB 通过某种选择范围来实现数据一致性，而不是走非常一致性和最终一致性这两种极端。 Cosmos DB 使开发人员能够从一致性范围中五个妥善定义的一致性模型（最强到最弱）之间进行选择 – **非常**、**有限过期**、**会话**、**一致前缀**和**最终**一致性。 五种一致性模型中的每一种在可用性与性能方面都有明确的利弊，并有全面的 SLA 作为保障。

## <a name="consistency-levels-and-latency"></a>一致性级别和延迟

- 所有一致性级别的**读取延迟**始终保证在第 99 百分位小于 10 毫秒，并有 SLA 作为保障。 平均（在第 50 百分位）读取延迟通常不超过 2 毫秒。
- 除非 Cosmos 帐户跨多个区域并配置有非常一致性，否则，所有一致性级别的**写入延迟**始终保证在第 99 百分位小于 10 毫秒，并有 SLA 作为保障。 平均（在第 50 百分位）写入延迟通常不超过 5 毫秒。
- 对于跨多个区域且配置有非常一致性的 Cosmos 帐户（目前以预览版提供），**写入延迟**保证在第 99 百分位小于 (2 * RTT) + 10 毫秒。 与 Cosmos 帐户关联的任意两个区域的最远端之间的 RTT。 确切的 RTT 延迟取决于光速距离和确切的 Azure 网络拓扑。 Azure 网络不会针对任意两个 Azure 区域之间的 RTT 提供任何延迟 SLA。 Azure 门户中会显示 Cosmos 帐户的 Cosmos DB 复制延迟，让你监视与 Cosmos 帐户关联的不同区域之间的复制延迟。

## <a name="consistency-levels-and-throughput"></a>一致性级别和吞吐量

- 对于相同数量的 RU，会话、一致前缀和最终一致性提供的读取吞吐量大约是非常一致性和有限过期一致性的 2 倍。
- 对于给定类型的写入操作（例如插入、替换、更新插入、删除等），所有一致性级别为 RU 提供的写入吞吐量是相同的。

## <a name="consistency-levels-and-durability"></a>一致性级别和持久性

在客户端确认写入操作之前，数据将由接受写入的区域中的副本仲裁持久提交。 此外，如果为容器配置了一致索引策略，则在客户端确认写入操作之前，副本仲裁还会同步更新、复制并持久提交索引。 

下表汇总了在跨多个区域的 Cosmos 帐户遇到区域性灾难时，潜在的数据丢失时间范围。

| **一致性级别** | **发生区域性灾难时潜在的数据丢失时间范围** |
| - | - |
| 非常 | 零 |
| 有限过期 | 限制为开发人员在 Cosmos 帐户中配置的“陈旧时间范围” |
| 会话 | 最多 5 秒 |
| 一致前缀 | 最多 5 秒 |
| 最终 | 最多 5 秒 |

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章来详细了解全局分布，以及分布式系统中的常规一致性利弊：

* [现代分布式数据库系统设计中的一致性利弊](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [高可用性](high-availability.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
