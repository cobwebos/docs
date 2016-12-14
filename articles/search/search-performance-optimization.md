---
title: "Azure 搜索性能和优化注意事项 | Microsoft Docs"
description: "优化 Azure 搜索性能，配置最佳规模"
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: liamca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 733aa17ef53cac654cbd2bc7ceed7059b07a0f13


---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure 搜索性能和优化注意事项
强大的搜索体验是许多移动和 Web 应用程序成功的关键。 无论是房地产，还是旧车市场，或者是联机目录，快速搜索及其相关结果都将影响客户体验。 本文档旨在帮助你发现如何充分利用 Azure 搜索的最佳实践，尤其是对于可伸缩性、多语言支持或自定义排名有复杂要求的高级方案。  此外，本文档概述了内部运作，并涵盖了在实际客户应用中可有效工作的方法。

## <a name="performance-and-scale-tuning-for-search-services"></a>适用于搜索服务的性能和规模优化
我们全都习惯于诸如 Bing 和 Google 等搜索引擎及其提供的高性能。  因此，当客户使用你的搜索支持的 Web 或移动应用程序时，他们将期望得到相似的性能特征。  针对搜索性能进行优化时，最佳方法之一是专注于优化延迟，也就是查询完成并返回结果所花费的时间。  在针对搜索延迟进行优化时，最重要的是：

1. 选取完成典型搜索请求应该花费的目标延迟（或最大时间量）。
2. 针对你的搜索服务，使用现实数据集来创建和测试工作负载，以测量这些延迟速率。
3. 从较小的每秒查询数量 (QPS) 开始，并不断增加在测试中执行的数量，直到查询延迟降到定义的目标延迟之下为止。  这是一个重要的基准，可帮助你计划应用程序在使用量增长方面的规模。
4. 只要有可能，请重用 HTTP 连接。  如果你使用 Azure 搜索 .NET SDK，这意味着你应该重用某个实例或 [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) 实例，并且如果你使用 REST API，就应该重用单个 HttpClient。

在创建这些测试工作负荷时，需要记住 Azure 搜索的下面这些特征︰

1. 它有可能会一次推送许多搜索查询，那样的话你的 Azure 搜索服务中可用的资源将会不堪重负。  发生这种情况时，你将看到 HTTP 503 响应代码。  出于此原因，最好从不同范围的搜索请求开始，以查看在你添加更多的搜索请求时延迟速率中的差异。
2. 将内容上载到 Azure 搜索将影响 Azure 搜索服务的整体性能和延迟。  如果你希望在用户执行搜索时发送数据，那么将此工作负荷考虑到你的测试中是很重要的。
3. 不是每个搜索查询都将在相同的性能级别执行。  例如，与包含大量平面和筛选器的查询的相比，文档查找或搜索建议的执行速度要更快一些。  在构建测试时，最好将你希望看到的各种查询都考虑在内。  
4. 搜索请求的变体很重要，因为如果你不断执行相同的搜索请求，那么比起包含一个更加迥然不同的查询集，数据的缓存将开始使性能看起变得更好。

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) 是真正适合执行基准测试的好方法，因为在你需要针对 Azure 搜索执行查询并启用请求的并行化时，它允许你执行 HTTP 请求。
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>调整 Azure 搜索的规模以获得较高的查询速率和受限制的请求
当你收到太多受限的请求或从某个增加的查询负荷已超过你的目标延迟速率时，你可以尝试使用以下两种方法之一来降低延迟速率︰

1. **增加副本数︰**副本就像你的数据副本，它允许 Azure 搜索对面向多个副本的请求进行负载平衡。  在副本之间的所有负载平衡和数据的复制都是由 Azure 搜索管理的，你可以随时更改为你的服务分配的副本数量。  你最大可在一个标准搜索服务中分配 12 个副本，并在一个基本搜索服务中分配 3 个副本。  可以在 [Azure 门户](search-create-service-portal.md)中，或使用 [Azure 搜索管理 API](search-get-started-management-api.md) 来调整副本数。
2. **增加搜索层︰**Azure 搜索来自[许多层](https://azure.microsoft.com/pricing/details/search/)，其中每个层都提供不同级别的性能。  在某些情况下，你可能有太多查询，即使在副本数超出限制时，你所在的层仍无法提供足够的低延迟速率。  在这种情况下，你可能需要考虑利用其中一个较高的搜索层，比如 Azure 搜索 S3 层，该层非常适合包含大量文档和极高查询工作负荷的方案。

## <a name="scaling-azure-search-for-slow-individual-queries"></a>针对速度慢的各个查询调整 Azure 搜索的规模
延迟速率很慢的另一个原因是，单个查询花费太长时间才完成。  在这种情况下，增加副本数量将不会提高延迟速率。  这种情况下，可以使用以下两个选项︰

1. **增加分区数**：分区是一种机制，可在额外资源之间拆分你的数据。  由于此原因，当你添加第二个分区时，你的数据会拆分为两份。  第三个分区会将你的索引拆分为三个，以此类推。这也会产生这样的效果，即在某些情形下，由于计算的并行化，速度慢的查询执行起来速度更快。  我们还见过一些例子，这种并行化特别适合具有低选择性查询的那些查询。  这包括匹配许多文档的查询，或者当分类需要提供对大量文档的计数时。  由于对文档的相关性进行评分或计算文档的数量需要大量的计算，因此，添加额外的分区可以帮助提供更多的计算能力。  
   
   在标准搜索服务中最多可以有 12 个分区，在基本搜索服务中最多可以有 1 个分区。  可以在 [Azure 门户](search-create-service-portal.md)中或使用 [Azure 搜索管理 API](search-get-started-management-api.md) 来调整分区。
2. **限制高基数字段︰**高基数字段包含具有大量唯一值的可查找或可筛选的字段，因此，会获取要在其上计算结果的大量资源。   例如，将“产品 ID”或“描述”字段设置为可查找的/可筛选的会导致高基数，因为各个文档中的大多数值都是唯一的。 只要有可能，请限制高基数字段的数量。
3. **增加搜索层︰**另一种方法是，向上移动到更高的 Azure 搜索层，可以为较慢的查询改进性能。  每个较高的层还会提供更快的 CPU 和更多的内存，这会对查询性能产生积极的影响。

## <a name="scaling-for-availability"></a>调整可用性的规模
副本不仅可以帮助缩短查询延迟，而且还可以允许高可用性。  借助单个副本，你应该可以预期周期性的停机时间，因为在软件更新之后，或针对其他将执行的维护活动后，服务器会周期性停机。  因此，请务必考虑你的应用程序是否需要搜索（查询）以及写入（编制索引事件）的高可用性。  Azure 搜索在具有以下属性的所有付费搜索产品/服务上提供 SLA 选项：

* 针对只读工作负荷（查询），需要有 2 个副本才可达到高可用性
* 针对读写工作负荷（查询和索引），需要有 3 个或更多副本才可达到高可用性

有关这方面的更多详细信息，请访问 [Azure 搜索服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由于副本是你的数据副本，因此，拥有多个副本可让 Azure 搜索同时针对一个副本执行计算机重新启动和维护，同时可继续针对其他副本执行查询。  出于此原因，如果查询现在必须针对少了一个副本的数据执行，则你还将需要考虑此停机可能对这些查询产生怎样的影响。

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>调整地理上分散的工作负荷的规模，并提供异地冗余
对于地理上分散的工作负荷，你会发现，距离托管你的 Azure 搜索服务的数据中心较远的用户将具有较高延迟速率。  由于此原因，让多个搜索服务位于更靠近这些用户的区域中通常很重要。  Azure 搜索当前不提供自动化方法来跨区域异地复制 Azure 搜索索引，但有一些技巧，可以用来使此过程很容易实现和管理。 我们将在下面几节介绍这些技巧。

地理上分散的搜索服务集的目标是，让两个或更多索引在两个或更多区域中可用，在这些区域中用户将被路由到 Azure 搜索服务，以提供最低延迟，如下面示例中所示：

   ![按区域的服务的交叉表][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>让数据在多个 Azure 搜索服务之间保持同步
有两个选项可让你的分布式搜索服务保持同步，包括使用 [Azure 搜索索引器](search-indexer-overview.md)或推送 API（也称为 [Azure 搜索 REST API](https://msdn.microsoft.com/library/dn798935.aspx)。  

### <a name="azure-search-indexers"></a>Azure 搜索索引器
如果你正在使用 Azure 搜索索引器，那么就已经在从诸如 Azure SQL DB 或 DocumentDB 等中央数据存储导入数据更改。 当创建新的搜索服务时，针对指向此相同数据存储的服务，你只需要创建一个新 Azure 搜索索引器即可。 这样一来，每当数据存储中出现新的更改，它们接着就将通过各种索引器被编入索引。  

下面的示例显示该体系结构的样子。

   ![具有分布式索引器和服务组合的单一数据源][2]

### <a name="push-api"></a>推送 API
如果你正在使用 Azure 搜索推送 API [更新你的 Azure 搜索索引中的内容](https://msdn.microsoft.com/library/dn798930.aspx)，那么可以在需要更新时，可以通过将更改推送到所有搜索服务，以保持你的各种搜索服务同步。  执行此操作时，请务必确保会处理某一个搜索服务的更新失败和一个或多个更新成功的情形。

## <a name="leveraging-azure-traffic-manager"></a>利用 Azure 流量管理器
通过使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)，可以将请求路由到多个地理定位的网站，而这些网站由多个 Azure 搜索服务支持。  流量管理器的一个优点是，它可以探测 Azure 搜索以确保其可用，并在发生停机时将用户路由到备用搜索服务。  此外，如果你通过 Azure 网站路由搜索请求，Azure 流量管理器允许你在网站启动但没有 Azure 搜索的情形下进行负载平衡。  下面是利用流量管理器的体系结构的示例。

   ![按区域服务的交叉表（带有中央流量管理器）][3]

## <a name="monitoring-performance"></a>监视性能
Azure 搜索提供通过[搜索流量分析 (STA)](search-traffic-analytics.md) 分析和监视你的服务的性能的能力。 通过 STA，你可以选择性地将单独的搜索操作以及聚合指标记录到 Azure 存储帐户中，然后可以进行处理以便分析或在 Power BI 中进行可视化。  通过使用 STA 指标，你可以查看性能统计信息，如查询或查询响应时间的平均数。  此外，通过操作日志记录，你可以向下钻取到解特定搜索操作的详细信息。

STA 是一个重要的工具，可用于从 Azure 搜索的角度了解延迟速率。  由于记录的查询性能指标基于在 Azure 搜索中完全处理一个查询所花费的时间（从请求它的时间到发送时间），因此，你能够使用此工具来确定延迟问题是来自 Azure 搜索服务端还是来自服务外部，如来自网络延迟。  

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



<!--HONumber=Nov16_HO3-->


