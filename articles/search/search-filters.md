---
title: 索引中用于限定搜索结果的范围的筛选器
titleSuffix: Azure Cognitive Search
description: 按用户安全标识、语言、地理位置或数字值进行筛选，以减少 Azure 认知搜索中托管云搜索服务 Microsoft Azure 的搜索结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7dd289005e91323010cfa2a0298c351b3e757d1d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792863"
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

+ `$select` 参数用于指定要在结果集中包含哪些字段，在将响应发送到调用方应用程序之前能够有效地修剪响应。 此参数不会优化查询或减少文档集合，但如果你的目标是较小的响应，则此参数是要考虑的选项。 

有关上述任一参数的详细信息，请参阅[搜索文档 > 请求 > 查询参数](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)。


## <a name="how-filters-are-executed"></a>如何执行筛选器

在查询时，筛选器分析器接受作为输入的条件，将表达式转换为表示为树的原子布尔表达式，然后对索引中的可筛选字段计算筛选器树。

筛选与搜索一起进行，可为文档检索和关联计分限定要包含在下游处理中的文档。 如果与搜索字符串配对，筛选器将有效地减少后续搜索操作的回调集。 如果单独使用（例如，在 `search=*` 中查询字符串为空时），筛选条件是唯一的输入。 

## <a name="defining-filters"></a>定义筛选器

筛选器为 OData 表达式，使用[Azure 认知搜索中支持的 Odata V4 语法子集](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)进行了表述。 

您可以为每个**搜索**操作指定一个筛选器，但筛选器本身可以包含多个字段和多个条件，如果使用**ismatch**函数，则可以使用多个全文搜索表达式。 在多部分筛选器表达式中，可以按任意顺序指定谓词（根据运算符优先级规则）。 如果尝试按特定的顺序重新排列谓词，性能不会有明显的提升。

筛选器表达式的一个限制是请求的最大大小限制。 整个 POST 请求（包括筛选器）最大可为 16 MB；对于 GET 请求，最大可为 8 KB。 筛选器表达式中的子句数也有限制。 根据经验，如果有数百个子句，则就存在达到限制的风险。 我们建议正确设计应用程序，使之不会生成大小不受限制的筛选器。

以下示例显示了多个 API 中的原型筛选器定义。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>筛选使用模式

下面的示例演示了多个筛选器方案的使用模式。 有关更多思路，请参阅 [OData 表达式语法 > 示例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)。

+ 单独使用 **$filter** 而不使用查询字符串：如果筛选表达式能够完全限定所需的文档，则此模式很有效。 不使用查询字符串也就不会执行词法或语言分析、评分和排名。 请注意，搜索字符串只是一个星号，这表示 "匹配所有文档"。

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ 将查询字符串与 **$filter** 结合使用：让筛选器创建子集，让查询字符串提供字词输入，用于对筛选的子集进行全文搜索。 使用带有查询字符串的筛选器是最常用的模式。

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ 复合查询：使用“or”分隔查询，每个查询有自身的筛选条件（例如，'beagles' in 'dog' or 'siamese' in 'cat'）。 与 `or` 组合在一起的表达式分别进行计算，并将与每个表达式相匹配的文档的并送回响应。 此使用模式是通过 `search.ismatchscoring` 函数实现的。 你还可以使用非计分版本 `search.ismatch`。

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  还可以 `search.ismatchscoring` 通过使用 `and` 而不是 `or`的筛选器将全文搜索与筛选器组合在一起，但这在功能上等效于在搜索请求中使用 `search` 和 `$filter` 参数。 例如，以下两个查询生成相同的结果：

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

请参阅以下文章获取有关特定用例的综合性指导：

+ [Facet 筛选器](search-filters-facets.md)
+ [语言筛选器](search-filters-language.md)
+ [安全修整](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>与筛选相关的字段要求

在 REST API 中，默认情况下，对于简单字段*启用 "* 可筛选"。 可筛选字段会增大索引大小；对于不打算真正在筛选器中使用的字段，请务必设置 `"filterable": false`。 有关字段定义设置的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。

在 .NET SDK 中，可筛选性默认为“关”。 可以通过将相应[字段](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet)对象的[IsFilterable 属性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet)设置为 `true`来使字段可筛选。 还可以使用[IsFilterable 属性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute)以声明方式执行此操作。 在下面的示例中，在映射到索引定义的模型类的 `BaseRate` 属性上设置属性。

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>使现有字段可筛选

不能修改现有字段以使其可筛选。 相反，您需要添加新字段或重新生成索引。 有关重新生成索引或重新填充字段的详细信息，请参阅[如何重新生成 Azure 认知搜索索引](search-howto-reindex.md)。

## <a name="text-filter-fundamentals"></a>文本筛选器基础知识

文本筛选器将字符串字段与您在筛选器中提供的文本字符串匹配。 与全文搜索不同，文本筛选器没有词法分析或断字，因此比较仅用于精确匹配。 例如，假定字段*f*包含 "sunny day"，`$filter=f eq 'Sunny'` 不匹配，但 `$filter=f eq 'sunny day'` 会。 

文本字符串区分大小写。 大小写字不能小写： `$filter=f eq 'Sunny day'` 将找不到 "sunny day"。

### <a name="approaches-for-filtering-on-text"></a>文本筛选方法

| 方法 | 描述 | 何时使用 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 一个函数，它将一个字段与一个字符串列表分隔开。 | 建议用于[安全筛选器](search-security-trimming-for-azure-search.md)和任何筛选器，其中许多原始文本值需要与字符串字段匹配。 **Search.in**函数的设计速度非常快，并且比使用 `eq` 和 `or`对每个字符串显式比较字段的速度要快得多。 | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 用于在同一个筛选表达式中将全文搜索操作与严格的布尔筛选操作混合使用的函数。 | 如果需要在一个请求中使用多个搜索筛选器组合，请使用**ismatch** （或其计分等效项**ismatchscoring**）。 还可以使用该函数来构建 *contains* 筛选器，以根据较大字符串中的部分字符串进行筛选。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 由字段、运算符和值组成的用户定义的表达式。 | 如果要查找字符串字段和字符串值之间的完全匹配项，请使用此项。 |

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

+ [Azure 中全文搜索的工作原理认知搜索](search-lucene-query-architecture.md)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
