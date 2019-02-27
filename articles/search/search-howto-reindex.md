---
title: 重新生成 Azure 搜索索引或刷新可搜索内容 - Azure 搜索
description: 添加新元素、更新现有元素或文档，或删除完整重新生成或部分增量索引中的过时文档，以刷新 Azure 搜索索引。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341634"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>如何重新生成 Azure 搜索索引

本文介绍如何重新生成 Azure 搜索索引、需要重新生成的情况以及有关如何减轻重新生成操作对正在进行的查询请求的影响的建议。

“重新生成”是指删除并重新创建与索引关联的物理数据结构，包括所有基于字段的反向索引。 在 Azure 搜索中，无法删除并重新创建各个字段。 若要重新生成索引，必须删除所有字段存储、基于现有或修订的索引架构重新创建，然后使用推送到索引或从外部源提取的数据重新填充。 在开发期间重新生成索引很常见，但可能还需要重新生成生产级索引以适应结构化的更改，例如，向建议器添加复杂类型或添加字段。

相较于使索引脱机的重新生成，“数据刷新”可作为后台任务运行。 你可以添加、删除和替换文档，这样对查询工作负载的干扰最小，不过查询通常需要更长时间才能完成。 有关更新索引内容的详细信息，请参阅[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

## <a name="rebuild-conditions"></a>重新生成条件

| 条件 | 说明 |
|-----------|-------------|
| 更改字段定义 | 修改字段名称、数据类型或特定的[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index)（可搜索、可筛选、可排序、可查找）需要完全重新生成。 |
| 向字段分配分析器 | [分析器](search-analyzers.md)是在索引中定义的，然后分配给字段。 随时都可以向索引添加新的分析器定义，但只有在创建字段时才能分配分析器。 对于 **analyzer** 和 **indexAnalyzer** 属性都是如此。 **searchAnalyzer** 属性是一个例外（可以向现有字段分配此属性）。 |
| 更新或删除索引中的分析器定义 | 无法删除或更改索引中的现有分析器配置（分析器、tokenizer、令牌筛选器或字符筛选器），除非重新生成整个索引。 |
| 将字段添加到建议器 | 如果某个字段已存在，并且希望将其添加到[建议器](index-add-suggesters.md)构造，则必须重新生成索引。 |
| 删除字段 | 若要以物理方式删除字段的所有跟踪，必须重新生成索引。 当即时重新生成无法实现时，可以修改应用程序代码来禁止访问“已删除”字段。 实际上，当你应用省略了相关字段的架构时，字段定义和内容会一直保留在索引中，直至下次重新生成。 |
| 切换层 | 如果需要更多容量，则无法就地升级。 在新的容量点创建新服务，必须在新服务中从头开始生成索引。 |

可以在不影响现有物理结构的情况下进行任何其他修改。 具体而言，以下更改不需要重新生成索引：

+ 添加新字段
+ 在现有字段上设置“可检索”属性
+ 在现有字段上设置 **searchAnalyzer**
+ 在索引中添加新的分析器定义
+ 添加、更新或删除计分概要文件
+ 添加、更新或删除 CORS 设置
+ 添加、更新或删除 synonymMaps

添加新字段时，将在新字段中为已编制索引的现有文档提供 null 值。 将来刷新数据时，外部源数据中的值会替换 Azure 搜索添加的 null 值。 有关更新索引内容的详细信息，请参阅[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

## <a name="partial-or-incremental-indexing"></a>部分或增量索引

在 Azure 搜索中，选择删除或重新创建特定字段时，无法控制基于每个字段的索引。 同样，也没有用于[基于条件为文档编制索引](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents)的内置机制。 必须通过自定义代码满足针对条件驱动的索引的任何要求。

不过，你可以轻松地执行索引中的“刷新文档”。 对于许多搜索解决方案，外部源数据是可变的，并且在源数据和搜索索引之间进行同步是常见做法。 在代码中，调用[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)操作或 [.NET 等效项](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet)来更新索引内容，或添加新字段的值。

## <a name="partial-indexing-with-indexers"></a>使用索引器进行部分索引

[索引器](search-indexer-overview.md)简化了数据刷新任务。 索引器只能为外部数据源中的一个表或视图编制索引。 若要为多个表编制索引，最简单的方法是创建一个视图，该视图联接表并投影要编制索引的列。 

当使用索引器抓取外部数据源时，请检查源数据中的“高使用标记”列。 如果存在，则可以仅选取包含新内容或修订内容的行来将其用于增量更改检测。 对于 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)，将使用 `lastModified` 字段。 在 [Azure 表存储](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)中，`timestamp` 起到相同的作用。 同样，[Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)和 [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md#indexing-changed-documents)都提供用于标记行更新的字段。 

有关索引器的详细信息，请参阅[索引器概述](search-indexer-overview.md)和[重置索引器 REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)。

## <a name="how-to-rebuild-an-index"></a>如何重新生成索引

如果索引架构具有不确定性，请在现行开发过程中经常规划完整重新生成。 对于已投入生产的应用程序，建议创建一个与现有索引并排运行的新索引，以避免查询时停机。

更新索引需要拥有服务级别的读写权限。 

无法在门户中重新生成索引。 可以采用编程方式调用[更新索引 REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) 或[等效的 .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) 进行完全重新生成。 更新索引请求与[创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 相同，但具有不同的上下文。

以下工作流偏向于 REST API，但同样适用于 .NET SDK。

1. 当重用某个索引名称时，请[删除现有索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)。 

   任何针对该索引的查询都会被立即删除。 删除索引是不可逆的，此操作会销毁字段集合和其他构造的物理存储空间。 在删除索引之前，请确保自己清楚删除索引的影响。 

2. 使用服务终结点、API 密钥和[管理密钥](https://docs.microsoft.com/azure/search/search-security-api-keys)构造[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)请求。 写入操作需要使用管理密钥。

3. 在请求正文中，提供具有已更改或已修改字段定义的索引架构。 请求正文包含索引架构，以及用于计分概要文件、分析器、建议器和 CORS 选项的构造。 架构要求都记录在[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)中。

4. 发送[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)请求以在 Azure 搜索上重新生成索引的物理表达式。 

5. 通过外部源[使用文件加载索引](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)。

创建索引时，将为索引架构中的每个字段分配物理存储，并为每个可搜索字段创建反向索引。 不可搜索的字段可以用于筛选器或表达式中，但没有反向索引也不支持全文或模糊搜索。 在重新生成索引时，将删除这些反向索引，并根据提供的索引架构重新创建。

加载索引时，每个文档中所有唯一的标记化字词都会填充每个字段的反向索引，并映射到相应的文档 ID。 例如，在为酒店数据集编制索引时，为城市字段创建的反向索引可能包含西雅图、波特兰等的术语。 对于城市字段中包含西雅图或波特兰的文档来说，文档 ID 将随术语一同列出。 在任何[添加、更新或删除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)操作中，都会相应地更新术语和文档 ID 列表。

> [!NOTE]
> 如果你有严格的 SLA 要求，可以考虑专门为此工作预配新服务，使开发和索引完全隔离于生产索引。 单独的服务在其自己的硬件上运行，这样消除了出现资源争用的可能性。 开发完成后，可以保留新索引，将查询重定向到新终结点和索引，也可以运行完成的代码以在原始 Azure 搜索服务上发布修订后的索引。 目前没有将即用型索引移至另一个服务的机制。

## <a name="view-updates"></a>查看更新

在加载第一个文档时就可以开始查询索引。 如果你知道文档的 ID，那么[查找文档 REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) 将返回特定的文档。 对于更大型的测试，应该等待索引完全加载，然后使用查询来验证你想看到的上下文。

## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [大规模编制大数据集索引](search-howto-large-index.md)
+ [在门户中编制索引](search-import-data-portal.md)
+ [Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)
+ [Azure Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)
+ [Azure 表存储索引器](search-howto-indexing-azure-tables.md)
+ [Azure 搜索中的安全性](search-security-overview.md)