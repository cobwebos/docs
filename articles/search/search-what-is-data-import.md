---
title: 在搜索索引中进行导入和数据引入
titleSuffix: Azure Cognitive Search
description: 填充数据并将数据上载到 Azure 中的索引认知搜索外部数据源。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113776"
---
# <a name="data-import-overview---azure-cognitive-search"></a>数据导入概述-Azure 认知搜索

在 Azure 认知搜索中，查询执行加载到的内容，并保存在[搜索索引](search-what-is-an-index.md)中。 本文介绍了两种用于填充索引的基本方法：以编程方式将数据*推送*到索引中，或将[Azure 认知搜索索引器](search-indexer-overview.md)置于支持的数据源以*请求*数据。

无论采用哪种方法，目的都是将数据从外部数据源*加载*到 Azure 认知搜索索引。 Azure 认知搜索允许你创建一个空索引，但在向其中推送数据或将数据拉取到其中时，它是不可查询的。

## <a name="pushing-data-to-an-index"></a>将数据推送至索引
用于以编程方式将数据发送到 Azure 认知搜索的推送模型是最灵活的方法。 首先，它对数据源类型没有限制。 假设数据集中的每个文档都具有映射到索引架构中定义的字段的字段，则可将包含 JSON 文档的任何数据集推送到 Azure 认知搜索索引。 其次，它对执行频率没有限制。 可以根据需要选择相应的频率，将更改推送到索引。 对于具有极低延迟要求的应用程序（例如，如果需要搜索操作与动态库存数据库同步），只能选择推送模型。

此方法相比拉模型更加灵活，因为可以单个或批量上传文档（每批最多 1000 个或 16MB，以先达到为准）。 推送模型还允许将文档上传到 Azure 认知搜索，而不考虑数据的位置。

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>如何将数据推送到 Azure 认知搜索索引

可以使用以下 API，将单个或多个文档加载到一个索引中：

+ [Add, Update, or Delete Documents (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)（添加、更新或删除文档 (REST API)）
+ [indexAction 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet)或 [indexBatch 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

目前尚没有支持通过门户推送数据的工具。

有关每种方法的简介，请参阅[快速入门：使用 PowerShell 创建 azure 认知搜索索引](search-create-index-rest-api.md)或[ C#快速入门：使用 .net SDK 创建 azure 认知搜索索引](search-get-started-dotnet.md)。

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>索引操作：上传、合并、mergeOrUpload、删除

可以按文档控制索引操作的类型，指定是应该完整地上传文档、与现有文档内容合并还是将其删除。

在 REST API 中，向 Azure 认知搜索索引的终结点 URL 发出包含 JSON 请求正文的 HTTP POST 请求。 “value”数组中的每个 JSON 对象都包含文档的密钥，并指定索引操作是添加、更新还是删除文档内容。 有关代码示例，请参阅[加载文档](search-get-started-dotnet.md#load-documents)。

在 .NET SDK 中，请将数据打包到 `IndexBatch` 对象中。 `IndexBatch` 封装 `IndexAction` 对象的集合，其中每个对象都包含一个文档和一个属性，告知 Azure 认知搜索对该文档执行的操作。 有关代码示例，请参阅 [C# 快速入门](search-get-started-dotnet.md)。


| @search.action | 说明 | 每个文档必需的字段 | 说明 |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |`upload` 操作类似于“upsert”，如果文档是新文档，则插入；如果文档已经存在，则进行更新/替换。 |关键字段以及要定义的任何其他字段 |更新/替换现有文档时，会将请求中未指定的任何字段设置为 `null`。 即使该字段之前设置为了非 null 值也是如此。 |
| `merge` |使用指定的字段更新现有文档。 如果索引中不存在该文档，merge 会失败。 |关键字段以及要定义的任何其他字段 |merge 中指定的任何字段都将替换文档中的现有字段。 在 .NET SDK 中，这包括 `DataType.Collection(DataType.String)` 类型的字段。 在 REST API 中，这包括 `Collection(Edm.String)` 类型的字段。 例如，如果文档包含值为 `tags` 的字段 `["budget"]`，并且已使用值 `["economy", "pool"]` 对 `tags` 执行合并，则 `tags` 字段的最终值将为 `["economy", "pool"]`。 而不会是 `["budget", "economy", "pool"]`。 |
| `mergeOrUpload` |如果索引中已存在具有给定关键字段的文档，则此操作的行为类似于 `merge`。 如果该文档不存在，则它的行为类似于对新文档进行 `upload` 。 |关键字段以及要定义的任何其他字段 |- |
| `delete` |从索引中删除指定文档。 |仅关键字段 |所指定关键字段以外的所有字段都会被忽略。 如果要从文档中删除单个字段，请改用 `merge`，只需将该字段显式设置为 null。 |

## <a name="decide-which-indexing-action-to-use"></a>确定要使用的索引操作
若要使用 .NET SDK 导入数据，请执行 upload、merge、delete 和 mergeOrUpload 操作。 根据选择的以下操作，每个文档必须仅包含某些特定的字段：


### <a name="formulate-your-query"></a>表述查询
有两种方法可以 [使用 REST API 搜索索引](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 一种方法是发出 HTTP POST 请求，这种请求的查询参数在请求主题的 JSON 对象中定义。 另一种方法是发出 HTTP GET 请求，这种请求的查询参数在请求 URL 中定义。 POST 的查询参数大小限制比 GET [宽松](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 因此建议使用 POST，使用 GET 更方便的特殊情况除外。

POST 和 GET 都需要在请求 URL 中提供*服务名称*、*索引名称*和正确的 *API 版本*（发布本文档时的 API 版本为 `2019-05-06`）。 GET 的 URL 末尾为*查询字符串*，用于提供查询参数。 有关 URL 格式，请参见以下内容：

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

POST 的 URL 格式相同，只是查询字符串参数仅包含 API 版本。


## <a name="pulling-data-into-an-index"></a>将数据拉取到索引中
提取模型对支持的数据源进行爬网，将数据自动上传到索引中。 在 Azure 认知搜索中，此功能通过*索引器*实现，当前可用于这些平台：

+ [Blob 存储](search-howto-indexing-azure-blob-storage.md)
+ [表存储](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure VM 上的 Azure SQL 数据库和 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

索引器将索引连接到数据源（通常是表、视图或等效的结构），将源字段映射到索引中的等效字段。 在执行期间，行集会自动转换为 JSON 并载入指定的索引中。 所有索引器支持计划，使用户能够指定数据的刷新频率。 大多数索引器提供更改跟踪（如果受数据源的支持）。 除了识别新文档外，通过跟踪对现有文档的更改和删除外，索引器免除了主动管理索引中数据的必要。 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>如何将数据提取到 Azure 认知搜索索引

索引器功能已在 [Azure 门户](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations) 和 [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions) 中公开。 

使用门户的优势在于，Azure 认知搜索通常可以通过读取源数据集的元数据来生成默认索引架构。 在处理生成的索引之前可对其进行修改，此后，只能编辑不需要重建索引的架构。 如果想要进行的更改会直接影响架构，则需要重建索引。 

## <a name="verify-data-import-with-search-explorer"></a>使用搜索浏览器验证数据导入

针对文档上传执行初步检查的捷径之一是在门户中使用**搜索浏览器**。 使用资源管理器可以直接查询索引，而无需编写任何代码。 搜索体验取决于默认设置，例如[简单语法](/rest/api/searchservice/simple-query-syntax-in-azure-search)和默认的 [searchMode 查询参数](/rest/api/searchservice/search-documents)。 结果以 JSON 格式返回，方便用户检查整个文档。

> [!TIP]
> 许多[Azure 认知搜索代码示例](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)包括嵌入或可随时可用的数据集，提供一种简单的入门方法。 门户中还提供了一个示例索引器，以及一个由小型房地产数据集组成的数据源（名为“realestate-us-sample”）。 针对示例数据源运行预配置的索引器时，会创建索引并连同文档一起加载该索引，然后，可以使用搜索浏览器或编写的代码查询该索引。

## <a name="see-also"></a>另请参阅

+ [索引器概述](search-indexer-overview.md)
+ [门户演练：创建、加载和查询索引](search-get-started-portal.md)
