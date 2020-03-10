---
title: Azure Cosmos DB 中的一致性级别
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: b5d9df7a0afa9b4270f0eff643e083e5bccfceb8
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933649"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性级别

依赖于复制实现高可用性和/或低延迟的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商业可用分布式数据库要求开发人员在两个极端一致性模型之间进行选择：*高度*一致性和*最终*一致性。 可线性化或强一致性模型是数据可编程性的黄金标准。 但它增加了延迟（处于稳定状态）和降低可用性（故障期间）的价格。 另一方面，最终一致性提供更高的可用性和更好的性能，但会使应用程序变得很困难。 

Azure Cosmos DB 通过某种选择范围来实现数据一致性，而不会走两种极端。 强大的一致性和最终一致性在大范围的末尾，但有很多一致性选择。 开发人员可以使用这些选项在高可用性和性能方面做出精确的选择和精确的权衡。 

借助 Azure Cosmos DB，开发人员可以在一致性范围内从五个明确定义的一致性模型中进行选择。 从最强到更宽松，模型包括*强*、*有限过期*、*会话*、*一致前缀*和*最终*一致性。 这些模型具有良好的定义和直观，可用于特定的实际方案。 每个模型都提供[可用性和性能的折衷](consistency-levels-tradeoffs.md)，并由 sla 提供支持。 下图显示了各种不同的一致性级别。

![范围形式的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性级别与区域无关，并可保证所有操作的运行，而不管读取和写入的提供区域、与 Azure Cosmos 帐户关联的区域数量，或者你的帐户是否配置了单个或多个写入区域。

## <a name="scope-of-the-read-consistency"></a>读取一致性的范围

读取一致性适用于分区键范围或逻辑分区内的单个读取操作。 读取操作可能由远程客户端或存储过程发出。

## <a name="configure-the-default-consistency-level"></a>配置默认一致性级别

随时都可在 Azure Cosmos DB 帐户中配置默认的一致性级别。 帐户上配置的默认一致性级别适用于该帐户下的所有 Azure Cosmos 数据库和容器。 针对某个容器或数据库发出的所有读取和查询默认使用指定的一致性级别。 有关详细信息，请参阅如何[配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)。

## <a name="guarantees-associated-with-consistency-levels"></a>与一致性级别关联的保证

Azure Cosmos DB 提供的综合 SLA 可保证 100% 的读取请求满足所选任何一致性级别的一致性保证。 如果满足与一致性级别关联的所有一致性保证，则读取请求满足一致性 SLA。 [Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 存储库中提供了使用 TLA + 规范语言 Azure Cosmos DB 中五个一致性级别的精确定义。

下面描述了五个一致性级别的语义：

- **强**：强一致性提供可线性化保证。 可线性化是指并发处理请求。 保证读取操作返回项的最新提交版本。 客户端永远不会看到未提交或不完整的写入。 始终保证用户读取最新确认的写入。

  下图说明了与音符的高度一致性。 将数据写入 "美国东部" 区域后，当您从其他区域读取数据时，您将获得最新的值：

  ![video](media/consistency-levels/strong-consistency.gif)

- **有限过期**：保证读取遵循一致前缀保证。 读取操作可能滞后于写入，最多 *"K"* 个版本（即 "更新"）或 *"T"* 时间间隔。 换句话说，当您选择 "有限过期" 时，可以通过两种方式配置 "过期"： 

  * 项的版本（*K*）的数目
  * 读操作可能滞后于写入的时间间隔（*T*） 

  有限过期提供全局整体顺序，但在“过期窗口”中除外。 过期窗口内部和外部的区域中提供单调读取保证。 强一致性与受限过期所提供的语义相同。 过期窗口等于零。 有限过期也称为“延时可线性化”。 当客户端在接受写入的区域内执行读取操作时，有限过期一致性提供的保证与强一致性的保证相同。

  受限过期通常由需要较低写入延迟但需要全局订单总保证的全局分布式应用程序选择。 受限过期对于具有组协作和共享、股票行情、出版/排队等的应用程序非常有用。下图显示了与音符的有限失效一致性。 将数据写入 "美国东部" 区域后，"美国西部" 和 "澳大利亚东部" 区域会根据配置的最大延迟时间或最大操作读取写入值：

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **会话**：保证在单个客户端会话读取中遵守一致前缀（假设单个 "写入器" 会话）、单调读取、单调写入、读写和读写操作保证。 执行写入操作的会话之外的客户端将看到最终一致性。

  会话一致性是适用于单个区域和全球分布式应用程序的广泛使用的一致性级别。 它提供写入延迟、可用性和读取吞吐量，可与最终一致性一致，同时提供一致性保证，以满足在用户上下文中运行的应用程序的需求。 下图演示了与音符的会话一致性。 "美国西部" 区域和 "美国东部" 区域使用同一会话（会话 A），因此它们都同时读取数据。 尽管 "澳大利亚东部" 区域正在使用 "Session B"，但它会在以后接收数据，但其顺序与写入的顺序相同。

  ![video](media/consistency-levels/session-consistency.gif)

- **一致前缀**：返回的更新包含所有更新的前缀，无间隔。 一致前缀一致性级别保证读取永远不会看到无序写入。

  如果按 `A, B, C` 顺序执行写入，则客户端会看到 `A`、`A,B` 或 `A,B,C`，但不会看到 `A,C` 或 `B,A,C` 这样的混乱顺序。 一致前缀为写入延迟、可用性和读取吞吐量与最终一致性相关，还提供满足订单重要性的方案的顺序保证。 下图显示了与音符一致的一致性前缀。 在所有区域中，读取操作永远不会看到无序写入：

  ![video](media/consistency-levels/consistent-prefix.gif)

- **最终**：不会对读取进行排序。 如果缺少任何进一步的写入，则副本最终会收敛。  
最终一致性是最弱的一致性形式，因为客户端可以读取早于其读取的值。 最终一致性非常适合于应用程序不需要任何顺序保证。 示例包括篇以上、赞或非线程注释的计数。 下图演示了与音符之间的最终一致性。

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>其他阅读材料

若要详细了解一致性的概念，请阅读以下文章：

- [Azure Cosmos DB 提供的五个一致性级别的高级 TLA+ 规范](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 借助棒球阐释复制数据一致性（视频）](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 借助棒球阐释复制数据一致性（白皮书）](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [弱一致性重复数据的会话保证](https://dl.acm.org/citation.cfm?id=383631)
- [现代分布式数据库系统设计中的一致性平衡方案：CAP 只是冰山一角](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)（实用部分仲裁的概率有限过期性 (PBS)）
- [最终一致性 - 再探](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Cosmos DB 中的一致性级别，请阅读以下文章：

* [为你的应用程序选择适当的一致性级别](consistency-levels-choosing.md)
* [跨 Azure Cosmos DB API 的一致性级别](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [替代默认一致性级别](how-to-manage-consistency.md#override-the-default-consistency-level)

