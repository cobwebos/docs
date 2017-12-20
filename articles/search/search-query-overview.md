---
title: "Azure 搜索中的查询 | Microsoft Docs"
description: "在 Azure 搜索中生成搜索查询，并使用搜索参数对搜索结果进行筛选和排序。"
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
ms.date: 11/13/2017
ms.author: ashmaka
ms.openlocfilehash: b1a96c2299e415db89f881fa72b150d50bb3c69c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="queries-in-azure-search"></a>Azure 搜索中的查询
> [!div class="op_single_selector"]
> * [概述](search-query-overview.md)
> * [门户](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

提交 Azure 搜索的搜索请求时，可同时指定许多参数，并可指定在应用程序的搜索框中键入的实际的字。 通过这些查询参数，可以更深层次地控制[全文搜索体验](search-lucene-query-architecture.md)。

下面的列表简要介绍了 Azure 搜索中查询参数的常见用法。 有关查询参数及其行为的完整信息，请参阅 [REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 和 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary) 的详细页面。

## <a name="types-of-queries"></a>查询类型
Azure 搜索提供了许多选项，可以创建功能非常强大的查询。 将用到两个主要查询类型，即 `search` 和 `filter`。 `search` 查询可在索引中全部可搜索字段内搜索一个或多个词，其工作原理与 Google 或必应等搜索引擎相同。 `filter` 查询可对索引中的全部可筛选字段计算布尔表达式。 与 `search` 查询不同，`filter` 查询会精确匹配字段的内容，这意味着字符串字段会区分大小写。

可单独使用 search 和 filter，也可一起使用。 如果一起使用，则会先对整个索引应用 filter，再对 filter 的结果执行 search。 filter 可减少 search 查询需要处理的文档集，因此是一种非常有用的技术，可用于提高查询性能。

filter 表达式的语法是 [OData filter 语言](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)的子集。 对于 search 查询，可以使用[简化的语法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)或 [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)，下面讨论这两种语法。

### <a name="simple-query-syntax"></a>简化的查询语法
[简化的查询语法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) 是 Azure 搜索中使用的默认查询语言。 简化的查询语法支持多种常用的搜索运算符，包括 AND、OR、NOT、短语、后缀和优先运算符。

### <a name="lucene-query-syntax"></a>Lucene 查询语法
通过 [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)，可使用作为 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 的一部分开发的、已被广泛采用的且富有表现力的查询语言。

使用这种查询语法，可轻松实现以下功能：[字段范围查询](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)、[模糊搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)、[邻近搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)、[字词增强](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)、[正则表达式搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)、[通配符搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)、[语法基础知识](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)和[使用布尔运算符的查询](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)。

## <a name="ordering-results"></a>对结果排序
接收搜索查询结果时，可请求 Azure 搜索按特定字段中的值对结果排序。 默认情况下，Azure 搜索根据每个文档的搜索评分（源自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)）排名对结果排序。

如果希望 Azure 搜索返回按搜索评分以外的某个值排序的结果，可以使用 `orderby` 搜索参数。 对于地理空间值，可以指定 `orderby` 参数的值，使其包括字段名称及对 [`geo.distance()` 函数](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) 的调用。 每个表达式后可跟 `asc` 表示升序请求结果，或跟 `desc` 表示按降序请求结果。 默认为升序。

## <a name="paging"></a>分页
Azure 搜索可轻松对搜索结果进行分页。 使用 `top` 和 `skip` 参数，可顺利地发出搜索请求，接收搜索结果总集，并通过其中易于管理的有序子集轻松完成效果良好的搜索 UI 操作。 接收较小的结果子集时，还可以在搜索结果总集中获得文档计数。

有关对搜索结果分页的详细信息，请参阅 [如何在 Azure 搜索中对搜索结果分页](search-pagination-page-layout.md)一文。

## <a name="hit-highlighting"></a>突出显示
在 Azure 搜索中，使用 `highlight`、`highlightPreTag` 和 `highlightPostTag` 参数可轻松强调搜索结果中与搜索查询相匹配的确切部分。 可以指定应强调其匹配文本的可搜索字段，还可指定要附加到 Azure 搜索所返回匹配文本的开头和结尾的精确字符串标记。

## <a name="try-out-query-syntax"></a>试用查询语法

了解语法差异最好的方法是提交查询并检查结果。

+ 在 Azure 门户中使用[搜索资源管理器](search-explorer.md)。 通过部署[示例索引](search-get-started-portal.md)，使用门户中的工具可在几分钟内查询索引。

+ 使用 Telerik Fiddler 或 Chrome Postman 将查询提交到已上传到搜索服务的索引。 这两种工具都支持对 HTTP 终结点的 REST 调用。 