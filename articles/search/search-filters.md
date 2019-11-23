---
title: Filter on search results
titleSuffix: Azure Cognitive Search
description: Filter by user security identity, language, geo-location, or numeric values to reduce search results on queries in Azure Cognitive Search, a hosted cloud search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f4ce3cd0db20f76aa6169f15254cf36ee64151a5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406741"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in Azure Cognitive Search 

A *filter* provides criteria for selecting documents used in an Azure Cognitive Search query. 未经筛选的搜索包含索引中的所有文档。 筛选器将搜索查询的范围限定为一部分文档。 例如，筛选器可将全文搜索限制为具有特定品牌或颜色，并且价位超过特定阈值的产品。

某些搜索体验在实施过程中会施加筛选要求，但你随时可以使用基于值的条件（将搜索范围限定为产品类型“书籍”、类别“纪实”、发布者“Simon 和 Schuster”）。

如果目标是针对特定的数据结构执行有针对性的搜索（将搜索范围限定为客户评论字段），可采用如下所述的替代方法。

## <a name="when-to-use-a-filter"></a>使用筛选器的时机

筛选器在多种搜索体验中不可或缺，包括“附近查找”、分面导航，以及仅显示允许用户查看的文档的安全筛选器。 如果实施其中任何一种体验，必须使用筛选器。 该筛选器会附加到提供地理位置坐标的搜索查询、用户选择的分面类别，或请求者的安全 ID。

示例方案包括：

1. 使用筛选器基于索引中的数据值将索引分片。 获得一个包含城市、房屋类型和设施的架构后，可以创建一个筛选器来显式选择满足条件（西雅图、共管式公寓、水滨）的文档。 

   使用相同输入的全文搜索通常生成类似的结果，但筛选器更精确，因为它要求筛选字词与索引中的内容完全匹配。 

2. 如果搜索体验附带筛选要求，请使用筛选器：

   * [分面导航](search-faceted-navigation.md)使用筛选器传回用户选择的分面类别。
   * 地理搜索使用筛选器在“附近查找”应用中传递当前位置的坐标。 
   * 安全筛选器将安全标识符作为筛选条件进行传递，在索引中找到的匹配项充当代理，提供对文档的访问权限。

3. 如果想要对数字字段施加搜索条件，请使用筛选器。 

   数字字段可在文档中检索并可显示在搜索结果中，但不可单独搜索（受全文搜索的限制）。 如果需要基于数字数据施加选择条件，请使用筛选器。

### <a name="alternative-methods-for-reducing-scope"></a>缩小范围的替代方法

如果想要缩小搜索结果的范围，筛选器并不是唯一的选择。 根据目标，以下替代方法可能更合适：

 + `searchFields` 查询参数将搜索范围限定为特定的字段。 例如，如果索引分别为英语和西班牙语说明提供了单独的字段，则可以使用 searchFields 来限定要用于全文搜索的字段。 

+ `$select` 参数用于指定要在结果集中包含哪些字段，在将响应发送到调用方应用程序之前能够有效地修剪响应。 This parameter does not refine the query or reduce the document collection, but if a smaller response is your goal, this parameter is an option to consider. 

有关上述任一参数的详细信息，请参阅[搜索文档 > 请求 > 查询参数](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)。


## <a name="how-filters-are-executed"></a>How filters are executed

At query time, a filter parser accepts criteria as input, converts the expression into atomic Boolean expressions represented as a tree, and then evaluates the filter tree over filterable fields in an index.

Filtering occurs in tandem with search, qualifying which documents to include in downstream processing for document retrieval and relevance scoring. When paired with a search string, the filter effectively reduces the recall set of the subsequent search operation. 如果单独使用（例如，在 `search=*` 中查询字符串为空时），筛选条件是唯一的输入。 

## <a name="defining-filters"></a>定义筛选器
Filters are OData expressions, articulated using a [subset of OData V4 syntax supported in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

You can specify one filter for each **search** operation, but the filter itself can include multiple fields, multiple criteria, and if you use an **ismatch** function, multiple full-text search expressions. In a multi-part filter expression, you can specify predicates in any order (subject to the rules of operator precedence). 如果尝试按特定的顺序重新排列谓词，性能不会有明显的提升。

One of the limits on a filter expression is the maximum size limit of the request. 整个 POST 请求（包括筛选器）最大可为 16 MB；对于 GET 请求，最大可为 8 KB。 There is also a limit on the number of clauses in your filter expression. 根据经验，如果有数百个子句，则就存在达到限制的风险。 我们建议正确设计应用程序，使之不会生成大小不受限制的筛选器。

以下示例显示了多个 API 中的原型筛选器定义。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filter usage patterns

The following examples illustrate several usage patterns for filter scenarios. 有关更多思路，请参阅 [OData 表达式语法 > 示例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)。

+ 单独使用 **$filter** 而不使用查询字符串：如果筛选表达式能够完全限定所需的文档，则此模式很有效。 不使用查询字符串也就不会执行词法或语言分析、评分和排名。 Notice the search string is just an asterisk, which means "match all documents".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ 将查询字符串与 **$filter** 结合使用：让筛选器创建子集，让查询字符串提供字词输入，用于对筛选的子集进行全文搜索。 The addition of terms (walking distance theaters) introduces search scores in the results, where documents that best match the terms are ranked higher. Using a filter with a query string is the most common usage pattern.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ 复合查询：使用“or”分隔查询，每个查询有自身的筛选条件（例如，'beagles' in 'dog' or 'siamese' in 'cat'）。 Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

请参阅以下文章获取有关特定用例的综合性指导：

+ [Facet 筛选器](search-filters-facets.md)
+ [语言筛选器](search-filters-language.md)
+ [安全修整](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>与筛选相关的字段要求

In the REST API, filterable is *on* by default for simple fields. 可筛选字段会增大索引大小；对于不打算真正在筛选器中使用的字段，请务必设置 `"filterable": false`。 有关字段定义设置的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。

在 .NET SDK 中，可筛选性默认为“关”。 You can make a field filterable by setting the [IsFilterable property](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) of the corresponding [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) object to `true`. You can also do this declaratively by using the [IsFilterable attribute](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Making an existing field filterable

You can't modify existing fields to make them filterable. Instead, you need to add a new field, or rebuild the index. For more information about rebuilding an index or repopulating fields, see [How to rebuild an Azure Cognitive Search index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>文本筛选器基础知识

Text filters match string fields against literal strings that you provide in the filter. Unlike full-text search, there is no lexical analysis or word-breaking for text filters, so comparisons are for exact matches only. For example, assume a field *f* contains "sunny day", `$filter=f eq 'Sunny'` does not match, but `$filter=f eq 'sunny day'` will. 

文本字符串区分大小写。 There is no lower-casing of upper-cased words: `$filter=f eq 'Sunny day'` will not find "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Approaches for filtering on text

| 方法 | 描述 | 何时使用 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | A function that matches a field against a delimited list of strings. | Recommended for [security filters](search-security-trimming-for-azure-search.md) and for any filters where many raw text values need to be matched with a string field. The **search.in** function is designed for speed and is much faster than explicitly comparing the field against each string using `eq` and `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 用于在同一个筛选表达式中将全文搜索操作与严格的布尔筛选操作混合使用的函数。 | Use **search.ismatch** (or its scoring equivalent, **search.ismatchscoring**) when you want multiple search-filter combinations in one request. 还可以使用该函数来构建 *contains* 筛选器，以根据较大字符串中的部分字符串进行筛选。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 由字段、运算符和值组成的用户定义的表达式。 | Use this when you want to find exact matches between a string field and a string value. |

## <a name="numeric-filter-fundamentals"></a>数字筛选器基础知识

在全文搜索的上下文中，数字字段不可搜索 (not `searchable`)。 只有字符串支持全文搜索。 例如，如果输入 99.99 作为搜索词，不会返回价格为 $99.99 的项， 而只会看到在文档字符串字段中包含数字 99 的项。 因此，如果使用了数字数据，则假设条件是要在范围、分面、组等筛选器中使用这些数据。 

如果数字字段（价格、大小、SKU、ID）已标记为 `retrievable`，则包含这些字段的文档会在搜索结果中提供这些值。 此处的要点是，全文搜索本身不适用于数字字段类型。

## <a name="next-steps"></a>后续步骤

首先，尝试在门户中使用“搜索浏览器”来提交包含 **$filter** 参数的查询。 将 [real-estate-sample 索引](search-get-started-portal.md)粘贴到搜索栏后，该索引会针对以下筛选的查询提供有趣的结果：

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

若要学习更多示例，请参阅 [OData 筛选表达式语法 > 示例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)。

## <a name="see-also"></a>另请参阅

+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
