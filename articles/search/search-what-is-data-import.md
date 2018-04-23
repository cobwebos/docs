---
title: Azure 搜索中的数据导入 | Microsoft Docs
description: 了解如何将数据上传到 Azure 搜索中的索引。
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: heidist
ms.openlocfilehash: 3f5f6c4ea8ba45d0fefb84e7338ffdc44f852246
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="indexing-in-azure-search"></a>在 Azure 搜索中编制索引
> [!div class="op_single_selector"]
> * [概述](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

在 Azure 搜索中，将会针对已加载到[搜索索引](search-what-is-an-index.md)中的内容执行查询。 本文介绍两种将内容加载到索引中的基本方法：一种是推送，即以编程方式将数据推送至索引；另一种是拉取，即将 [Azure 搜索索引器](search-indexer-overview.md)指向支持的数据源，以便拉取数据。

## <a name="pushing-data-to-an-index"></a>将数据推送至索引
推送模式用于以编程方式将数据发送到 Azure 搜索，是最灵活的方法。 首先，它对数据源类型没有限制。 任何由 JSON 文档组成的数据集都可以推送至 Azure 搜索索引，前提是数据集中的每个文档的字段都映射到索引架构中定义的字段。 其次，它对执行频率没有限制。 可以根据需要选择相应的频率，将更改推送到索引。 对于具有极低延迟要求的应用程序（例如，如果需要搜索操作与动态库存数据库同步），只能选择推送模型。

此方法相比拉模型更加灵活，因为可以单个或批量上传文档（每批最多 1000 个或 16MB，以先达到为准）。 推送模型还允许将文档上传到 Azure 搜索，而不考虑数据的位置。

### <a name="how-to-push-data-to-an-azure-search-index"></a>如何将数据推送至 Azure 搜索索引

可以使用以下 API，将单个或多个文档加载到一个索引中：

+ [Add, Update, or Delete Documents (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)（添加、更新或删除文档 (REST API)）
+ [indexAction 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet)或 [indexBatch 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

目前尚没有支持通过门户推送数据的工具。

有关每种方法的简介，请参阅[使用 REST 来导入数据](search-import-data-rest-api.md)或[使用 .NET 来导入数据](search-import-data-dotnet.md)。


## <a name="pulling-data-into-an-index"></a>将数据拉取到索引中
提取模型对支持的数据源进行爬网，将数据自动上传到索引中。 在 Azure 搜索中，此功能是通过索引器实现的，目前适用于以下平台：

+ [Blob 存储](search-howto-indexing-azure-blob-storage.md)
+ [表存储](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Azure VM 上的 Azure SQL 数据库和 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

索引器将索引连接到数据源（通常是表、视图或等效的结构），将源字段映射到索引中的等效字段。 在执行期间，行集会自动转换为 JSON 并载入指定的索引中。 所有索引器支持计划，使用户能够指定数据的刷新频率。 大多数索引器提供更改跟踪（如果受数据源的支持）。 除了识别新文档外，通过跟踪对现有文档的更改和删除外，索引器免除了主动管理索引中数据的必要。 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>如何将数据拉取至 Azure 搜索索引

索引器功能已在 [Azure 门户](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations) 和 [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations) 中公开。 

使用门户的一个优势在于，Azure 搜索通常可以通过读取源数据集的元数据来生成默认的索引架构。 在处理生成的索引之前可对其进行修改，此后，只能编辑不需要重建索引的架构。 如果想要进行的更改会直接影响架构，则需要重建索引。 

## <a name="verify-data-import-with-search-explorer"></a>使用搜索资源管理器验证数据导入

针对文档上传执行初步检查的捷径之一是在门户中使用“搜索资源管理器”。 使用资源管理器可以直接查询索引，而无需编写任何代码。 搜索体验取决于默认设置，例如[简单语法](/rest/api/searchservice/simple-query-syntax-in-azure-search)和默认的 [searchMode 查询参数](/rest/api/searchservice/search-documents)。 结果以 JSON 格式返回，方便用户检查整个文档。

> [!TIP]
> 有大量的 [Azure 搜索代码示例](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)包含了嵌入的或随时可用的数据集，帮助用户轻松入门。 门户中还提供了一个示例索引器，以及一个由小型房地产数据集组成的数据源（名为“realestate-us-sample”）。 针对示例数据源运行预配置的索引器时，会创建索引并连同文档一起加载该索引，然后，可以使用搜索资源管理器或编写的代码查询该索引。

## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [门户演练：创建、加载和查询索引](search-get-started-portal.md)