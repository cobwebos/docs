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
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923375"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的简单查询语法

Azure 认知搜索实现两种基于 Lucene 的查询语言：[简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)和 [Lucene 查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。

简单分析器更加灵活，并且即使请求撰写得不够完美，它也会尝试对其进行解释。 这种灵活性使其成为了 Azure 认知搜索中查询的默认设置。 

简单语法用于在[搜索文档请求](/rest/api/searchservice/search-documents)的 `search` 参数中传递的查询表达式，不要与用于该搜索文档 API 的 [$filter 表达式](search-filters.md)中的 [OData 语法](query-odata-filter-orderby-syntax.md)相混淆。 `search` 和 `$filter` 参数具有不同的语法，有各自的用于构造查询、转义字符串等操作的规则。

尽管简单分析器基于 [Apache Lucene 简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)类，但 Azure 认知搜索中的实现排除了模糊搜索。 如果需要[模糊搜索](search-query-fuzzy.md)或其他高级查询形式，请考虑改用[完整 Lucene 查询语法](query-lucene-syntax.md)。

## <a name="invoke-simple-parsing"></a>调用简单分析

简单语法为默认语法。 仅当将语法从“完整”重置为“简单”时才需要调用。 若要显式设置语法，请使用 `queryType` 搜索参数。 有效值包括 `queryType=simple` 或 `queryType=full`（其中 `simple` 为默认值），`full` 调用[完整的 Lucene 查询分析程序](query-lucene-syntax.md)进行更高级的查询。 

## <a name="syntax-fundamentals"></a>语法基础知识

包含一个或多个词条的任何文本都被视为查询执行的有效起点。 Azure 认知搜索将匹配包含任何或所有词条的文档，其中包括在分析文本期间发现的任何变体。

尽管听起来很简单，但 Azure 认知搜索中的查询执行的一个方面*可能会*产生意外结果，导致搜索结果增加而不是减少，因为更多的词条和运算符被添加到输入字符串中。 这种扩展是否会实际发生取决于是否包含 NOT 运算符，以及组合使用的 searchMode 参数设置，该参数设置决定了如何根据 AND 或 OR 行为解释 NOT。 有关详细信息，请参阅 [NOT 运算符](#not-operator)。

### <a name="precedence-operators-grouping"></a>优先运算符（分组）

可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi|luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

字段分组与之类似，但将分组范围限定为单个字段。 例如，`hotelAmenities:(gym+(wifi|pool))` 在“hotelAmenities”字段中搜索“gym”和“wifi”，或者“gym”和“pool”。  

### <a name="escaping-search-operators"></a>转义搜索运算符  

在简单语法中，搜索运算符包含以下字符：`+ | " ( ) ' \`  

如果其中的任何字符是索引中的令牌的一部分，请在查询中为其添加一个反斜杠 (`\`) 作为前缀对其进行转义。 例如，假设你使用自定义分析器完成了对整个术语的词汇切分，并且索引包含字符串“Luxury+Hotel”。 若要完全匹配此令牌，请插入转义字符： `search=luxury\+hotel`。 

为了让更典型的情况变得简单，此规则有两个不需要进行转义的例外：  

+ 仅当 NOT 运算符 `-` 是空格之后的第一个字符时才需要对其进行转义。 如果 `-` 位于中间（例如在 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` 中），则不需要对其进行转义。

+ 仅当后缀运算符 `*` 是空格之前的最后一个字符时才需要对其进行转义。 如果 `*` 位于中间（例如在 `4*4=16` 中），则不需要对其进行转义。

> [!NOTE]  
> 默认情况下，标准分析器会在[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)时删除连字符、空格、& 符和其他字符，并在这些字符处拆分单词。 如果需要在查询字符串中保留特殊字符，则可能需要使用一个分析器将它们保留在索引中。 可供选择的一些项包括 Microsoft 自然[语言分析器](index-add-language-analyzers.md)（它会保留带连字符的单词）和自定义分析器（用于更复杂的模式）。 有关详细信息，请参阅[部分词语、模式和特殊字符](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全和保留字符进行编码。 例如，“#”是不安全字符，因为它是 URL 中的片段/定位标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 请参阅 [RFC1738：统一资源定位器 (URL)](https://www.ietf.org/rfc/rfc1738.txt) 获取更多详细信息。

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

### <a name="querying-for-special-characters"></a>查询特殊字符

在某些情况下，可能需要搜索特殊字符，如“❤”表情符号或“€”符号。 在此类情况下，请确保所使用的分析器不会筛选掉这些字符。标准分析器会忽略很多特殊字符，因此这些字符不会成为索引中的标记。

因此，第一步是确保所使用的分析器会考虑这些元素标记。 例如，“空格”分析器将由空格分隔的任何字符序列视为标记，因此“❤”字符串会被视为标记。 另外，诸如 Microsoft 英语分析器（“en.microsoft”）之类的分析器会将“€”字符串视为标记。 可以[测试分析器](/rest/api/searchservice/test-analyzer)，看它为给定的查询生成什么标记。

使用 Unicode 字符时，请确保在查询 URL 中正确转义了符号（例如，对于“❤”，将使用转义序列 `%E2%9D%A4+`）。 Postman 会自动执行此转换。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> 查询大小限制

 存在对可以发送到 Azure 认知搜索的查询大小的限制。 具体而言，最多可以有 1024 条子句（以 AND、OR 等分隔的表达式）。 此外，查询中任何单个术语的大小限制为大约 32 KB。 如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。  

## <a name="boolean-search"></a>布尔值搜索

可以在查询字符串中嵌入布尔运算符（AND、OR、NOT），以生成丰富的一组用于查找匹配文档的标准。 

### <a name="and-operator-"></a>AND 运算符 `+`

AND 运算符是一个加号。 例如，`wifi + luxury` 将搜索包含 `wifi` 和 `luxury` 的文档。

### <a name="or-operator-"></a>OR 运算符 `|`

OR 运算符是一个竖条或管状字符。 例如，`wifi | luxury` 将搜索包含 `wifi` 或 `luxury` 或两者的文档。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 运算符 `-`

NOT 运算符是一个减号。 例如：`wifi –luxury` 将搜索包含 `wifi` 词语且/或不包含 `luxury` 的文档。

查询请求中的 searchMode 参数控制具有 NOT 运算符的词语是通过 AND 运算符还是通过 OR 运算符与查询中的其他词语组合到一起（假定其他词语中没有 `+` 或 `|` 运算符）。 有效值包括 `any` 或 `all`。

`searchMode=any` 通过包含更多结果来提高查询的查全率，且默认情况下 `-` 会被解释为“OR NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。

`searchMode=all` 通过包含更少结果来提高查询的查准率，且默认情况下“-”会被解释为“AND NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，如果想要优化搜索的查准率（而非查全率），且用户在搜索中频繁使用 `-` 运算符，则应考虑使用 `searchMode=all` 而不是 `searchMode=any`。

在决定 searchMode 设置时，请考虑不同应用程序中的查询的用户交互模式。 搜索信息的用户更有可能在查询中包含运算符，相对而言，电子商务网站具有更多的内置导航结构。

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>通配符前缀匹配（*、?）

对于“开头为”查询，请添加后缀运算符作为词条剩余部分的占位符。 使用星号 `*` 表示多个字符，或使用 `?` 表示单个字符。 例如，`lingui*` 会匹配“linguistic”或“linguini”（忽略大小写）。 

与筛选器类似，前缀查询查找完全匹配项。 因此，不存在相关性评分（所有结果的搜索分数均为 1.0）。 请注意，前缀查询可能会很慢，尤其是在索引较大且前缀包含的字符数较少的情况下。 另一种方法（如“边缘 n 元语法标记化”）执行速度可能较快。

对于其他通配符查询变体，比如后缀或中缀与一个词的末尾或中间匹配，请使用[适用于通配符搜索的完整 Lucene 语法](query-lucene-syntax.md#bkmk_wildcard)。

## <a name="phrase-search-"></a>短语搜索 `"`

词语搜索是针对一个或多个词语的查询，其中任何词语都被视为一个匹配项。 短语搜索是用引号 `" "` 引起来的精确短语。 例如，`Roach Motel`（没有引号）会以任何顺序在任何位置搜索包含 `Roach` 和/或 `Motel` 的文档，而 `"Roach Motel"`（带引号）则只会匹配包含整个短语并按该顺序排列的文档（词法分析仍然适用）。

## <a name="see-also"></a>另请参阅  

+ [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档 REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene 查询语法](query-lucene-syntax.md)
+ [OData 表达式语法](query-odata-filter-orderby-syntax.md)