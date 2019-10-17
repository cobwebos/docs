---
title: 优化性能的部署策略和最佳做法-Azure 搜索
description: 了解优化 Azure 搜索性能和配置最佳规模的技术与最佳做法。
author: LiamCavanagh
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 566c208ef415f6fc9f3ada419e2f9e9244bc066d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333163"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>针对 Azure 搜索优化性能的部署策略和最佳实践

本文介绍针对可伸缩性和可用性具有复杂要求的高级方案的最佳实践。 

## <a name="develop-baseline-numbers"></a>开发基线编号
优化搜索性能时，应重点关注缩短查询执行时间。 为此，您需要了解典型的查询负载。 以下准则可帮助您获得基准查询编号。

1. 选取完成典型搜索请求应该花费的目标延迟（或最大时间量）。
2. 针对搜索服务，使用现实数据集来创建和测试工作负载，以测量这些延迟速率。
3. 以较低的每秒查询数（QPS）开始，并逐渐增加在测试中执行的数量，直到查询延迟降到定义的目标延迟以下。 这是一个重要的基准，可帮助你计划应用程序在使用量增长方面的规模。
4. 只要有可能，请重用 HTTP 连接。 如果使用 Azure 搜索 .NET SDK，这意味着你应该重用某个实例或 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 实例，并且如果使用 REST API，就应该重用单个 HttpClient。
5. 改变查询请求的实质，使搜索发生在索引的不同部分。 变体非常重要，因为如果持续执行相同的搜索请求，则数据的缓存将开始比使用更完全不同的查询集更好地显示性能。
6. 改变查询请求的结构，以便获得不同类型的查询。 并非每个搜索查询都在同一级别执行。 例如，对于具有大量 facet 和筛选器的查询，文档查找或搜索建议通常更快。 测试组合应包括各种查询，与生产中预期的比率大致相同。  

在创建这些测试工作负荷时，需要记住 Azure 搜索的下面这些特征︰

+ 可以通过一次推送过多的搜索查询来重载服务。 发生这种情况时，会看到 HTTP 503 响应代码。 若要避免在测试过程中出现503，请从各种搜索请求范围开始，以查看添加更多搜索请求时的延迟速率差异。

+ Azure 搜索不会在后台运行索引任务。 如果你的服务同时处理查询和索引工作负荷，请考虑将索引作业引入查询测试，或浏览用于在非高峰时段运行索引作业的选项。

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) 是真正适合执行基准测试的好方法，因为在需要针对 Azure 搜索执行查询并启用请求的并行化时，它允许执行 HTTP 请求。
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>高查询量和限制请求的缩放
如果接收的请求数过多，或超出目标延迟率，则会增加查询负载，可以通过以下两种方式之一来降低滞后率：

1. **增加副本数︰** 副本就像数据副本，它允许 Azure 搜索对面向多个副本的请求进行负载均衡。  在副本之间的所有负载均衡和数据的复制都是由 Azure 搜索管理的，可以随时更改为服务分配的副本数量。  最大可在一个标准搜索服务中分配 12 个副本，并在一个基本搜索服务中分配 3 个副本。 可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整副本。
2. **增加搜索层︰** Azure 搜索来自[许多层](https://azure.microsoft.com/pricing/details/search/)，其中每个层都提供不同级别的性能。  在某些情况下，即使在副本被极限时，您所使用的层也不能提供足够低的延迟率。在这种情况下，你可能需要考虑利用更高级的搜索层（如 Azure 搜索 S3 层），该级别非常适用于具有大量文档和极高查询工作负载的方案。

## <a name="scaling-for-slow-individual-queries"></a>为慢速单个查询缩放
高延迟率的另一个原因是，需要花费很长时间才能完成的单个查询。 在这种情况下，添加副本将不会有帮助。 可能有两个可能的选项可帮助包括：

1. **增加分区数**：分区是一种机制，可在额外资源之间拆分数据。 添加第二个分区会将数据拆分为两个分区，第三个分区会将其拆分为三个分区，依此类推。 一种很好的副作用是，速度较慢的查询有时会由于并行计算而变得更快。 我们注意到了并行度较低的查询（如与多个文档匹配的查询），或提供大量文档的计数的方面。 由于需要大量计算来对文档的相关性进行评分，或者要对文档的数量进行计数，因此添加额外的分区有助于更快地完成查询。  
   
   在标准搜索服务中最多可以有 12 个分区，在基本搜索服务中最多可以有 1 个分区。  可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整分区。

2. **限制高基数字段：** 高基数字段包含具有大量唯一值的可查找或可筛选字段，因此，在计算结果时，会消耗大量资源。 例如，将 "产品 ID" 或 "说明" 字段设置为 "可查找/可筛选" 将计为高基数，因为文档中的大部分值都是唯一的。 只要有可能，请限制高基数字段的数量。

3. **增加搜索层︰** 另一种方法是，向上移动到更高的 Azure 搜索层，可以为较慢的查询改进性能。 每个更高的层提供更快的 Cpu 和更多的内存，这两者都对查询性能产生积极影响。

## <a name="scaling-for-availability"></a>调整可用性的规模
副本不仅可以帮助缩短查询延迟，而且还可以允许高可用性。 借助单个副本，应该可以预期周期性的停机时间，因为在软件更新之后，或针对其他将执行的维护活动后，服务器会周期性停机。  因此，请务必考虑应用程序是否需要搜索（查询）以及写入（编制索引事件）的高可用性。 Azure 搜索在具有以下属性的所有付费搜索产品/服务上提供 SLA 选项：

* 针对只读工作负荷（查询），需要有 2 个副本才可达到高可用性
* 针对读写工作负荷（查询和索引），需要有 3 个或更多副本才可达到高可用性

有关这方面的更多详细信息，请访问 [Azure 搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由于副本是你的数据的副本，因此具有多个副本将允许 Azure 搜索对一个副本执行计算机重启和维护，同时允许查询执行在其他副本上继续。 相反，如果将副本删除，则会导致查询性能下降，前提是这些副本是未充分利用的资源。

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>针对异地分布式工作负荷和异地冗余进行缩放
对于异地分布式工作负荷，从 Azure 搜索托管的数据中心位置的用户将具有更高的延迟率。 一项缓解措施是在区域中预配多个搜索服务，使其更接近于这些用户。 Azure 搜索当前不提供自动化方法来跨区域异地复制 Azure 搜索索引，但有一些技巧，可以用来使此过程很容易实现和管理。 我们会在下面几节介绍这些技巧。

地理分布式一组搜索服务的目标是让两个或更多个索引在用户路由到 Azure 搜索服务的两个或更多个区域中可用，如以下示例中所示：

   ![按区域的服务的交叉表][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>让数据在多个 Azure 搜索服务之间保持同步
有两个选项可让分布式搜索服务保持同步，包括使用 [Azure 搜索索引器](search-indexer-overview.md)或推送 API（也称为 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引器更新多个服务的内容

如果已对一个服务使用索引器，则可以将另一个服务上的第二个索引器配置为使用同一数据源对象，并从同一位置提取数据。 每个区域中的每个服务都有自己的索引器和目标索引（搜索索引不是共享的，这意味着，数据是重复的），但每个索引器都引用同一个数据源。

下面是体系结构的大致外观。

   ![具有分布式索引器和服务组合的单一数据源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST Api 将内容更新推送到多个服务
如果你使用 Azure 搜索 REST API 来[推送 Azure 搜索索引中的内容](https://docs.microsoft.com/rest/api/searchservice/update-index)，则只要需要更新，就可以通过将更改推送到所有搜索服务来使各种搜索服务保持同步。 在代码中，请确保处理对一个搜索服务的更新失败但对其他搜索服务成功的情况。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理器
通过使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)，可以将请求路由到多个地理定位的网站，而这些网站由多个 Azure 搜索服务支持。 流量管理器的一个优点是，它可以探测 Azure 搜索以确保其可用，并在发生停机时会用户路由到备用搜索服务。 此外，如果通过 Azure 网站路由搜索请求，Azure 流量管理器允许在网站启动但没有 Azure 搜索的情形下进行负载均衡。 下面是利用流量管理器的体系结构的示例。

   ![按区域服务的交叉表（带有中央流量管理器）][3]

## <a name="next-steps"></a>后续步骤
若要深入了解有关定价层和每层的服务限制，请参阅 [Azure 搜索中的服务限制](search-limits-quotas-capacity.md)。

请访问[容量规划](search-capacity-planning.md)，了解有关分区和副本组合的详细信息。

要获得有关性能的更多深入分析，或者要查看如何实现在这篇文章中讨论的优化演示，请观看以下视频︰

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
