---
title: 了解 Azure Cosmos DB NoSQL 和关系数据库之间的差异
description: 本文枚举 NoSQL 数据库和关系数据库之间的差异
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896618"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>了解 NoSQL 数据库和关系数据库之间的差异

本文将通过关系数据库枚举 NoSQL 数据库的一些主要优点。 我们还将讨论如何使用 NoSQL。 若要深入了解现有的不同数据存储，请参阅有关[选择正确的数据存储](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)的文章。

## <a name="high-throughput"></a>高吞吐量

维护关系数据库系统时最明显的难题之一是大多数关系引擎都应用锁和闩锁来强制实施严格的[ACID 语义](https://en.wikipedia.org/wiki/ACID)。 此方法的优点是确保数据库中的数据状态一致。 但是，并发性、延迟和可用性方面的折衷。 由于这些基础结构限制，高事务量可能会导致需要手动分片数据。 实现手动分片可能是一个耗时且痛苦的练习。

在这些情况下，[分布式数据库](https://en.wikipedia.org/wiki/Distributed_database)可以提供更可伸缩的解决方案。 不过，维护仍可能是一个成本高昂且耗时的练习。 管理员可能必须执行额外的工作，以确保系统的分布式性质是透明的。 它们还可能需要考虑数据库的 "断开连接" 特性。

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)通过在所有 Azure 区域中进行全球部署，简化了这些难题。 分区范围可以进行动态细分，使数据库与应用程序无缝增长，同时保持高可用性。 云本机资源管理可进行精细的多租户和严格控制，可简化[惊人的延迟保证](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency)和可预测的性能。 分区是完全托管的，因此管理员无需编写代码或管理分区。

如果事务量达到了极大的级别（例如，每秒多达数千个事务），则应考虑使用分布式 NoSQL 数据库。 请考虑 Azure Cosmos DB 以获得最高效率、易维护性并降低总拥有成本。

![后端](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>分层数据

数据库中的事务可以包含许多父子关系的情况有很多用例。 随着时间的推移，这些关系可能会急剧增长，并难以管理。 [层次结构数据库](https://en.wikipedia.org/wiki/Hierarchical_database_model)的窗体在 20-20 中出现，但由于存储效率低下而不受欢迎。 由于[李小明 Codd 的关系模型](https://en.wikipedia.org/wiki/Relational_model)成为了几乎所有主流数据库管理系统所使用的事实标准，因此它们也失去了吸引力。

但如今，文档样式数据库的普及已大幅增加。 这些数据库可能被视为层次结构数据库范例的变革，现在 uninhibited 了在磁盘上存储数据的成本。 因此，在关系数据库中维护很多复杂的父子实体关系现在可以视为与基于文档的新式方法的反模式。

[面向对象的设计](https://en.wikipedia.org/wiki/Object-oriented_design)的出现以及将其与关系模型组合时所发生的[阻抗不匹配](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)，还在关系数据库中突出显示某些用例的反模式。 隐藏但通常会出现大量的维护成本。 尽管[ORM 方法](https://en.wikipedia.org/wiki/Object-relational_mapping)已演变为部分缓解这一点，但面向文档的数据库仍可使用面向对象的方法更好地结合。 利用这种方法，开发人员不会被强制提交到 ORM 驱动程序或订购语言特定的[OO 数据库引擎](https://en.wikipedia.org/wiki/Object_database)。 如果数据包含许多父子关系和层次结构的深层层次结构，则可能需要考虑使用 NoSQL 文档数据库，如[AZURE COSMOS DB SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction)。

![OrderDetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>复杂的网络和关系

考虑到具有讽刺意味的名称，关系数据库提供的解决方案低于优化的解决方案，无法进行深层和复杂的关系建模。 原因在于，实体之间的关系实际上并不存在于关系数据库中。 它们需要在运行时进行计算，复杂关系需要笛卡尔联接才能允许使用查询进行映射。 因此，随着关系的增加，操作在计算方面变得更加昂贵。 在某些情况下，尝试管理此类实体的关系数据库将无法使用。

在出现关系数据库时，各种形式的 "网络" 数据库都是出现的，但是对于层次结构数据库，这些系统会受到困扰。 采用缓慢的原因是缺少用例，存储效率低下。 目前，可以将图形数据库引擎视为重新出现网络数据库范例。 这些系统的主要好处是在数据库中将关系存储为 "第一类公民"。 因此，遍历关系可在固定时间内完成，而不是随着每个新的联接或叉积增加时间复杂性。

如果在数据库中维护复杂的关系网络，则可能需要考虑使用图形数据库（如[Azure Cosmos DB GREMLIN API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) ）来管理此数据。

![图形](./media/relational-or-nosql/graph.png)

Azure Cosmos DB 是多模型数据库服务，它为所有主要 NoSQL 模型类型提供 API 投影;列系列、文档、图形和键-值。 [Gremlin （图形）](https://docs.microsoft.com/azure/cosmos-db/gremlin-support)和 SQL （内核）文档 API 层可完全互操作。 这对于在可编程性级别的不同模型之间进行切换具有一些好处。 可以通过复杂的网络遍历以及在同一存储中作为文档记录建模的事务来查询图形存储。

## <a name="fluid-schema"></a>流体架构

关系数据库的另一特定特征是需要在设计时定义架构。 这在引用完整性和数据符合性方面具有优势。 但是，随着应用程序的增长，此方法也会受到限制。 在逻辑上独立的模型中，对共享同一个表或数据库定义的架构所做的更改可能会在一段时间内变得复杂。 此类用例通常从要 devolved 到应用程序的架构受益，并按记录进行管理。 这要求数据库为 "架构不可知"，并允许记录在其中包含的数据为 "自描述"。

如果你正在管理其结构不断变化的数据，尤其是当事务可能来自外部源，而在这种情况下，很难在整个数据库中强制符合性，你可能想要考虑更多的架构不可知方法使用托管的 NoSQL 数据库服务，如 Azure Cosmos DB。

## <a name="microservices"></a>微服务

[微服务](https://en.wikipedia.org/wiki/Microservices)模式在最近几年中已大幅增加。 此模式在[面向服务的体系结构](https://en.wikipedia.org/wiki/Service-oriented_architecture)中具有其根。 这些新式微服务体系结构中的数据传输事实上的标准是[JSON](https://en.wikipedia.org/wiki/JSON)，这也是大多数面向文档的 NoSQL 数据库的存储介质。 这会使 NoSQL 文档存储在复杂的微服务实现中的持久性和同步（使用[事件源模式](https://en.wikipedia.org/wiki/Event-driven_architecture)）之间的更紧密。 更传统的关系数据库在这些体系结构中更复杂。 这是因为在 Api 之间状态和同步所需的转换量更大。 尤其 Azure Cosmos DB 具有许多功能，使其更适合基于 JSON 的微服务体系结构，而不是许多 NoSQL 数据库：

* 纯粹的 JSON 数据类型的选择
* 数据库中内置的 JavaScript 引擎和[查询 API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) 。
* 一种先进的[更改源](https://docs.microsoft.com/azure/cosmos-db/change-feed)，客户端可以订阅这些数据源以获取对容器进行修改的通知。

## <a name="some-challenges-with-nosql-databases"></a>NoSQL 数据库的一些挑战

虽然实现 NoSQL 数据库有一些明显的优势，但你可能还需要考虑一些挑战。 当使用关系模型时，这些可能不会出现相同的程度：

* 具有多个关系指向同一实体的事务。
* 要求跨整个数据集的强一致性的事务。

通过查看第一个挑战，NoSQL 数据库中的缩略规则通常是非规范化的，如前文所述，在分布式系统中生成更有效的读取。 但是，在这种方法中，有一些设计挑战。 我们来看一个与一个类别和多个标记相关的产品示例：

![联接](./media/relational-or-nosql/many-joins.png)

NoSQL 文档数据库中的最佳做法方法是直接在 "产品文档" 中非规范化类别名称和标记名称。 但是，为了使类别、标记和产品保持同步，有助于实现此目的的设计选项增加了维护复杂性，因为数据是在产品的多个记录中重复的，而不是 "一对多" 中的简单更新关系和用于检索数据的联接。 

权衡是在非规范化记录中进行读取的效率更高，随着概念上联接的实体数量的增加，它们的效率越来越高。 不过，正如在非规范化记录中增加的已联接实体数增加时的读取效率一样，使实体保持同步的维护复杂度也随之增加。缓解这种折衷的一种方法是创建[混合数据模型](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)。

虽然 NoSQL 数据库中有更多灵活性可用于处理这些权衡，但增加了灵活性还能产生更多的设计决策。 请参阅我们的文章[如何使用真实的示例对 Azure Cosmos DB 上的数据进行建模和分区](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)，其中包括一种将非[规范化用户数据保持同步](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames)的方法，其中用户不仅位于不同的分区中，而且在不同的容器中。

对于强一致性，很少需要在整个数据集中进行此设置。 但是，在需要这种情况下，它可能是分布式数据库中的挑战。 为了确保强一致性，在允许客户端读取数据之前，需要在所有副本和区域之间同步数据。 这会增加读取的延迟时间。

同样，Azure Cosmos DB 比在此处相关的各种折衷的关系数据库提供更大的灵活性，但对于小规模实现，这种方法可能会添加更多的设计注意事项。 有关此主题的更多详细信息，请参阅有关[一致性、可用性和性能的折衷方案](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)。

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure Cosmos 帐户和其他概念：

* [如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [全局分发](distribute-data-globally.md)
* [一致性级别](consistency-levels.md)
* [使用 Azure Cosmos 容器和项](databases-containers-items.md)
* [Azure Cosmos 帐户的 VNET 服务终结点](vnet-service-endpoint.md)
* [Azure Cosmos 帐户的 IP 防火墙](firewall-support.md)
* [如何在 Azure Cosmos 帐户中添加和删除 Azure 区域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
