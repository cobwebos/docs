---
title: 简化的查询语法
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索中用于全文搜索查询的简单查询语法的参考文档。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194935"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的简单查询语法

Azure 认知搜索实现两种基于 Lucene 的查询语言：[简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)和 [Lucene 查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

简单分析器更灵活，并且即使未完美撰写请求，也会尝试解释该请求。 由于这种灵活性，它是 Azure 认知搜索中查询的默认值。 

简单语法用于在`search` [搜索文档请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)的参数中传递的查询表达式，而不是与用于相同搜索文档 API 的[$filter 表达式](search-filters.md)参数的[OData 语法](query-odata-filter-orderby-syntax.md)混淆。 `search`和`$filter`参数具有不同的语法，以及用于构造查询、转义字符串等的规则。

尽管简单分析器基于[Apache Lucene 简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)类，但 Azure 中的实现认知搜索排除模糊搜索。 如果需要[模糊搜索](search-query-fuzzy.md)或其他高级查询窗体，请考虑改用替代的[完整 Lucene 查询语法](query-lucene-syntax.md)。

## <a name="invoke-simple-parsing"></a>调用简单分析

简单语法为默认语法。 仅当将语法从“完整”重置为“简单”时才需要调用。 若要显式设置语法，请使用 `queryType` 搜索参数。 有效值`queryType=simple`包括或`queryType=full`，其中`simple`为默认值，并`full`调用完整的[Lucene 查询分析器](query-lucene-syntax.md)以获得更高级的查询。 

## <a name="syntax-fundamentals"></a> 语法基础

包含一个或多个词条的任何文本都被视为查询执行的有效起点。 Azure 认知搜索将匹配包含任何或所有词条的文档，其中包括在分析文本期间发现的任何变体。

尽管听起来很简单，但 Azure 认知搜索中的查询执行的一个方面*可能会*产生意外结果，导致搜索结果增加而不是减少，因为更多的词条和运算符被添加到输入字符串中。 此扩展是否确实发生的情况取决于是否包含 NOT 运算符，以及**searchMode**参数设置，该设置决定了如何在和或或行为方面解释不能。 有关详细信息，请参阅 [NOT 运算符](#not-operator)。

### <a name="precedence-operators-grouping"></a>优先级运算符（分组）

可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi|luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

字段分组与之类似，但将分组范围限定为单个字段。 例如，`hotelAmenities:(gym+(wifi|pool))` 在“hotelAmenities”字段中搜索“gym”和“wifi”，或者“gym”和“pool”。  

### <a name="escaping-search-operators"></a>转义搜索运算符  

在简单语法中，搜索运算符包含以下字符：`+ | " ( ) ' \`  

如果其中的任何字符是索引中的令牌的一部分，则通过在查询中使用单个反斜杠（`\`）对其进行前缀来对其进行转义。 例如，假设您使用了一个用于整个术语标记的自定义分析器，并且您的索引包含字符串 "有多个 + 酒店"。 若要获取此标记的完全匹配项，请插入转义符 `search=luxury\+hotel`：。 

为简化更典型情况下的操作，此规则有两个例外，不需要进行转义：  

+ 如果 NOT 运算符`-`是空格后的第一个字符，则该运算符只需转义。 如果在`-`中间显示（例如，在中`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`），则可以跳过转义。

+ 后缀运算符`*`仅在空格前的最后一个字符时才需要转义。 如果中间`*`出现（例如，中`4*4=16`的），则不需要进行转义。

> [!NOTE]  
> 默认情况下，标准分析器将在[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)期间删除并删除连字符、空格、符号和其他字符上的单词。 如果需要特殊字符保留在查询字符串中，则可能需要一个分析器来将它们保留在索引中。 一些选择包括 Microsoft 自然[语言分析器](index-add-language-analyzers.md)，用于保留断字符的单词或自定义分析器用于更复杂的模式。 有关详细信息，请参阅[部分术语、模式和特殊字符](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全和保留字符进行编码。 例如，"#" 是一个不安全的字符，因为它是 URL 中的片段/定位点标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 有关更多详细信息，请参阅[RFC1738：统一资源定位器（URL）](https://www.ietf.org/rfc/rfc1738.txt) 。

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>查询大小限制

 存在对可以发送到 Azure 认知搜索的查询大小的限制。 具体而言，最多可以有 1024 条子句（以 AND、OR 等分隔的表达式）。 此外，查询中任何单个术语的大小限制为大约 32 KB。 如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。  

## <a name="boolean-search"></a>布尔值搜索

可以在查询字符串中嵌入布尔运算符（AND、OR、NOT），以生成一组丰富的条件，用于发现匹配的文档。 

### <a name="and-operator-"></a>AND 运算符 `+`

AND 运算符是一个加号。 例如，`wifi + luxury` 将搜索包含 `wifi` 和 `luxury` 的文档。

### <a name="or-operator-"></a>OR 运算符 `|`

OR 运算符是一个竖条或管状字符。 例如，`wifi | luxury` 将搜索包含 `wifi` 或 `luxury` 或两者的文档。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 运算符 `-`

NOT 运算符是一个减号。 例如， `wifi –luxury`将搜索具有`wifi`术语 and/or 的文档。 `luxury`

查询请求上的**searchMode**参数控制具有 NOT 运算符的字词是 And 还是运算与查询中的其他字词一起使用（假设没有`+` or `|`运算符。 有效值包括 `any` 或 `all`。

`searchMode=any`通过包括更多结果来增加查询的调用，并且默认`-`情况下将解释为 "OR NOT"。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。

`searchMode=all`通过包含更少结果来提高查询的精度，并且默认情况下会将解释为 "NOT"。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，你应考虑使用`searchMode=all`而不`searchMode=any`是，如果想要优化精确搜索，而不是重新调用，*并且*用户经常`-`在搜索中使用运算符。

确定**searchMode**设置时，请考虑不同应用程序中的查询的用户交互模式。 搜索信息的用户更有可能在查询中包含运算符，而不是具有更多内置导航结构的电子商务站点。

<a name="prefix-search"></a>

## <a name="prefix-search"></a>前缀搜索

后缀运算符是一个星号 `*`。 例如， `lingui*`将发现 "语言" 或 "linguini"，忽略大小写。 

与筛选器类似，前缀查询查找完全匹配项。 同样，没有关联计分（所有结果都接收到1.0 的搜索分数）。 前缀查询可能会很慢，尤其是在索引较大且前缀包含少量字符时。 

如果要执行后缀查询，请在字符串的最后一个部分进行匹配，使用[通配符搜索](query-lucene-syntax.md#bkmk_wildcard)和完整的 Lucene 语法。

## <a name="phrase-search-"></a>短语搜索`"`

字词搜索是针对一个或多个字词的查询，其中任何词都被视为匹配。 短语搜索是用引号引起来`" "`的精确短语。 例如，`Roach Motel`（没有引号）会以任何顺序在任何位置搜索包含 `Roach` 和/或 `Motel` 的文档，而 `"Roach Motel"`（带引号）只会匹配包含整个短语并按该顺序排列的文档（文本分析仍然适用）。

## <a name="see-also"></a>另请参阅  

+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整的 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档 REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene 查询语法](query-lucene-syntax.md)
+ [OData 表达式语法](query-odata-filter-orderby-syntax.md) 
