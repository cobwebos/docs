---
title: "查询你的 Azure 搜索索引 |Microsoft 文档"
description: "生成 Azure 搜索中的搜索查询并使用筛选器和排序的搜索结果的搜索参数。"
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: ashmaka
ms.openlocfilehash: a22b82829df4659681940267e64c98d345453958
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="query-your-azure-search-index"></a>查询 Azure Search 索引
> [!div class="op_single_selector"]
> * [概述](search-query-overview.md)
> * [门户](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

提交时对 Azure 搜索的搜索请求，有了多个可以与你的应用程序在搜索框中键入的实际单词一起指定的参数。 这些查询参数允许你实现的一些更深入地控制[的全文搜索体验](search-lucene-query-architecture.md)。

下面是简要介绍了 Azure 搜索中的查询参数的常见用途的列表。 有关查询参数和它们的行为的详细信息，请参阅有关的详细的页面[REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)和[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary)。

## <a name="types-of-queries"></a>类型的查询
Azure 搜索提供许多选项来创建功能非常强大的查询。 查询将使用两种主要类型是`search`和`filter`。 A`search`查询将搜索中所有的一个或多个术语*可搜索*字段在索引中，并且你希望搜索引擎，如 Google 或必应工作方式。 A`filter`查询计算的布尔表达式结果通过所有*可筛选*索引中的字段。 与不同`search`查询，`filter`与查询匹配的字段，这意味着它们是区分大小写的字符串字段的具体内容。

单独或一起，可以使用搜索和筛选器。 如果你同时使用了它们，到整个索引中，首先应用筛选器，然后于筛选器的结果执行搜索。 因此，筛选器可以是一种用于提高查询性能，因为它们降低的搜索查询需要处理的文档集的有用技术。

筛选表达式的语法是的子集[OData 筛选器语言](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)。 为搜索查询你可以使用[简化语法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)或[Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)下面将讨论。

### <a name="simple-query-syntax"></a>简单的查询语法
[简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)是在 Azure 搜索中使用的默认查询语言。 简单的查询语法支持多种常用的搜索运算符包括 AND、 OR、 NOT、 短语、 后缀和优先级的运算符。

### <a name="lucene-query-syntax"></a>Lucene 查询语法
[Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)允许你使用作为的一部分开发的广泛采用和可表达查询语言[Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

使用此查询语法可以轻松实现以下功能：[字段划分作用域查询](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)，[模糊搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)，[邻近搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)，[字词的提升](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)，[正则表达式搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)，[通配符搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)，[语法基础知识](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)，和[查询使用布尔运算符](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)。

## <a name="ordering-results"></a>对结果进行排序
如果接收的搜索查询的结果，你可以请求 Azure 搜索服务中的特定字段的值排序的结果。 默认情况下，Azure 搜索对结果进行排序搜索基于每个文档的搜索评分，该类派生自的秩[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)。

如果你想要返回搜索评分，以外的值可以使用排序的结果的 Azure 搜索`orderby`搜索参数。 你可以指定的值`orderby`参数，以包括字段名称和对调用[`geo.distance()`函数](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)地理空间值。 每个表达式可以跟`asc`以指示升序排序，请求的结果和`desc`以指示以降序方式请求结果。 升序默认排名。

## <a name="paging"></a>分页
Azure 搜索更便于实现分页搜索结果。 通过使用`top`和`skip`参数，你可以顺利地发出允许您在易于管理、 有序好的搜索 UI 实践中轻松地实现的子集中接收的总的一套搜索结果的搜索请求。 在接收时这些较小的结果的子集，也可以搜索结果的总的集中接收的文档的计数。

你可以了解有关分页文章中的搜索结果[如何在 Azure 搜索中的搜索结果进行分页](search-pagination-page-layout.md)。

## <a name="hit-highlighting"></a>命中突出显示
在 Azure 搜索中，突出显示以匹配搜索查询的搜索结果的确切部分轻松使用进行`highlight`， `highlightPreTag`，和`highlightPostTag`参数。 您可以指定哪个*可搜索*字段应具有其匹配的文本，以及指定确切的字符串标记要追加到的开始和结束匹配 Azure 搜索返回的文本的强调。

## <a name="try-out-query-syntax"></a>试用查询语法

了解语法差异的最佳方法是通过提交查询并检查结果。

+ 使用[搜索资源管理器](search-explorer.md)在 Azure 门户中。 通过部署[示例索引](search-get-started-portal.md)，以分钟为单位在门户中使用的工具，可以查询索引。

+ 使用[Fiddler](search-fiddler.md)或 Chrome Postman 将查询提交到已上载到你的搜索服务的索引。 这两种工具支持 REST 调用的 HTTP 终结点。 