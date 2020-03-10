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
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396100"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>如何在 Azure 中为复杂数据类型建模认知搜索

用于填充 Azure 认知搜索索引的外部数据集可以有多个形状。 有时它们包含分层或嵌套的子结构。 示例可能包括单个客户的多个地址、单个 SKU 的多种颜色和大小、单个书籍的多个作者等等。 在建模术语中，你可能会看到这些结构称为*复杂*、*复合*、*复合*或*聚合*数据类型。 Azure 认知搜索用于此概念的术语是**复杂类型**。 在 Azure 认知搜索中，使用**复杂字段**对复杂类型建模。 "复杂字段" 是包含子字段（子字段）的字段，它们可以是任何数据类型，包括其他复杂类型。 这与编程语言中的结构化数据类型的工作方式类似。

复杂字段表示文档中的单个对象或对象的数组，具体取决于数据类型。 `Edm.ComplexType` 类型的字段表示单个对象，而类型 `Collection(Edm.ComplexType)` 的字段表示对象的数组。

Azure 认知搜索本机支持复杂类型和集合。 这些类型使你能够为 Azure 认知搜索索引中的几乎任何 JSON 结构建模。 在早期版本的 Azure 中，认知搜索 Api，只能导入平展行集。 在最新版本中，您的索引现在可以更密切地与源数据相对应。 换句话说，如果源数据具有复杂类型，则索引也可以具有复杂类型。

若要开始，我们建议你可以在 Azure 门户的 "**导入数据**" 向导中加载[宾馆数据集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 向导将检测源中的复杂类型，并根据检测到的结构建议索引架构。

> [!Note]
> `api-version=2019-05-06`中公开了对复杂类型的支持。 
>
> 如果你的搜索解决方案是以集合中平展数据集的更早解决方法为基础构建的，则应更改索引，使其包含在最新 API 版本中受支持的复杂类型。 有关升级 API 版本的详细信息，请参阅[升级到最新 REST API 版本](search-api-migration.md)或[升级到最新的 .net SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>复杂结构的示例

下面的 JSON 文档由简单的字段和复杂字段组成。 复杂字段（如 `Address` 和 `Rooms`）具有子字段。 对于这些子字段，`Address` 具有一组值，因为它是文档中的单个对象。 与此相反，`Rooms` 的子字段有多组值，集合中的每个对象各有一个值。

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

与任何索引定义一样，可以使用门户、 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet)创建包含复杂类型的架构。 

下面的示例演示具有简单字段、集合和复杂类型的 JSON 索引架构。 请注意，在复杂类型中，每个子字段都具有类型并且可能具有属性，就像顶层字段一样。 此架构对应于上面的示例数据。 `Address` 是一个不是集合的复杂字段（一个旅馆有一个地址）。 `Rooms` 是一种复杂的集合字段（一个旅馆有很多房间）。

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

适用于常规字段的所有[索引编制规则](search-howto-reindex.md)仍适用于复杂字段。 在这里，重述几个主要规则，添加字段不需要重新生成索引，但大多数修改都是如此。

### <a name="structural-updates-to-the-definition"></a>定义的结构更新

您可以随时向复杂字段添加新的子字段，而无需重新生成索引。 例如，可以将 "ZipCode" 添加到 `Address` 或将 "" 添加到 `Rooms` 中，就像向索引添加顶级字段一样。 现有文档在通过更新数据显式填充这些字段之前，新字段的值为 null。

请注意，在复杂类型中，每个子字段都具有类型并且可能具有属性，就像顶层字段一样。

### <a name="data-updates"></a>数据更新

使用 `upload` 操作更新索引中的现有文档的工作方式对于复杂字段和简单字段都是相同的，所有字段都将被替换。 但是 `merge` （或应用于现有文档时 `mergeOrUpload`）在所有字段中都不起作用。 具体而言，`merge` 不支持合并集合中的元素。 对于基元类型和复杂集合的集合存在此限制。 若要更新集合，需要检索完整的集合值，进行更改，然后在索引 API 请求中包括新集合。

## <a name="searching-complex-fields"></a>搜索复杂字段

自由格式搜索表达式在复杂类型上按预期方式工作。 如果文档中任意位置的任何可搜索字段或子字段均匹配，则文档本身就是匹配项。

当你有多个字词和运算符时，查询会获得更多的微妙，一些术语指定了字段名，如[Lucene 语法](query-lucene-syntax.md)所示。 例如，此查询尝试根据地址字段的两个子字段来匹配两个字词： "上" 和 "OR"：

    search=Address/City:Portland AND Address/State:OR

与筛选器不同，此类*查询与全文搜索不相关*。 在筛选器中，使用[`any` 或 `all`](search-query-odata-collection-operators.md)中的范围变量来关联复杂集合的子字段查询。 上述 Lucene 查询返回包含 "Maine" 和 "俄勒冈州" 的文档以及俄勒冈州的其他城市。 出现这种情况的原因是，每个子句都适用于其在整个文档中的字段的所有值，因此没有 "当前子文档" 的概念。 有关此内容的详细信息，请参阅[了解 Azure 认知搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>选择复杂字段

`$select` 参数用于选择在搜索结果中返回的字段。 若要使用此参数来选择复杂字段的特定子字段，请包含用斜杠（`/`）分隔的父字段和子字段。

    $select=HotelName, Address/City, Rooms/BaseRate

如果要在搜索结果中填写字段，则必须将其标记为可检索。 只能在 `$select` 语句中使用标记为可检索的字段。

## <a name="filter-facet-and-sort-complex-fields"></a>筛选、facet 和排序复杂字段

用于筛选和现场搜索的相同[OData 路径语法](query-odata-filter-orderby-syntax.md)还可用于分面、排序和选择搜索请求中的字段。 对于复杂类型，规则适用，用于管理可以标记为可排序或可查找的子字段。 有关这些规则的详细信息，请参阅[创建索引 API 参考](/rest/api/searchservice/create-index)。

### <a name="faceting-sub-fields"></a>分面子字段

任何子字段均可标记为可查找，除非该字段的类型 `Edm.GeographyPoint` 或 `Collection(Edm.GeographyPoint)`。

在分面结果中返回的文档数计算为父文档（宾馆），而不是复杂集合（房间）中的子文档。 例如，假设某酒店的 "suite" 类型为20个房间。 给定此方面参数 `facet=Rooms/Type`，分面计数将是旅馆的一个，而不是用于房间的20个。

### <a name="sorting-complex-fields"></a>排序复杂字段

排序操作适用于文档（宾馆），而不适用于子文档（房间）。 如果你有一个复杂类型的集合（如聊天室），请务必认识到根本就不能对文件组进行排序。 事实上，您不能对任何集合进行排序。

当字段具有每个文档的单个值、字段是简单字段还是复杂类型中的子字段时，排序操作将起作用。 例如，可以对 `Address/City` 进行排序，因为每个宾馆只有一个地址，因此 `$orderby=Address/City` 会按城市对酒店进行排序。

### <a name="filtering-on-complex-fields"></a>筛选复杂字段

您可以引用筛选器表达式中的复杂字段的子字段。 只需使用用于分面、排序和选择字段的相同[OData 路径语法](query-odata-filter-orderby-syntax.md)。 例如，以下筛选器将返回加拿大的所有酒店：

    $filter=Address/Country eq 'Canada'

若要对复杂集合字段进行筛选，可以将**lambda 表达式**与[`any` 和 `all` 运算符](search-query-odata-collection-operators.md)一起使用。 在这种情况下，lambda 表达式的**范围变量**是具有子字段的对象。 可以通过标准 OData 路径语法来引用这些子字段。 例如，以下筛选器将返回至少具有一个高级房间和所有非吸烟房间的所有酒店：

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

与顶级简单字段一样，复杂字段的简单子字段只能包含在筛选器中（如果**它们在索引**定义中设置为 `true`。 有关详细信息，请参阅[创建索引 API 参考](/rest/api/searchservice/create-index)。

## <a name="next-steps"></a>后续步骤

请在 "**导入数据**" 向导中尝试[酒店数据集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。 你将需要该自述文件中提供的 Cosmos DB 连接信息来访问数据。

使用该信息时，向导中的第一步是创建新的 Azure Cosmos DB 数据源。 在向导中，当您转到目标索引页时，您将看到一个具有复杂类型的索引。 创建并加载此索引，然后执行查询来了解新的结构。

> [!div class="nextstepaction"]
> [快速入门：导入、索引和查询的门户向导](search-get-started-portal.md)
