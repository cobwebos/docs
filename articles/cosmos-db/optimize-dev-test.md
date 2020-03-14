---
title: Azure Cosmos DB 中的开发和测试优化
description: 本文介绍 Azure Cosmos DB 如何为免费开发和测试服务提供多种选择。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246677"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中优化开发和测试成本

本文介绍了用于免费开发和测试 Azure Cosmos DB 的不同选项，以及用于优化开发或测试帐户成本的方法。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 模拟器（可以本地下载的版本）

[Azure Cosmos DB 模拟器](local-emulator.md)是一个本地的可下载版本，模拟 Azure Cosmos DB 云服务。 即使没有网络连接，也可以编写并测试使用 Azure Cosmos DB API 的代码，不需支付任何费用。 Azure Cosmos DB 模拟器提供了一个用于开发的具有云服务高保真特性的本地环境。 可以在本地开发和测试应用程序，无需创建 Azure 订阅。 做好将应用程序部署到云的准备以后，即可更新连接到云中的 Azure Cosmos DB 终结点所需的连接字符串，不需进行其他的修改。 也可在 Azure DevOps 中[通过 Azure Cosmos DB 模拟器生成任务设置 CI/CD 管道](tutorial-setup-ci-cd.md)，以便运行测试。 若要开始操作，可以参阅 [Azure Cosmos DB 模拟器](local-emulator.md)一文。

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 免费层 
Azure Cosmos DB 免费级别，可以轻松入门、开发和测试应用程序，甚至免费运行小型生产工作负荷。 如果在帐户上启用了 "免费" 层，则会在帐户中获取第 400 RU/秒和 5 GB 存储空间。 你还可以创建一个包含25个容器的共享吞吐量数据库，该数据库在数据库级别上共享 400 RU/s，所有免费层都涵盖此数据库（免费层帐户中的最大限制为5个共享吞吐量数据库）。 免费层在帐户的生存期内无限期持续下去，并附带定期 Azure Cosmos DB 帐户的所有[权益和功能](introduction.md#key-benefits)，包括无限制的存储和吞吐量（RU/s）、sla、高可用性、在所有 Azure 区域中的全局分发，等等。 每个 Azure 订阅最多可以有一个免费层帐户，创建帐户时必须选择加入。 若要开始，请[创建一个启用了免费层的新帐户](create-cosmosdb-resources-portal.md)。 如需更多详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

## <a name="try-azure-cosmos-db-for-free"></a>免费试用 Azure Cosmos DB

免费[试用 Azure Cosmos DB 免费试用](https://azure.microsoft.com/try/cosmosdb/)，无需注册 Azure 帐户，也无需使用信用卡即可试验云中的 Azure Cosmos DB。 试用 Azure Cosmos DB 帐户在目前的30天内可用。 可以随时续订。 尝试 Azure Cosmos DB 帐户可以轻松地评估 Azure Cosmos DB、构建和测试应用程序，或使用快速入门或教程。 你还可以创建演示、执行单元测试，甚至创建多区域帐户并在其上运行应用，而无需支付任何费用。 在 Try Azure Cosmos DB 帐户中，可以有一个最多25个容器和 20000 RU/s 吞吐量的共享吞吐量数据库，或一个最多为 5000 RU/秒的容器。 若要开始操作，请参阅[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 页。

## <a name="azure-free-account"></a>Azure 免费帐户

Azure Cosmos DB 包含在 [Azure 免费帐户](https://azure.microsoft.com/free)中，该帐户提供 Azure 信用额度和资源，可以免费使用特定的一段时间。 特别对于 Azure Cosmos DB，该免费帐户提供 5 GB 的存储和 400 RU 的全年预配吞吐量。 任何开发人员都可以通过此体验轻松地测试 Azure Cosmos DB 的功能，或者将其与其他 Azure 服务集成，没有任何费用。 Azure 免费帐户提供 $200 的信用额度，可以在头 30 天使用。 在选择升级之前，即使开始使用服务也不会收费。 若要开始操作，请访问 [Azure 免费帐户](https://azure.microsoft.com/free)页。

## <a name="use-shared-throughput-databases"></a>使用共享吞吐量数据库

在[共享的吞吐量数据库](set-throughput.md#set-throughput-on-a-database)中，数据库中的所有容器都共享数据库的预配吞吐量（RU/秒）。 例如，如果你预配了 400 RU/s 的数据库并具有四个容器，则所有四个容器都将共享 400 RU/秒。 在开发或测试环境中，每个容器的访问频率可能较低，因此要求低于最小值 400 RU/秒，将容器置于共享吞吐量数据库可以帮助优化成本。 

例如，假设您的开发或测试帐户具有四个容器。 如果创建的四个容器具有专用吞吐量（最小为 400 RU/秒），则每秒的总 RU 数将为 1600 RU/秒。 与此相反，如果创建共享吞吐量数据库（最小 400 RU/s）并将容器放在该处，则每秒的总 RU 数将是 400 RU/秒。 通常，在任何单个容器上不需要保证吞吐量的情况下，共享吞吐量数据库都非常有用。  了解有关[共享吞吐量数据库的](set-throughput.md#set-throughput-on-a-database)详细信息。

## <a name="next-steps"></a>后续步骤

可以按照以下文章的说明，开始使用模拟器或免费的 Azure Cosmos DB 帐户：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](optimize-cost-queries.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)

