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
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923086"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure 认知搜索 - 常见问题解答 (FAQ)

 查找与 Azure 认知搜索有关的概念、代码和方案的常见问题的解答。

## <a name="platform"></a>平台

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure 认知搜索与 DBMS 中的全文搜索有何不同？

Azure 认知搜索支持多个数据源、[针对多种语言的语言分析](/rest/api/searchservice/language-support)、[对有趣和异常数据输入的自定义分析](/rest/api/searchservice/custom-analyzers-in-azure-search)、通过[评分配置文件](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)控制搜索优先级，以及自动提示、搜索词突出显示和分面导航等用户体验功能。 它还包含其他功能，如同义词和丰富的查询语法，但通常来说，这些不属于区别性功能。

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>能否暂停 Azure 认知搜索服务并停止计费？

无法暂停服务。 创建服务时，计算和存储资源是分配给用户单独使用的。 无法按需发布和回收此类资源。

## <a name="indexing-operations"></a>索引操作

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>是否可以移动、备份和还原索引或索引快照？

在开发阶段，你可能想要在搜索服务之间移动索引。 例如，可以使用“基本”或“免费”定价层开发索引，然后将其移到“标准”或更高的层以用于生产。 

或者，你可能想要将索引快照备份到文件中，以后可以使用这些文件来还原该快照。 

使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码可以实现所有这些目的。 

此外，随时可以使用 Azure 认知搜索 REST API [获取索引定义](/rest/api/searchservice/get-index)。

Azure 门户目前不提供内置的索引提取、快照或备份/还原功能。 但是，我们正在考虑在将来的版本中添加备份和还原功能。 若要表达你对此功能的支持，请在 [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index) 中投票。

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>删除后能否还原索引或服务？

不可以。如果删除 Azure 认知搜索索引或服务，将无法予以恢复。 删除 Azure 认知搜索服务会永久删除该服务中的所有索引。 如果删除包含一个或多个 Azure 认知搜索服务的 Azure 资源组，将永久删除所有服务。  

重新创建索引、索引器、数据源和技能集等资源需要通过代码重新创建它们。 

若要重新创建索引，必须为外部源中的数据重新编制索引。 因此，建议在其他数据存储（如 Azure SQL 数据库或 Cosmos DB）保留原始数据的主控副本或备份。

作为替代方法，可以使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码，将索引定义和索引快照备份到一系列 JSON 文件。 以后，可以根据需要使用工具和文件来还原索引。  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>能否从 SQL 数据库副本（适用于 [Azure SQL 数据库索引器](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)）进行索引？

从头开始创建索引时，对使用主要或次要副本作为数据源没有任何限制。 然而，使用增量更新（基于已更改的记录）刷新索引时需要主要副本。 此需求来自于 SQL 数据库，它仅确保主要副本上的更改跟踪。 如果尝试为索引刷新工作负荷使用次要副本，则无法保证获得所有数据。

## <a name="search-operations"></a>搜索操作

### <a name="can-i-search-across-multiple-indexes"></a>能否跨多个索引进行搜索？

不，不支持此操作。 搜索始终限制在单一索引内。

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>能否根据用户身份限制搜索索引访问？

可以使用 `search.in()` 筛选器实现[安全筛选器](./search-security-trimming-for-azure-search.md)。 使用 [Azure Active Directory(AAD) 等标识管理服务](./search-security-trimming-for-azure-search-with-aad.md)可很好地编写筛选器，并基于定义的用户组成员身份裁剪搜索结果。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>为什么确定有效的术语没有匹配项？

最常见的情况是不了解每种查询类型支持不同的搜索行为和语言分析级别。 全文搜索是主要的工作负荷，包括将术语分解成词根形式的语言分析阶段。 查询分析的这种特性拓宽了可能的匹配范围，因为标记化的术语能够匹配更多变体。

但是，通配符查询、模糊查询和正则表达式查询的分析方法与常规词或短语查询不同，并且当查询与单词在搜索索引中的分析形式不匹配时可能会导致再次调用性能不佳。 有关查询解析和分析的详细信息，请参阅[查询体系结构](./search-lucene-query-architecture.md)。

### <a name="my-wildcard-searches-are-slow"></a>通配符搜索速度较慢。

大多数通配符搜索查询（如前缀、模糊和正则表达式）会使用搜索索引中匹配的词在内部重写。 这一扫描搜索索引的额外处理会增加延迟时间。 此外，广泛搜索查询（例如 `a*`），可能会使用许多词重写，因此速度可能会非常慢。 对于高性能通配符搜索，请考虑定义[自定义分析器](/rest/api/searchservice/custom-analyzers-in-azure-search)。

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>为什么每个搜索词的搜索优先级是一个常数，或都等于 1.0？

默认情况下，根据[匹配术语的统计属性](search-lucene-query-architecture.md#stage-4-scoring)对搜索结果打分，在结果集中从高到低排序。 但某些查询类型（通配符、前缀、正则表达式）始终会给文档总评分贡献一个常数分数。 此行为是设计使然。 Azure 认知搜索设置一个常量评分后，便可以在结果中包含通过查询扩展找到的匹配项，且不会影响排名。

例如，假设在通配符搜索中输入“tour*”，会产生匹配结果“tours”、“tourettes”和“tourmaline”。 由于这些结果的性质，我们无法合理推断出哪些字词的相关性高于其他字词。 因此，在为通配符、前缀和正则表达式类型的查询结果评分时，我们会忽略字词频率。 建立在不完整输入上的搜索结果获得一个常数分数，以避免可能的意外匹配偏差。

## <a name="skillset-operations"></a>技能组操作

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>在引入时，是否有任何提示或技巧可降低认知服务费用？

可以理解的是，你不希望执行的内置技能或自定义技能超出绝对必要的范围，尤其是在处理数百万个要处理的文档的情况下。 考虑到这一点，我们已将“增量扩充”功能添加到技能组执行。 实质上，你可以提供一个缓存位置（blob 存储连接字符串），用于存储“中间”扩充步骤的输出。  这可以使扩充管道智能化，并且使扩充管道只应用那些在修改技能组时必需的扩充。 这自然也会节省索引时间，因为管道会更加高效。

详细了解[增量扩充](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>设计模式

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>实现本地化搜索的最佳方法是什么？

涉及到支持相同索引中的不同区域设置（语言）时，大多数客户会选择专用字段而非集合。 通过区域设置特定字段可分配适当的分析器。 例如，将 Microsoft 法语分析器分配给包含法语字符串的字段。 这样也简化了筛选过程。 如果已知在 fr-fr 页面上启动了一个查询，则可将搜索结果限制为该字段。 或者，创建一个[计分概要文件](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以增加该字段的相关性。 Azure 认知搜索支持 [50 多种语言分析器](./search-language-support.md)，可从中进行选择。

## <a name="next-steps"></a>后续步骤

问题是否与缺少功能相关？ 请在 [User Voice 网站](https://feedback.azure.com/forums/263029-azure-search)上请求该功能。

## <a name="see-also"></a>另请参阅

 [StackOverflow：Azure 认知搜索](https://stackoverflow.com/questions/tagged/azure-search)   
 [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)  
 [什么是 Azure 认知搜索？](search-what-is-azure-search.md)