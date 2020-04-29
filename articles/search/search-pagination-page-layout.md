---
title: 如何使用搜索结果
titleSuffix: Azure Cognitive Search
description: 对搜索结果进行组织和排序、获取记录计数并将内容导航添加到 Azure 认知搜索中的搜索结果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f815003449f0600bce1cb8927b92b85b51b09a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641624"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中使用搜索结果

本文介绍如何获取返回的查询响应，其中包含匹配文档的总数、分页结果、排序结果和突出显示的字词。

响应的结构由查询中的参数确定：在 REST API 中[搜索文档](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)，或在 .net SDK 中的[DocumentSearchResult 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)中搜索。

## <a name="result-composition"></a>结果组合

尽管搜索文档可能包含大量字段，但通常只需要少数几个字段来表示结果集中的每个文档。 在查询请求中，追加`$select=<field list>`以指定在响应中显示哪些字段。 在要包含在结果中的索引中，字段必须属性化为可**检索**。 

最合适的字段包括对比和区分文档的字段，提供足够的信息来邀请用户部分的单击响应。 在电子商务网站上，它可能是产品名称、说明、品牌、颜色、大小、价格和评级。 对于酒店索引内置示例，它可能是以下示例中的字段：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 如果希望在结果中包括图像文件（如产品照片或徽标），请将其存储在 Azure 认知搜索之外，但在索引中包含一个字段，用于引用搜索文档中的图像 URL。 支持结果中的图像的示例索引包括**realestate-us**演示程序、本[快速入门](search-create-app-portal.md)和纽约的 "[作业演示应用](https://aka.ms/azjobsdemo)"。

## <a name="paging-results"></a>分页结果

默认情况下，搜索引擎最多返回前50个匹配项，如果查询为全文搜索，则按搜索评分确定，或按任意顺序返回完全匹配查询。

若要返回不同数量的匹配文档，请`$top`将`$skip`和参数添加到查询请求。 下面的列表介绍了逻辑。

+ 添加`$count=true`以获取索引中匹配文档总数的计数。

+ 返回第一组15个匹配文档以及总计匹配项的计数：`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 返回第二个集，跳过前15个，获取下 15 `$top=15&$skip=15`个：。 对第三组15执行相同操作：`$top=15&$skip=30`

如果基础索引正在更改，则不保证分页查询的结果是稳定的。 分页将`$skip`为每个页面更改的值，但每个查询都是独立的，并且在查询中数据存在时，在数据的当前视图上进行操作（换言之，没有结果的缓存或快照，如在常规用途数据库中找到的结果）。
 
下面举例说明了如何获取重复项。 假设有四个文档的索引：

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
现在假设您希望每次返回两个结果，并按评级排序。 您将执行此查询以获取结果的第一页： `$top=2&$skip=0&$orderby=rating desc`，生成以下结果：

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
在服务上，假定在查询调用之间向索引添加了第五个文档： `{ "id": "5", "rating": 4 }`。  稍后，您将执行查询来提取第二页： `$top=2&$skip=2&$orderby=rating desc`，并获取以下结果：

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
请注意，文档2被提取了两次。 这是因为，新文档5的评级值较大，因此在第一页上的文档2和页面间排序。 虽然这种行为可能是意外的，但它通常是搜索引擎的行为方式。

## <a name="ordering-results"></a>对结果排序

对于全文搜索查询，结果将根据搜索评分自动排名，搜索分数根据文档中的字词频率和邻近度计算，较高的分数表示搜索词的匹配程度更高或更强。 

搜索评分传达了一般的相关性，反映了与同一结果集中的其他文档相比的匹配程度。 从一个查询到下一个查询，分数并非总是一致的，因此，在处理查询时，你可能会注意到搜索文档的排序方式的细微差异。 对于这种情况的原因，有几个解释。

| 原因 | 说明 |
|-----------|-------------|
| 数据变动率 | 添加、修改或删除文档时，索引内容会有所不同。 随着时间的推移处理索引更新，术语频率将会改变，从而影响匹配文档的搜索分数。 |
| 多个副本 | 对于使用多个副本的服务，将并行对每个副本发出查询。 用于计算搜索评分的索引统计信息根据每个副本计算，并在查询响应中合并和排序结果。 副本基本上是彼此的镜像，但由于状态的细微差异，统计信息可能会有所不同。 例如，一个副本可能已删除了其统计信息的相关文档，这些文档已合并到其他副本。 通常，每个副本的统计信息中的差异在小型索引中更为明显。 |
| 相同分数 | 如果多个文档具有相同分数，则其中的任何一个文档可能会首先出现。  |

### <a name="consistent-ordering"></a>一致性排序

考虑到弹性的结果顺序，如果一致性是应用程序要求，你可能需要浏览其他选项。 最简单的方法是按字段值（如评分或 date）进行排序。 对于想要按特定字段（例如，评级或日期）进行排序的情况，您可以显式定义一个[ `$orderby`表达式](query-odata-filter-orderby-syntax.md)，该表达式可应用于索引为可**排序**的任何字段。

另一种方法是使用[自定义计分配置文件](index-add-scoring-profiles.md)。 通过评分配置文件，可以更好地控制搜索结果中的项目排名，并能够提高特定字段中找到的匹配项。 额外的评分逻辑有助于覆盖副本间的细微差别，因为每个文档的搜索分数都在相隔。 对于此方法，我们建议[排名算法](index-ranking-similarity.md)。

## <a name="hit-highlighting"></a>突出显示

命中突出显示指的是在结果中应用于匹配字词的文本格式（例如粗体或黄色突出显示），这样就可以轻松地找到匹配项。 [查询请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上提供了命中突出显示说明。 搜索引擎将匹配项包含在标记中， `highlightPreTag`并`highlightPostTag`在代码中处理响应（例如，应用粗体字体）。

格式设置将应用于整个术语查询。 在下面的示例中，在 "说明" 字段中找到的术语 "sandy"、"沙滩"、"海滩"、"浮水" 标记为突出显示。 在引擎中触发查询扩展的查询（如模糊和通配符搜索）对命中突出显示功能的支持有限。

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>新行为（从7月15日起）

2020年7月15日之后创建的服务将提供不同的突出显示体验。 在该日期之前创建的服务不会在其突出显示行为中发生更改。 

新行为：

* 仅返回与完整短语查询匹配的短语。 查询 "超级杯" 将返回如下所示的突出显示：

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  请注意，*芯片*的 "字词" 字样没有任何突出显示，因为它与完整短语不匹配。
  
* 可以指定为突出显示返回的片段大小。 片段大小指定为字符数（最大为1000个字符）。

编写实现命中突出显示的客户端代码时，请注意此更改。 请注意，这不会对你产生影响，除非你创建全新的搜索服务。

## <a name="next-steps"></a>后续步骤

若要快速为客户端生成搜索页面，请考虑以下选项：

+ [应用程序生成器](search-create-app-portal.md)，在门户中创建一个 HTML 页面，其中包含一个搜索栏、多面导航和包括图像的结果区域。
+ 使用[c # 创建第一个应用程序](tutorial-csharp-create-first-app.md)是一个生成功能客户端的教程。 示例代码演示分页查询、命中突出显示和排序。

几个代码示例包含一个 web 前端接口，可在此处找到：纽约的 "[城市作业" 演示应用](https://aka.ms/azjobsdemo)、[使用实时演示网站的 JavaScript 示例代码](https://github.com/liamca/azure-search-javascript-samples)和[CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。
