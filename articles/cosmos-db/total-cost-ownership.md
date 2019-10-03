---
title: Azure Cosmos DB 的总拥有成本 (TCO)
description: 本文比较包含 IaaS 和本地数据库的 Azure Cosmos DB 的总拥有成本
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 911f5fa9985fc5e34f758dfb739d84521f91f5c0
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716936"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Azure Cosmos DB 的总拥有成本 (TCO)

Azure Cosmos DB 中设计了精细的多租户和资源调控。 这种设计可让 Azure Cosmos DB 以明显降低的成本运行，并帮助用户节省成本。 目前，Azure Cosmos DB 支持在一台计算机上运行 280 多个客户工作负荷（密度还是不断提升），以及在一个群集中运行数千个客户工作负荷。 它在一个群集中的不同计算机上以及在一个数据中心内的多个群集之间负载平衡客户工作负荷的副本。 若要了解详细信息[, 请参阅 Azure Cosmos DB:扩大全球分布式数据库的边界](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/)。 由于实施资源调控、多租户并与 Azure 基础结构的剩余部分原生集成，与 MongoDB、Cassandra 或者在 IaaS 上运行的其他 OSS NoSQL 相比，Azure Cosmos DB 的费用平均要便宜 4 到 6 倍；与本地运行的数据库引擎相比，则最多便宜 10 倍。 请参阅有关 [NoSQL 数据库云服务的总（非）拥有成本](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)的白皮书。

Apache Cassandra、MongoDB、HBase、引擎等 OSS NoSQL 数据库解决方案是为本地设计的。 作为托管服务提供时，它们相当于一个资源管理器模板，其中包含用于管理预配群集和监视支持的租户数据库。 OSS NoSQL 体系结构需要很大的运营开销，了解其专业知识可能比较困难且费用不菲。 另一方面，Azure Cosmos DB 是完全托管式的云服务，可让开发人员专注于业务创新，而不是管理和维护数据库基础结构。 

与云原生的数据库服务 Azure Cosmos DB 不同，设计和构建的 OSS NoSQL 数据库引擎不会根据基本的体系结构原则包含资源调控或精细的多租户功能。 Cassandra 和 MongoDB 等 OSS NoSQL 数据库引擎做出这种基本假设：运行这些引擎的虚拟机的所有资源可供这些引擎使用。 如果资源量降到特定的阈值以下，则其中的许多数据库引擎将无法正常运行。 例如，小型 VM 实例可用的资源根据供应商建议的配置而定，这意味着，大型 VM 的成本往往更高。 因此，无法托管 OSS NoSQL 或其他任何本地数据库引擎，并使用基于消耗量的计费模型（例如每秒请求数，或消耗的存储）来提供资源。

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Azure Cosmos DB 的总拥有成本 

Azure Cosmos DB 的无服务器预配模型消除了过度预配数据库基础结构的需求。 无需专业化的配置或许可即可提供 Azure Cosmos DB 资源。 因此，与 OSS NoSQL 数据库相比，Azure Cosmos DB 支持的应用程序的总拥有成本最多可以节省 70%。 有关部分实时示例，请参阅[客户用例](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc)。 Azure Cosmos DB 定价模型的其他优势包括：

* **价格体现高价值：** 市场分析师、客户和合作伙伴已经确认，与自行实施或通过其他供应商实施这些解决方案相比，Azure Cosmos DB 提供的所有功能的价值更高，且价格要低得多。 使用 Azure Cosmos DB 可以大幅简化全局分布、多主数据库、妥善定义的直观一致性模型和自动索引等数据库功能，且消除了任何复杂性、开销或停机时间。

* **无需进行 NoSQL DevOps 管理：** 使用 Azure Cosmos DB 时无需采用 DevOps 来管理部署以及执行维护、缩放或修补。 可以像对本地或云基础结构中托管的 OSS NoSQL 群集所做的那样来执行所有工作负荷。

![Azure Cosmos DB 拥有成本](./media/total-cost-ownership/tco.png)

* **可以弹性缩放：** 可以扩展和缩减 Azure Cosmos DB 吞吐量，从而在非高峰期降低拥有成本。 云基础结构中部署的 OSS NoSQL 群集提供有限的弹性，而本地部署在定义上不提供弹性。 在 Azure Cosmos DB 中，如果预配更高的吞吐量，则可以保证吞吐量呈线性扩展。 这一保证附带 SLA 的经济保障，在任何部署规模都能在 99% 的时间提供这种保证。

* **规模效益：** Azure Cosmos DB 等托管服务使用大量的节点来运行，原生与网络、存储和计算等资源集成。 由于 Azure Cosmos DB 的规模较大，通过标准化可以节省成本。

* **针对云进行优化：** Azure Cosmos DB 是从无到有设计出来的，提供精细的多租户控制和性能隔离。 这样，便可以跨群集与数据中心以最佳方式放置、执行和均衡数千个租户及其工作负荷。 相比之下，最新一代的 OSS NoSQL 数据库使用假设运行单个租户工作负荷的虚拟机在本地工作。 同样，这些数据库也不是设计为在最大程度上利用云提供商的基础结构和硬件。 例如，OSS NoSQL 数据库引擎不知道如何区分虚拟机关闭与例行映像升级，或者已三向复制高级磁盘这一事实。 它不能利用这些优势，因此无法向客户传达这些优势和成本节省。

* **按小时付费：** 需要在任意时间点缩放的大型工作负荷只需按小时付费。 应用程序中的工作负荷通常会在一年中的不同时间因查询的数据有所变化。 使用 Azure Cosmos DB 可按需进行扩展或缩减，只有所需的资源才产生费用。 不能搭配本地或 IaaS 托管的系统使用此模型，因为没有任何方法可以每隔一小时解除硬件。 在这种情况下，使用 Azure Cosmos DB 可能会将成本平均节省 10 到 14 倍。

* **获得大量免费功能：** 在 Azure Cosmos DB 中，写入工作负荷的成本要比替代型数据库服务便宜得多。 此外，Azure Cosmos DB 免费提供[自动索引](indexing-policies.md)、[生存时间 (TTL)](time-to-live.md)、[更改源](change-feed.md)等功能，而其他数据库服务通常对此收费。

* **对不同的工作负荷使用统一的货币：** 与替代型产品/服务不同，举例而言，在 Azure Cosmos DB 中，无需将工作负荷划分为读取和写入。 也不需要根据工作负荷类型预配吞吐量，即读取吞吐量与写入吞吐量。 在 Azure Cosmos DB 中，预配的吞吐量将使用统一的规范化货币按每秒请求单位 (RU) 数予以保留。Azure Cosmos DB 不强制要求对工作负荷分配优先级、执行容量规划，或者单独为每种容量付费。 借助这种方法可以在不同的操作和工作负荷类型之间轻松进行相同的 RU/秒换算。

* **无需预配 VM 即可缩放：** 大多数操作数据库要求使用大型虚拟机，以避免干扰性邻居并实现松散的资源调控（如果需要缩放）。 这给客户的前期成本承诺带来了负担。 使用 Azure Cosmos DB 可以从较小的规模着手，然后无缝扩展到大规模工作负荷，这不会造成任何停机，也不影响数据可用性。

* **可以尽最大限度利用预配的吞吐量：** 凭借 Azure Cosmos DB 的子核心多路复用优势，可在更高的程度充分利用预配的吞吐量，而 IaaS 托管选项或第三方产品/服务却做不到这一点。 与替代型解决方案相比，此方法可以节省大量的成本。

* **Azure Cosmos DB 与其他 Azure 服务的深度集成。** Azure Cosmos DB 原生与网络、计算、Azure Functions（无服务器）、Azure IoT 和其他 Azure 服务相集成。 这种集成可以提供最佳的性能、全球范围内的数据复制速度优势，并保证可靠。 第三方解决方案做不到这一点，或者通常以付费型高级版的形式提供此类功能。

* **默认情况下至少提供 10 到 20 个容错域，让你自动获得高可用性：** Azure Cosmos DB 支持跨容错域的工作负荷分布，这项功能对于高可用性至关重要。 它在全球的任何位置，在 99% 的时间内提供 99.999 的读写高可用性。 而你自行实现或通过第三方解决方案实现此类功能的成本则比较高。

* **自动免费获得所有企业功能。** Azure Cosmos DB 免费提供极其全面的合规性认证、安全性以及静态和动态加密（与我们的竞争产品相比）。 在全球任何位置都可自动获得区域可用性。 可让数据库跨越任意数目的 Azure 区域，并随时可以添加或删除区域。

* **通过预留容量将成本最多节省 65%：** 借助 Azure Cosmos DB [预留容量](cosmos-db-reserved-capacity.md)，可以通过预付为期一年或三年的 Azure Cosmos DB 资源费用来节省资金。 与采用一般定价相比，预付为期一年或三年的承诺费用可以享受 20-65% 的折扣，从而大幅节省成本。 对于任务关键型工作负荷，SLA 提供更有利的预配容量保障。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Cosmos DB 定价模型如何对客户而言更具经济效益](total-cost-ownership.md)
* 详细了解如何[进行开发和测试优化](optimize-dev-test.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](optimize-cost-queries.md)
* 详细了解如何[优化多区域 Cosmos 帐户的成本](optimize-cost-regions.md)
* 详细了解 [NoSQL 数据库云服务的总（非）拥有成本](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
