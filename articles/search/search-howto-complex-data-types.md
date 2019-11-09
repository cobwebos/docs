---
title: 如何为复杂数据类型建模
titleSuffix: Azure Cognitive Search
description: 使用 ComplexType 和集合数据类型，可以在 Azure 认知搜索索引中对嵌套或分层数据结构建模。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c7578b84fe1d23d2b4d97aa263cac576305db240
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889913"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>如何在 Azure 中为复杂数据类型建模认知搜索

用于填充 Azure 认知搜索索引的外部数据集可以有多个形状。 有时它们包含分层或嵌套的子结构。 示例包括单个客户的多个地址、单个 SKU 的多个颜色和大小、一本书籍的多位作者等等。 在建模术语中，这些结构可能称作复杂、组合、复合或聚合数据类型。 Azure 认知搜索用于此概念的术语是**复杂类型**。 在 Azure 认知搜索中，使用**复杂字段**对复杂类型建模。 复杂字段是包含子级（子字段）的字段，这些子级可以是任何数据类型（包括其他复杂类型）。 其工作原理类似于编程语言中的结构化数据类型。

复杂字段表示文档中的单个对象，或对象的数组，具体取决于数据类型。 `Edm.ComplexType` 类型的字段表示单个对象，而 `Collection(Edm.ComplexType)` 类型的字段表示对象的数组。

Azure 认知搜索本机支持复杂类型和集合。 这些类型使你能够为 Azure 认知搜索索引中的几乎任何 JSON 结构建模。 在早期版本的 Azure 中，认知搜索 Api，只能导入平展行集。 在最新版本中，索引可以更密切地对应于源数据。 换言之，如果源数据使用复杂类型，则索引也可以使用复杂类型。

若要开始，我们建议使用 [Hotels 数据集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)，可以在 Azure 门户上“导入数据”向导中加载该数据集。 该向导会检测源中的复杂类型，并根据检测到的结构建议一个索引架构。

> [!Note]
> `api-version=2019-05-06` 正式支持复杂类型。 
>
> 如果你的搜索解决方案是基于以前的解决方法（集合中的平展数据集）生成的，应更改索引，使之包含最新 API 版本支持的复杂类型。 有关升级的 API 版本的详细信息，请参阅[升级到最新的 REST API 版本](search-api-migration.md)或[升级到最新的 .NET SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>复杂结构的示例

以下 JSON 文档由简单字段和复杂字段构成。 复杂字段（例如 `Address` 和 `Rooms`）包含子字段。 `Address` 包含这些子字段的单个值集，因为它是文档中的单个对象。 相反，`Rooms` 包含其子字段的多个值集，集合中的每个对象各有一个值集。

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>创建复杂字段

与处理任何索引定义时一样，可以使用门户、[REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 或 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 创建包含复杂类型的架构。 

以下示例演示了包含简单字段、集合与复杂类型的 JSON 索引架构。 请注意，在复杂类型中，与顶级字段一样，每个子字段包含一个类型，有时还包含属性。 架构对应于以上示例数据。 `Address` 是一个非集合的复杂字段（一家酒店只有一个地址）。 `Rooms` 是复杂集合字段（一家酒店有多间客房）。

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Cognitive Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>更新复杂字段

一般情况下，应用于字段的所有[重建索引规则](search-howto-reindex.md)仍会应用于复杂字段。 在此处重述一些主要规则以及添加字段并不需要重建索引，但大多数修改操作需要重建索引。

### <a name="structural-updates-to-the-definition"></a>对定义的结构更新

随时可以将新的子字段添加到复杂字段，而无需重建索引。 例如，允许将“ZipCode”添加到 `Address`或者将“Amenities”添加到 `Rooms`，就如同将顶级字段添加到索引一样。 在通过更新数据显式填充新字段之前，现有文档将对这些字段使用 null 值。

请注意，在复杂类型中，与顶级字段一样，每个子字段包含一个类型，有时还包含属性

### <a name="data-updates"></a>数据更新

对于复杂字段和简单字段而言，使用 `upload` 操作更新索引中现有文档的过程是相同的 -- 将替换所有字段。 但是，`merge`（应用于现有文档时使用 `mergeOrUpload`）对所有字段的运行方式不同。 具体而言，`merge` 不支持合并集合中的元素。 基元类型集合与复杂集合存在此限制。 若要更新集合，需要检索整个集合值，进行更改，然后在索引 API 请求中包含新的集合。

## <a name="searching-complex-fields"></a>搜索复杂字段

可按预期方式对复杂类型运行自由形式的搜索表达式。 如果文档中的任何位置存在任何一个匹配的可搜索字段或子字段，则该文档本身就是一个匹配项。

如果使用多个字词或运算符，并且某些字词指定了字段名（可以使用 [Lucene 语法](query-lucene-syntax.md)来指定），则查询会变得更微妙。 例如，此查询尝试将两个字词“Portland”和“OR”与 Address 字段的两个子字段相匹配：

    search=Address/City:Portland AND Address/State:OR

此类查询对于全文搜索是不相关联的，这与筛选器不同。 在筛选器中，基于复杂集合子字段的查询将通过 [`any` 或 `all`](search-query-odata-collection-operators.md) 中的范围变量相关联。 上述 Lucene 查询返回包含“Portland, Maine”和“Portland, Oregon”以及 Oregon 中其他城市的文档。 之所以返回此结果，是因为每个子句将应用到其在整个文档中的字段的所有值，因此没有“当前子文档”的概念。 有关此内容的详细信息，请参阅[了解 Azure 认知搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>选择复杂字段

`$select` 参数用于选择要在搜索结果中返回哪些字段。 若要使用此参数来选择复杂字段的特定子字段，请包含斜杠 (`/`) 分隔的父字段和子字段。

    $select=HotelName, Address/City, Rooms/BaseRate

如果希望这些字段在搜索结果中出现，必须在索引中将其标记为可检索。 只有标记为可检索的字段才能在 `$select` 语句中使用。

## <a name="filter-facet-and-sort-complex-fields"></a>筛选、分面和排序复杂字段

用作筛选和带字段搜索的 [OData 路径语法](query-odata-filter-orderby-syntax.md)同样也可用于分面、排序和选择搜索请求中的字段。 对于复杂类型，可以应用规则来控制可将哪些子字段标记为可排序或可分面。 有关这些规则的详细信息，请参阅[创建索引 API 参考](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。

### <a name="faceting-sub-fields"></a>分面子字段

除非类型为 `Edm.GeographyPoint` 或 `Collection(Edm.GeographyPoint)`，否则任何子字段都可标记为可分面。

分面结果中返回的文档计数是根据父文档（酒店）计算的，而不是根据复杂集合中的子文档（客房）计算的。 例如，假设某家酒店有 20 间“套房”类型的客房。 如果此分面参数为 `facet=Rooms/Type`，则分面计数将是 1 家酒店，而不是 20 间客房。

### <a name="sorting-complex-fields"></a>排序复杂字段

排序操作将应用于文档（酒店）而不是子文档（客房）。 使用复杂类型集合（例如客房）时必须认识到，根据无法按“客房”排序。 事实上，无法按任何集合进行排序。

如果每个文档的字段是单值的，不管字段是简单字段，还是复杂类型中的子字段，排序操作都可正常运行。 例如，允许 `Address/City` 可排序，因为每家酒店只有一个地址，因此 `$orderby=Address/City` 将按城市对酒店排序。

### <a name="filtering-on-complex-fields"></a>根据复杂字段进行筛选

可以在筛选表达式中引用复杂字段的子字段。 只需使用对分面、排序和选择字段所用的相同 [OData 路径语法](query-odata-filter-orderby-syntax.md)。 例如，以下筛选器将返回位于加拿大的所有酒店：

    $filter=Address/Country eq 'Canada'

若要根据复杂集合字段进行筛选，可以结合 和 [ 运算符`any`使用 `all`Lambda 表达式](search-query-odata-collection-operators.md)。 在这种情况下，Lambda 表达式的**范围变量**是包含子字段的对象。 可以使用标准 OData 路径语法来引用这些子字段。 例如，以下筛选器将返回至少提供一间豪华客房，且所有客房都禁止吸烟的所有酒店：

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

与顶级简单字段一样，仅当已在索引定义中将复杂字段的简单子字段的 **filterable** 属性设置为 `true` 时，才能在筛选器中包含这些子字段。 有关详细信息，请参阅[创建索引 API 参考](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。

## <a name="next-steps"></a>后续步骤

尝试在“导入数据”向导中练习 [Hotels 数据集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 需要使用自述文件中提供的 Cosmos DB 连接信息来访问这些数据。

获取该信息后，向导中的第一步是创建新的 Azure Cosmos DB 数据源。 然后，在向导中进入目标索引页后，会看到使用复杂类型的索引。 请创建并加载此索引，然后执行查询来了解新结构。

> [!div class="nextstepaction"]
> [快速入门：用于导入、索引编制和查询的门户向导](search-get-started-portal.md)
