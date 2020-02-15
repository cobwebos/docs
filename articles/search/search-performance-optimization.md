---
title: 性能规模
titleSuffix: Azure Cognitive Search
description: 了解优化 Azure 认知搜索性能和配置最佳缩放的方法和最佳实践。
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212387"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Azure 认知搜索的性能缩放

本文介绍针对可伸缩性和可用性具有复杂要求的高级方案的最佳实践。

## <a name="start-with-baseline-numbers"></a>从基线编号开始

在进行更大规模的部署工作之前，请确保了解典型的查询负载。 以下准则可帮助您获得基准查询编号。

1. 选取完成典型搜索请求应该花费的目标延迟（或最大时间量）。

1. 使用真实的数据集为搜索服务创建和测试实际工作负荷，以度量这些延迟速率。

1. 以较低的每秒查询数（QPS）开始，然后逐渐增加在测试中执行的数量，直到查询延迟降到预定义的目标以下。 这是一个重要的基准，可帮助你计划应用程序在使用量增长方面的规模。

1. 只要有可能，请重用 HTTP 连接。 如果你使用的是 Azure 认知搜索 .NET SDK，这意味着你应重复使用实例或[SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)实例，如果使用 REST API，则应重用单个 HttpClient。

1. 改变查询请求的实质，使搜索发生在索引的不同部分。 变体非常重要，因为如果持续执行相同的搜索请求，则数据的缓存将开始比使用更完全不同的查询集更好地显示性能。

1. 改变查询请求的结构，以便获得不同类型的查询。 并非每个搜索查询都在同一级别执行。 例如，对于具有大量 facet 和筛选器的查询，文档查找或搜索建议通常更快。 测试组合应包括各种查询，与生产中预期的比率大致相同。  

创建这些测试工作负荷时，Azure 认知搜索有一些特征需要记住：

+ 可以通过一次推送过多的搜索查询来重载服务。 发生这种情况时，会看到 HTTP 503 响应代码。 若要避免在测试过程中出现503，请从各种搜索请求范围开始，以查看添加更多搜索请求时的延迟速率差异。

+ Azure 认知搜索不会在后台运行索引任务。 如果你的服务同时处理查询和索引工作负荷，请考虑将索引作业引入查询测试，或浏览用于在非高峰时段运行索引作业的选项。

> [!Tip]
> 您可以使用负载测试工具来模拟真实的查询负载。 尝试使用[Azure DevOps 进行负载测试，](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops)或使用其中一种[替代方法](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)。

## <a name="scale-for-high-query-volume"></a>高查询量的规模

当查询时间过长或服务开始删除请求时，会对服务进行过载。 如果发生这种情况，可以通过以下两种方式之一解决该问题：

+ **添加副本**  

  每个副本都是数据的副本，允许服务对多个副本的请求进行负载均衡。  所有负载平衡和数据复制均由 Azure 认知搜索进行管理，你可以随时更改为服务分配的副本数。 最大可在一个标准搜索服务中分配 12 个副本，并在一个基本搜索服务中分配 3 个副本。 可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整副本。

+ **在更高的层上创建新服务**  

  Azure 认知搜索分为多个级别，每个[层](https://azure.microsoft.com/pricing/details/search/)提供不同级别的性能。 在某些情况下，你可能会遇到如此多的查询，因为你所在的层不能提供足够的周转时间，甚至在副本被极限时也是如此。在这种情况下，请考虑移动到性能较高的层，如标准 S3 层，这是为具有大量文档和极高查询工作负荷的方案而设计的。

## <a name="scale-for-slow-individual-queries"></a>用于慢速单个查询的缩放

高延迟率的另一个原因是，需要花费很长时间才能完成的单个查询。 在这种情况下，添加副本将不会有帮助。 可能有两个可能的选项可帮助包括：

+ **增加分区**

  分区跨额外的计算资源拆分数据。 两个分区将数据拆分为半个，第三个分区将数据拆分为三分之二，依此类推。 一种很好的副作用是，速度较慢的查询有时会由于并行计算而变得更快。 我们注意到了并行度较低的查询（如与多个文档匹配的查询），或提供大量文档的计数的方面。 由于需要大量计算来对文档的相关性进行评分，或者要对文档的数量进行计数，因此添加额外的分区有助于更快地完成查询。  
   
  在标准搜索服务中最多可以有12个分区，在基本搜索服务中最多可以有1个分区。 可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整分区。

+ **限制高基数字段**

  高基数字段包含具有大量唯一值的可查找或可筛选字段，因此，在计算结果时，会消耗大量资源。 例如，将 "产品 ID" 或 "说明" 字段设置为 "可查找/可筛选" 将计为高基数，因为文档中的大部分值都是唯一的。 只要有可能，请限制高基数字段的数量。

+ **增加搜索层**  

  向上移动到更高版本的 Azure 认知搜索可以通过另一种方法提高速度慢的查询的性能。 每个更高的层提供更快的 Cpu 和更多的内存，这两者都对查询性能产生积极影响。

## <a name="scale-for-availability"></a>可用性缩放

副本不仅有助于减少查询延迟，还可以实现高可用性。 借助单个副本，应该可以预期周期性的停机时间，因为在软件更新之后，或针对其他将执行的维护活动后，服务器会周期性停机。 因此，请务必考虑应用程序是否需要搜索（查询）以及写入（编制索引事件）的高可用性。 Azure 认知搜索为所有付费搜索产品/服务提供 SLA 选项，其中包含以下属性：

+ 对于只读工作负荷（查询），需要有两个副本才能实现高可用性

+ 用于读写工作负荷（查询和索引）的高可用性的三个或更多副本

有关此内容的详细信息，请访问[Azure 认知搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由于副本是你的数据的副本，因此具有多个副本将允许 Azure 认知搜索针对一个副本执行计算机重启和维护，同时查询执行将继续在其他副本上执行。 相反，如果将副本删除，则会导致查询性能下降，前提是这些副本是未充分利用的资源。

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>地理分布式工作负荷和异地冗余的规模

对于地理上分散的工作负荷，位于主机数据中心以外的用户会有更高的延迟费率。 一项缓解措施是在区域中预配多个搜索服务，使其更接近于这些用户。

Azure 认知搜索当前不提供跨区域的异地复制 Azure 认知搜索索引的自动方法，但有一些方法可用于使此过程易于实现和管理。 我们会在下面几节介绍这些技巧。

地理分布式一组搜索服务的目标是让两个或更多的索引在两个或更多个区域中可用，在这种情况下，用户会路由到 Azure 认知搜索服务，该服务提供的延迟时间最低，如以下示例所示：

   ![按区域的服务的交叉表][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>跨多个服务保持数据同步

有两个选项可让分布式搜索服务保持同步，其中包括使用[azure 认知搜索索引器](search-indexer-overview.md)或推送 API （也称为[azure 认知搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)）。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引器更新多个服务的内容

如果已对一个服务使用索引器，则可以将另一个服务上的第二个索引器配置为使用同一数据源对象，并从同一位置提取数据。 每个区域中的每个服务都有自己的索引器和目标索引（搜索索引不是共享的，这意味着，数据是重复的），但每个索引器都引用同一个数据源。

下面是体系结构的大致外观。

   ![具有分布式索引器和服务组合的单一数据源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST Api 将内容更新推送到多个服务

如果使用 Azure 认知搜索 REST API 来[推送 azure 认知搜索索引中的内容](https://docs.microsoft.com/rest/api/searchservice/update-index)，则只要需要更新，就可以通过将更改推送到所有搜索服务来使各种搜索服务保持同步。 在代码中，请确保处理对一个搜索服务的更新失败但对其他搜索服务成功的情况。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理器

使用[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)，你可以将请求路由到多个地理位置的网站，然后多个搜索服务对其进行支持。 流量管理器的一个优点是，它可以探测 Azure 认知搜索以确保它可用，并在发生故障时将用户路由到备用搜索服务。 此外，如果您通过 Azure 网站路由搜索请求，Azure 流量管理器允许您对网站已启动但不是 Azure 认知搜索的情况进行负载平衡。 下面是利用流量管理器的体系结构的示例。

   ![按区域服务的交叉表（带有中央流量管理器）][3]

## <a name="next-steps"></a>后续步骤

若要详细了解每种定价层和服务限制，请参阅[服务限制](search-limits-quotas-capacity.md)。 有关分区和副本组合的详细信息，请参阅[规划容量](search-capacity-planning.md)。

有关本文所述技术的性能和演示的讨论，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
