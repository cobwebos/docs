---
title: Azure Cosmos DB 中各种一致性级别的可用性和性能权衡
description: Azure Cosmos DB 中各种一致性级别的可用性和性能利弊。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ee0dc1bec39bf95cbf4f3bf7ecea92b877a78b88
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113747"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>一致性、可用性和性能权衡 

依赖复制以实现高可用性和/或低延迟的分布式数据库必须做出权衡。 在读取一致性与可用性、延迟和吞吐量之间做出权衡。

Azure Cosmos DB 通过某种选择范围来实现数据一致性。 此方法包含多个选项，而不会走非常一致性和最终一致性这两种极端。 可以从一致性范畴的五个定义完善的模型中进行选择。 按最强到最弱的顺序，模型分别为：

- 非常
- 有限过期性
- 会话
- 一致前缀
- 最终

每个模型均提供可用性和性能权衡，并由全面的 SLA 提供保障。

## <a name="consistency-levels-and-latency"></a>一致性级别和延迟

- 所有一致性级别的读取延迟始终保证在第 99 百分位小于 10 毫秒。 此读取延迟由 SLA 提供保障。 平均（在第 50 百分位）读取延迟通常不超过 2 毫秒。 跨多个区域且使用非常一致性配置的 Azure Cosmos 帐户不在此保障内。

- 剩余一致性级别的写入延迟始终保证在第 99 百分位小于 10 毫秒。 此写入延迟由 SLA 提供保障。 平均（在第 50 百分位）写入延迟通常不超过 5 毫秒。

某些 Azure Cosmos 帐户可能有多个使用非常一致性配置的区域。 在这种情况下，写入延迟保证在第 99 百分位小于往返时间 (RTT) 的两倍加上 10 毫秒。 任意两个最远区域之间的 RTT 与你的 Azure Cosmos 帐户关联。 等同于与你的 Azure Cosmos 帐户关联的任意两个最远区域之间的 RTT。 此选项目前处于预览状态。

确切的 RTT 延迟取决于光速距离和 Azure 网络拓扑。 Azure 网络不会针对任意两个 Azure 区域之间的 RTT 提供任何延迟 SLA。 对于你的 Azure Cosmos 帐户，将在 Azure 门户中显示复制延迟。 可以使用 Azure 门户监视与你的帐户关联的各个区域之间的复制延迟。

## <a name="consistency-levels-and-throughput"></a>一致性级别和吞吐量

- 对于相同数量的请求单元，会话、一致前缀和最终一致性级别提供的读取吞吐量大约是非常一致性和有限过期一致性的 2 倍。

- 对于给定类型的写入操作（例如插入、替换、更新插入和删除），所有一致性级别为请求单元提供的写入吞吐量是相同的。

## <a name="consistency-levels-and-data-durability"></a>一致性级别和数据持续性

在全球分布式数据库环境中，当发生区域范围的服务中断时，一致性级别与数据持续性之间存在直接关系。 此表定义了当发生区域范围的服务中断时，一致性模型与数据持续性之间的关系。 请务必注意，在分布式系统中，由于 CAP 定理的存在，即使具有非常一致性，也不可能存在 RPO 和 RTO 为零的分布式数据库。 若要详细了解原因，请参阅  [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。

|**区域**|**复制模式**|**一致性级别**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|单主或多主数据库|任何一致性级别|< 240 分钟|<1 周|
|>1|单主数据库|会话、一致的前缀或最终|< 15 分钟|< 15 分钟|
|>1|单主数据库|有限过期|K & T*|< 15 分钟|
|>1|多主数据库|会话、一致的前缀或最终|< 15 分钟|0|
|>1|多主数据库|有限过期|K & T*|0|
|>1|单主或多主数据库|非常|0|< 15 分钟|

* K & T = 某个项的“K”版本（更新）的数目。 或“T”时间间隔。



## <a name="next-steps"></a>后续步骤

详细了解全局分布，以及分布式系统中的常规一致性利弊。 请参阅以下文章：

- [现代分布式数据库系统设计中的一致性利弊](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [高可用性](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
