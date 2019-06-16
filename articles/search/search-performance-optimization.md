---
title: 有关优化性能的部署策略和最佳做法 - Azure 搜索
description: 了解优化 Azure 搜索性能和配置最佳规模的技术与最佳做法。
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 32352a857f0a74dc008dc1ad76b4a5951a36b956
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024550"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>有关在 Azure 搜索中优化性能的部署策略和最佳做法

本文介绍高级方案的最佳做法，以及可伸缩性和可用性方面的复杂要求。 

## <a name="develop-baseline-numbers"></a>制定基准数字
优化搜索性能时，应将重点放在减少查询执行时间上。 为此，需要了解典型查询负载的大致情况。 以下准则可帮助你制定出基准查询数字。

1. 选取完成典型搜索请求应该花费的目标延迟（或最大时间量）。
2. 针对搜索服务，使用现实数据集来创建和测试工作负载，以测量这些延迟速率。
3. 从较小的每秒查询数 (QPS) 开始，并逐渐增加在测试中执行的数量，直到查询延迟降到定义的目标延迟之下为止。 这是一个重要的基准，可帮助你计划应用程序在使用量增长方面的规模。
4. 只要有可能，请重用 HTTP 连接。 如果使用 Azure 搜索 .NET SDK，这意味着你应该重用某个实例或 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 实例，并且如果使用 REST API，就应该重用单个 HttpClient。
5. 差异化查询请求的主旨，以针对索引的不同组成部分执行搜索。 差异化很重要，因为如果不断执行相同的搜索请求，那么比起包含一个更加迥然不同的查询集，数据的缓存将开始使性能看起变得更好。
6. 差异化查询请求的结构，以获取不同类型的查询。 并非每个搜索查询都在相同的级别执行。 例如，与包含大量平面和筛选器的查询的相比，文档查找或搜索建议的执行速度要更快一些。 测试组成部分应包括各种查询，各查询的比例应与生产环境中预期使用的比例大致相同。  

在创建这些测试工作负荷时，需要记住 Azure 搜索的下面这些特征︰

+ 一次性推送过多的搜索查询可能会使服务过载。 发生这种情况时，会看到 HTTP 503 响应代码。 为了避免在测试期间出现 503 代码，请从不同范围的搜索请求开始，以查看在添加更多的搜索请求时延迟速率中的差异。

+ Azure 搜索不会在后台运行索引编制任务。 如果服务同时处理查询和索引编制工作负荷，请考虑到这一点：将索引编制作业引入查询测试，或者探讨在非高峰期运行索引编制作业的选项。

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) 是真正适合执行基准测试的好方法，因为在需要针对 Azure 搜索执行查询并启用请求的并行化时，它允许执行 HTTP 请求。
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>针对高查询量和限制请求进行缩放
收到太多受限的请求或从某个增加的查询负荷已超过目标延迟率时，可以尝试使用以下两种方法之一来降低延迟率：

1. **增加副本数：** 副本就像数据副本，它允许 Azure 搜索对面向多个副本的请求进行负载均衡。  在副本之间的所有负载均衡和数据的复制都是由 Azure 搜索管理的，可以随时更改为服务分配的副本数量。  最大可在一个标准搜索服务中分配 12 个副本，并在一个基本搜索服务中分配 3 个副本。 可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整副本。
2. **增加搜索层：** Azure 搜索来自[许多层](https://azure.microsoft.com/pricing/details/search/)，其中每个层都提供不同级别的性能。  在某些情况下，可能有太多查询，即使在副本数超出限制时，所在的层仍无法提供足够的低延迟速率。在这种情况下，可能需要考虑利用其中一个较高的搜索层，比如 Azure 搜索 S3 层，该层非常适合包含大量文档和极高查询工作负荷的方案。

## <a name="scaling-for-slow-individual-queries"></a>针对单个查询速度缓慢进行缩放
延迟率增大的另一个原因是，完成单个查询花费了太长的时间。 在这种情况下，添加副本不起作用。 有作用的两个可能选项包括：

1. **增加分区数**：分区是一种机制，可在额外资源之间拆分数据。 添加第二个分区会将数据拆分为两部分，添加第三个分区会将数据拆分为三部分，依此类推。 一个有利的副作用是，由于并行计算，较慢的查询有时执行速度更快。 我们在低选择性查询（例如，匹配许多文档的查询，或提供大量文档的计数的分面）上注意到了并行化效果。 由于为文档相关性评分或统计文档数目需要消耗大量的计算资源，添加额外的分区有助于加快查询的完成速度。  
   
   在标准搜索服务中最多可以有 12 个分区，在基本搜索服务中最多可以有 1 个分区。  可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整分区。

2. **限制高基数字段：** 高基数字段包含具有大量唯一值的可查找或可筛选字段，因此，会在计算结果时会消耗大量的资源。 例如，将“产品 ID”或“描述”字段设置为可查找/可筛选会导致高基数，因为大多数值在不同的文档中是唯一的。 只要有可能，请限制高基数字段的数量。

3. **增加搜索层：** 另一种方法是，向上移动到更高的 Azure 搜索层，可以为较慢的查询改进性能。 每个更高的层提供更快的 CPU 和更多的内存，这会对查询性能产生积极的影响。

## <a name="scaling-for-availability"></a>调整可用性的规模
副本不仅可以帮助缩短查询延迟，而且还可以允许高可用性。 借助单个副本，应该可以预期周期性的停机时间，因为在软件更新之后，或针对其他将执行的维护活动后，服务器会周期性停机。  因此，请务必考虑应用程序是否需要搜索（查询）以及写入（编制索引事件）的高可用性。 Azure 搜索在具有以下属性的所有付费搜索产品/服务上提供 SLA 选项：

* 针对只读工作负荷（查询），需要有 2 个副本才可达到高可用性
* 针对读写工作负荷（查询和索引），需要有 3 个或更多副本才可达到高可用性

有关这方面的更多详细信息，请访问 [Azure 搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由于副本是数据的副本，因此，使用多个副本可让 Azure 搜索针对一个副本执行计算机重新启动和维护，同时可继续针对其他副本执行查询。 相反，如果删除副本，将会导致查询性能下降，并认为这些副本是未充分利用的资源。

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>针对地理分散的工作负荷和异地冗余进行缩放
对于地理分散的工作负荷，与托管 Azure 搜索的数据中心距离较远的用户将遇到更高的延迟率。 一种缓解措施是在与这些用户更靠近的区域中预配多个搜索服务。 Azure 搜索当前不提供自动化方法来跨区域异地复制 Azure 搜索索引，但有一些技巧，可以用来使此过程很容易实现和管理。 我们会在下面几节介绍这些技巧。

地理分散的搜索服务集的目标是，让两个或更多索引在两个或更多区域中可用，在这些区域中用户会被路由到 Azure 搜索服务，以提供最低延迟，如以下示例中所示：

   ![按区域的服务的交叉表][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>让数据在多个 Azure 搜索服务之间保持同步
有两个选项可让分布式搜索服务保持同步，包括使用 [Azure 搜索索引器](search-indexer-overview.md)或推送 API（也称为 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引器更新多个服务中的内容

如果已在一个服务中使用索引器，可以在另一个服务中配置另一个索引器，以使用相同的数据源对象，并从相同的位置提取数据。 每个区域中的每个服务具有自身的索引器和目标索引（搜索索引不会共享，这意味着数据是重复的），但每个索引器引用相同的数据源。

下面是该体系结构的概要视图。

   ![具有分布式索引器和服务组合的单一数据源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST API 在多个服务中推送内容更新
如果使用 Azure 搜索 REST API [推送 Azure 搜索索引中的内容](https://docs.microsoft.com/rest/api/searchservice/update-index)，则可以在需要更新时，可以通过将更改推送到所有搜索服务，以保持各种搜索服务同步。 在代码中，请确保处理好这种情况：对一个搜索服务的更新失败，但对于其他搜索服务也失败。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理器
通过使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)，可以将请求路由到多个地理定位的网站，而这些网站由多个 Azure 搜索服务支持。 流量管理器的一个优点是，它可以探测 Azure 搜索以确保其可用，并在发生停机时会用户路由到备用搜索服务。 此外，如果通过 Azure 网站路由搜索请求，Azure 流量管理器允许在网站启动但没有 Azure 搜索的情形下进行负载均衡。 下面是利用流量管理器的体系结构的示例。

   ![按区域服务的交叉表（带有中央流量管理器）][3]

## <a name="monitor-performance"></a>监视性能
Azure 搜索提供通过[搜索流量分析](search-traffic-analytics.md) 分析和监视服务的性能的能力。 启用此功能并将检测添加到客户端应用时，可以选择性地将单独的搜索操作以及聚合指标记录到 Azure 存储帐户中，然后可以进行处理以便分析或在 Power BI 中进行可视化。 以此方式捕获的指标提供性能统计信息，如查询或查询响应时间的平均数。 此外，通过操作日志记录，可以向下钻取到解特定搜索操作的详细信息。

使用流量分析可以从 Azure 搜索的角度了解延迟率。 由于记录的查询性能指标基于在 Azure 搜索中完全处理一个查询所花费的时间（从请求它的时间到发送时间），因此，能够使用此工具来确定延迟问题是来自 Azure 搜索服务端还是来自服务外部，如来自网络延迟。  

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
