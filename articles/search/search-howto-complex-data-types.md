---
title: 如何为复杂数据类型建模 - Azure 搜索
description: 可以在 Azure 搜索索引中使用 ComplexType 和 Collections 数据类型为嵌套或分层数据结构建模。
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: e7e6ddefd13d669c949389bc4fad85fb6cff4d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621364"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>如何在 Azure 搜索中为复杂数据类型建模

在多个形状可以来自外部数据集用于填充 Azure 搜索索引。 有时，它们包括分层或嵌套子结构。 示例可能包括多个地址的单个客户、 多个颜色和大小的单个 SKU，单个书籍的多位作者等等。 在建模术语中，可能会看到这些结构称为*复杂*，*复合*，*复合*，或者*聚合*数据类型。 Azure 搜索使用的这一概念术语**复杂类型**。 在 Azure 搜索中，复杂类型使用进行建模**复杂字段**。 复杂的字段是包含子级 （子字段） 可以是任何数据类型，包括其他复杂类型的字段。 这可以以类似的方式与编程语言中的结构化的数据类型。

复杂的字段表示是在文档中，单个对象或对象，具体取决于数据类型的数组。 类型的字段`Edm.ComplexType`表示单个对象，类型的字段时`Collection(Edm.ComplexType)`表示对象的数组。

Azure 搜索原生支持复杂类型和集合。 这些类型，可在 Azure 搜索索引中的几乎所有 JSON 结构都建模。 在旧版的 Azure 搜索 API 中，只能导入平展的行集。 在最新版本中，索引可以更密切地对应于源数据。 换言之，如果源数据使用复杂类型，则索引也可以使用复杂类型。

若要开始，我们建议使用 [Hotels 数据集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)，可以在 Azure 门户上“导入数据”向导中加载该数据集。  该向导会检测源中的复杂类型，并根据检测到的结构建议一个索引架构。

> [!Note]
> `api-version=2019-05-06` 正式支持复杂类型。 
>
> 如果你的搜索解决方案是基于以前的解决方法（集合中的平展数据集）生成的，应更改索引，使之包含最新 API 版本支持的复杂类型。 有关升级的 API 版本的详细信息，请参阅[升级到最新的 REST API 版本](search-api-migration.md)或[升级到最新的 .NET SDK 版本](search-dotnet-sdk-migration-version-9.md)。

## <a name="example-of-a-complex-structure"></a>复杂结构的示例

以下 JSON 文档由简单字段和复杂字段构成。 复杂字段（例如 `Address` 和 `Rooms`）包含子字段。 `Address` 具有一组这些子字段的值，因为它是单个对象在文档中。 相反，`Rooms` 包含其子字段的多个值集，集合中的每个对象各有一个值集。

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

以下示例演示了包含简单字段、集合与复杂类型的 JSON 索引架构。 请注意，在复杂类型中，与顶级字段一样，每个子字段包含一个类型，有时还包含属性。 架构对应于以上示例数据。 `Address` 是一个复杂的字段，不是一个集合 （一家酒店都有一个地址）。 `Rooms` 是复杂集合字段（一家酒店有多间客房）。

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

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

一般情况下，应用于字段的所有[重建索引规则](search-howto-reindex.md)仍会应用于复杂字段。 主要规则在这里，重述一些添加字段不需要重新生成索引，但大多数修改执行操作。

### <a name="structural-updates-to-the-definition"></a>对定义的结构更新

随时可以将新的子字段添加到复杂字段，而无需重建索引。 例如，允许将“ZipCode”添加到 `Address`或者将“Amenities”添加到 `Rooms`，就如同将顶级字段添加到索引一样。 在通过更新数据显式填充新字段之前，现有文档将对这些字段使用 null 值。

请注意，在复杂类型中，与顶级字段一样，每个子字段包含一个类型，有时还包含属性

### <a name="data-updates"></a>数据更新

更新现有文档索引中包含`upload`操作的工作方式相同复杂和简单字段-替换所有字段。 但是， `merge` (或`mergeOrUpload`时应用于现有文档) 不起作用相同的所有字段。 具体而言，`merge`不支持合并集合中的元素。 为基元类型的集合和复杂集合存在此限制。 若要更新集合，您将需要检索完整的集合值进行的更改，并将包含新集合在索引 API 请求。

## <a name="searching-complex-fields"></a>搜索复杂字段

可按预期方式对复杂类型运行自由形式的搜索表达式。 如果文档中的任何位置存在任何一个匹配的可搜索字段或子字段，则该文档本身就是一个匹配项。

如果使用多个字词或运算符，并且某些字词指定了字段名（可以使用 [Lucene 语法](query-lucene-syntax.md)来指定），则查询会变得更微妙。 例如，此查询尝试将两个字词“Portland”和“OR”与 Address 字段的两个子字段相匹配：

    search=Address/City:Portland AND Address/State:OR

与此类似的查询*无关联*用于全文搜索，与筛选器不同。 在筛选器中，通过一系列复杂的子字段的查询使用关联中的范围变量[`any`或`all` ](search-query-odata-collection-operators.md)。 上面的 Lucene 查询返回包含"波特兰，Maine"和"波特兰，俄勒冈州"，以及其他城市俄勒冈州的文档。 这是因为每个子句适用于在整个文档中，其字段的所有值，因此不存在的"当前子文档"的概念。 有关这方面的详细信息，请参阅[Azure 搜索中的了解 OData 集合筛选器](search-query-understand-collection-filters.md)。

## <a name="selecting-complex-fields"></a>选择复杂字段

`$select` 参数用于选择要在搜索结果中返回哪些字段。 若要使用此参数来选择复杂字段的特定子字段，请包含斜杠 (`/`) 分隔的父字段和子字段。

    $select=HotelName, Address/City, Rooms/BaseRate

如果希望这些字段在搜索结果中出现，必须在索引中将其标记为可检索。 只有标记为可检索的字段才能在 `$select` 语句中使用。

## <a name="filter-facet-and-sort-complex-fields"></a>筛选、分面和排序复杂字段

用作筛选和带字段搜索的 [OData 路径语法](query-odata-filter-orderby-syntax.md)同样也可用于分面、排序和选择搜索请求中的字段。 对于复杂类型，可以应用规则来控制可将哪些子字段标记为可排序或可分面。 这些规则的详细信息，请参阅[创建索引 API 引用](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。

### <a name="faceting-sub-fields"></a>分面子字段

除非类型为 `Edm.GeographyPoint` 或 `Collection(Edm.GeographyPoint)`，否则任何子字段都可标记为可分面。

分面结果中返回的文档计数进行计算的父文档 （酒店） 不是复杂集合 （聊天室） 中的子文档。 例如，假设某家酒店有 20 间“套房”类型的客房。 给定此分面参数`facet=Rooms/Type`，分面计数将是一个用于旅馆房间的不 20。

### <a name="sorting-complex-fields"></a>排序复杂字段

排序操作将应用于文档（酒店）而不是子文档（客房）。 在必须将复杂类型集合，例如聊天室，请务必认识到，不能在对排序聊天室根本。 事实上，不能对任何集合进行排序。

排序操作时工作字段具有单个值每个文档，该字段是一个简单的字段，还是复杂类型中的子字段。 例如，`Address/City`可以是可排序，因为因此每个酒店，只能将一个地址`$orderby=Address/City`将对按市/县的酒店进行排序。

### <a name="filtering-on-complex-fields"></a>对复杂字段进行筛选

您可以引用子字段的筛选器表达式中的复杂字段。 只需使用相同[OData 路径语法](query-odata-filter-orderby-syntax.md)用于分面、 排序和选择的字段。 例如，以下筛选器将在加拿大返回的所有酒店：

    $filter=Address/Country eq 'Canada'

若要筛选的复杂集合字段，可以使用**lambda 表达式**与[`any`并`all`运算符](search-query-odata-collection-operators.md)。 在这种情况下，**范围变量**的 lambda 表达式是具有子字段的对象。 您可以参考标准 OData 路径语法使用这些子字段。 例如，以下筛选器将返回与至少一个高级聊天室的所有酒店和所有非的-吸烟聊天室：

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

因为顶级简单字段后，简单的子字段的复杂字段只能包含筛选器中如果它们具有**可筛选**属性设置为`true`索引定义中。 有关详细信息，请参阅[创建索引 API 引用](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。

## <a name="next-steps"></a>后续步骤

尝试在“导入数据”向导中练习 [Hotels 数据集](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)。  需要使用自述文件中提供的 Cosmos DB 连接信息来访问这些数据。

获取该信息后，向导中的第一步是创建新的 Azure Cosmos DB 数据源。 进一步上在向导中，转到目标索引页中，您将看到索引具有复杂类型。 请创建并加载此索引，然后执行查询来了解新结构。

> [!div class="nextstepaction"]
> [快速入门：用于导入、索引编制和查询的门户向导](search-get-started-portal.md)