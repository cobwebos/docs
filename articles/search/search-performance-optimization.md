---
title: 性能规模
titleSuffix: Azure Cognitive Search
description: 了解优化 Azure 认知搜索性能和配置最佳规模的技术与最佳做法。
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212387"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>在 Azure 认知搜索上扩展性能

本文介绍高级方案的最佳做法，以及可伸缩性和可用性方面的复杂要求。

## <a name="start-with-baseline-numbers"></a>从基线编号开始

在进行更大的部署工作之前，请确保您知道典型的查询负载是什么样子。 以下准则可帮助你制定出基准查询数字。

1. 选取完成典型搜索请求应该花费的目标延迟（或最大时间量）。

1. 使用真实数据集根据搜索服务创建和测试实际工作负载，以测量这些延迟率。

1. 从每秒少量查询 （QPS） 开始，然后逐渐增加在测试中执行的数量，直到查询延迟低于预定义目标。 这是一个重要的基准，可帮助你计划应用程序在使用量增长方面的规模。

1. 只要有可能，请重用 HTTP 连接。 如果使用 Azure 认知搜索 .NET SDK，这意味着你应该重用某个实例或 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 实例，并且如果使用 REST API，就应该重用单个 HttpClient。

1. 差异化查询请求的主旨，以针对索引的不同组成部分执行搜索。 差异化很重要，因为如果不断执行相同的搜索请求，那么比起包含一个更加迥然不同的查询集，数据的缓存将开始使性能看起变得更好。

1. 差异化查询请求的结构，以获取不同类型的查询。 并非每个搜索查询都在相同的级别执行。 例如，与包含大量平面和筛选器的查询的相比，文档查找或搜索建议的执行速度要更快一些。 测试组成部分应包括各种查询，各查询的比例应与生产环境中预期使用的比例大致相同。  

在创建这些测试工作负荷时，需要记住 Azure 认知搜索的下面这些特征：

+ 一次性推送过多的搜索查询可能会使服务过载。 发生这种情况时，会看到 HTTP 503 响应代码。 为了避免在测试期间出现 503 代码，请从不同范围的搜索请求开始，以查看在添加更多的搜索请求时延迟速率中的差异。

+ Azure 认知搜索不会在后台运行索引编制任务。 如果服务同时处理查询和索引编制工作负荷，请考虑到这一点：将索引编制作业引入查询测试，或者探讨在非高峰期运行索引编制作业的选项。

> [!Tip]
> 您可以使用负载测试工具模拟真实的查询负载。 尝试[使用 Azure DevOps 进行负载测试](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops)或使用这些[替代方法](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)之一。

## <a name="scale-for-high-query-volume"></a>高查询量的缩放

当查询时间过长或服务开始丢弃请求时，服务会负担过重。 如果发生这种情况，您可以通过以下两种方式之一解决问题：

+ **添加副本**  

  每个副本都是数据的副本，允许服务对多个副本加载平衡请求。  所有数据的负载平衡和复制都由 Azure 认知搜索管理，您可以随时更改为服务分配的副本数。 最大可在一个标准搜索服务中分配 12 个副本，并在一个基本搜索服务中分配 3 个副本。 可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整副本。

+ **在更高级别创建新服务**  

  Azure 认知搜索具有[多个层](https://azure.microsoft.com/pricing/details/search/)，每个层都提供不同级别的性能。 在某些情况下，您可能有如此多的查询，以至于您所打开的层无法提供足够的周转，即使副本已满。在这种情况下，请考虑迁移到性能更高的层，例如标准 S3 层，专为具有大量文档和极高查询工作负载的方案而设计。

## <a name="scale-for-slow-individual-queries"></a>缩放以进行慢速单个查询

延迟率增大的另一个原因是，完成单个查询花费了太长的时间。 在这种情况下，添加副本不起作用。 有作用的两个可能选项包括：

+ **增加分区**

  分区将数据拆分到额外的计算资源中。 两个分区将数据分成两半，第三个分区将其拆分为三分之二，依此类推。 一个有利的副作用是，由于并行计算，较慢的查询有时执行速度更快。 我们在低选择性查询（例如，匹配许多文档的查询，或提供大量文档的计数的分面）上注意到了并行化效果。 由于为文档相关性评分或统计文档数目需要消耗大量的计算资源，添加额外的分区有助于加快查询的完成速度。  
   
  标准搜索服务中最多可以有 12 个分区，基本搜索服务中最多可以有 1 个分区。 可以从 [Azure 门户](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 调整分区。

+ **限制高基数字段**

  高基数字段包含具有大量唯一值的可查找或可筛选字段，因此，会在计算结果时会消耗大量的资源。 例如，将“产品 ID”或“描述”字段设置为可查找/可筛选会导致高基数，因为大多数值在不同的文档中是唯一的。 只要有可能，请限制高基数字段的数量。

+ **增加搜索层**  

  另一种方法是，向上移动到更高的 Azure 认知搜索层，可以为较慢的查询改进性能。 每个更高的层提供更快的 CPU 和更多的内存，这会对查询性能产生积极的影响。

## <a name="scale-for-availability"></a>扩展以寻求可用性

副本不仅有助于减少查询延迟，还允许高可用性。 借助单个副本，应该可以预期周期性的停机时间，因为在软件更新之后，或针对其他将执行的维护活动后，服务器会周期性停机。 因此，请务必考虑应用程序是否需要搜索（查询）以及写入（编制索引事件）的高可用性。 Azure 认知搜索在具有以下属性的所有付费搜索产品/服务上提供 SLA 选项：

+ 对于只读工作负荷（查询），需要有两个副本才能实现高可用性

+ 三个或更多副本，用于高可用性读写工作负载（查询和索引）

有关这方面的更多详细信息，请访问 [Azure 认知搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由于副本是数据的副本，因此具有多个副本允许 Azure 认知搜索对一个副本执行计算机重新启动和维护，而其他副本的查询执行将继续。 相反，如果删除副本，将会导致查询性能下降，并认为这些副本是未充分利用的资源。

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>扩展地理分布工作负载和异地冗余

对于地理分布工作负载，远离主机数据中心的用户的延迟率更高。 一种缓解措施是在与这些用户更靠近的区域中预配多个搜索服务。

Azure 认知搜索当前不提供自动化方法来跨区域异地复制 Azure 认知搜索索引，但有一些技巧，可以用来使此过程很容易实现和管理。 我们会在下面几节介绍这些技巧。

地理分布的搜索服务集的目标是在两个或多个区域中提供两个或多个索引，其中用户路由到 Azure 认知搜索服务，该服务提供最低延迟，如以下示例所示：

   ![按区域的服务的交叉表][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>使数据跨多个服务保持同步

有两个选项用于保持分布式搜索服务同步，包括使用[Azure 认知搜索索引器](search-indexer-overview.md)或推送 API（也称为[Azure 认知搜索 REST API）。](https://docs.microsoft.com/rest/api/searchservice/)  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引器更新多个服务中的内容

如果已在一个服务中使用索引器，可以在另一个服务中配置另一个索引器，以使用相同的数据源对象，并从相同的位置提取数据。 每个区域中的每个服务具有自身的索引器和目标索引（搜索索引不会共享，这意味着数据是重复的），但每个索引器引用相同的数据源。

下面是该体系结构的概要视图。

   ![具有分布式索引器和服务组合的单一数据源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST API 在多个服务中推送内容更新

如果使用 Azure 认知搜索 REST API [推送 Azure 认知搜索索引中的内容](https://docs.microsoft.com/rest/api/searchservice/update-index)，则可以在需要更新时，可以通过将更改推送到所有搜索服务，以保持各种搜索服务同步。 在代码中，请确保处理好这种情况：对一个搜索服务的更新失败，但对于其他搜索服务成功。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理器

通过使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)，可以将请求路由到多个地理定位的网站，而这些网站由多个搜索服务支持。 流量管理器的一个优点是，它可以探测 Azure 认知搜索以确保其可用，并在发生停机时会用户路由到备用搜索服务。 此外，如果通过 Azure 网站路由搜索请求，Azure 流量管理器允许在网站启动但没有 Azure 认知搜索的情形下进行负载均衡。 下面是利用流量管理器的体系结构的示例。

   ![按区域服务的交叉表（带有中央流量管理器）][3]

## <a name="next-steps"></a>后续步骤

要了解有关每个定价层和服务限制的更多详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。 有关有关分区和副本组合的详细信息，请参阅[计划容量](search-capacity-planning.md)。

有关本文中讨论的技术的性能和演示的讨论，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
