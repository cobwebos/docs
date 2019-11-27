---
title: 搜索结果筛选器
titleSuffix: Azure Cognitive Search
description: 按用户安全标识、语言、地理位置或数字值进行筛选，以减少 Azure 认知搜索中托管云搜索服务 Microsoft Azure 的搜索结果。
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
# <a name="filters-in-azure-cognitive-search"></a>Azure 认知搜索中的筛选器 

*筛选器*提供了用于选择 Azure 认知搜索查询中使用的文档的条件。 未经筛选的搜索包含索引中的所有文档。 筛选器将搜索查询的范围限定为一部分文档。 例如，筛选器可将全文搜索限制为具有特定品牌或颜色，并且价位超过特定阈值的产品。

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

+ `$select` 参数用于指定要在结果集中包含哪些字段，在将响应发送到调用方应用程序之前能够有效地修剪响应。 此参数不会具体化查询或缩小文档集合，但如果目标是获取更小的响应，则可以考虑使用此参数。 

有关上述任一参数的详细信息，请参阅[搜索文档 > 请求 > 查询参数](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)。


## <a name="how-filters-are-executed"></a>如何执行筛选器

在查询时，筛选器分析器会接受作为输入提供的条件、将表达式转换为树状表示的原子布尔表达式，然后基于索引中的可筛选字段评估筛选器树。

筛选是配合搜索发生的，限定要将哪些文档包含在下游处理流程中进行文档检索和相关性评分。 与搜索字符串搭配使用时，筛选器能够有效减小后续搜索操作的重调集。 如果单独使用（例如，在 `search=*` 中查询字符串为空时），筛选条件是唯一的输入。 

## <a name="defining-filters"></a>定义筛选器
筛选器为 OData 表达式，使用[Azure 认知搜索中支持的 Odata V4 语法子集](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)进行了表述。 

可为每个**搜索**操作指定一个筛选器，但筛选器本身可以包含多个字段和多个条件，如果使用 **ismatch** 函数，则还可以包含多个全文搜索表达式。 在多部分筛选表达式中，可按任意顺序指定谓词（受运算符优先顺序规则的约束）。 如果尝试按特定的顺序重新排列谓词，性能不会有明显的提升。

筛选表达式的限制之一是请求的最大大小限制。 整个 POST 请求（包括筛选器）最大可为 16 MB；对于 GET 请求，最大可为 8 KB。 筛选表达式中的子句数也有限制。 根据经验，如果有数百个子句，则就存在达到限制的风险。 我们建议正确设计应用程序，使之不会生成大小不受限制的筛选器。

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

## <a name="filter-usage-patterns"></a>筛选器使用模式

以下示例演示了筛选器方案的多种使用模式。 有关更多思路，请参阅 [OData 表达式语法 > 示例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)。

+ 单独使用 **$filter** 而不使用查询字符串：如果筛选表达式能够完全限定所需的文档，则此模式很有效。 不使用查询字符串也就不会执行词法或语言分析、评分和排名。 请注意，搜索字符串只是一个星号，表示“匹配所有文档”。

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ 将查询字符串与 **$filter** 结合使用：让筛选器创建子集，让查询字符串提供字词输入，用于对筛选的子集进行全文搜索。 添加术语（遍历距离电影院）会在结果中引入搜索评分，其中最佳匹配词的文档排名更高。 将筛选器与查询字符串结合使用是最常见的使用模式。

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ 复合查询：使用“or”分隔查询，每个查询有自身的筛选条件（例如，'beagles' in 'dog' or 'siamese' in 'cat'）。 以 `or` 合并的表达式将单独求值，响应中会发回与每个表达式匹配的文档的联合。 此使用模式是通过 `search.ismatchscoring` 函数实现的。 也可以使用无评分版本 `search.ismatch`。

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  还可以使用 `search.ismatchscoring`（而不是 `and`）通过包含筛选器的 `or` 来合并全文搜索，但此功能相当于在搜索请求中使用 `search` 和 `$filter` 参数。 例如，以下两个查询生成相同的结果：

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

请参阅以下文章获取有关特定用例的综合性指导：

+ [Facet 筛选器](search-filters-facets.md)
+ [语言筛选器](search-filters-language.md)
+ [安全修整](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>与筛选相关的字段要求

在 REST API 中，默认为简单字段启用了可筛选性。 可筛选字段会增大索引大小；对于不打算真正在筛选器中使用的字段，请务必设置 `"filterable": false`。 有关字段定义设置的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。

在 .NET SDK 中，可筛选性默认为“关”。 可以通过将相应 [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) 对象的 [IsFilterable 属性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet)设置为 `true`，使某个字段可筛选。 也可以使用 [IsFilterable 特性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute)以声明方式实现此目的。 在以下示例中，该特性已在一个映射到索引定义的模型类的 `BaseRate` 属性中设置。

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>使现有字段可筛选

无法通过修改现有字段来使其可筛选。 为此需要添加新字段或重建索引。 有关重新生成索引或重新填充字段的详细信息，请参阅[如何重新生成 Azure 认知搜索索引](search-howto-reindex.md)。

## <a name="text-filter-fundamentals"></a>文本筛选器基础知识

文本筛选器根据筛选器中提供的文本字符串匹配字符串字段。 与全文搜索不同，对于文本筛选器，不会执行词法分析或分词，因此，比较操作仅用于精确匹配。 例如，假设字段 *f* 包含“sunny day”，则 `$filter=f eq 'Sunny'` 与条件不匹配，但 `$filter=f eq 'sunny day'` 匹配。 

文本字符串区分大小写。 大写的单词不会转换成小写：`$filter=f eq 'Sunny day'` 不会查找“sunny day”。

### <a name="approaches-for-filtering-on-text"></a>基于文本进行筛选的方法

| 方法 | 说明 | 何时使用 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 根据字符串分隔列表匹配字段的函数。 | 建议用于[安全筛选器](search-security-trimming-for-azure-search.md)，以及其中的许多原始文本值需要与某个字符串字段匹配的任何筛选器。 **search.in** 函数旨在提高速度，相比于显式使用 `eq` 和 `or` 将字段与每个字符串进行比较，其速度要快得多。 | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 用于在同一个筛选表达式中将全文搜索操作与严格的布尔筛选操作混合使用的函数。 | 想要在一个请求中使用多种搜索-筛选组合时，请使用 **search.ismatch**（或其等效的评分函数 **search.ismatchscoring**）。 还可以使用该函数来构建 *contains* 筛选器，以根据较大字符串中的部分字符串进行筛选。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 由字段、运算符和值组成的用户定义的表达式。 | 想要在字符串字段与字符串值之间查找完全匹配项时，请使用此函数。 |

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

## <a name="see-also"></a>请参阅

+ [Azure 中全文搜索的工作原理认知搜索](search-lucene-query-architecture.md)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
