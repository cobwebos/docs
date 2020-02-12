---
title: 简化的查询语法
titleSuffix: Azure Cognitive Search
description: 用于 Azure 认知搜索中全文搜索查询的简单查询语法参考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152663"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的简单查询语法

Azure 认知搜索实现了两种基于 Lucene 的查询语言：[简单查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html)和[lucene 查询分析器](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)。 在 Azure 认知搜索中，简单查询语法排除模糊/slop 选项。  

> [!NOTE]
> 简单查询语法用于在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)API 的**搜索**参数中传递的查询表达式，而不是与用于该 api 的[$filter](search-filters.md)参数的[OData 语法](query-odata-filter-orderby-syntax.md)混淆。 这些不同的语法具有各自的规则用于构造查询、转义字符串等。
>
> Azure 认知搜索为**搜索**参数中的更复杂查询提供替代的[完整 Lucene 查询语法](query-lucene-syntax.md)。 若要详细了解查询分析体系结构和每种语法的优点，请参阅[Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)。

## <a name="how-to-invoke-simple-parsing"></a>如何调用简单分析

简单语法为默认语法。 仅当将语法从“完整”重置为“简单”时才需要调用。 若要显式设置语法，请使用 `queryType` 搜索参数。 有效值包括 `simple|full`，其中默认值为 `simple`，`full` 则用于 Lucene。 

## <a name="query-behavior-anomalies"></a>查询行为异常

包含一个或多个词条的任何文本都被视为查询执行的有效起点。 Azure 认知搜索将匹配包含任何或所有字词的文档，包括在分析文本期间找到的任何变体。 

正如这种声音，Azure 认知搜索中存在查询执行的一个方面，这*可能会*产生意外的结果，增加而不是减少搜索结果，因为在输入字符串中添加了更多的术语和运算符。 这种扩展是否会实际发生取决于是否包含 NOT 运算符，以及组合使用的 `searchMode` 参数设置，该参数设置确定如何根据 AND 或 OR 行为解释 NOT。 在默认值为 `searchMode=Any` 并使用 NOT 运算符的情况下，该运算会作为 OR 操作进行计算，以便 `"New York" NOT Seattle` 返回非 Seattle 的所有城市。  

通常情况下，更有可能在搜索内容的应用程序的用户交互模式中看到这些行为，其中用户更有可能在查询中包含运算符，而不是具有更多内置导航结构的电子商务网站。 有关详细信息，请参阅 [NOT 运算符](#not-operator)。 

## <a name="boolean-operators-and-or-not"></a>布尔运算符（AND、OR、NOT） 

可以在查询字符串中嵌入运算符，以生成一组丰富的条件，用于发现匹配的文档。 

### <a name="and-operator-"></a>AND 运算符 `+`

AND 运算符是一个加号。 例如，`wifi+luxury` 将搜索包含 `wifi` 和 `luxury` 的文档。

### <a name="or-operator-"></a>OR 运算符 `|`

OR 运算符是一个竖条或管状字符。 例如，`wifi | luxury` 将搜索包含 `wifi` 或 `luxury` 或两者的文档。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 运算符 `-`

NOT 运算符是一个减号。 例如，`wifi –luxury` 将搜索包含 `wifi` 词条和/或不包含 `luxury`（和/或由 `searchMode` 控制）的文档。

> [!NOTE]  
>  `searchMode` 选项控制在没有 `+` 或 `|` 运算符的情况下，带有 NOT 运算符的词条是与查询中的其他词条进行 AND 运算还是 OR 运算。 请记住，`searchMode` 可设置为 `any`（默认）或 `all`。 如果使用 `any`，可以以包含更多结果的方式提高查询的查全率，且默认情况下将 `-` 解释为“OR NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。 如果使用 `all`，可以以包含更少结果的方式提高查询的精确度，且默认情况下将 - 解释为“AND NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，如果想要优化搜索精确度（而非查全率），`searchMode=all`且`searchMode=any`用户在搜索中频繁使用 *运算符，则应考虑使用* 而不是 `-`。

## <a name="suffix-operator"></a>后缀运算符

后缀运算符为星号 `*`。 例如，`lux*` 将搜索包含以 `lux` 开头的词条的文档（忽略大小写）。  

## <a name="phrase-search-operator"></a>短语搜索运算符

短语运算符将短语括在引号 `" "`中。 例如，`Roach Motel`（没有引号）会以任何顺序在任何位置搜索包含 `Roach` 和/或 `Motel` 的文档，而 `"Roach Motel"`（带引号）只会匹配包含整个短语并按该顺序排列的文档（文本分析仍然适用）。

## <a name="precedence-operator"></a>优先级运算符

优先级运算符将字符串用括号括起来 `( )`。 例如，`motel+(wifi | luxury)` 将搜索包含 motel 项的文档，并且 `wifi` 或 `luxury` （或两者）。  

## <a name="escaping-search-operators"></a>转义搜索运算符  

 为了将上述符号实际用于搜索文本，应将反斜杠用作其前缀对它们进行转义。 例如，`luxury\+hotel` 将生成 `luxury+hotel` 词条。 为了让更典型的情况变得简单，此规则有两个不需要进行转义的例外：  

- 仅当 NOT 运算符 `-` 是空格之后的第一个字符时才需要对其进行转义，如果它位于词条中间则不需要对其进行转义。 例如，`wi-fi` 是单一词条；而 GUID（例如 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`）被视为单一标记。
- 仅当后缀运算符 `*` 是空格之前的最后一个字符时才需要对其进行转义，如果它位于词条中间则不需要对其进行转义。 例如，`wi*fi` 被视为单一标记。

> [!NOTE]  
>  虽然转义使标记保持在一起，但文本分析可能会将它们拆分，具体取决于分析模式。 有关详细信息，请参阅[语言支持&#40;Azure 认知搜索 REST API&#41; ](index-add-language-analyzers.md) 。  

## <a name="see-also"></a>另请参阅  

+ [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 查询语法](query-lucene-syntax.md)
+ [OData 表达式语法](query-odata-filter-orderby-syntax.md) 
