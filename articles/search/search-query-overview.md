---
title: 查询类型和组合
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中生成搜索查询并使用参数对结果进行筛选、选择和排序的基础知识。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282817"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure 认知搜索中的查询类型和组成部分

在 Azure 认知搜索中，查询是往返操作的完整规范。 请求中的参数提供匹配条件用于查找索引中的文档、要包含或排除的字段、传递给引擎的执行指令，以及用于调整响应的指令。 如果未指定这些内容 (`search=*`)，查询会作为全文搜索操作针对所有可搜索字段运行，返回一个任意排序的未评分结果集。

以下示例是在 [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中构造的代表性查询。 此示例针对[酒店演示索引](search-get-started-portal.md)并包含通用参数。

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** 设置解析器，该解析器是[默认的简单查询解析器](search-query-simple-examples.md)（最适合全文搜索），或用于高级查询构造（如正则表达式、邻近搜索、模糊搜索和通配符搜索等）[的完整 Lucene 查询解析器](search-query-lucene-examples.md)，仅举几例。

+ **`search`** 提供匹配条件，通常是文本，但通常伴随着布尔运算符。 包含单个独立字词的查询称为字词查询。** 由括在引号中的多个部分组成的查询称为关键短语查询。** 搜索可以未定义，如 在**`search=*`** 中所示，但更可能包括与示例中显示的术语、短语和运算符类似的术语、短语和运算符。

+ **`searchFields`** 将查询执行约束到特定字段。 在索引架构中设置了 *searchable* 属性的任何字段都适合指定此参数。

还可以通过查询中包含的参数来调整响应。 在此示例中，结果集由**`select`** 语句中列出的字段组成。 只有标记为 *retrievable* 的字段才能在 $select 语句中使用。 此外，此查询**`top`** 中仅返回 10 个命中，**`count`** 同时告诉您总体匹配的文档数，这比返回的文档数还多。 在此查询中，行已按“评级”的降序排序。

在 Azure 认知搜索中，查询执行始终针对一个使用请求中提供的 API 密钥进行身份验证的索引。 在 REST 中，两者均在请求标头中提供。

### <a name="how-to-run-this-query"></a>如何运行此查询

若要执行此查询，请使用[搜索浏览器和酒店演示索引](search-get-started-portal.md)。 

可将此查询字符串粘贴到浏览器的搜索栏中：`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>索引如何启用查询操作

索引设计和查询设计在 Azure 认知搜索中紧密耦合。 需要提前知道的一个重要事实是，包含每个字段中属性的索引架构确定了可以生成的查询类型。** 

字段中的索引属性设置允许的操作 - 字段在索引中是否可搜索、在结果中是否可检索、是否可排序、是否可筛选，等等。******** 在示例查询字符串中，只有 `"$orderby": "Rating"` 可以正常工作，因为 Rating 字段在索引架构中标记为 *sortable*。 

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

以上屏幕截图是酒店示例的索引属性的部分列表。 可在门户中查看整个索引架构。 有关索引属性的详细信息，请参阅[创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)。

> [!Note]
> 某些查询功能在索引范围启用，而不是按字段启用。 这些功能包括：[同义词映射](search-synonyms.md)、[自定义分析器](index-add-custom-analyzers.md)、[建议器构造（用于自动完成和建议的查询）](index-add-suggesters.md)、[排名结果的评分逻辑](index-add-scoring-profiles.md)。

## <a name="elements-of-a-query-request"></a>查询请求的元素

查询始终指向单个索引。 不能联接索引或者创建自定义或临时数据结构作为查询目标。 

查询请求上的必需元素包括以下内容：

+ 服务终结点和索引文档集合，表示为包含固定和用户定义的组件的 URL：**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**（仅 REST） 是必需的，因为 API 的多个版本在任何时候都可用。 
+ **`api-key`**，查询或管理员 api 键，对服务的请求进行身份验证。
+ **`queryType`**，如果使用内置的默认简单语法，可以省略简单语法。
+ **`search`** 或**`filter`** 提供匹配条件，如果要执行空搜索，可以未指定匹配条件。 这两种查询类型都是作为简单分析器讨论的，但即使是高级查询，也需要通过搜索参数来传递复杂的查询表达式。

所有其他搜索参数都为可选参数。 有关属性的完整列表，请参阅[创建索引 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index)。 有关如何在处理期间使用参数的详细信息，请参阅 [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)。

## <a name="choose-apis-and-tools"></a>选择 API 和工具

下表列出用于提交查询的 API 和基于工具的方法。

| 方法 | 描述 |
|-------------|-------------|
| [搜索资源管理器（门户）](search-explorer.md) | 提供搜索栏，以及索引和 API 版本选项。 结果会以 JSON 文档的形式返回。 建议用于浏览、测试和验证。 <br/>[了解详细信息。](search-get-started-portal.md#query-index) | 
| [Postman 或其他 REST 工具](search-get-started-postman.md) | Web 测试工具是用公式表示 REST 调用的极佳选择。 REST API 支持 Azure 认知搜索中的每个可能操作。 在本文中，了解如何设置 HTTP 请求标头和正文，以便向 Azure 认知搜索发送请求。  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | 可用于查询 Azure 认知搜索索引的客户端。  <br/>[了解详细信息。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [搜索文档 (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 索引上的 GET 或 POST 方法，使用查询参数进行其他输入。  |

## <a name="choose-a-parser-simple--full"></a>选择一个分析器：简单 | 完整

Azure 认知搜索基于 Apache Lucene ，提供两种查询分析器选择，分别用于处理典型查询和专用查询。 使用简单分析器的请求是通过[简单查询语法](query-simple-syntax.md)构建的。由于在自由格式文本查询中具有速度和效率优势，这种语法已选作默认语法。 此语法支持多种常用的搜索运算符，包括 AND、OR、NOT、短语、后缀和优先运算符。

在将 `queryType=full` 添加到请求时所启用的[完整 Lucene 查询语法](query-Lucene-syntax.md#bkmk_syntax)公开作为 [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 的一部分开发的、已被广泛采用的且富有表达能力的查询语言。 完整语法扩展了简单语法。 为简单语法编写的任何查询在完整 Lucene 分析器下运行。 

以下示例演示了一个要点：采用不同 queryType 设置的同一个查询会产生不同的结果。 在第一个查询中，`historic` 后面的 `^3` 被视为搜索字词的一部分。 此查询的最高排名结果是“Marquis Plaza & Suites”，其说明中包含 *ocean*

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

使用完整 Lucene 分析器的同一查询会将 `^3` 解释为字段内字词提升器。 切换分析器会更改排名，并将包含 *historic* 一词的结果移到最前面。

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>查询类型

Azure 认知搜索支持广泛的查询类型。 

| 查询类型 | 使用情况 | 示例和详细信息 |
|------------|--------|-------------------------------|
| 自由格式文本搜索 | 搜索参数和任一分析器| 全文搜索在索引中所有可搜索字段内扫描一个或多个字词，其工作原理与 Google 或必应等搜索引擎相同。** 简介中的示例属于全文搜索。<br/><br/>全文搜索默认使用标准 Lucene 分析器来执行文本分析，以将字词设为小写，并删除“the”等干扰词。 可将默认设置替代为可以修改文本分析的[非英语分析器](index-add-language-analyzers.md#language-analyzer-list)或[专用的与语言无关的分析器](index-add-custom-analyzers.md#AnalyzerTable)。 例如，将整个字段内容视为单个标记的[关键字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)。 此分析器可用于邮政编码、ID 和某些产品名称等数据。 | 
| 筛选的搜索 | [OData 筛选表达式](query-odata-filter-orderby-syntax.md)和任一分析器 | 筛选器查询对索引中的所有可筛选字段计算布尔表达式**。 与搜索不同，筛选器查询与字段内容完全匹配，包括字符串字段的大小写区分。 另一项差别在于，筛选器查询以 OData 语法表示。 <br/>[筛选表达式示例](search-query-simple-examples.md#example-3-filter-queries) |
| 地理搜索 | 字段中的 [Edm.GeographyPoint 类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)、筛选表达式和任一分析器 | 存储在字段中的具有 Edm.GeographyPoint 的坐标用于“附近查找”或基于地图的搜索控件。 <br/>[地理搜索示例](search-query-simple-examples.md#example-5-geo-search)|
| 范围搜索 | 筛选表达式和简单分析器 | 在 Azure 认知搜索中，范围查询是使用筛选器参数生成的。 <br/>[范围筛选器示例](search-query-simple-examples.md#example-4-range-filters) | 
| [字段搜索](query-lucene-syntax.md#bkmk_fields) | 搜索参数和完整分析器 | 针对单个字段生成复合查询表达式。 <br/>[字段化搜索示例](search-query-lucene-examples.md#example-2-fielded-search) |
| [模糊搜索](query-lucene-syntax.md#bkmk_fuzzy) | 搜索参数和完整分析器 | 匹配具有类似构造或拼写方式的字词。 <br/>[模糊搜索示例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [邻近搜索 (proximity search)](query-lucene-syntax.md#bkmk_proximity) | 搜索参数和完整分析器 | 查找在文档中相互靠近的字词。 <br/>[邻近搜索示例](search-query-lucene-examples.md#example-4-proximity-search) |
| [术语提升](query-lucene-syntax.md#bkmk_termboost) | 搜索参数和完整分析器 | 如果某个文档包含提升的字词（相对于其他未提升的字词），则提高其排名。 <br/>[字词提升示例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正则表达式搜索](query-lucene-syntax.md#bkmk_regex) | 搜索参数和完整分析器 | 基于正则表达式的内容进行匹配。 <br/>[正则表达式示例](search-query-lucene-examples.md#example-6-regex) |
|  [通配符或前缀搜索](query-lucene-syntax.md#bkmk_wildcard) | 搜索参数和完整分析器 | 基于前缀和波浪符 (`~`) 或单个字符 (`?`) 进行匹配。 <br/>[通配符搜索示例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>管理搜索结果 

查询结果会流式处理为 REST API 中的 JSON 文档，但如果使用 .NET API，则会内置序列化功能。 可通过在查询中设置参数并为响应选择特定字段来调整结果。

可通过以下方式使用查询上的参数来调整结果集的结构：

+ 对结果中的文档数量（默认为 50 个）进行限制或分批
+ 选择结果中要包含的字段
+ 设置排列顺序
+ 添加突出显示效果，以便在搜索结果正文中清楚看到匹配的搜索词

### <a name="tips-for-unexpected-results"></a>意外结果提示

有时可能会出现预料外的结果内容（而不是结构）。 如果查询结果并不是预期内容，可以尝试对查询进行以下修改，然后查看结果是否改进：

+ 更改**`searchMode=any`**（默认）以**`searchMode=all`** 要求匹配所有条件（而不是任何条件）。 在查询包含布尔运算符时更应如此。

+ 如果需要进行文本或词法分析但查询类型排除了语言处理环节，请更改查询方法。 在全文搜索中，文本或词法分析会自动更正拼写错误、单复数形式以及不合规范的谓词或名词。 对于模糊搜索和通配符搜索等查询，其查询分析管道中不包含文本分析。 在某些情况下会采用正则表达式作为解决方法。 

### <a name="paging-results"></a>分页结果
Azure 认知搜索可轻松对搜索结果进行分页。 通过使用**`top`** 和**`skip`** 参数，您可以顺利发出搜索请求，以便以可管理的有序子集接收搜索结果总数，从而轻松启用良好的搜索 UI 实践。 接收较小的结果子集时，还可以在搜索结果总集中获得文档计数。

有关对搜索结果分页的详细信息，请参阅 [如何在 Azure 认知搜索中对搜索结果分页](search-pagination-page-layout.md)一文。

### <a name="ordering-results"></a>对结果排序
接收搜索查询结果时，可请求 Azure 认知搜索按特定字段中的值对结果排序。 默认情况下，Azure 认知搜索根据每个文档的搜索评分（源自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)）排名对结果排序。

如果希望 Azure 认知搜索返回按搜索分数以外的值排序的结果，则可以使用**`orderby`** 搜索参数。 您可以指定**`orderby`** 参数的值，以包括字段名称和对地理空间值[**`geo.distance()`函数**](query-odata-filter-orderby-syntax.md)的调用。 每个表达式可以跟后`asc`，以指示按升序请求结果，并**`desc`** 指示按降序请求结果。 默认为升序。


### <a name="hit-highlighting"></a>突出显示
在 Azure 认知搜索中，通过使用**`highlight`** 和**`highlightPreTag`****`highlightPostTag`** 参数，可以轻松强调与搜索查询匹配的搜索结果的确切部分。 可以指定应强调其匹配文本的可搜索字段，还可指定要附加到 Azure 认知搜索所返回匹配文本的开头和结尾的精确字符串标记**。

## <a name="see-also"></a>请参阅

+ [Azure 认知搜索中全文搜索的工作原理（查询分析体系结构）](search-lucene-query-architecture.md)
+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](search-query-dotnet.md)
+ [如何在 REST 中进行查询](search-create-index-rest-api.md)
