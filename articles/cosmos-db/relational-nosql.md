---
title: 了解 Azure Cosmos DB NoSQL 数据库与关系数据库之间的差别
description: 本文列举 NoSQL 数据库与关系数据库之间的差别
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896618"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>了解 NoSQL 数据库与关系数据库之间的差别

本文列举 NoSQL 数据库相比关系数据库的一些重要优势。 此外，将讨论使用 NoSQL 时存在的一些难点。 若要深入了解现存的不同数据存储，请查看有关[选择适当的数据存储](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)的文章。

## <a name="high-throughput"></a>高吞吐量

在维护关系数据库系统时，最突出的难题之一是，大多数关系引擎都会应用闩锁机制来实施严格的 [ACID 语义](https://en.wikipedia.org/wiki/ACID)。 这种做法的优点是可以确保数据库中数据的一致状态。 但是，它在并发性、延迟和可用性方面也会带来严重的弊端。 由于存在这种根本性的体系结构限制，在事务量较高时，可能需要手动将数据分片。 实现手动分片可能很耗时且非常棘手。

针对这种情况，[分布式数据库](https://en.wikipedia.org/wiki/Distributed_database)可提供更具伸缩性的解决方案。 但是，维护过程仍可能成本高昂且很耗时。 管理员可能需要执行额外的工作来确保系统分布性是透明的。 它们可能还必须考虑到数据库的"断开连接"性质。

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)通过跨所有 Azure 区域部署来简化这些挑战。 可以动态划分分区范围，使数据库随着应用程序的扩展而无缝增长，同时保持高可用性。 精细粒度的多租户功能以及严格受控的云原生资源监管功能有助于提供[令人惊叹的延迟保证](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency)和可预测的性能。 分区是完全托管式的，因此管理员无需编写代码或管理分区。

如果事务量达到极限（例如，每秒数千个事务），则应考虑采用分布式 NoSQL 数据库。 若要实现最大效率、简化维护和降低总拥有成本，请考虑采用 Azure Cosmos DB。

![后端](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>分层数据

在大量的用例中，数据库中的事务可能包含许多父子关系。 随着时间的推移，这些关系可能会急剧增加，最终变得难以管理。 [分层数据库](https://en.wikipedia.org/wiki/Hierarchical_database_model)形式在上世纪 80 年代即已出现，但由于存储效率低下而并未普及。 随着[Ted Codd的关系模型](https://en.wikipedia.org/wiki/Relational_model)成为几乎所有主流数据库管理系统使用的实际标准，它们也失去了吸引力。

但如今，文档式数据库的普及性已得到大幅提高。 这种数据库可被视为分层数据库范型的重新改造。由于解除了在磁盘上存储数据造成的成本忧虑，此类数据库让人随心所欲。 因此，相较于面向文档的新式方法，在关系数据库中维护许多复杂父子实体关系现在可被视为一种反模式。

[面向对象的设计](https://en.wikipedia.org/wiki/Object-oriented_design)的出现，以及将它与关系模型相结合时所发生的[阻抗失配](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)，在某些用例中也突出了关系数据库中的反模式。 因此可能会造成隐含的但往往很高昂的维护成本。 尽管 [ORM 方法](https://en.wikipedia.org/wiki/Object-relational_mapping)已有演进，可在一定程度上缓解此问题，但面向文档的数据库与面向对象的方法的融合度要好得多。 利用此方法，开发人员无需致力于开发 ORM 驱动程序，也无需定制语言特定的 [OO 数据库引擎](https://en.wikipedia.org/wiki/Object_database)。 如果数据包含许多父子关系和深度的层次级别，可以考虑使用 NoSQL 文档数据库，例如 [Azure Cosmos DB SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction)。

![OrderDetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>复杂的网络和关系

讽刺的是，在为深层且复杂的关系建模时，关系数据库的名称虽然带有“关系”二字，但还算不上最佳的解决方案。 原因在于，关系数据库中实际上并不存在实体之间的关系。 这些关系需要在运行时计算，而复杂的关系还需要运用笛卡尔联接才能使用查询进行映射。 因此，在计算方面，随着关系的增加，运算的开销将呈指数级增大。 在某些情况下，尝试管理此类实体的关系数据库将不可用。

在关系数据库出现期间，确实出现了各种形式的"网络"数据库，但与分层数据库一样，这些系统很难获得普及。 对其的采用裹足不前的原因是一时缺少用例，且存储效率低下。 当今，可将图形数据库引擎视为网络数据库范型的再生。 这些系统的主要好处是，关系在数据库中存储为"一流公民"。 因此，关系的遍历可在恒定的时间内完成，而不会在每次计算新的联接或叉积时增大时间和复杂性。

如果在数据库中维护复杂的关系网络，可以考虑使用图形数据库（例如 [Azure Cosmos DB Gremlin API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)）来管理此类数据。

![图形](./media/relational-or-nosql/graph.png)

Azure Cosmos DB 是一个多模型数据库服务，它为所有主要 NoSQL 模型类型（列系列、文档、图形和键-值）提供 API 投影。 [Gremlin（图形）](https://docs.microsoft.com/azure/cosmos-db/gremlin-support)和 SQL（核心）文档 API 层完全可互操作。 其优点是可以在编程级别切换不同的模型。 可以通过复杂的网络遍历以及建模为同一存储中的文档记录的事务来查询图形存储。

## <a name="fluid-schema"></a>流体架构

关系数据库的另一个特殊特征是需要在设计时定义架构。 这在引用完整性和数据合规性方面带来了优势。 但是，随着应用程序的增大，这种特征也会带来限制。 为了应对各个逻辑独立的模型中的架构变化，共享相同的表或数据库定义可能会变得越来越复杂。 将架构转移到应用程序并按记录进行管理往往可让此类用例受益。 这要求数据库具有"不可知的架构"，并允许记录在包含的数据方面"自我描述"。

如果你正在管理其结构不断频繁变化的数据（尤其是当事务可能来自外部源，而在这些源中很难对整个数据库强制实施合规性时），可以考虑使用托管的 NoSQL 数据库服务（例如 Azure Cosmos DB）来实施架构不可知性更高的方法。

## <a name="microservices"></a>微服务

近年来，[微服务](https://en.wikipedia.org/wiki/Microservices)模式已得到长足发展。 此模式根植于[面向服务的体系结构](https://en.wikipedia.org/wiki/Service-oriented_architecture)。 在这些新式微服务体系结构中，数据传输的事实标准是 [JSON](https://en.wikipedia.org/wiki/JSON)，而 JSON 也正好是绝大多数面向文档的 NoSQL 数据库的存储媒介。 JSON 大大提高了 NoSQL 文档存储的无缝吻合性，可在复杂的微服务实施方案中实现持久性和同步（通过[事件寻源模式](https://en.wikipedia.org/wiki/Event-driven_architecture)）。 在这些体系结构中维护较传统的关系数据库可能要复杂得多。 这是因为，在 API 之间维护状态和同步需要更多的转换。 具体而言，相比 NoSQL 数据库，Azure Cosmos DB 提供许多功能来进一步提高基于 JSON 的微服务体系结构的无缝吻合度：

* 丰富的纯 JSON 数据类型
* 内置于数据库中的 JavaScript 引擎和[查询 API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api)。
* 可供客户端订阅的先进[更改源](https://docs.microsoft.com/azure/cosmos-db/change-feed)，对容器进行修改后可以发出通知。

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 数据库存在的一些难题

尽管实施 NoSQL 数据库可以获得一些明显的优势，但同时也要考虑到它存在的一些难题。 使用关系模型时，可能达不到相同程度的效果：

* 事务中包含许多关系，而这些关系指向同一实体。
* 事务要求对整个数据集实现强一致性。

对于第一个难题，NoSQL 数据库的经验法则通常是反规范化，而根据前文所述，这可以在分布式系统中生成更有效的读取。 但是，在使用这种方法时，会遇到一些设计难题。 让我们举一个与一个类别和多个标签相关的产品示例：

![联接](./media/relational-or-nosql/many-joins.png)

NoSQL 文档数据库中的一个最佳实践方法是直接在"产品文档中"中取消类别名称和标记名称的规范化。 但是，为了保持类别、标签和产品同步，便于实现的设计选项增加了维护复杂性，因为数据在产品中的多个记录中重复，而不是在"一对多"中进行简单的更新关系和用于检索数据的联接。 

优势是反规范化记录中读取效率更高，并且随着概念上联接的实体数量的增加，效率进一步提高。 但是，正如读取效率随着在非规范化记录中联接的实体数量的增加而增加一样，保持实体同步的维护复杂性也也在增加。缓解这种权衡的一个方法是创建一个[混合数据模型](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)。

尽管 NoSQL 数据库提供更高的灵活性来处理这些利弊，但灵活性的增高也可能会产生更多的设计决策。 请参阅我们的文章[如何使用真实示例为 Azure Cosmos DB 中的数据建模和分区](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)，其中介绍了[使反规范化的用户数据保持同步](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames)的方法，其中的用户不仅位于不同的分区中，而且还位于不同的容器中。

极少需要对整个数据集实现强一致性。 但是，如果存在这种需要，在分布式数据库中可能很难做到这一点。 为确保强一致性，需要在所有副本和区域之间同步数据，然后再允许客户端读取数据。 这可能会增大读取延迟。

同样，对于此处相关的各种利弊，Azure Cosmos DB 提供的灵活性高于关系数据库，但对于小规模的实施方案，使用此方法可能需要在设计时考虑到更多因素。 有关本主题的更多详细信息，请参阅有关[一致性、可用性和性能利弊](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)的文章。

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure Cosmos 帐户，以及了解其他概念：

* [如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [全球分销](distribute-data-globally.md)
* [一致性级别](consistency-levels.md)
* [使用 Azure Cosmos 容器和项](databases-containers-items.md)
* [Azure Cosmos 帐户的 VNET 服务终结点](vnet-service-endpoint.md)
* [Azure Cosmos 帐户的 IP 防火墙](firewall-support.md)
* [如何在 Azure Cosmos 帐户中添加和删除 Azure 区域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
