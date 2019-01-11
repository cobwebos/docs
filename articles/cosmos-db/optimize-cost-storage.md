---
title: 优化 Azure Cosmos DB 中的存储成本
description: 本文介绍如何管理 Azure Cosmos DB 中存储的数据的存储成本
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 6a6f8b47044d36d767721e2d3503d11518dbf5a6
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036114"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>优化 Azure Cosmos DB 中的存储成本

Azure Cosmos DB 提供无限的存储和吞吐量。 与必须在 Azure Cosmos 容器或数据库上预配/配置的吞吐量不同，存储将根据使用情况进行计费。 仅需为使用的逻辑存储计费，并且不必提前预留任何存储空间。 存储会根据添加或删除到 Azure Cosmos DB 容器的数据自动纵向扩展和缩减。

## <a name="storage-cost"></a>存储成本

存储的计费单位为 GB。 数据和索引使用本地 SSD 支持的存储。 使用的总存储量等于使用 Azure Cosmos DB 的所有区域中使用的数据和索引所需的存储。 如果跨三个区域全局复制 Azure Cosmos 帐户，则将为这三个区域中的每个区域支付总存储成本。 要估算存储要求，请参阅[容量规划器](https://www.documentdb.com/capacityplanner)工具。 Azure Cosmos DB 中的存储成本为 $0.25/GB/月，有关最新更新，请参阅[定价页面](https://azure.microsoft.com/pricing/details/cosmos-db/)。 可以设置警报以确定 Azure Cosmos 容器使用的存储，要监视存储，请参阅[监视 Azure Cosmos DB ](monitor-accounts.md)一文。

## <a name="optimize-cost-with-item-size"></a>通过项目大小优化成本

Azure Cosmos DB 希望项目大小不超过 2 MB，以获得最佳性能和成本优势。 如果需要任何项目来存储大于 2 MB 的数据，请考虑重新设计项目架构。 对于极少数无法重新设计架构的情况，可将项目拆分为子项目，并使用公共标识符 (ID) 从逻辑上链接它们。 所有 Azure Cosmos DB 功能都通过定位到该逻辑标识符来一致地工作。

## <a name="optimize-cost-with-indexing"></a>通过索引优化成本

默认情况下，数据会自动编入索引，这会增加所消耗的总存储量。 但是，可应用自定义索引策略来减少此开销。 尚未通过策略调整的自动索引约为项目大小的 10-20％。 通过删除或自定义索引策略，无需为写入支付额外成本，也不需要额外的吞吐量容量。 请参阅 [Azure Cosmos DB 中的索引编制](indexing-policies.md)以配置自定义索引策略。 如果你曾使用过关系数据库，你可能会认为“对所有内容编制索引”意味着存储成本会翻倍或更高。 但是，在 Azure Cosmos DB 中，在中值情况下，存储成本实际要低得多。 在 Azure Cosmos DB 中，即使使用自动索引，索引的存储开销通常也很低 (10-20%)，因为它专为低存储容量而设计。 通过管理索引策略，你可以以更细粒度的方式控制索引占用空间和查询性能之间的权衡。

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>使用生存时间和更改源优化成本

一旦你不再需要数据，就可使用[生存时间](time-to-live.md)和[更改源](change-feed.md)从 Azure Cosmos 帐户中正常删除数据，或者可将旧数据迁移到其他数据存储，例如 Azure blob 存储或 Azure 数据仓库。 利用生存时间或 TTL，Azure Cosmos DB 能够在特定一段时间后自动将项从容器中删除。 默认情况下，可以在容器级别设置生存时间，并基于每个项替代该值。 在容器或项级别设置 TTL 后，Azure Cosmos DB 会在一段时间（自上次修改项的时间开始算起）后自动删除这些项。 通过使用更改源，可将数据迁移到 Azure Cosmos DB 中的另一个容器或外部数据存储。 迁移需要零停机时间，当完成迁移时，可删除或配置生存期以删除源 Azure Cosmos 容器。

## <a name="optimize-cost-with-rich-media-data-types"></a>使用丰富的媒体数据类型优化成本 

如果希望存储丰富的媒体类型，例如视频、图像等，可在 Azure Cosmos DB 中使用许多选项。 一种选项是将这些富媒体类型存储为 Azure Cosmos 项。 每个项都有一个 2 MB 的限制，可通过将数据项链接到多个子项来避免此限制。 也可将它们存储在 Azure Blob 存储中，并使用元数据从 Azure Cosmos 项中引用它们。 这种方法有许多优点和缺点。 除常规 Azure Cosmos 项目外，第一种方法在延迟、吞吐量 SLA 以及富媒体数据类型的统包全局分发功能方面性能最佳。 但是，这种支持的价格更高。 通过在 Azure Blob 存储中存储媒体，可降低总体成本。 如果延迟非常重要，可对来自 Azure Cosmos 项的富媒体文件使用高级存储。 该存储与 CDN 本机集成，以较低的成本从边缘服务器提供图像，以规避地理限制。 此方案的缺点是，必须处理 Azure Cosmos DB 和 Azure Blob 存储这两种服务，这可能会增加运营成本。 

## <a name="check-storage-consumed"></a>检查使用的存储

要检查 Azure Cosmos 容器的存储消耗情况，可以在容器上运行 HEAD 或 GET请求，并检查 `x-ms-request-quota` 和 `x-ms-request-usage` 标头。 或者，如果使用 .NET SDK，可使用 [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)) 和 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 属性来消耗存储。

## <a name="using-sdk"></a>使用 SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](optimize-cost-queries.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)

