---
title: "Azure CosmosDB：每分钟请求单位数 (RU/m) | Microsoft Docs"
description: "了解如何使用每分钟请求单位数来降低成本。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 0c597fa4afa816f9731edb744ae494b6ef928b9e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Azure Cosmos DB 中的每分钟请求单位数

Azure Cosmos DB 旨在帮助你实现快速、可预测的性能，并随着应用程序的增长无缝扩展。 可以在 Cosmos DB 容器中以每秒和每分钟 (RU/m) 粒度预配吞吐量。 以每分钟粒度预配的吞吐量用于管理工作负荷中以每秒粒度发生的意外高峰。 

本文概述每分钟请求单位数 (RU/m) 预配工作原理。 预配 RU/m 的目标是根据不可预测的需求（尤其是如果需要基于操作数据运行分析时）和高峰工作负荷提供可预测的性能。 我们希望客户使用更多的预配吞吐量，以便能够省心地快速扩展。

阅读本文后，你将能够回答以下问题：

* 每分钟请求单位数是如何工作的？
* 每分钟请求单位数和每秒请求单位数之间有什么差别？
* 如何预配 RU/m？
* 在哪种情形下应考虑预配每分钟请求单位数？
* 如何使用门户指标优化成本和性能？
* 定义哪种类型的请求可能会消耗 RU/m 预算？

## <a name="provisioning-request-units-per-minute-rum"></a>预配每分钟请求单位数 (RU/m)

在以秒粒度 (RU/s) 预配 Azure Cosmos DB 时，如果吞吐量未超过该秒内预配的容量，则可以保证请求能够以较低的延迟成功完成。 使用 RU/m 时，粒度为分钟，可保证请求在该分钟内成功完成。 与喷发性系统相比，我们可以确保你获得的性能可预测，并且可以针对性能做出规划。

每分钟预配的工作方式相当简单：

* RU/m 按小时计费，此外还包括 RU/s 费用。 有关详细信息，请访问 Cosmos DB [定价页](https://aka.ms/acdbpricing)。
* 可在集合级别启用 RU/m。 可以通过 SDK（Node.js、Java 或 .Net）或门户（还包括 MongoDB API 工作负荷）启用 RU/m。
* 启用 RU/m 后，对于预配的每 100 个 RU/s，你还会获得 1,000 个预配的 RU/m（10 倍比率）
* 在给定的秒内，仅当已超过该秒内的每秒预配时，请求单位才会消耗 RU/m 预配
* 60 秒期限 (UTC) 结束后，将会重填每分钟预配
* 只能为每个分区最多预配了 5,000 RU/s 的集合启用 RU/m。 如果扩展吞吐量需求并为每个分区使用这么高的预配级别，将会出现警告消息

下面是一个具体的示例，其中，客户可以预配 10kRU/s 和 100kRU/m，通过在已预配 10,000 RU/s 和 100,000 RU/m 的集合中使用 90 秒期限，将高峰预配 (50kRU/s) 的成本节省 73%：

* 第 1 秒：RU/m 预算设置为 100,000
* 第 3 秒：在该秒内，请求单位消耗量为 11,010 RU，比 RU/s 预配高出 1,010 RU。 因此，将从 RU/m 预算中减去 1,010 RU。 98,990 RU 可用于 RU/m 预算中的后 57 秒
* 第 29 秒：在该秒内，发生了一个较大的高峰（比每秒预配高 4 倍以上），请求单位消耗量为 46,920 RU。 将从 RU/m 预算中减去 36,920 RU，因此 RU 数从 92,323（第 28 秒）降至 55,403（第 29 秒）
* 第 61 秒：RU/m 预算设置回到 100,000 RU。
 
![显示 Azure Cosmos DB 消耗与预配的示意图](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>使用 RU/m 指定请求单位容量

创建 Azure Cosmos DB 集合时，可以指定要为集合保留的每秒请求单位数（每秒 RU 数）。 还可以确定是否要添加每分钟 RU 数。 可以通过门户或 SDK 完成此操作。 

### <a name="through-the-portal"></a>通过门户

在预配集合时，只需按一下鼠标即可启用或禁用每分钟 RU 数。 

 ![显示如何在 Azure 门户中设置 RU/m 的屏幕截图](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>通过 SDK
首先，必须注意 RU/m 仅适用于以下 SDK：

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

以下代码片段使用 .NET SDK 创建每秒 3,000 个请求单位、每分钟 30,000 个请求单位的集合：

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

以下代码片段使用 .NET SDK 将集合的吞吐量更改为每秒 5,000 个请求单位，且不预配每分钟 RU 数：

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>适合的方案

本部分将概述非常适合用于启用每分钟请求单位数的方案。

**开发/测试环境：**适合。 在开发阶段，如果你要使用不同的工作负荷测试应用程序，RU/m 可在此阶段提供灵活性。 同时，[模拟器](local-emulator.md)也是一个用于测试 Azure Cosmos DB 的极佳免费工具。 但是，如果你想要在云环境中开始，RU/m 可为解决即席性能需求提供极大的灵活性。 这样就可以将更多的时间放在开发上，不需要一开始就过多地考虑性能需求。 我们建议从最小的 RU/s 预配开始，并启用 RU/m。

**不可预测的高峰分钟粒度需求：**适合 – 节省：25-75%。 我们已看到了 RU/m 带来的极大改善，大多数生产方案都能适应该组。 如果某个 IoT 工作负荷在一分钟内多次出现高峰，或者在系统同时执行大量插入时运行查询，那么则需要提供额外的容量来处理高峰需求。 我们建议应用下面所述的逐步方法来优化资源需求。

 ![显示 5 分钟粒度请求消耗量的示意图](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *图 - RU 消耗量基准*

**高枕无忧：**适合 – 节省：10-20%。 有时，你只是希望能够做到高枕无忧，不必担心潜在的高峰和限制。 此功能正好能够满足这种需要。 在此情况下，我们建议启用 RU/m 并略微降低每秒预配。 这种情况与前面不同，因为你不需要尝试大幅优化预配。 这主要是一种“零限制”思路。

具有即席需求的关键操作：有时，我们建议只允许关键操作访问 RU/m 预算，使预算不被即席操作或不太重要的操作消耗。 可根据下一部分所述轻松定义这种设置。

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>使用门户指标优化成本和性能

**在未来几周，我们将继续制作文档来介绍如何监视每分钟 RU 消耗量，以优化吞吐量需求。**

通过门户指标可以查看消耗的常规每秒 RU 数与每分钟 RU 数。 监视这些指标有助于优化预配。 

我们建议采用逐步的方法，使用 RU/m 来增大自身的优势。 在每个步骤中，应该获得代表工作负荷完整周期（可能是小时、天甚至周）的 RU 消耗概览，以及有关预配项利用率的见解。

这种方法的原理是使吞吐量预配尽量接近与下面所述性能准则相符的预配点。 

![显示 5 分钟粒度请求消耗量的示意图](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
若要了解工作负荷的最佳预配点，需要了解：

* 消耗模式：无高峰、不经常出现高峰或者持续出现峰值？ 小（平均值的 2 倍）、中或大（平均值的 10 倍以上）峰值？
* 限制请求百分比：是否能够接受一定的限制？ 如果可以，限制度可以有多高？ 

确定目标后，便可以进一步实现最佳预配。

我们将提供总体指导，帮助你根据 RU/m 消耗量优化预配。 此指导并不适用所有类型的工作负载，而是基于对个人预览版的了解。 随着知识的不断丰富，我们可能会更改此类基准：

|RU/m 利用率|RU/m 利用程度|有关预配的建议操作|
|---|---|---|
|0-1%|利用不足|降低 RU/s 来消耗更多的 RU/m|
|1-10%|正常使用|保留相同的预配级别|
|超过 10%|利用过度|提高 RU/s 以减少对 RU/m 的依赖|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>选择哪些操作可以消耗 RU/m 预算

在请求级别，还可以启用/禁用 RU/m 预算来为请求提供服务（不管操作类型如何）。 如果已消耗常规的预配 RU/s 预算并且请求无法消耗 RU/m 预算，此请求将被限制。 默认情况下，如果已激活 RU/m 吞吐量预算，则任何请求将由 RU/m 预算提供服务。 

以下代码片段使用 DocumentDB API for CRUD 和查询操作禁用 RU/m 预算。

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>后续步骤

本文已介绍 Azure Cosmos DB 中的分区工作原理、如何创建分区集合，以及如何为应用程序选择适当的分区键。

* 使用 Azure Cosmos DB 执行规模和性能测试。 有关示例，请参阅[使用 Azure Cosmos DB 执行性能和规模测试](performance-testing.md)。
* 开始使用 [SDK](documentdb-sdk-dotnet.md) 或 [REST API](/rest/api/documentdb/) 编写代码
* 了解 Azure Cosmos DB 中的[预配吞吐量](request-units.md) 


