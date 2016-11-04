---
title: 查询 Azure 搜索索引 | Microsoft Docs
description: 在 Azure 搜索中生成搜索查询，并使用搜索参数对搜索结果进行筛选和排序。
services: search
documentationcenter: ''
author: ashmaka

ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka

---
# 查询 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-query-overview.md)
> * [门户](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

提交 Azure 搜索的搜索请求时，可同时指定许多参数，并可指定在应用程序的搜索框中键入的实际的字。通过这些查询参数，可以更深层次地控制全文搜索体验。

下面的列表简要介绍了 Azure 搜索中查询参数的常见用法。有关查询参数及其行为的完整信息，请参阅 [REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx) 和 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx) 的详细页面。

## 查询类型
Azure 搜索提供了许多选项，可以创建功能非常强大的查询。可以使用的两个主要查询类型分别为 `search` 和 `filter`。`search` 查询可搜索索引中全部_可搜索_字段中的一个或多个词，并且其工作原理与 Google 或 Bing 等搜索引擎相同。`filter` 查询可对索引中的全部_可筛选_字段计算布尔表达式。与 `search` 查询不同，`filter` 查询会精确匹配字段的内容，这意味着字符串字段会区分大小写。

可单独使用 search 和 filter，也可一起使用。如果一起使用，则会先对整个索引应用 filter，然后再对 filter 的结果执行 search。filter 可减少 search 查询需要处理的文档集，因此是一种非常有用的技术，可用于提高查询性能。

filter 表达式的语法是 [OData filter 语言](https://msdn.microsoft.com/library/azure/dn798921.aspx)的子集。而 search 查询则可以使用[简化的语法](https://msdn.microsoft.com/library/azure/dn798920.aspx)或 [Lucene 查询语法](https://msdn.microsoft.com/library/azure/mt589323.aspx)，这两种语法将在后面讨论。

### 简化的查询语法
[简化的查询语法](https://msdn.microsoft.com/library/azure/dn798920.aspx)是 Azure 搜索中使用的默认查询语言。简化的查询语法支持多种常用的搜索运算符，包括 AND、OR、NOT、短语、后缀和优先运算符。

### Lucene 查询语法
通过 [Lucene 查询语法](https://msdn.microsoft.com/library/azure/mt589323.aspx)，可使用已开发的、广泛采用的且富有表现力的查询语言作为 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 的一部分。

使用这种查询语法，可轻松实现以下功能：[域范围查询](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields)、[模糊搜索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy)、[邻近搜索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity)、[字词增强](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost)、[正则表达式搜索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex)、[通配符搜索](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard)、[语法基础知识](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)和[使用布尔运算符的查询](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean)。

## 对结果排序
接收搜索查询结果时，可请求 Azure 搜索按特定字段中的值对结果排序。默认情况下，Azure 搜索根据每个文档的搜索评分（源自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)）排名对结果排序。

如果希望 Azure 搜索返回按搜索评分以外的某个值排序的结果，可以使用 `orderby` 搜索参数。可以将 `orderby` 参数的值指定为包含字段名称以及调用地理空间值的 [`geo.distance()` 函数](https://msdn.microsoft.com/library/azure/dn798921.aspx)。每个表达式后面可跟 `asc` 或 `desc`升序请求结果，后者表示按降序请求结果。默认为升序。

## 分页
Azure 搜索可轻松对搜索结果进行分页。通过使用 `top` 和 `skip` 参数，可以顺利地发出搜索请求，接收搜索结果总集，这些搜索结果的子集已经过排序并易于管理，可实现良好的搜索 UI 实践。接收较小的结果子集时，还可以在搜索结果总集中获得文档计数。

有关对搜索结果分页的详细信息，请参阅[如何在 Azure 搜索中对搜索结果分页](search-pagination-page-layout.md)一文。

## 突出显示
在 Azure 搜索中，使用 `highlight`、`highlightPreTag`和 `highlightPostTag` 参数可轻松突出显示与搜索查询相匹配的搜索结果的确切部分。可以指定应突出显示匹配文本的_可搜索_字段，以及要附加到 Azure 搜索返回的匹配文本开头和结尾的精确字符串标记。

<!---HONumber=AcomDC_0921_2016-->