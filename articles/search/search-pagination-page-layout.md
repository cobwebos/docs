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
ms.openlocfilehash: 8543894f3f518df6b9b0054973ca1683b82e38f1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548992"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中使用搜索结果

本文介绍如何获取查询响应，该响应附带了匹配文档、分页结果、排序结果和点击突出显示的术语的总数。

响应的结构由查询中的参数确定：REST API 中的[搜索文档](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)，或 .NET SDK 中的[DocumentSearchResult 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)。

## <a name="result-composition"></a>结果组合

虽然搜索文档可能由大量字段组成，但通常只需要几个字段来表示结果集中的每个文档。 在查询请求上，追加以`$select=<field list>`指定响应中显示的字段。 字段必须归为要包含在结果中的索引中的**可检索**字段。 

最起作用的字段包括那些对文档进行对比和区分的字段，这些字段提供了足够的信息来邀请用户进行点击式响应。 在电子商务网站上，它可能是产品名称、描述、品牌、颜色、大小、价格和评级。 对于酒店示例索引内置示例，可能是以下示例中的字段：

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> 如果要在结果中包含图像文件（如产品照片或徽标），请将它们存储在 Azure 认知搜索之外，但在索引中包含一个字段以引用搜索文档中的图像 URL。 支持结果中图像的示例索引包括**房地产样本-us**演示，在此[快速入门](search-create-app-portal.md)中介绍，以及[纽约市就业演示应用程序](https://aka.ms/azjobsdemo)。

## <a name="results-returned"></a>返回的结果

默认情况下，如果查询为全文搜索，或者按任意顺序返回前 50 个匹配项，由搜索分数确定。

要返回不同数量的匹配文档，请向查询`$top`请求`$skip`添加 和 参数。 下面的列表解释了逻辑。

+ 添加`$count=true`以获取索引中匹配文档总数的计数。

+ 返回第一组 15 个匹配文档以及总匹配计数：`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ 返回第二盘，跳过前15，得到下一个15： `$top=15&$skip=15`。 对于第三组 15 执行相同的操作：`$top=15&$skip=30`

如果基础索引正在更改，则分页查询的结果不能保证是稳定的。 分页会更改每个页面`$skip`的值，但每个查询都是独立的，并且对查询时在索引中存在的数据的当前视图进行操作（换句话说，没有缓存或快照的结果，例如在通用数据库中找到的结果）。
 
下面是如何获取重复项的示例。 假设索引有四个文档：

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
现在假设您希望结果一次返回两个，按评级排序。 您将执行此查询以获取结果的第一页： `$top=2&$skip=0&$orderby=rating desc`，生成以下结果：

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
在服务上，假设查询调用之间的索引中添加了第五个文档： `{ "id": "5", "rating": 4 }`。  不久之后，您将执行查询以获取第二页：`$top=2&$skip=2&$orderby=rating desc`获取这些结果：

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
请注意，文档 2 被提取两次。 这是因为新文档 5 具有更大的评级值，因此它在文档 2 之前排序并落在第一页上。 虽然这种行为可能出乎意料，但它是搜索引擎行为的典型表现。

## <a name="ordering-results"></a>对结果排序

对于全文搜索查询，结果根据文档中的术语频率和邻近程度计算，结果自动按搜索分数排序，较高的分数将到搜索词上匹配更多或更强的文档。 搜索分数传达一般的相关性感，相对于同一结果集中的其他文档，并且不能保证从一个查询到下一个查询一致。

处理查询时，您可能会注意到排序结果的微小差异。 有几个解释来解释为什么会发生这种情况。

| 条件 | 说明 |
|-----------|-------------|
| 数据波动性 | 当您添加、修改或删除文档时，索引的内容会有所不同。 随着索引更新的处理时间的变化，期限频率将发生变化，从而影响匹配文档的搜索分数。 |
| 查询执行位置 | 对于使用多个副本的服务，将并行针对每个副本发出查询。 用于计算搜索分数的索引统计信息按副本计算，结果在查询响应中合并并排序。 副本大多是彼此的镜像，但由于状态差异小，统计信息可能不同。 例如，一个副本可能删除了导致其统计信息的文档，这些文档从其他副本中合并。 通常，每个副本统计信息的差异在较小的索引中更为明显。 |
| 在相同的搜索分数之间打平 | 当搜索文档的分数相同时，也会发生有序结果的差异。 在这种情况下，当您重新运行同一查询时，无法保证首先显示哪个文档。 |

### <a name="consistent-ordering"></a>一致的订购

给定搜索评分的灵活性，如果结果订单中的一致性是应用程序要求，则可能需要探索其他选项。 最简单的方法是按字段值（如评级或日期）进行排序。 对于要按特定字段排序的方案（如评级或日期），可以显式定义[`$orderby`表达式](query-odata-filter-orderby-syntax.md)，该表达式可以应用于索引为**可排序**的任何字段。

另一个选项是使用[自定义评分配置文件](index-add-scoring-profiles.md)。 评分配置文件使您能够对搜索结果中的项目排名进行更多控制，并能够提升特定字段中的匹配项。 附加评分逻辑可帮助覆盖副本之间的细微差异，因为每个文档的搜索分数相距较远。 我们建议此方法的排名[算法](index-ranking-similarity.md)。

## <a name="hit-highlighting"></a>突出显示

点击突出显示是指应用于结果匹配术语的文本格式（如粗体或黄色突出显示），便于发现匹配项。 [查询请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)上提供了命中突出显示说明。 The search engine encloses the matching term in tags, `highlightPreTag` and `highlightPostTag`, and your code handles the response (for example, applying a bold font).

格式应用于整个术语查询。 在下面的示例中，在"描述"字段中找到的术语"沙"，"沙子"，"海滩"，"海滩"被标记为突出显示。 对部分术语（如模糊搜索或通配符搜索）的查询（如导致引擎中查询扩展）不能使用命中突出显示。

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> 2020 年 7 月 15 日之后创建的服务将提供不同的突出显示体验。 该日期之前创建的服务在突出显示行为中不会改变。 使用此更改，将仅返回与完整短语查询匹配的短语。 此外，还可以指定为高光返回的片段大小。
>
> 编写实现命中突出显示的客户端代码时，请注意此更改。 请注意，除非您创建全新的搜索服务，否则这不会影响您。

## <a name="next-steps"></a>后续步骤

要快速生成客户端的搜索页，请考虑以下选项：

+ [应用程序生成器](search-create-app-portal.md)在门户中创建一个 HTML 页面，其中包含搜索栏、分面导航和包含图像的结果区域。
+ [在 C# 中创建第一个应用](tutorial-csharp-create-first-app.md)是构建功能客户端的教程。 示例代码演示分页查询、点击突出显示和排序。

几个代码示例包括一个 Web 前端界面，您可以在这里找到：[纽约市工作演示应用程序](https://aka.ms/azjobsdemo)[，JavaScript 示例代码与实时演示网站](https://github.com/liamca/azure-search-javascript-samples)， 和[认知搜索FrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)。