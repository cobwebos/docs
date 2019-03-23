---
title: 自动建议添加到搜索框-Azure 搜索的下拉列表中条款的示例
description: 通过创建建议器，并构建调用 Azure 搜索中的建议的查询的请求添加建议的查询的输入。
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373070"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>示例：添加下拉列表中的查询选择的自动建议

搜索术语输入可以包括一个建议的查询词的下拉列表。 您已了解此功能在商业搜索引擎，并且可以在 Azure 搜索中使用实现相似的体验[建议器构造](index-add-suggesters.md)和上一个查询请求的建议操作。 本文使用示例来演示如何使用建议器已经定义了一个自动建议查询的设计。 

## <a name="rest-api"></a>REST API

可以使用[Postman](search-fiddler.md)或[PowerShell](search-create-index-rest-api.md)并[REST API](https://docs.microsoft.com/rest/api/searchservice/)尝试此示例中，但结果将返回为 JSON 文档。 可以使用查找更加真实，并且 visual 体验[记忆式键入功能的示例代码](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)。

### <a name="1---index-with-a-suggester-construct"></a>1-索引使用建议器构造

自动建议开头[建议器构造](index-add-suggesters.md)添加到索引，由提供下拉列表中值的字段组成。 给定以下架构，建议的查询将使用进行表述值从 hotelName 和类别字段。

假设在快速入门中找到的非常小的示例数据集，酒店名称包括"花哨保持"和"Roach motel"和类别包括"Luxury"和"Budget"。

如果已有 hotels 索引，应删除并重新创建它使用以下架构。 此架构将添加建议器。 请记住重新加载数据。

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2-查询使用建议运算符

若要使用建议器和调用自动建议，必须在发送[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)请求使用 GET 或 POST。 在此类请求中，搜索服务将扫描的可能匹配项后接收到的前三个字符。 

在请求标头，设置**api 密钥**管理员或查询注册表项，并**内容类型**为 application/json。 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

所有字段都包含在建议器 （hotelName 和类别），则请求扫描返回以下响应：

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

若要传送的预期的结果，客户端代码将呈现为下一个搜索栏下拉列表中的结果。

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1-索引使用建议器构造

在.NET SDK 中，使用[建议器类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 建议器是一个集合，但该方法仅采用一个项。

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2-查询使用建议的方法

[DocumentsOperationsExtensions.Suggest 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)用于返回建议的查询字符串。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>另请参阅

+ [了解使用 Postman 的 REST API](search-fiddler.md)
+ 示例：[记忆式键入功能](search-autocomplete-tutorial.md)
+ [将建议器添加到索引](index-add-suggesters.md)
+ [添加使用.NET 的建议器类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [调用建议使用 GET 或 POST (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [调用使用 SuggestWithHttpMessagesAsync (.NET) 的建议](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview)或 