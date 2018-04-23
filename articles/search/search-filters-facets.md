---
title: Azure 搜索中的分面筛选器 | Microsoft Docs
description: 按用户安全标识、语言、地理位置或数字值进行条件筛选可以减少 Azure 搜索（Microsoft Azure 上的托管云搜索服务）中的查询返回的搜索结果。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 3f2cfea52d3c3f4bfc75364d0662a4218219152d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>如何在 Azure 搜索中生成分面筛选器 

分面导航用于在搜索应用中自定向筛选查询结果，其中应用程序提供 UI 控件以将搜索范围限定到文档组（例如，类别或品牌），Azure 搜索提供数据结构以支持体验。 在本文中，可快速了解创建分面导航的基本步骤，支持想要提供的搜索体验。 

> [!div class="checklist"]
> * 选择用于筛选和分面的字段
> * 设置字段属性
> * 生成索引和加载数据
> * 将分面筛选器添加到查询
> * 处理结果

分面为动态并在查询中返回。 搜索响应带有用于导航结果的分面类别。 如果不熟悉分面，可通过以下示例了解分面导航结构。

  ![](./media/search-filters-facets/facet-nav.png)

不熟悉分面导航并希望了解更多详情？ 请参阅[如何在 Azure 搜索中实现分面导航](search-faceted-navigation.md)。

## <a name="choose-fields"></a>选择字段

可通过单值字段以及集合计算分面。 适合分面导航的字段的多重性较低：搜索语料库（例如，颜色、国家/地区或品牌名列表）的文档中重复的不同值较少。 

通过将以下属性设置为 True，便可在创建索引时逐字段启用分面：`filterable`、`facetable`。 只有可筛选的字段才可分面。

可能用于分面导航的所有[字段类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)均标记为“可查找”：

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ 数值字段类型：Edm.Int32、Edm.Int64、Edm.Double

不能在分面导航中使用 Edm.GeographyPoint。 分面基于可人工读取的文本或数字而构造。 因此，地理坐标不支持分面。 需要城市或区域字段才可实现按位置进行分面。

## <a name="set-attributes"></a>设置属性

将控制字段使用方式的索引属性添加到索引中的各字段定义。 在以下示例中，具有低多重性并且适合用于分面的字段包括：类别（酒店、汽车旅馆、招待所）、便利设施和分级。 

在 .NET API 中，必须显式设置筛选属性。 在 REST API 中，分面和筛选默认启用，这意味着只需在想要将其关闭时显式设置属性即可。 虽然从技术上讲这不是必需的，但为方便说明，我们在下面的 REST 示例中介绍了这些属性。 

> [!Tip]
> 为实现最佳性能和存储优化，请针对绝不应用作分面的字段关闭分面功能。 具体而言，应将单独值的字符串字段（例如 ID 或产品名称）设置为“可查找”：false，以避免在分面导航中意外（和无效）使用它们。


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> 此索引定义复制自[使用 REST API 创建 Azure 搜索索引](https://docs.microsoft.com/azure/search/search-create-index-rest-api)。 除了字段定义的表面差异外，二者完全相同。 将可筛选和可查找属性显式添加到类别、标记、含停车、允许吸烟和分级字段。 在实践中，可在 Edm.String、Edm.Boolean 和 Edm.Int32 字段类型中免费获取可筛选和可查找属性。 

## <a name="build-and-load-an-index"></a>生成和加载索引

编写查询之前的一个中间步骤（也许是众所周知的步骤）是[生成并填充索引](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index)。 为了保持内容完整，此处阐述了此步骤。 确定索引是否可用的一种方法是在[门户](https://portal.azure.com)中查看索引列表。

## <a name="add-facet-filters-to-a-query"></a>将分面筛选器添加到查询

在应用程序代码中构造一个查询，该查询指定有效查询的所有部分，包括搜索表达式、分面、筛选器、计分配置文件 - 所有这些内容都用于明确表述请求。 以下示例基于住宿、分级及其他便利设施类型来生成创建分面导航的请求。

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>针对单击事件返回筛选结果

筛选器表达式处理分面值的单击事件。 假设分面依据为类别，通过 `$filter` 表达式单击类别“汽车旅馆”，表示选择属于该类型的住宿。 当用户单击“汽车旅馆”，指示应仅显示汽车旅馆时，应用程序发送的下一条查询将包括 $filter=category eq ‘motels’。

下面的代码片段添加类别以筛选用户是否从类别分面中选择某个值。

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
使用 REST API 时，请求被表达为 `$filter=category eq 'c1'`。 要使类别成为多值字段，请使用以下语法：`$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>提示和解决方法

### <a name="initialize-a-page-with-facets-in-place"></a>就地使用分面初始化页面

如果希望就地使用分面初始化页面，可在页面初始化过程中发送查询，以借助初始分面结构设定页面的种子。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>以异步方式保留筛选结果的分面导航结构

在 Azure 搜索中使用分面导航所面临的一个难题是，分面仅针对当前结果存在。 在实践中，通常会保留一组静态分面，便于用户按相反顺序进行导航，回顾步骤以通过搜索内容了解可选路径。 

虽然这是常见的用例，但并不是分面导航结构当前提供的现成内容。 通过发布 2 个筛选查询（一个将范围限定到结果，另一个用于创建针对导航的静态分面列表），需要静态分面的开发人员通常便可解决限制问题。

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中的筛选器](search-filters.md)
+ [创建索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

