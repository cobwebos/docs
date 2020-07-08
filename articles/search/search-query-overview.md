---
title: 查询类型和组合
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中生成搜索查询并使用参数对结果进行筛选、选择和排序的基础知识。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 8f170d541ec314020702ab53606eed4d660cea9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130800"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure 认知搜索中的查询类型和组成部分

在 Azure 认知搜索中，查询是往返操作的完整规范。 在请求中，有一些参数可提供引擎的执行指令，还提供了用于重新绘制响应的参数。 未指定（ `search=*` ），没有匹配条件并使用 null 或默认参数，查询将针对所有可搜索字段作为全文搜索操作执行，并以任意顺序返回未评分结果集。

以下示例是在 [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中构造的代表性查询。 此示例的目标是[旅馆演示程序索引](search-get-started-portal.md)，并包含通用参数，以便您可以了解查询的外观。

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

+ **`queryType`** 设置分析器，该分析器可以是[默认的简单查询分析器](search-query-simple-examples.md)（最适合用于全文搜索），也可以是[完整的 Lucene 查询分析器](search-query-lucene-examples.md)（用于正则表达式、邻近搜索、模糊和通配符搜索等高级查询构造）。

+ **`search`** 提供匹配条件，通常是整个字词或短语，但通常伴随布尔运算符。 包含单个独立字词的查询称为字词查询。  引号括起来的多部分查询是*短语*查询。 搜索可以是未定义的， **`search=*`** 但在中，如果没有要匹配的条件，则结果集由任意选定的文档组成。

+ **`searchFields`** 将查询执行约束为特定的字段。 在索引架构中设置了 *searchable* 属性的任何字段都适合指定此参数。

还可以通过查询中包含的参数来整形响应：

+ **`select`** 指定要在响应中返回的字段。 只能在 select 语句中使用在索引中标记为可*检索*的字段。

+ **`top`** 返回指定数量的匹配文档。 在此示例中，只返回10个命中。 您可以使用 top 和 skip （未显示）来对结果进行分页。

+ **`count`** 告诉您整个索引中的文档总数，它们可能大于返回的文档数。 

+ **`orderby`** 如果要按值（如评分或位置）对结果进行排序，则使用。 否则，默认值是使用关联分数对结果进行排序。

在 Azure 认知搜索中，查询执行始终针对一个使用请求中提供的 API 密钥进行身份验证的索引。 在 REST 中，两者均在请求标头中提供。

### <a name="how-to-run-this-query"></a>如何运行此查询

编写任何代码之前，您可以使用查询工具来了解语法并使用不同的参数进行试验。 最快的方法是内置门户工具[搜索资源管理器](search-explorer.md)。

如果按照本[快速入门创建了酒店演示程序索引](search-get-started-portal.md)，则可以将此查询字符串粘贴到资源管理器的搜索栏中，以运行第一个查询：`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>索引如何启用查询操作

索引设计和查询设计在 Azure 认知搜索中紧密耦合。 需要提前知道的一个重要事实是，包含每个字段中属性的索引架构确定了可以生成的查询类型。  

字段中的索引属性设置允许的操作 - 字段在索引中是否可搜索、在结果中是否可检索、是否可排序、是否可筛选，等等。     在示例查询字符串中，只有 `"$orderby": "Rating"` 可以正常工作，因为 Rating 字段在索引架构中标记为 *sortable*。 

![酒店示例的索引定义](./media/search-query-overview/hotel-sample-index-definition.png "酒店示例的索引定义")

以上屏幕截图是酒店示例的索引属性的部分列表。 可在门户中查看整个索引架构。 有关索引属性的详细信息，请参阅[创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)。

> [!Note]
> 某些查询功能在索引范围启用，而不是按字段启用。 这些功能包括：[同义词映射](search-synonyms.md)、[自定义分析器](index-add-custom-analyzers.md)、[建议器构造（用于自动完成和建议的查询）](index-add-suggesters.md)、[排名结果的评分逻辑](index-add-scoring-profiles.md)。

## <a name="elements-of-a-query-request"></a>查询请求的元素

查询始终指向单个索引。 不能联接索引或者创建自定义或临时数据结构作为查询目标。 

查询请求上的必需元素包括以下内容：

+ 以 URL 表示的、包含固定和用户定义组件的服务终结点与索引文档集合： **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ 之所以需要 **`api-version`** （仅适用于 REST），是因为始终有多个可用的 API 版本。 
+ **`api-key`** ：查询或管理 API 密钥，用于对服务请求进行身份验证。
+ **`queryType`** ：简单或完整类型，如果想使用内置的默认简单语法，则可以省略此元素。
+ **`search`** 或 **`filter`** 提供匹配条件，如果想要执行空搜索，则可以不指定此元素。 这两种查询类型都是作为简单分析器讨论的，但即使是高级查询，也需要通过搜索参数来传递复杂的查询表达式。

所有其他搜索参数都为可选参数。 有关属性的完整列表，请参阅[创建索引 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index)。 有关如何在处理期间使用参数的详细信息，请参阅 [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)。

## <a name="choose-apis-and-tools"></a>选择 API 和工具

下表列出用于提交查询的 API 和基于工具的方法。

| 方法 | 说明 |
|-------------|-------------|
| [搜索浏览器（门户）](search-explorer.md) | 提供搜索栏，以及索引和 API 版本选项。 结果会以 JSON 文档的形式返回。 建议用于浏览、测试和验证。 <br/>[了解详细信息。](search-get-started-portal.md#query-index) | 
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
| 自由格式文本搜索 | 搜索参数和任一分析器| 全文搜索在索引中所有可搜索字段内扫描一个或多个字词，其工作原理与 Google 或必应等搜索引擎相同。** 简介中的示例属于全文搜索。<br/><br/>全文搜索使用标准 Lucene 分析器（默认情况下）进行词法分析，并删除 "the" 之类的停止词。 您可以用[非英语分析器](index-add-language-analyzers.md#language-analyzer-list)或用于修改词法分析的[专用语言不可知分析器](index-add-custom-analyzers.md#AnalyzerTable)替代默认值。 例如，将整个字段内容视为单个标记的[关键字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)。 此分析器可用于邮政编码、ID 和某些产品名称等数据。 | 
| 筛选的搜索 | [OData 筛选表达式](query-odata-filter-orderby-syntax.md)和任一分析器 | 筛选器查询对索引中的所有可筛选字段计算布尔表达式  。 与搜索不同，筛选器查询与字段内容完全匹配，包括字符串字段的大小写区分。 另一项差别在于，筛选器查询以 OData 语法表示。 <br/>[筛选表达式示例](search-query-simple-examples.md#example-3-filter-queries) |
| 地理搜索 | 字段中的 [Edm.GeographyPoint 类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)、筛选表达式和任一分析器 | 存储在字段中的具有 Edm.GeographyPoint 的坐标用于“附近查找”或基于地图的搜索控件。 <br/>[地理搜索示例](search-query-simple-examples.md#example-5-geo-search)|
| 范围搜索 | 筛选表达式和简单分析器 | 在 Azure 认知搜索中，范围查询是使用筛选器参数生成的。 <br/>[范围筛选器示例](search-query-simple-examples.md#example-4-range-filters) | 
| [字段化搜索](query-lucene-syntax.md#bkmk_fields) | 搜索参数和完整分析器 | 针对单个字段生成复合查询表达式。 <br/>[字段化搜索示例](search-query-lucene-examples.md#example-2-fielded-search) |
| [模糊搜索](query-lucene-syntax.md#bkmk_fuzzy) | 搜索参数和完整分析器 | 匹配具有类似构造或拼写方式的字词。 <br/>[模糊搜索示例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [邻近搜索](query-lucene-syntax.md#bkmk_proximity) | 搜索参数和完整分析器 | 查找在文档中相互靠近的字词。 <br/>[邻近搜索示例](search-query-lucene-examples.md#example-4-proximity-search) |
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

+ 将 **`searchMode=any`** （默认）更改为 **`searchMode=all`** 可获取符合所有条件而不是某个条件的匹配项。 在查询包含布尔运算符时更应如此。

+ 如果需要进行文本或词法分析但查询类型排除了语言处理环节，请更改查询方法。 在全文搜索中，文本或词法分析会自动更正拼写错误、单复数形式以及不合规范的谓词或名词。 对于某些查询，如模糊或通配符搜索，词法分析不属于查询分析管道。 在某些情况下会采用正则表达式作为解决方法。 

### <a name="paging-results"></a>分页结果
Azure 认知搜索可轻松对搜索结果进行分页。 使用 **`top`** 和 **`skip`** 参数可顺利地发出搜索请求，接收搜索结果总集，并通过其中易于管理的有序子集轻松完成效果良好的搜索 UI 操作。 接收较小的结果子集时，还可以在搜索结果总集中获得文档计数。

有关对搜索结果分页的详细信息，请参阅 [如何在 Azure 认知搜索中对搜索结果分页](search-pagination-page-layout.md)一文。

### <a name="ordering-results"></a>对结果排序
接收搜索查询结果时，可请求 Azure 认知搜索按特定字段中的值对结果排序。 默认情况下，Azure 认知搜索会根据每个文档的搜索分数的排名对搜索结果进行排序，此排名是从[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)派生的。

如果希望 Azure 认知搜索返回按搜索评分以外的某个值排序的结果，可以使用 **`orderby`** 搜索参数。 对于地理空间值，可以指定 **`orderby`** 参数的值，使其包含字段名称及对 [ **`geo.distance()` 函数**](query-odata-filter-orderby-syntax.md)的调用。 每个表达式可后接 `asc` 来指示按升序请求结果，或后接 **`desc`** 来指示按降序请求结果。 默认为升序。


### <a name="hit-highlighting"></a>突出显示
在 Azure 认知搜索中，使用 **`highlight`** 、 **`highlightPreTag`** 和 **`highlightPostTag`** 参数可轻松强调搜索结果中与搜索查询相匹配的确切部分。 可以指定应强调其匹配文本的可搜索字段，还可指定要附加到 Azure 认知搜索所返回匹配文本的开头和结尾的精确字符串标记  。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中全文搜索的工作原理（查询分析体系结构）](search-lucene-query-architecture.md)
+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](search-query-dotnet.md)
+ [如何在 REST 中进行查询](search-create-index-rest-api.md)
