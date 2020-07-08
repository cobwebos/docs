---
title: Azure Cosmos DB 中的一致性级别
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 5ba3fc70a2ccfbe342e222dbb475658629ec60a4
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851692"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性级别

依赖于复制实现高可用性和/或低延迟的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商用分布式数据库都要求开发人员在两种极端一致性模型之间进行选择：非常一致和最终一致。  非常一致性模型的可线性化是数据可编程性的黄金标准。 但它导致的写入延迟代价较高（稳定状态下）且会降低可用性（遇到故障时）。 另一方面，最终一致性可提供更高的可用性和性能，但会加大应用程序的编程难度。

Azure Cosmos DB 通过某种选择范围来实现数据一致性，而不会走两种极端。 开发人员可以使用这些选项在高可用性和性能方面做出精确的选择和细致的取舍。

借助 Azure Cosmos DB，开发人员可以在一致性范围内从五个明确定义的一致性级别中进行选择。 这些级别包括“非常”、“有限过期”、“会话”、“一致前缀”和“最终”一致性。     这些级别具有明确的定义且非常直观，可用于特定的真实场景。 每个级别[在可用性与性能方面各有利弊](consistency-levels-tradeoffs.md)，并有 SLA 作为保障。 下图以范围区间形式显示了不同的一致性级别。

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="范围形式的一致性" border="false" :::

一致性级别与区域无关，无论在哪个区域为读取和写入操作提供服务、与 Azure Cosmos 帐户关联的区域数量是多少，或者帐户是配置了单个还是多个写入区域，都可以保证所有操作获得这种一致性。

## <a name="scope-of-the-read-consistency"></a>读取一致性的范围

读取一致性适用于逻辑分区范围内的单个读取操作。 读取操作可能由远程客户端或存储过程发出。

## <a name="configure-the-default-consistency-level"></a>配置默认一致性级别

随时都可在 Azure Cosmos DB 帐户中配置默认的一致性级别。 在帐户中配置的默认一致性级别适用于该帐户下的所有 Azure Cosmos 数据库和容器。 针对某个容器或数据库发出的所有读取和查询默认使用指定的一致性级别。 有关详细信息，请参阅如何[配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)。 你还可以覆盖特定请求的默认一致性级别，若要了解详细信息，请参阅如何[覆盖默认的一致性级别](how-to-manage-consistency.md?#override-the-default-consistency-level)一文。

## <a name="guarantees-associated-with-consistency-levels"></a>与一致性级别关联的保证

Azure Cosmos DB 提供的综合 SLA 可保证 100% 的读取请求满足所选任何一致性级别的一致性保证。 如果满足与一致性级别关联的所有一致性保证，则读取请求满足一致性 SLA。 [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 存储库中提供了 Azure Cosmos DB 中使用 TLA+ 规范语言精确定义的五个一致性级别。

下面描述了五个一致性级别的语义：

- **非常一致性**：非常一致性提供可线性化保证。 可线性化是指并发处理请求。 保证读取操作返回项的最新提交版本。 客户端永远不会看到未提交或不完整的写入。 始终保证用户读取最新确认的写入。

  下图以乐谱形式演示了非常一致性。 将数据写入 "美国西部 2" 区域后，当您从其他区域读取数据时，您将获得最新的值：

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="视频":::

- **受限停滞一致性**：保证读取操作遵循一致性前缀保证。 读取操作可能滞后于写入，最多 *"K"* 个版本（即 "更新"）或 *"T"* 时间间隔，以先达到的方式为准。 换言之，如果选择有限过期，则可以通过两种方式配置“过期”：

- 项的版本数 (*K*)
- 读取操作可以滞后于写入操作的时间间隔 (*T*)

有限过期在“过期窗口”之外提供全局整体顺序。 当客户端在接受写入的区域中执行读取操作时，有限过期一致性提供的保证与非常一致性的保证相同。

在过期窗口内，有限过期提供以下一致性保证：

- 对于单主帐户，同一区域中的客户端的一致性为“非常”
- 对于单主帐户，不同区域中的客户端的一致性为“一致前缀”
- 对于多主帐户，向单个区域进行写入的客户端的一致性为“一致前缀”
- 对于多主帐户，向不同区域进行写入的客户端的一致性为“最终”

  受限过期通常由需要较低写入延迟但需要全局订单总保证的全局分布式应用程序选择。 有限过期非常适合提供小组协作和共享、股票行情、发布-订阅/排队等功能的应用程序。下图以乐谱形式演示了有限过期一致性。 将数据写入 "美国西部 2" 区域后，"美国东部 2" 和 "澳大利亚东部" 区域会根据配置的最大延迟时间或最大操作读取写入值：

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="视频":::

- **会话一致性**：在单个客户端会话中，将保证读取操作遵循一致前缀、单调读取、单调写入、读取写入和读取后写入保证。 这采用单个“写入器”会话，或者多个写入器共享会话令牌。

在会话外部执行写入的客户端将获得以下保证：

- 对于单主帐户，同一区域中的客户端的一致性为“一致前缀”
- 对于单主帐户，不同区域中的客户端的一致性为“一致前缀”
- 对于多主帐户，向单个区域进行写入的客户端的一致性为“一致前缀”
- 对于多主帐户，向多个区域进行写入的客户端的一致性为“最终”

  会话一致性是适用于单个区域和全球分布式应用程序的最广泛使用的一致性级别。 它不仅提供与最终一致性相当的写入延迟、可用性和读取吞吐量，还提供一致性保证，从而满足了编写为在用户上下文中运行的应用程序的需求。 下图以乐谱形式演示了会话一致性。 "美国西部2作者" 和 "美国西部2读者" 正在使用同一个会话（会话 A），因此他们同时读取相同的数据。 而“澳大利亚东部”区域正在使用“会话 B”，因此，它会稍后才会接收到数据，但接收顺序与写入顺序相同。

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="视频":::

- 一致前缀：返回的更新包含所有更新的一些前缀，不带间隔。 一致前缀一致性级别保证读取操作永远不会看到无序写入。

如果写入是按 `A, B, C` 顺序执行的，则客户端会看到 `A`、`A,B` 或 `A,B,C`，但永远不会看到类似于 `A,C` 或 `B,A,C` 的失序排列情况。 一致前缀的延迟、可用性和读取吞吐量与最终一致性相当，但还会提供顺序保证，以适应顺序非常重要的方案的需求。 

下面是一致前缀的一致性保证：

- 对于单主帐户，同一区域中的客户端的一致性为“一致前缀”
- 对于单主帐户，不同区域中的客户端的一致性为“一致前缀”
- 对于多主帐户，向单个区域进行写入的客户端的一致性为“一致前缀”
- 对于多主帐户，向多个区域进行写入的客户端的一致性为“最终”

下图以乐谱形式演示了一致前缀一致性。 在所有区域中，读取操作永远不会看到无序写入：

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="视频":::

- **最终一致性**：不保证读取的顺序。 如果缺少任何进一步的写入，则副本最终会收敛。  
最终一致性是最弱的一致性形式，因为客户端可能会读取比之前读取的值还要旧的值。 最终一致性非常适合不需要任何顺序保证的应用程序。 示例包括推文、点赞或无回复评论的计数。 下图以乐谱形式演示了最终一致性。

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="视频":::

## <a name="additional-reading"></a>其他阅读材料

若要详细了解一致性的概念，请阅读以下文章：

- [Azure Cosmos DB 提供的五个一致性级别的高级 TLA+ 规范](https://github.com/Azure/azure-cosmos-tla)
- [通过 Doug Terry 通过棒球（视频）说明复制的数据一致性](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 借助棒球阐释复制数据一致性（白皮书）](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [弱一致性重复数据的会话保证](https://dl.acm.org/citation.cfm?id=383631)
- [现代分布式数据库系统设计中的一致性利弊：CAP 只是冰山一角](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)（实用部分仲裁的概率有限过期性 (PBS)）
- [最终一致性 - 再探](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Cosmos DB 中的一致性级别，请阅读以下文章：

- [为你的应用程序选择适当的一致性级别](consistency-levels-choosing.md)
- [跨 Azure Cosmos DB API 的一致性级别](consistency-levels-across-apis.md)
- [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
- [配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [替代默认一致性级别](how-to-manage-consistency.md#override-the-default-consistency-level)
