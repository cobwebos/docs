---
title: 简化的查询语法
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索中用于全文搜索查询的简单查询语法的参考文档。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258858"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的简单查询语法

Azure 认知搜索实现两种基于 Lucene 的查询语言：[简单查询解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)和[Lucene 查询解析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。 

在 Azure 认知搜索中，简单的查询语法排除了模糊搜索操作。 相反，使用完整的 Lucene 语法进行[模糊搜索](search-query-fuzzy.md)。

> [!NOTE]
> 简单的查询语法用于在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)API**的搜索**参数中传递的查询表达式，不要与用于该 API [$filter](search-filters.md)参数的[OData 语法](query-odata-filter-orderby-syntax.md)混淆。 这些不同的语法具有用于构造查询、转义字符串等自己的规则。
>
> Azure 认知搜索为**搜索**参数中的更复杂的查询提供了备用[的完整 Lucene 查询语法](query-lucene-syntax.md)。 若要详细了解查询分析体系结构和每种语法的好处，请参阅 [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)。

## <a name="invoke-simple-parsing"></a>调用简单解析

简单语法为默认语法。 仅当将语法从“完整”重置为“简单”时才需要调用。 若要显式设置语法，请使用 `queryType` 搜索参数。 有效值包括`queryType=simple``queryType=full`或`simple`，默认位置，并`full`调用完整的[Lucene 查询解析器](query-lucene-syntax.md)以进行更高级的查询。 

## <a name="syntax-fundamentals"></a> 语法基础

包含一个或多个词条的任何文本都被视为查询执行的有效起点。 Azure 认知搜索将匹配包含任何或所有词条的文档，其中包括在分析文本期间发现的任何变体。

尽管听起来很简单，但 Azure 认知搜索中的查询执行的一个方面*可能会*产生意外结果，导致搜索结果增加而不是减少，因为更多的词条和运算符被添加到输入字符串中。 此扩展是否实际发生取决于是否包含 NOT 运算符，以及**一个搜索模式**参数设置，该参数设置确定如何根据 AND 或 OR 行为来解释"不"。 有关详细信息，请参阅 [NOT 运算符](#not-operator)。

### <a name="precedence-operators-grouping"></a>优先级运算符（分组）

可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi||luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

字段分组与之类似，但将分组范围限定为单个字段。 例如，`hotelAmenities:(gym+(wifi||pool))` 在“hotelAmenities”字段中搜索“gym”和“wifi”，或者“gym”和“pool”。  

### <a name="escaping-search-operators"></a>转义搜索运算符  

为了将任何搜索运算符用作搜索文本的一部分，请使用单个反斜杠 （）`\`的前缀来转义该字符。 例如，对于 在 的`https://`通配符搜索`://`，其中是查询字符串的一部分，可以`search=https\:\/\/*`指定 。 同样，转义电话号码模式可能如下所示`\+1 \(800\) 642\-7676`。

需要转义的特殊字符包括以下内容：`- * ? \ /`  

为了让更典型的情况变得简单，此规则有两个不需要进行转义的例外：  

+ 仅当 NOT 运算符 `-` 是空格之后的第一个字符时才需要对其进行转义，如果它位于词条中间则不需要对其进行转义。 例如，以下 GUID 在没有转义字符的情况下有效： `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`。

+ 仅当后缀运算符 `*` 是空格之前的最后一个字符时才需要对其进行转义，如果它位于词条中间则不需要对其进行转义。 例如，`4*4=16`不需要反斜杠。

> [!NOTE]  
> 尽管转义会将令牌放在一起，但索引期间的[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)可能会将它们剥离出来。例如，标准 Lucene 分析器将删除和中断连字符、空格和其他字符上的单词。 如果查询字符串中需要特殊字符，则可能需要一个分析器来在索引中保留这些字符。 一些选择包括微软自然[语言分析器](index-add-language-analyzers.md)，它保留连字符词，或一个自定义分析器更复杂的模式。 有关详细信息，请参阅[部分术语、模式和特殊字符](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全和保留字符进行编码。 例如，"*"是一个不安全的字符，因为它是 URL 中的片段/锚点标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 有关详细信息，请参阅[RFC1738：统一资源定位器 （URL）。](https://www.ietf.org/rfc/rfc1738.txt)

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>查询大小限制

 存在对可以发送到 Azure 认知搜索的查询大小的限制。 具体而言，最多可以有 1024 条子句（以 AND、OR 等分隔的表达式）。 此外，查询中任何单个术语的大小限制为大约 32 KB。 如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。  

## <a name="boolean-search"></a>布尔搜索

您可以将布尔运算符 （AND、 OR、 NOT） 嵌入到查询字符串中，以生成一组丰富的条件，并基于这些条件可以找到匹配的文档。 

### <a name="and-operator-"></a>AND 运算符 `+`

AND 运算符是一个加号。 例如，`wifi+luxury` 将搜索包含 `wifi` 和 `luxury` 的文档。

### <a name="or-operator-"></a>OR 运算符 `|`

OR 运算符是一个竖条或管状字符。 例如，`wifi | luxury` 将搜索包含 `wifi` 或 `luxury` 或两者的文档。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 运算符 `-`

NOT 运算符是一个减号。 例如，`wifi –luxury`将搜索具有`wifi`术语和/或没有`luxury`的文档。

查询请求上的**searchMode**参数控制带有 NOT 运算符的术语是"已使用"的，还是带有查询中其他术语的 ORed（假设`+`其他`|`术语上没有或运算符）。 有效值包括 `any` 或 `all`。

`searchMode=any`通过包含更多结果来增加查询的撤回，默认情况下`-`将解释为"OR NOT"。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。

`searchMode=all`通过包含较少的结果来提高查询的精度，默认情况下， 将解释为"不"。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，您应该考虑`searchMode=all`使用而不是`searchMode=any`，如果您想要优化搜索的精度，而不是撤回，*并且*您的用户经常在搜索中使用`-`运算符。

在决定**搜索模式**设置时，请考虑各种应用程序中查询的用户交互模式。 搜索信息的用户更有可能在查询中包括运算符，而不是具有更多内置导航结构的电子商务网站。

<a name="prefix-search"></a>

## <a name="prefix-search"></a>前缀搜索

后缀运算符是一个星号 `*`。 例如，`lingui*`将查找"语言"或"语言"，忽略案例。 

与筛选器类似，前缀查询查找完全匹配。 因此，没有相关性评分（所有结果都会获得 1.0 的搜索分数）。 前缀查询可能很慢，尤其是在索引较大且前缀由少量字符组成时。 

如果要执行后缀查询（在字符串的最后一部分匹配），请使用[通配符搜索](query-lucene-syntax.md#bkmk_wildcard)和完整的 Lucene 语法。

## <a name="phrase-search-"></a>短语搜索`"`

术语搜索是一个或多个术语的查询，其中任何术语都被视为匹配项。 短语搜索是包含在引号`" "`中的准确短语。 例如，`Roach Motel`（没有引号）会以任何顺序在任何位置搜索包含 `Roach` 和/或 `Motel` 的文档，而 `"Roach Motel"`（带引号）只会匹配包含整个短语并按该顺序排列的文档（文本分析仍然适用）。

## <a name="see-also"></a>另请参阅  

+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene 查询语法](query-lucene-syntax.md)
+ [OData 表达式语法](query-odata-filter-orderby-syntax.md) 
