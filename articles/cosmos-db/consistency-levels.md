---
title: Azure Cosmos DB 中的一致性级别 | Microsoft Docs
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
keywords: 最终一致性, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2611c25764503551c4da918d06bcaabe315cbf7c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963075"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性级别

依赖于高可用性、低延迟或两者的复制的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商用分布式数据库都要求开发人员在两种极端一致性模型之间进行选择：非常一致和最终一致。 尽管 [可线性化](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)或非常一致性模型是数据可编程性的黄金标准，但该模型导致的延迟代价太高（稳定状态下）且会降低可用性（在故障期间）。 另一方面，最终一致性可提供更高的可用性和性能，但应用程序编程难度很大。

Cosmos DB 通过某种选择范围来实现数据一致性，而不会走两种极端。 尽管非常一致性和最终一致性处于该范围的两个极端，但在整个范围中，还有很多一致性选项。 这些一致性选项使开发人员能够做出精确的选择，并在高可用性或性能方面做出细致的取舍。 Cosmos DB 使开发人员能够从一致性范围中五个妥善定义的一致性模型（最强到最弱）之间进行选择 – **非常**、**有限过期**、**会话**、**一致前缀**和**最终**一致性。 这些一致性模型中的每一种都具有明确的定义并直观呈现，可用于特定的真实场景。 五种一致性模型中的每一种在[可用性与性能方面都进行了权衡](consistency-levels-tradeoffs.md)，并有全面的 SLA 作为保障。

![范围形式的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性级别与区域无关。 不管以下属性为何，都能保证 Cosmos DB 帐户的所有读取操作的一致性级别：

- 为读取和写入操作提供服务的区域
- 与 Cosmos 帐户关联的区域数量
- 为帐户配置了一个还是多个写入区域

## <a name="scope-of-the-read-consistency"></a>读取一致性的范围

读取一致性应用到分区键范围（即逻辑分区）内的单个读取操作。 读取操作可能由远程客户端或存储过程发出。

## <a name="configuring-the-default-consistency-level"></a>配置默认的一致性级别

随时可以在 Cosmos DB 帐户中配置**默认的一致性级别**。 在帐户中配置的默认一致性级别将应用到该帐户下的所有 Cosmos 数据库（和容器）。 针对某个容器或数据库发出的所有读取和查询默认使用指定的一致性级别。 有关详细信息，请参阅[配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)一文。

## <a name="guarantees-associated-with-consistency-levels"></a>与一致性级别关联的保证

Azure Cosmos DB 提供的综合 SLA 可保证 100%的读取请求满足所选任何一致性级别的一致性保证。 如果满足与一致性级别关联的所有一致性保证，则读取请求被视为已满足一致性 SLA。 [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 存储库中提供了 Cosmos DB 中五个一致性级别的、使用 [TLA+ 规范语言](http://lamport.azurewebsites.net/tla/tla.html)的精确定义。 五个一致性级别的语义如下所述：

- **Consistency level = "strong"**：非常一致性提供[可线性化](https://aphyr.com/posts/313-strong-consistency-models)保证，即保证读取操作返回项的最新提交版本。 客户端永远不会看到未提交或不完整的写入。 始终保证用户读取最新确认的写入。

- **Consistency level = "bounded staleness"**：保证读取操作遵循一致性前缀保证。 读取操作可以滞后于写入操作最多 K 个项版本（即“更新”）或“t”时间间隔。 如果选择有限过期，则可以通过两种方式配置“停滞”： 

  * 项的版本数 (K)，或
  * 读取操作可以滞后于写入操作的时间间隔 (t)。 

  有限过期提供全局整体顺序，但在“过期窗口”中除外。 “过期窗口”内部和外部的区域中提供单调读取保证。 非常一致性的语义与有限过期提供的语义相同，但“过期窗口”等于零。 有限过期也称为“延时可线性化”。 当客户端在接受写入的区域中执行读取操作时，有限过期提供的一致性保证等同于非常一致性。

- **Consistency level = "session"**：保证读取操作遵循一致前缀、单调读取、单调写入、读取-写入、和写入后读取保证。 会话一致性划归到客户端会话。

- **Consistency level = "consistent prefix"**：返回的更新包含所有更新的某些前缀，不带间隔。 一致前缀保证读取永远不会看到无序写入。

- **Consistency level = "eventual"**：不保证读取的顺序。 如果缺少任何进一步的写入，则副本最终会收敛。

## <a name="consistency-levels-explained-through-baseball"></a>借助棒球解释一致性级别

让我们以棒球比赛场景为例。如[借助棒球阐释复制数据一致性](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf)白皮书中所述，假设某个写入序列代表了棒球比赛的回合比分。 这场虚构的棒球比赛目前正处于第七局的中段（谚语“第七局伸懶腰时间”），主队以 2-5 领先。

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **本垒打次数** |
| - | - | - | - | - | - | - | - | - | - | - |
| **客队** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **主队** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Cosmos DB 容器保持客队和主队的本垒打总次数。 当比赛正在进行时，不同的读取保证可能会导致客户端读取不同的分数。 下表列出了使用每种（共五种）一致性保证读取客队和主队分数后可能返回的完整分数集。 客队的分数先列出，不同的可能返回值以逗号分隔。

| **一致性级别** | **分数** |
| - | - |
| **非常** | 2-5 |
| **有限过期** | 最多一个已过期的回合比分：2-3、2-4、2-5 |
| **会话** | <ul><li>写入者：2-5</li><li> 写入者以外的任何人：0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5</li><li>读取 1-3 后：1-3、1-4、1-5、2-3、2-4、2-5</li> |
| **一致前缀** | 0-0、0-1、1-1、1-2、1-3、2-3、2-4、2-5 |
| **最终** | 0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5 |

## <a name="additional-reading"></a>其他阅读材料

若要详细了解一致性的概念，请阅读以下文章：

- [Azure Cosmos DB 提供的五个一致性级别的高级 TLA+ 规范](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 借助棒球阐释复制数据一致性（视频）](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 借助棒球阐释复制数据一致性（白皮书）](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [弱一致性重复数据的会话保证](https://dl.acm.org/citation.cfm?id=383631)
- [现代分布式数据库系统设计中的一致性平衡方案：CAP 只是冰山一角](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)（实用部分仲裁的概率有限过期性 (PBS)）
- [最终一致性 - 再探](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>后续步骤

若要详细了解 Cosmos DB 中的一致性级别，请阅读以下文章：

* [为应用程序选择适当的一致性级别](consistency-levels-choosing.md)
* [跨 Cosmos DB API 的一致性级别](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [如何配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [如何替代默认一致性级别](how-to-manage-consistency.md#override-the-default-consistency-level)

