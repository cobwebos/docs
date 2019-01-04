---
title: 在 Azure Cosmos DB 中优化吞吐量成本
description: 本文介绍如何优化 Azure Cosmos DB 中存储的数据的吞吐量成本。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: f0d0442a8640a75b21e95e3ae024fd7994602b51
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807935"
---
# <a name="optimizing-throughput-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中优化吞吐量成本

通过提供预配的吞吐量模型，Azure Cosmos DB 可在任何规模下提供可预测的性能。 预留或提前预配吞吐量消除了对性能造成的“干扰性邻居影响”。 只需指定所需的确切吞吐量，然后，Azure Cosmos DB 就能确保配置的吞吐量，并以 SLA 作为保障。

可以从 400 RU/秒的最低吞吐量着手，今后可以扩展到每秒数千万个请求或更高的吞吐量。 针对 Azure Cosmos 容器或数据库发出的每个请求，例如读取请求、写入请求、查询请求和存储过程，都会产生相应的成本，这些成本将从预配的吞吐量中扣减。 如果预配 400 RU/秒的吞吐量并发出成本为 40 RU 的查询，则每秒可以发出 10 个此类查询。 超过该限制的任何请求将受到速率限制，应重试该请求。 如果使用客户端驱动程序，它们支持自动重试逻辑。

可以针对数据库或容器预配吞吐量，每个策略可以帮助你根据方案节省成本。

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>在不同的级别通过预配吞吐量进行优化

* 如果针对某个数据库预配吞吐量，该数据库中的所有容器（例如集合/表/图形）可以基于负载共享该吞吐量。 在数据库级别预留的吞吐量将会根据特定容器集上的工作负荷以不均匀的方式进行共享。

* 如果针对某个容器预配吞吐量，则可以保证该容器的吞吐量，并提供 SLA 保障。 所选的逻辑分区键对于在容器的所有逻辑分区之间均匀分配负载至关重要。 有关更多详细信息，请参阅[分区](partitioning-overview.md)和[水平缩放](partition-data.md)文章。

下面是确定预配吞吐量策略时可以参考的一些指导原则：

**对于以下情况，考虑针对 Azure Cosmos DB 数据库（包含一组容器）预配吞吐量**：

1. 有几十个 Azure Cosmos 容器，并想要在部分或所有容器之间共享吞吐量。 

2. 从专用于在 IaaS 托管的 VM 上运行或本地运行的单租户数据库（例如，NoSQL 数据库或关系数据库）迁移到 Azure Cosmos DB。 有许多集合/表/图形，并且不想要对数据模型进行任何更改。 请注意，如果在从本地数据库迁移时不更新数据模型，可能需要牺牲 Azure Cosmos DB 提供的一些优势。 建议始终重新访问数据模型，以获得最大性能并优化成本。 

3. 想要在数据库级别利用入池吞吐量，来缓解容易出现意外高峰的工作负荷中的计划外高峰。 

4. 不针对单个容器设置特定的吞吐量，而是考虑如何在数据库中的一组容器之间获得聚合吞吐量。

**对于以下情况，考虑针对单个容器预配吞吐量：**

1. Azure Cosmos 容器较少。 由于 Azure Cosmos DB 不区分架构，容器可以包含采用异构架构的项，无需客户创建多个容器类型 - 为每个实体各创建一个。 如果将 10 到 20 个容器划分到单个容器有利，则始终适合考虑这种做法。 如果容器的最低吞吐量为 400 RU，则将所有 10 到 20 个容器组建成一个池可能更具成本效益。 

2. 想要控制特定容器的吞吐量，并使给定的容器获得有保证的、且有 SLA 作为保障的吞吐量。

**考虑混合上面两种策略：**

1. 如前所述，Azure Cosmos DB 允许混合搭配上述两种策略，因此，现在可以在 Azure Cosmos 数据库中创建一些容器（它们可以共享针对数据库预配的吞吐量），并可以在同一数据库中创建一些容器（它们可以获得专用的预配吞吐量）。 

2. 可对混合配置应用上述策略，其中，可在两个数据库级别提供配置吞吐量，某些容器具有专用的吞吐量。

如下表中所示，根据所选的 API，可以不同的粒度预配吞吐量。

|API|对于**共享**吞吐量，请配置 |对于**专用**吞吐量，请配置 |
|----|----|----|
|SQL API|数据库|容器|
|Azure Cosmos DB 的用于 MongoDB 的 API|数据库|集合|
|Cassandra API|密钥空间|表|
|Gremlin API|数据库帐户|图形|
|表 API|数据库帐户|表|

在不同的级别预配吞吐量可以根据工作负荷特征优化成本。 如前所述，随时能够以编程方式针对单个容器或者同时针对一组容器增加或减少预配的吞吐量。 根据工作负荷的变化弹性缩放吞吐量，只需为配置的吞吐量付费。 如果单个容器或一组容器分布在多个区域之间，则所有区域都能保证提供针对这些容器配置的吞吐量。

## <a name="optimize-with-rate-limiting-your-requests"></a>使用请求的速率限制进行优化

对于不易受延迟影响的工作负荷，可以预配更低的吞吐量，并在实际吞吐量超过预配的吞吐量时，让应用程序处理速率限制。 服务器将抢先结束出现 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 `x-ms-retry-after-ms` 标头，该标头指示重试请求之前用户必须等待的时间长短（以毫秒为单位）。 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>SDK 中的重试逻辑 

本机 SDK（.NET/.NET Core、Java、Node.js 和 Python）隐式捕获此响应，遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问你的帐户，否则下次重试将会成功。

如果累计有多个客户端一贯在超过请求速率的情况下运行，则当前设置为 9 的默认重试计数可能并不足够。 在这种情况下，客户端会向应用程序引发 `DocumentClientException` 并返回状态代码 429。 可以通过在 ConnectionPolicy 实例上设置 `RetryOptions` 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则在 30 秒的累积等待时间后将返回 DocumentClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（默认值 9 或用户定义的值），也会发生这种情况。 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryAtte) 设置为 3，因此，在这种情况下，如果请求操作由于超过集合的预留吞吐量而受到速率限制，则请求操作将重试三次，然后向应用程序引发异常。 [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) 设置为 60，因此，在这种情况下，如果自首次请求以来，累积重试等待时间（以秒为单位）超过 60 秒，则会引发异常。

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>分区策略和预配的吞吐量成本

良好的分区策略对于优化 Azure Cosmos DB 中的成本非常重要。 确保分区不存在偏斜，这可以通过存储指标来观察。 确保分区的吞吐量不存在偏斜，这可以通过吞吐量指标来观察。 确保特定的分区键不存在偏斜。 存储中的主导键通过指标公开，但该键取决于应用程序访问模式。 最好是考虑适当的逻辑分区键。 良好的分区键预期具有以下特征：

* 选择可以持续在所有分区之间均匀分散工作负荷的分区键。 换而言之，不应该将某些键用于大部分数据，同时将某些键用于少部分数据或者不用于任何数据。 

* 选择可在逻辑分区之间均匀分散访问模式的分区键。 工作负荷在所有键之间合理均衡分布。 换而言之，大部分工作负荷不应注重于少数几个特定键。 

* 选择具有宽广值范围的分区键。 

基本理念是在逻辑分区集之间分散容器中的数据和活动，以便可以在逻辑分区之间分配数据存储和吞吐量的资源。 分区键的候选项可能包括，经常在查询中显示为筛选器的属性。 通过在筛选器谓词中包含分区键，可以有效地路由查询。 使用此类分区策略可以大大简化预配吞吐量的优化。 

### <a name="design-smaller-items-for-higher-throughput"></a>设计较小的项以提高吞吐量 

给定操作的请求费用或请求处理成本与项的大小直接相关。 针对大项执行操作的成本比针对小项执行操作的成本要高一些。 

## <a name="data-access-patterns"></a>数据访问模式 

根据访问数据的频率以逻辑方式将数据划分成逻辑类别始终是良好的做法。 将数据分类成热数据、中性数据或冷数据，可以微调消耗的存储和所需的吞吐量。 根据访问频率，可将数据放入单独的容器（例如表、图形和集合），并微调其预配的吞吐量，以适应数据分段的需要。 

此外，如果你正在使用 Azure Cosmos DB，并知道你不会按特定的数据值进行搜索或者很少访问这些值，则应存储这些属性的压缩值。 使用此方法可以节省存储空间、索引空间和预配的吞吐量，从而较低成本。

## <a name="optimize-by-changing-indexing-policy"></a>通过更改索引策略进行优化 

默认情况下，Azure Cosmos DB 自动为每条记录的每个属性编制索引。 这是为了简化开发，并确保跨许多不同类型的即席查询具有优异的性能。 如果你的大型记录包含数千个属性，购买吞吐量来为每个属性编制索引的做法可能并不有效，尤其是只针对其中的 10 个或 20 个属性运行查询时。 在接近于能够控制特定的工作负荷时，我们的指导原则是优化索引策略。 在[此处](indexing-policies.md)可以找到有关 Azure Cosmos DB 索引策略的完整详细信息。 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>监视预配的吞吐量和消耗的吞吐量 

可以在 Azure 门户中监视预配的 RU 总数、受速率限制的请求数，以及已消耗的 RU 数。 下图显示了示例用量指标：

![在 Azure 门户中监视请求单位数](./media/optimize-cost-throughput/monitoring.png)

还可以设置警报，以检查受速率限制的请求数是否超过了特定阈值。 有关更多详细信息，请参阅[如何监视 Azure Cosmos DB](use-metrics.md) 一文。 这些警报可以向帐户管理员发送电子邮件，或者调用自定义 HTTP Webhook 或 Azure 函数来自动增加预配的吞吐量。 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>按需弹性缩放吞吐量 

由于你需要为预配的吞吐量付费，因此，使预配的吞吐量符合需要有助于避免未使用的吞吐量产生费用。 随时可以按需扩展或缩减预配的吞吐量。  

* 监视 RU 消耗量以及受速率限制的请求比率可以发现，在整个一天或一周中，并不需要保持恒定的预配吞吐量。 在晚上或周末，收到的流量可能更少。 使用 Azure 门户或者 Azure Cosmos DB 本机 SDK 或 REST API 可以随时缩放预配的吞吐量。 Azure Cosmos DB REST API 提供终结点用于以编程方式更新容器的性能级别，以及根据日期时间或者星期日期直接在代码中调整吞吐量。 执行此操作不会造成任何停机，通常在一分钟内就能产生效果。 

* 举例而言，应该缩放吞吐量的场合之一是在数据迁移过程中将数据引入 Azure Cosmos DB。 完成迁移后，可以缩减预配的吞吐量以处理解决方案的稳定状态。  

* 请记住，将按一小时粒度计费，因此，如果以高于一小时的频率更改预配的吞吐量，则无法节省成本。

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>确定新工作负荷所需的吞吐量 

若要确定新工作负荷的预配吞吐量，可使用以下步骤： 

1. 使用容量规划器执行初始的粗略评估，并借助 Azure 门户中的 Azure Cosmos Explorer 调整估算值。 

2. 建议使用超过预期的吞吐量创建容器，然后根据需要进行缩减。 

3. 建议使用某个本机 Azure Cosmos DB SDK，以便在请求受到速率限制时利用自动重试。 如果在不受支持的平台上操作，同时使用 Cosmos DB REST API，请使用 `x-ms-retry-after-ms` 标头实施自己的重试策略。 

4. 确保应用程序代码正常支持所有重试都失败的情况。 

5. 可以在 Azure 门户中配置警报，以便在发生速率限制时收到通知。 一开始可以采用保守的限制，例如，过去 15 分钟有 10 个请求受到速率限制，然后在测算出实际消耗量之后，改用更激进的规则。 偶尔发生速率限制是正常的，它们表示你正在演练设置的限制，而这完全符合你的意图。 

6. 使用监视来了解流量模式，以便可以考虑是否需要动态调整一天或一周的吞吐量预配。 

7. 定期监视预配吞吐量与消耗吞吐量的比率，确保预配的容器和数据库不会超过所需的数目。 稍微超过预配的吞吐量是一种良好的安全措施。  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>有关优化预配吞吐量的最佳做法 

以下步骤可帮助你在使用 Azure Cosmos DB 时保持解决方案的高度可伸缩性和经济效益。  

1. 如果容器和数据库明显超过预配的吞吐量，应该检查预配的 RU 和消耗的 RU，并微调工作负荷。  

2. 有一种方法可以估算应用程序所需的预留吞吐量：在针对应用程序所用的代表性 Azure Cosmos 容器或数据库运行典型操作时，记录与之相关的请求单位 (RU) 费用，然后估算预计每秒会执行的操作数目。 同时请务必测量并包含典型查询及其用量。 若要了解如何以编程方式或使用门户估算查询的 RU 成本，请参阅[优化查询成本](online-backup-and-restore.md)。 

3. 获取操作及其 RU 成本的另一种方法是启用 Log Analytics，它会提供操作/持续时间的细目以及请求费用。 Azure Cosmos DB 提供每个操作的请求费用，因此，可以存储响应中的每笔操作费用，然后将其用于分析。 

4. 可按需弹性扩展和缩减预配的吞吐量，以适应工作负荷的需要。 

5. 可按需添加和删除与 Azure Cosmos 帐户关联的区域，并控制成本。 

6. 确保在容器的逻辑分区之间均匀分布数据和工作负荷。 如果分区分布不均，可能会导致预配的吞吐量高于所需的值。 如果你发现分布有偏斜，我们建议在分区之间均匀重新分布工作负荷，或者将数据重新分区。 

7. 如果有多个容器并且这些容器不需要 SLA，则对于每个容器吞吐量 SLA 不适用的情况，可以使用基于数据库的产品/服务。 应确定要将哪些 Azure Cosmos 容器迁移到数据库级吞吐量产品/服务，然后使用基于更改源的解决方案将其迁移。 

8. 考虑对开发/测试方案使用“Cosmos DB 免费层”（一年免费）、试用版 Cosmos DB（最多可在三个区域中使用）或可下载的 Cosmos DB 仿真器。 将这些选项用于开发/测试可以明显降低成本。  

9. 可以进一步执行工作负荷特定的成本优化 – 例如，增加批大小、对跨多个区域的读取操作进行负载均衡，以及删除重复数据（如果适用）。

10. 使用 Azure Cosmos DB 预留容量可以获得为期三年的高达 65% 的大幅折扣。 Azure Cosmos DB 预留容量模型是针对一段时间内所需的请求单位数提前做出的承诺。 折扣是分层的，长期使用的请求单位数越多，折扣就越大。 这些折扣会立即应用。 超过预配值的任何 RU 将根据非预留容量费率计费。 有关更多详细信息，请参阅 [Cosmos DB 预留容量](cosmos-db-reserved-capacity.md)。 考虑购买预留容量，以进一步降低预配的吞吐量成本。  

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解 [Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](optimize-cost-queries.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)

