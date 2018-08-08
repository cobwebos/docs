---
title: Azure 搜索中的查询基础知识 | Microsoft Docs
description: 在 Azure 搜索中生成搜索查询并使用参数对结果进行筛选、选择和排序的基础知识。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366440"
---
# <a name="query-fundamentals-in-azure-search"></a>Azure 搜索中的查询基础知识

Azure 搜索中的查询定义是请求的完整规范，其中包括以下部分：服务 URL 终结点、目标索引、用于对请求进行身份验证的 API 密钥、API 版本以及查询字符串。 

查询字符串由[搜索文档 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中定义的参数构成。 其中最重要的是“search=”参数，它可以是未定义的（`search=*`），但更可能包含术语、短语和运算符，类似于以下示例：

```
"search": "seattle townhouse +\"lake\""
```

其他参数用于指示查询处理或增强响应。 参数的使用示例包括：将搜索范围设置为特定字段、设置搜索模式以调整回调精准率偏差以及添加用于跟踪结果的计数。 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

虽然查询定义是基础，但索引架构也同样重要，它指定了每个字段所允许的操作。 在开发索引期间，字段属性决定了允许的操作。 例如，若要实现全文搜索并显示在搜索结果中，必须将字段标记为“可搜索”和“可检索”。

查询时，使用请求中提供的 API 密钥进行身份验证，且始终针对一个索引执行查询。 不能联接索引或者创建自定义或临时数据结构作为查询目标。  

查询结果会流式处理为 REST API 中的 JSON 文档，但如果使用 .NET API，则会内置序列化功能。 可通过在查询上设置参数并为结果选择特定字段来调整结果

总之，查询请求的内容将指定范围和操作：搜索中包含哪些字段、结果集中包含哪些字段以及是否需要进行排序或筛选等。 如果未指定这些内容，查询会作为全文搜索操作针对所有可搜索字段运行，返回一个任意排序的未评分结果集。

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>查询类型：搜索和筛选

Azure 搜索提供了许多选项，可以创建功能非常强大的查询。 将用到两个主要查询类型，即 `search` 和 `filter`。 

+ `search` 查询可在索引中所有可搜索字段内扫描一个或多个词，其工作原理与 Google 或必应等搜索引擎相同。 该简介中的示例采用 `search` 参数。

+ `filter` 查询可对索引中的所有可筛选字段计算布尔表达式。 与 `search` 不同，`filter` 查询与字段内容完全匹配，包括字符串字段的大小写区分。

可单独使用 search 和 filter，也可一起使用。 单独使用 filter，而不使用查询字符串：如果筛选表达式能够完全限定所需的文档，则此模式很有效。 不使用查询字符串也就不会执行词法或语言分析、评分和排名。 请注意，搜索字符串为空。

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

如果一起使用，则会先对整个索引应用 filter，再对筛选结果执行 search。 filter 可减少 search 查询需要处理的文档集，因此是一种非常有用的技术，可用于提高查询性能。

filter 表达式的语法是 [OData filter 语言](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)的子集。 对于 search 查询，可以使用[简化的语法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)或 [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)，下面讨论这两种语法。


## <a name="choose-a-syntax-simple-or-full"></a>选择一种语法：简单语法或完整语法

Azure 搜索基于 Apache Lucene ，提供两种查询分析器选择，分别用于处理典型查询和专用查询。 典型搜索请求使用默认的[简单查询语法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)表述。 此语法支持多种常用的搜索运算符，包括 AND、OR、NOT、短语、后缀和优先运算符。

在将 queryType=full 添加到请求时所启用的 [Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)公开了作为 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 的一部分开发的、已被广泛采用的且富有表现力的查询语言。 使用此查询语法可以实现以下专用查询：

+ [字段范围查询](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [模糊搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [邻近搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [术语提升](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [正则表达式搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [通配符搜索](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

布尔运算符在两种语法中大致相同，在完整 Lucene 语法中则存在其他格式：

+ [简单语法中的布尔运算符](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [完整 Lucene 语法中的布尔运算符](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>必需元素和可选元素

提交 Azure 搜索的搜索请求时，可同时指定许多参数，并可指定在应用程序的搜索框中键入的实际的字。 通过这些查询参数，可以更深层次地控制[全文搜索体验](search-lucene-query-architecture.md)。

查询请求上的必需元素包括以下内容：

+ 服务终结点和索引文档集合，此处表示为 URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`。
+ API 版本（仅 REST），表示为 api-version=
+ 查询或管理 API 密钥，表示为 api-key=
+ 查询字符串，表示为 search=，如果想执行空搜索，可以不指定此元素。 还可以只发送一个 $filter= 筛选表达式。
+ queryType= 指定采用简单语法还是完整语法，如果想使用默认的简单语法，则可以省略此元素。

所有其他搜索参数都为可选参数。

## <a name="apis-and-tools-for-testing"></a>用于测试的 API 和工具

下表列出用于提交查询的 API 和基于工具的方法。

| 方法 | Description |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | 可用于查询 Azure 搜索索引的客户端。  <br/>[了解详细信息。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [搜索文档 (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 索引上的 GET 或 POST 方法，使用查询参数进行其他输入。  |
| [Fiddler、Postman 或其他 HTTP 测试工具](search-fiddler.md) | 介绍如何设置请求标头和正文，以便向 Azure 搜索发送查询。  |
| [Azure 门户中的搜索浏览器](search-explorer.md) | 提供搜索栏，以及索引和 API 版本选项。 结果会以 JSON 文档的形式返回。 <br/>[了解详细信息。](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>管理搜索结果

可通过以下方式使用查询上的参数来调整结果集的结构：

+ 对结果中的文档数量（默认为 50 个）进行限制或分批
+ 选择结果中要包含的字段
+ 设置排列顺序
+ 添加突出显示效果，以便在搜索结果正文中清楚看到匹配的搜索词

### <a name="tips-for-unexpected-results"></a>意外结果提示

有时可能会出现预料外的结果内容（而不是结构）。 如果查询结果并不是预期内容，可以尝试对查询进行以下修改，然后查看结果是否改进：

+ 将 `searchMode=any`（默认）更改为 `searchMode=all`，从而得出符合所有条件而不是某个条件的匹配项。 在查询包含布尔运算符时更应如此。

+ 如果需要进行文本或词法分析但查询类型排除了语言处理环节，请更改查询方法。 在全文搜索中，文本或词法分析会自动更正拼写错误、单复数形式以及不合规范的谓词或名词。 对于模糊搜索和通配符搜索等查询，其查询分析管道中不包含文本分析。 在某些情况下会采用正则表达式作为解决方法。 

### <a name="paging-results"></a>分页结果
Azure 搜索可轻松对搜索结果进行分页。 使用 `top` 和 `skip` 参数，可顺利地发出搜索请求，接收搜索结果总集，并通过其中易于管理的有序子集轻松完成效果良好的搜索 UI 操作。 接收较小的结果子集时，还可以在搜索结果总集中获得文档计数。

有关对搜索结果分页的详细信息，请参阅 [如何在 Azure 搜索中对搜索结果分页](search-pagination-page-layout.md)一文。

### <a name="ordering-results"></a>对结果排序
接收搜索查询结果时，可请求 Azure 搜索按特定字段中的值对结果排序。 默认情况下，Azure 搜索根据每个文档的搜索评分（源自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)）排名对结果排序。

如果希望 Azure 搜索返回按搜索评分以外的某个值排序的结果，可以使用 `orderby` 搜索参数。 对于地理空间值，可以指定 `orderby` 参数的值，使其包括字段名称及对 [`geo.distance()` 函数](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) 的调用。 每个表达式后可跟 `asc` 表示升序请求结果，或跟 `desc` 表示按降序请求结果。 默认为升序。


### <a name="hit-highlighting"></a>突出显示
在 Azure 搜索中，使用 `highlight`、`highlightPreTag` 和 `highlightPostTag` 参数可轻松强调搜索结果中与搜索查询相匹配的确切部分。 可以指定应强调其匹配文本的可搜索字段，还可指定要附加到 Azure 搜索所返回匹配文本的开头和结尾的精确字符串标记。

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中全文搜索的工作原理（查询分析体系结构）](search-lucene-query-architecture.md)
+ [搜索资源管理器](search-explorer.md)
+ [如何在 .NET 中进行查询](search-query-dotnet.md)
+ [如何在 REST 中进行查询](search-query-rest-api.md)
