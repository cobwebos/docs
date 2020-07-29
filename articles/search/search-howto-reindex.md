---
title: 重新生成搜索索引
titleSuffix: Azure Cognitive Search
description: 添加新元素、更新现有元素或文档，或删除完整重新生成或部分索引中的过时文档，以刷新 Azure 认知搜索索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 96177686e78a0595ac4ad49b9969b22d862facd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85051728"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中重新生成索引

本文介绍如何重新生成 Azure 认知搜索索引、需要重新生成的情况以及有关如何减轻重新生成操作对正在进行的查询请求的影响的建议。

“重新生成”是指删除并重新创建与索引关联的物理数据结构，包括所有基于字段的反向索引  。 在 Azure 认知搜索中，无法删除并重新创建各个字段。 若要重新生成索引，必须删除所有字段存储、基于现有或修订的索引架构重新创建，然后使用推送到索引或从外部源提取的数据重新填充。 

当您循环访问索引设计时，通常会在开发过程中重新生成索引，但您可能还需要重新生成一个生产级别的索引来容纳结构更改，如添加复杂类型或向建议器添加字段。

## <a name="rebuild-versus-refresh"></a>"重新生成" 和 "刷新"

重新生成不应与使用新的、已修改或已删除的文档刷新索引的内容混淆。 对于每个搜索应用程序而言，刷新搜索语料库几乎都是最新的，在某些情况下需要最新的更新（例如，搜索语料库需要在联机销售应用中反映库存更改）。

只要不更改索引的结构，就可以使用最初用于加载索引的相同技术来刷新索引：

* 对于推送模式索引，请调用[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)，将更改推送到索引。

* 对于索引器，你可以[计划索引器执行](search-howto-schedule-indexers.md)，并使用更改跟踪或时间戳来标识增量。 如果更新的反射速度必须比计划程序可以管理的速度更快，则可以改用推送模式索引。

## <a name="rebuild-conditions"></a>重新生成条件

如果满足以下任一条件，则删除并重新创建索引。 

| 条件 | 描述 |
|-----------|-------------|
| 更改字段定义 | 修改字段名称、数据类型或特定的[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index)（可搜索、可筛选、可排序、可查找）需要完全重新生成。 |
| 向字段分配分析器 | [分析器](search-analyzers.md)是在索引中定义的，然后分配给字段。 随时都可以向索引添加新的分析器定义，但只有在创建字段时才能分配** 分析器。 对于 **analyzer** 和 **indexAnalyzer** 属性都是如此。 **searchAnalyzer** 属性是一个例外（可以向现有字段分配此属性）。 |
| 更新或删除索引中的分析器定义 | 无法删除或更改索引中的现有分析器配置（分析器、tokenizer、令牌筛选器或字符筛选器），除非重新生成整个索引。 |
| 将字段添加到建议器 | 如果某个字段已存在，并且希望将其添加到[建议器](index-add-suggesters.md)构造，则必须重新生成索引。 |
| 删除字段 | 若要以物理方式删除字段的所有跟踪，必须重新生成索引。 当即时重新生成不可行时，你可以修改应用程序代码以禁用对 "已删除" 字段的访问，或使用[$select 查询参数](search-query-odata-select.md)选择在结果集中表示的字段。 实际上，当你应用省略了相关字段的架构时，字段定义和内容会一直保留在索引中，直至下次重新生成。 |
| 切换层 | 如果需要更多容量，则无法在 Azure 门户中就地升级。 必须创建新服务，必须在新服务中从头开始生成索引。 若要自动完成此过程，可以使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码。 此应用将索引备份到一系列 JSON 文件，然后在指定的搜索服务中重新创建该索引。|

## <a name="update-conditions"></a>更新条件

可以进行许多其他修改，而不会影响现有的物理结构。 具体而言，以下更改*不*需要重新生成索引。 对于这些更改，您可以使用您的更改来[更新索引定义](https://docs.microsoft.com/rest/api/searchservice/update-index)。

+ 添加新字段
+ 在现有字段上设置“可检索”属性****
+ 在现有字段上设置 **searchAnalyzer**
+ 在索引中添加新的分析器定义
+ 添加、更新或删除计分概要文件
+ 添加、更新或删除 CORS 设置
+ 添加、更新或删除 synonymMaps

添加新字段时，将在新字段中为已编制索引的现有文档提供 null 值。 将来刷新数据时，外部源数据中的值会替换 Azure 认知搜索添加的 null 值。 有关更新索引内容的详细信息，请参阅[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

## <a name="how-to-rebuild-an-index"></a>如何重新生成索引

在开发过程中，索引架构经常更改。 可以通过创建可以删除、重新创建的索引，并使用小型代表性数据集快速重新加载索引来规划。

对于已投入生产的应用程序，建议创建一个与现有索引并排运行的新索引，以避免查询时停机。 应用程序代码提供到新索引的重定向。

索引不在后台运行，并且服务将针对正在进行的查询平衡额外的索引。 在编制索引期间，你可以在门户中[监视查询请求](search-monitor-queries.md)，以确保查询及时完成。

1. 确定是否需要重新生成。 如果只是添加字段或更改与字段无关的部分索引，则可以只[更新定义](https://docs.microsoft.com/rest/api/searchservice/update-index)，而无需删除、重新创建和完全重新加载它。

1. [获取索引定义](https://docs.microsoft.com/rest/api/searchservice/get-index)以备将来参考。

1. [删除现有索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)，假设你没有并行运行新索引和旧索引。 

   任何针对该索引的查询都会被立即删除。 请记住，删除索引是不可逆的，因而会销毁字段集合和其他构造的物理存储。 在删除之前，请先暂停以考虑其含义。 

1. [创建修改后的索引](https://docs.microsoft.com/rest/api/searchservice/create-index)，其中请求的正文包含已更改或修改的字段定义。

1. 通过外部源[使用文件加载索引](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

创建索引时，将为索引架构中的每个字段分配物理存储，并为每个可搜索字段创建反向索引。 不可搜索的字段可以用于筛选器或表达式中，但没有反向索引也不支持全文或模糊搜索。 在重新生成索引时，将删除这些反向索引，并根据提供的索引架构重新创建。

加载索引时，每个文档中所有唯一的标记化字词都会填充每个字段的反向索引，并映射到相应的文档 ID。 例如，在为酒店数据集编制索引时，为城市字段创建的反向索引可能包含西雅图、波特兰等的术语。 对于城市字段中包含西雅图或波特兰的文档来说，文档 ID 将随术语一同列出。 在任何[添加、更新或删除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)操作中，都会相应地更新术语和文档 ID 列表。

> [!NOTE]
> 如果你有严格的 SLA 要求，可以考虑专门为此工作预配新服务，使开发和索引完全隔离于生产索引。 单独的服务在其自己的硬件上运行，这样消除了出现资源争用的可能性。 开发完成后，可以保留新索引，将查询重定向到新终结点和索引，也可以运行完成的代码以在原始 Azure 认知搜索服务上发布修订后的索引。 目前没有将即用型索引移至另一个服务的机制。

## <a name="check-for-updates"></a>检查更新

在加载第一个文档时就可以开始查询索引。 如果你知道文档的 ID，那么[查找文档 REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 将返回特定的文档。 对于更大型的测试，应该等待索引完全加载，然后使用查询来验证你想看到的上下文。

可以使用[搜索资源管理器](search-explorer.md)或 Web 测试工具（如[Postman](search-get-started-postman.md) ）检查更新的内容。

如果添加或重命名了字段，请使用[$select](search-query-odata-select.md)返回该字段：`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [大规模编制大数据集索引](search-howto-large-index.md)
+ [在门户中编制索引](search-import-data-portal.md)
+ [Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)
+ [Azure Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)
+ [Azure 表存储索引器](search-howto-indexing-azure-tables.md)
+ [Azure 认知搜索中的安全性](search-security-overview.md)