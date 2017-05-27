---
title: "欢迎 DocumentDB 客户使用 Azure Cosmos DB | Microsoft Docs"
description: "了解 Build 2017 中发布的公告，其中的 DocumentDB 客户现已改称为 Azure Cosmos DB 客户。"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: n/a
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 05/12/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f60c3502ed51424647088fa8109f13cabc4e73da
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017

---

# <a name="welcome-documentdb-customers-to-azure-cosmos-db"></a>欢迎 DocumentDB 客户使用 Azure Cosmos DB

尊敬的 DocumentDB 客户，我们很高兴宣布，你们现已成为 Azure Cosmos DB 家庭的成员！ 

[Microsoft Build 2017 大会宣布推出的](https://azure.microsoft.com/blog/azure-cosmos-db-microsofts-globally-distributed-multi-model-database-service/) Azure Cosmos DB 是第一款全球分布式数据库服务，可让你在数目不限的地理区域中弹性缩放吞吐量和存储，同时提供低延迟、高可用性和一致性的保证，享有行业中极为全面的 SLA 保障，支持众多的数据模型和 API。 

![Azure Cosmos DB 是 Microsoft 的全球分布式数据库服务，可以灵活扩展，已保证低延迟，有五个一致性模型，并提供全面 SLA 的保证](./media/welcome-documentdb-customers/azure-cosmos-db.png)

Azure Cosmos DB 支持的 API 之一是 DocumentDB API，另外还支持文档数据模型。 你们应该很熟悉 DocumentDB API， 正是使用这些 API 运行当前的 DocumentDB 应用程序。 这些 API _不会_有变化 - NuGet 包、命名空间和所有依赖项都将保持不变。 **无需进行任何更改**即可继续运行使用 DocumentDB API 构建的应用。 Azure Cosmos DB 只是这些 API 目前所属的服务的名称。 

让我们探讨一些问题。 

## <a name="why-move-to-azure-cosmos-db"></a>为何要转移到 Azure Cosmos DB？ 

Azure Cosmos DB 是全球分布式的、大规模云数据库的重大飞跃。 现在，DocumentDB 客户可以访问 Azure Cosmos DB 提供的突破性系统和功能。

Azure Cosmos DB 在 2010 年以“Project Florence”的名义开始解决 Microsoft 内部大型应用程序开发人员所面临的难题。 注意到构建全球分布式应用并不是只有 Microsoft 才会遇到的难题，在 2015 年，我们以 Azure DocumentDB 的形式向 Azure 开发人员提供了与此相关的第一代技术。 

自此之后，我们添加了新的特性，并引入了重要新功能。  成果就是 Azure Cosmos DB。  随着 Azure Cosmos DB 的发布，DocumentDB 客户连同其数据将自动成为 Azure Cosmos DB 客户。 这种过渡是无缝式的，你们可以访问 Azure Cosmos DB 提供的突破性系统和功能。 这些功能涉及的领域包括核心数据库引擎以及全局分发、弹性可伸缩性，并享有行业领先的全面 SLA 保障。 具体而言，我们已改进了 Azure Cosmos DB 数据库引擎，使其能够有效地将所有热门数据模型、类型系统和 API 映射到 Azure Cosmos DB 的基础数据模型。 

对于当前的开发人员而言，此项工作的成果包括对 [Gremlin](graph-introduction.md) 和[表存储 API](table-introduction.md) 的全新支持。 而这只是开端... 我们将不断添加其他热门 API 和更新的数据模型，为全球规模的性能和存储带来进步。 

必须指出的是，DocumentDB 的 [SQL 语言](../documentdb/documentdb-sql-query.md)始终是基础 Cosmos DB 能够支持的多种 API 之一。 开发人员使用 Azure Cosmos DB 等完全托管的服务时，该服务的唯一接口就是该服务公开的 API。 因此，现有 DocumentDB 客户在操作时与以往其实并无不同。 Azure Cosmos DB 提供的 SQL API 与 DocumentDB 完全相同。 但是，现在（和将来）你们可以访问其他功能，而以前却做不到这一点。 

我们持续努力的另一个成果是扩建了吞吐量和存储的全局分发与弹性缩放的基础。 最突出的成果之一就是 [RU/m](request-units-per-minute.md)，但我们在这些方面会不断地宣布更多功能。 这些新功能可帮助客户降低各种工作负荷的成本。 我们对全局分发子系统做了几项基础性的增强。 对于许多开发人员而言，此项工作的成果之一就是前缀一致性模型（总共提供五个定义完善的一致性模型）。 但是，我们还会推出其他许多有用的功能，在它们成熟时即会发布。 

## <a name="what-do-i-need-to-do-to-ensure-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>如何确保 DocumentDB 资源可继续在 Azure Cosmos DB 上运行？

无变化。 不需要进行任何更改。 DocumentDB 资源现在称为 Azure Cosmos DB 资源，过渡后，服务不会发生任何中断。

## <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>要进行哪些更改才能让应用配合 Azure Cosmos DB 工作？

不需要进行任何更改。 类、命名空间和 NuGet 包名称都没有变化。 与往常一样，我们建议使用最新的 SDK，以利用最新的功能和改进。 

## <a name="whats-changed-in-the-azure-portal"></a>Azure 门户有哪些变化？

Azure DocumentDB 不再以 Azure 服务的形式显示在门户中。 Azure Cosmos DB 将显示为一个新图标，如下图所示。 可以像以前一样使用所有集合，并且仍可以缩放吞吐量、更改一致性和监视 SLA。 **数据资源管理器（预览）**的功能已得到增强。 现在，可以在一个页面中查看和编辑文档、创建和运行查询，以及使用存储过程、触发器和 UDF，如下图所示。 

![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/welcome-documentdb-customers/cosmos-db-data-explorer.png)

## <a name="are-there-changes-to-pricing"></a>价格是否有变化？

没有，在 Azure Cosmos DB 上运行应用的费用与以前相同。 但是，你们可以受益于我们宣布的**“每分钟请求单位数”新功能**。 有关详细信息，请参阅[缩放每分钟吞吐量](request-units-per-minute.md)一文。

## <a name="are-there-changes-to-the-service-level-agreements-slas"></a>服务级别协议 (SLA) 是否有变化？

没有，有关可用性、一致性、延迟和吞吐量的 SLA 都保持不变，并且仍会显示在门户中。 有关 SLA 的详细信息，请参阅 [Azure Cosmos DB 的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。
   
![包含示例数据的待办事项应用](./media/welcome-documentdb-customers/azure-cosmosdb-portal-metrics-slas.png)

## <a name="whats-next-with-azure-cosmos-db"></a>对于 Azure Cosmos DB，你们接下来有什么举措？

Azure Cosmos DB 是不断发展的数据库服务。 所有新功能已在 Microsoft 内部的大型应用程序中验证，后续将对重要的外部客户公开，最终将会全球发布。 

新的 Gremlin 和表 API 支持只是 Azure Cosmos DB 最初支持的一些热门 API 和数据模型，在宣布新的数据模型后，我们将与大家分享。 我们将持续为全球规模的性能和存储带来巨大进步。 

再次感谢你们成为 Azure DocumentDB 客户，我们很高兴与全球各地的所有开发人员和客户分享 Azure Cosmos DB。 我们的使命是开发全球最值得信赖的数据库服务，并让大家更轻松地构建功能极为强大、可全球缩放的应用。 欢迎试用 Azure Cosmos DB 中的新功能，并告知你们的想法！  能够看到你们使用 Cosmos DB 构建的应用，我们将兴奋不已。

- 你的好友，Azure Cosmos DB [@AzureCosmosDB](https://twitter.com/AzureCosmosDB)

