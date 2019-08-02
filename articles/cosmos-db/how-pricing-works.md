---
title: Azure Cosmos DB 的定价模型
description: 本文介绍 Azure Cosmos DB 的定价模型，以及该模型如何简化成本管理和成本计划。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: rimman
ms.openlocfilehash: 43a4b46199277da11d2820a11ec0801c0345e62b
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717602"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB 中的定价模型 

Azure Cosmos DB 的定价模型可简化成本管理和计划。 使用 Azure Cosmos DB 时，需要为预配的吞吐量和消耗的存储支付费用。

* **预配的吞吐量**：预配的吞吐量（也称为预留吞吐量）可保证提供任何规模的高性能。 指定所需的吞吐量 (RU/s)，Azure Cosmos DB 提供所需资源来保证配置的吞吐量。 按指定时间内最大的预配吞吐量以小时来收费。

   > [!NOTE]
   > 由于预配的吞吐量模型会将资源提供给容器或数据库，因此即使不运行任何工作负载，也要为预配的吞吐量付费。

* **使用的存储**：按指定时间内用于数据和索引的总存储量 (GBs) 来收取统一费用。

预配的吞吐量按每秒的[请求单位数](request-units.md)（即 RU/s）指定，允许从容器中读取数据或者将数据写入容器或数据库。 可以[在数据库或容器上预配吞吐量](set-throughput.md)。 根据工作负荷需求，可以随时调整吞吐量的大小。 Azure Cosmos DB 的定价是弹性的，且与数据库或容器上配置的吞吐量成正比。 最小吞吐量和存储值以及规模增量为所有客户（从小型容器到大型容器）提供全面的价格和弹性范围。 对于以 100 RU/s 为单位预配的、最少为 400 RU/s 的吞吐量和以 GB 消耗的存储，每个数据库或容器按小时计费。 不同于预配的吞吐量，存储按消耗进行计费。 也就是说，无需提前预留任何存储。 仅为所使用的存储付费。

有关详细信息，请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)和[了解 Azure Cosmos DB 帐单](understand-your-bill.md)。

Azure Cosmos DB 中的定价模型在所有 API 中都是一致的。 有关详细信息，请参阅 [Azure Cosmos DB 定价模型如何对客户而言更具经济效益](total-cost-ownership.md)。 数据库或容器需要最小吞吐量来确保 SLA，可以按每 100 RU/s 6 美元增加或减少预配的吞吐量。

目前，基于数据库和容器的吞吐量的最低价格是 24 美元/月（请参阅 [Azure Cosmos DB 定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)获取最新信息）。 如果工作负荷使用多个容器，那么可以通过使用数据库级别的吞吐量来优化成本，因为数据库级别的吞吐量使得数据库中的任意数量的容器可以在容器之间共享吞吐量。 下表总结了预配的吞吐量和不同实体的成本：

|**实体**  | **最小吞吐量和成本** |**规模增量和成本** |**预配范围** |
|---------|---------|---------|-------|
|数据库    | 400 RU/s（24 美元/月）    | 100 RU/s（6 美元/月）   |吞吐量预留给数据库，并由数据库中的容器共享 |
|容器     | 400 RU/s（24 美元/月）    | 100 RU/s（6 美元/月）  |吞吐量预留给特定容器 |

如上表所示，Azure Cosmos DB 的最低吞吐量最低价格为 24 美元/月。 如果从最低吞吐量开始，并随着时间逐步扩大以支持生产工作负荷，那么成本将平稳上升，即每月增加 6 美元。 Azure Cosmos DB 的定价模型是弹性的，按比例增加或减少时，价格会平稳地上升或下降。

## <a name="try-azure-cosmos-db-for-free"></a>免费试用 Azure Cosmos DB 

Azure Cosmos DB 免费为开发人员提供多个选项。 这些选项包括：

* **Azure 免费帐户**：Azure 提供[免费层级](https://azure.microsoft.com/free/)，即提供 200 美元的 Azure 额度（可在前 30 天内使用），以及 12 个月的限量免费服务。 有关详细信息，请参阅 [Azure 免费帐户](../billing/billing-avoid-charges-free-account.md)。 Azure Cosmos DB 是 Azure 免费帐户的一部分。 特别对于 Azure Cosmos DB，该免费帐户提供 5 GB 的存储空间和 400 RU 的全年预配吞吐量。 

* **免费试用 Azure Cosmos DB**：Azure Cosmos DB 通过使用“试用 Azure Cosmos DB 免费帐户”提供限时体验。 可以通过使用快速入门和教程创建 Azure Cosmos DB 帐户、创建数据库和集合，以及运行示例应用程序。 可以运行示例应用程序，而无需订阅 Azure 帐户或使用信用卡。 [免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 提供一个月的 Azure Cosmos DB 服务，并且可以任意次数更新帐户。

* **Azure Cosmos DB 模拟器**：为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 模拟器免费提供，并且具有对云服务的高保真度。 使用 Azure Cosmos DB 模拟器可在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 投入生产之前，可以在本地使用模拟器开发应用程序。 如果对模拟器的应用程序功能感到满意，可切换到云中的“使用 Azure Cosmos DB 帐户”，从而大幅节省成本。 有关模拟器的详细信息，请参阅[使用 Azure Cosmos DB 进行开发和测试](local-emulator.md)一文。

## <a name="pricing-with-reserved-capacity"></a>预留容量定价

Azure Cosmos DB [预留容量](cosmos-db-reserved-capacity.md)有助于通过预付为期一年或三年的 Azure Cosmos DB 资源费用为你省钱。 与采用一般定价相比，预付为期一年或三年的承诺费用可以享受 20-65% 的折扣，从而大幅节省成本。 Azure Cosmos DB 的保留容量帮助通过预付一年或三年的预配的吞吐量 (RU/s) 来降低成本，并且帮助针对所预配的吞吐量获取折扣。 

预留容量提供一种计费折扣，并且不会影响 Azure Cosmos DB 资源的运行时状态。 所有 API（包括 MongoDB、Cassandra、SQL、Gremlin 和 Azure表）和全球所有地区都可以一致地使用预留容量。 有关预留容量的详细信息，请参阅[使用预留容量预付 Azure Cosmos DB 资源](cosmos-db-reserved-capacity.md)一文，并可从 [Azure 门户](https://portal.azure.com/)购买预留容量。

## <a name="next-steps"></a>后续步骤

可在以下文章中了解更多关于优化 Azure Cosmos DB 资源成本的信息：

* 了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](optimize-cost-queries.md)
* 详细了解如何[优化多区域 Cosmos 帐户的成本](optimize-cost-regions.md)
* 了解 [Azure Cosmos DB 预留容量](cosmos-db-reserved-capacity.md)
* 了解 [Azure Cosmos DB 模拟器](local-emulator.md)
