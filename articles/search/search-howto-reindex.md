---
title: 重新生成 Azure 搜索索引或刷新可搜索内容 | Microsoft Docs
description: 添加新元素、更新现有元素或文档，或删除完整重新生成或部分增量索引中的过时文档，以刷新 Azure 搜索索引。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 374e7601169647f0eb7d3a214cf15567b7b11090
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "34641418"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>如何重新生成 Azure 搜索索引

重新生成某个索引会更改其结构，并改变该索引在 Azure 搜索服务中的物理表示形式。 相反，刷新索引只会更新内容，可从相关的外部数据源拾取最新的更改。 本文提供有关如何以结构化方式和实质方式更新索引的指导。

更新索引需要拥有服务级别的读写权限。 可以使用指定更新选项的参数，针对内容的完整重新生成或增量索引以编程方式调用 REST 或 .NET API。 

一般情况下，索引更新是按需执行的操作。 但是，对于使用特定于源的[索引器](search-indexer-overview.md)填充的索引，可以使用内置计划程序。 计划程序支持以高达每 15 分钟一次的频率，以所需的最短时间间隔和模式刷新文档。 如果使用更快的刷新频率，则需要手动推送索引更新，推送方法可能包括对事务执行双重写入，或者同时更新外部数据源和 Azure 搜索索引。

## <a name="full-rebuilds"></a>完整重新生成

对于许多类型的更新，需要执行完整重新生成。 完整重新生成表示删除索引、删除数据和元数据，然后重新填充外部数据源中的索引。 以编程方式[删除](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[创建](https://docs.microsoft.com/rest/api/searchservice/create-index)并[重新加载](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)索引即可重新生成索引。 

请记住，在重新生成后，如果已测试查询模式和评分配置文件，在基础内容发生变化的情况下，查询结果预期也会发生变化。

## <a name="when-to-rebuild"></a>何时重新生成

如果索引架构具有不确定性，请在现行开发过程中经常规划完整重新生成。

| 修改 | 重新生成状态|
|--------------|---------------|
| 更改字段名称、数据类型或其[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index) | 更改字段定义的代价通常是需要重新生成，但以下[索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index)除外：Retrievable、SearchAnalyzer、SynonymMaps。 可将 Retrievable、SearchAnalyzer 和 SynonymMaps 属性添加到现有字段，而无需重新生成其索引。|
| 添加字段 | 重新生成没有严格的要求。 将在新字段中为已编制索引的现有文档提供 null 值。 将来重新编制索引时，源数据中的值会替换 Azure 搜索添加的 null 值。 |
| 删除字段 | 无法直接从 Azure 搜索索引中删除字段。 应该让应用程序忽略“已删除的”字段，以避免使用它。 实际上，字段定义和内容会一直保留在索引中，直到下次使用忽略有问题字段的架构重新生成索引为止。|

> [!Note]
> 如果切换了层，则也需要执行重新生成。 如果在某个时间你决定要增加容量，则不需要执行就地升级。 必须在新的容量点创建新服务，必须在新服务中从头开始生成索引。 

## <a name="partial-or-incremental-indexing"></a>部分或增量索引

将索引投入生产环境后，请将注意力转移到增量索引，这通常不会造成明显的服务中断。 部分或增量索引是仅限内容的工作负荷，它会同步搜索索引的内容，以反映相关数据源中内容的状态。 在源中添加或删除的文档将在索引中添加或删除。 在代码中，调用[添加、更新或删除文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)操作或 .NET 等效操作。

> [!Note]
> 使用抓取外部数据源的索引器时，将利用源系统中的更改跟踪机制进行增量索引。 对于 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)，将使用 `lastModified` 字段。 在 [Azure 表存储](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)中，`timestamp` 起到相同的作用。 同样，[Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)和 [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md#indexing-changed-documents)都提供用于标记行更新的字段。 有关索引器的详细信息，请参阅[索引器概述](search-indexer-overview.md)。


## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [大规模编制大数据集索引](search-howto-large-index.md)
+ [在门户中编制索引](search-import-data-portal.md)
+ [Azure SQL 数据库索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)
+ [Azure Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)
+ [Azure 表存储索引器](search-howto-indexing-azure-tables.md)
+ [Azure 搜索中的安全性](search-security-overview.md)