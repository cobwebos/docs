---
title: 常见问题 (FAQ)
titleSuffix: Azure Cognitive Search
description: 获取有关 Microsoft Azure 认知搜索服务（Microsoft Azure 上的云托管搜索服务）的常见问题的解答。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 520699b81024de9491f34263f16872428ddbd487
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618042"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure 认知搜索 - 常见问题解答 (FAQ)

 查找与 Azure 认知搜索有关的概念、代码和方案的常见问题的解答。

## <a name="platform"></a>Platform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure 认知搜索与 DBMS 中的全文搜索有何不同？

Azure 认知搜索支持多个数据源、[针对多种语言的语言分析](https://docs.microsoft.com/rest/api/searchservice/language-support)、[对有趣和异常数据输入的自定义分析](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)、通过[评分配置文件](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)控制搜索优先级，以及自动提示、搜索词突出显示和分面导航等用户体验功能。 它还包含其他功能，如同义词和丰富的查询语法，但通常来说，这些不属于区别性功能。

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>能否暂停 Azure 认知搜索服务并停止计费？

无法暂停服务。 创建服务时，计算和存储资源是分配给用户单独使用的。 无法按需发布和回收此类资源。

## <a name="indexing-operations"></a>索引操作

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>是否可以移动、备份和还原索引或索引快照？

在开发阶段，你可能想要在搜索服务之间移动索引。 例如，可以使用“基本”或“免费”定价层开发索引，然后将其移到“标准”或更高的层以用于生产。 

或者，你可能想要将索引快照备份到文件中，以后可以使用这些文件来还原该快照。 

使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码可以实现所有这些目的。 

此外，随时可以使用 Azure 认知搜索 REST API [获取索引定义](https://docs.microsoft.com/rest/api/searchservice/get-index)。

Azure 门户目前不提供内置的索引提取、快照或备份/还原功能。 但是，我们正在考虑在将来的版本中添加备份和还原功能。 如果要显示您对此功能的支持，请对[用户语音](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)进行投票。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>删除后能否还原索引或服务？

不可以。如果删除 Azure 认知搜索索引或服务，将无法予以恢复。 删除 Azure 认知搜索服务会永久删除该服务中的所有索引。 如果删除包含一个或多个 Azure 认知搜索服务的 Azure 资源组，将永久删除所有服务。  

重新创建资源（如索引、索引器、数据源和技能集）需要从代码重新创建这些资源。 

若要重新创建索引，必须为外部源中的数据重新编制索引。 因此，建议在其他数据存储（如 Azure SQL 数据库或 Cosmos DB）保留原始数据的主控副本或备份。

作为替代方法，可以使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码，将索引定义和索引快照备份到一系列 JSON 文件。 以后，可以根据需要使用工具和文件来还原索引。  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>能否从 SQL 数据库副本（适用于 [Azure SQL 数据库索引器](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)）进行索引？

从头开始创建索引时，对使用主要或次要副本作为数据源没有任何限制。 然而，使用增量更新（基于已更改的记录）刷新索引时需要主要副本。 此需求来自于 SQL 数据库，它仅确保主要副本上的更改跟踪。 如果尝试为索引刷新工作负荷使用次要副本，则无法保证获得所有数据。

## <a name="search-operations"></a>搜索操作

### <a name="can-i-search-across-multiple-indexes"></a>能否跨多个索引进行搜索？

不，不支持此操作。 搜索始终限制在单一索引内。

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>能否根据用户身份限制搜索索引访问？

可以使用 `search.in()` 筛选器实现[安全筛选器](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)。 使用 [Azure Active Directory(AAD) 等标识管理服务](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad)可很好地编写筛选器，并基于定义的用户组成员身份裁剪搜索结果。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>为什么确定有效的术语没有匹配项？

最常见的情况是不了解每种查询类型支持不同的搜索行为和语言分析级别。 全文搜索是主要的工作负荷，包括将术语分解成词根形式的语言分析阶段。 查询分析的这种特性拓宽了可能的匹配范围，因为标记化的术语能够匹配更多变体。

但是，通配符查询、模糊查询和正则表达式查询的分析方法与常规词或短语查询不同，并且当查询与单词在搜索索引中的分析形式不匹配时可能会导致再次调用性能不佳。 有关查询解析和分析的详细信息，请参阅[查询体系结构](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)。

### <a name="my-wildcard-searches-are-slow"></a>通配符搜索速度较慢。

大多数通配符搜索查询（如前缀、模糊和正则表达式）会使用搜索索引中匹配的词在内部重写。 这一扫描搜索索引的额外处理会增加延迟时间。 此外，广泛搜索查询（例如 `a*`），可能会使用许多词重写，因此速度可能会非常慢。 对于高性能通配符搜索，请考虑定义[自定义分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>为什么每个搜索词的搜索优先级是一个常数，或都等于 1.0？

默认情况下，根据[匹配术语的统计属性](search-lucene-query-architecture.md#stage-4-scoring)对搜索结果打分，在结果集中从高到低排序。 但某些查询类型（通配符、前缀、正则表达式）始终会给文档总评分贡献一个常数分数。 这是设计的行为。 Azure 认知搜索设置一个常量评分后，便可以在结果中包含通过查询扩展找到的匹配项，且不会影响排名。

例如，假设通配符搜索中的"tour+"输入在"旅游"、"旅游"和"Tourmaline"上生成匹配项。 由于这些结果的性质，我们无法合理推断出哪些字词的相关性高于其他字词。 因此，在为通配符、前缀和正则表达式类型的查询结果评分时，我们会忽略字词频率。 建立在不完整输入上的搜索结果获得一个常数分数，以避免可能的意外匹配偏差。

## <a name="skillset-operations"></a>技能集操作

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>是否有任何提示或技巧来降低认知服务费用的摄入？

您可以理解的是，您不想执行比绝对必要的更多内置技能或自定义技能，尤其是在处理要处理的数百万个文档时。 考虑到这一点，我们添加了"增量扩充"功能，以进行技能集执行。 从本质上讲，您可以提供一个缓存位置（Blob 存储连接字符串），用于存储"中间"扩充步骤的输出。  这允许浓缩管道是智能的，并且仅应用修改技能集时所需的扩充。 这自然会节省索引时间，因为管道效率会更高。

了解有关[增量扩充](cognitive-search-incremental-indexing-conceptual.md)的更多

## <a name="design-patterns"></a>设计模式

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>实现本地化搜索的最佳方法是什么？

涉及到支持相同索引中的不同区域设置（语言）时，大多数客户会选择专用字段而非集合。 通过区域设置特定字段可分配适当的分析器。 例如，将 Microsoft 法语分析器分配给包含法语字符串的字段。 这样也简化了筛选过程。 如果已知在 fr-fr 页面上启动了一个查询，则可将搜索结果限制为该字段。 或者，创建一个[计分概要文件](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以增加该字段的相关性。 Azure 认知搜索支持 [50 多种语言分析器](https://docs.microsoft.com/azure/search/search-language-support)，可从中进行选择。

## <a name="next-steps"></a>后续步骤

问题是否与缺少功能相关？ 请在 [User Voice 网站](https://feedback.azure.com/forums/263029-azure-search)上请求该功能。

## <a name="see-also"></a>另请参阅

 [堆栈溢出：Azure 认知搜索](https://stackoverflow.com/questions/tagged/azure-search)   
 [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)  
 [Azure 认知搜索是什么？](search-what-is-azure-search.md)
