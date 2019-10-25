---
title: 应用中搜索导航的分面筛选器
titleSuffix: Azure Cognitive Search
description: 按用户安全标识、地理位置或数字值筛选条件，以减少 Azure 认知搜索中托管的云搜索服务 Microsoft Azure 的搜索结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792898"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>如何在 Azure 中生成 facet 筛选器认知搜索 

分面导航用于在搜索应用中查询结果的自定向筛选，其中应用程序提供了用于将搜索范围限定到文档组（例如类别或品牌）的 UI 控件，以及 Azure 认知搜索提供数据结构备份体验。 在本文中，可快速了解创建分面导航的基本步骤，支持想要提供的搜索体验。 

> [!div class="checklist"]
> * 选择用于筛选和分面的字段
> * 设置字段属性
> * 生成索引和加载数据
> * 将分面筛选器添加到查询
> * 处理结果

分面为动态并在查询中返回。 搜索响应带有用于导航结果的分面类别。 如果不熟悉分面，可通过以下示例了解分面导航结构。

  ![](./media/search-filters-facets/facet-nav.png)

不熟悉分面导航并希望了解更多详情？ 请参阅[如何在 Azure 认知搜索中实现分面导航](search-faceted-navigation.md)。

## <a name="choose-fields"></a>选择字段

可通过单值字段以及集合计算分面。 最适合分面导航的字段具有低基数：在搜索语料库中，在文档中重复的少量非重复值（例如，颜色、国家/地区或品牌名称的列表）。 

通过将 `facetable` 特性设置为 `true`来创建索引时，逐字段启用分面。 您通常还应将此类字段的 `filterable` 属性设置为 `true`，以便您的搜索应用程序可以基于最终用户选择的方面对这些字段进行筛选。 

使用 REST API 创建索引时，可能会在分面导航中使用的任何[字段类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)在默认情况下将标记为 `facetable`：

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ 数值字段类型： `Edm.Int32`、`Edm.Int64``Edm.Double`
+ 以上类型（例如 `Collection(Edm.String)` 或 `Collection(Edm.Double)`）的集合

不能在分面导航中使用 `Edm.GeographyPoint` 或 `Collection(Edm.GeographyPoint)` 字段。 Facet 最适用于具有较低基数的字段。 由于地理坐标的解决方法，在给定的数据集中，两组坐标都不等。 因此，地理坐标不支持分面。 需要城市或区域字段才可实现按位置进行分面。

## <a name="set-attributes"></a>设置属性

将控制字段使用方式的索引属性添加到索引中的各字段定义。 在下面的示例中，具有较低基数的字段对于分面非常有用，它包含： `category` （旅馆、motel、招待所）、`tags`和 `rating`。 出于说明目的，这些字段在以下示例中显式设置了 `filterable` 和 `facetable` 属性。 

> [!Tip]
> 为实现最佳性能和存储优化，请针对绝不应用作分面的字段关闭分面功能。 具体而言，应将唯一值的字符串字段（例如 ID 或产品名称）设置为 `"facetable": false`，以防止在分面导航中意外（和无效）使用它们。


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> [使用 REST API 从创建 Azure 认知搜索索引](https://docs.microsoft.com/azure/search/search-create-index-rest-api)复制此索引定义。 除了字段定义的表面差异外，二者完全相同。 `filterable` 和 `facetable` 属性显式添加到 `category`、`tags`、`parkingIncluded`、`smokingAllowed`和 `rating` 字段上。 在实践中，使用 REST API 时，默认情况下会对这些字段启用 `filterable` 和 `facetable`。 使用 .NET SDK 时，必须显式启用这些属性。

## <a name="build-and-load-an-index"></a>生成和加载索引

编写查询之前的一个中间步骤（也许是众所周知的步骤）是[生成并填充索引](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index)。 为了保持内容完整，此处阐述了此步骤。 确定索引是否可用的一种方法是在[门户](https://portal.azure.com)中查看索引列表。

## <a name="add-facet-filters-to-a-query"></a>将分面筛选器添加到查询

在应用程序代码中构造一个查询，该查询指定有效查询的所有部分，包括搜索表达式、分面、筛选器、计分配置文件 - 所有这些内容都用于明确表述请求。 以下示例基于住宿、分级及其他便利设施类型来生成创建分面导航的请求。

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>针对单击事件返回筛选结果

当最终用户单击分面值时，单击事件的处理程序应使用筛选器表达式来实现用户的意图。 给定 `category` 方面，单击 "motel" 类别时，将使用选择该类型的便利的 `$filter` 表达式来实现。 当用户单击 "motel" 指示只显示汽车旅馆时，应用程序发送的下一个查询将包括 `$filter=category eq 'motel'`。

下面的代码片段添加类别以筛选用户是否从类别分面中选择某个值。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

如果用户单击集合 `tags`字段（例如，值为 "pool"）的方面值，应用程序应使用以下筛选器语法： `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>提示和解决方法

### <a name="initialize-a-page-with-facets-in-place"></a>就地使用分面初始化页面

如果希望就地使用分面初始化页面，可在页面初始化过程中发送查询，以借助初始分面结构设定页面的种子。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>以异步方式保留筛选结果的分面导航结构

Azure 认知搜索中的分面导航的难题之一是仅存在于当前结果中的方面。 在实践中，通常会保留一组静态分面，便于用户按相反顺序进行导航，回顾步骤以通过搜索内容了解可选路径。 

虽然这是常见的用例，但并不是分面导航结构当前提供的现成内容。 通过发布 2 个筛选查询（一个将范围限定到结果，另一个用于创建针对导航的静态分面列表），需要静态分面的开发人员通常便可解决限制问题。

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索中的筛选器](search-filters.md)
+ [创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
