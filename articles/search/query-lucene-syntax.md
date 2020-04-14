---
title: Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 在适用于通配符、模糊搜索、正则表达式和其他高级查询构造的 Azure 认知搜索中使用的完整 Lucene 查询语法的参考。
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
ms.openlocfilehash: f4c3330b23b8b724cdbf5d7e09eec8a8dd5b8cfa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258977"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的 Lucene 查询语法

可以基于用于专用查询窗体的丰富 [Lucene 查询分析](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)语法写入针对 Azure 认知搜索的查询：通配符、模糊搜索、邻近搜索、正则表达式等。 除了通过 `$filter` 表达式在 Azure 认知搜索中构造的“范围搜索”之外，大部分 Lucene 查询分析器语法都[在 Azure 认知搜索中完整实现](search-lucene-query-architecture.md)**。 

> [!NOTE]
> 完整的 Lucene 语法用于在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)API**的搜索**参数中传递的查询表达式，不要与用于该 API [$filter](search-filters.md)参数的[OData 语法](query-odata-filter-orderby-syntax.md)混淆。 这些不同的语法具有用于构造查询、转义字符串等自己的规则。

## <a name="invoke-full-parsing"></a>调用完全解析

设置 `queryType` 搜索参数来指定要使用的分析。 有效值包括 `simple|full`，其中默认值为 `simple`，`full` 则用于 Lucene。 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>显示完整语法的示例

下面的示例使用 Lucene 查询语法在索引中查找文档，其在 `queryType=full` 参数中清晰易见。 此查询返回酒店，其中类别字段包含字词“budget”和所有包含短语“recently renovated”的可搜索字段。 作为字词提升值 (3)，包含短语“最近更新”的文档排名会更高。  

`searchMode=all` 参数是在此示例中是相关的。 无论运算符何时出现在查询上，通常都应该设置 `searchMode=all` 以确保匹配所有条件**。

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 或者使用 POST：  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

有关其他示例，请参阅[在 Azure 认知搜索中生成查询的 Lucene 查询语法示例](search-query-lucene-examples.md)。 有关指定查询参数的完整条件的详细信息，请参阅[搜索文档（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。

> [!NOTE]  
>  Azure 认知搜索还支持[简单查询语法](query-simple-syntax.md)，即可用于简单关键字搜索的简易可靠的查询语言。  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> 语法基础  

以下语法基础知识适用于使用 Lucene 语法的所有查询。  

### <a name="operator-evaluation-in-context"></a>上下文中的运算符评估

位置决定符号解释为运算符或者解释为字符串中的另一个字符。

例如，Lucene 完整语法中，波浪线 (~) 用于模糊搜索和邻近搜索。 如果放在引用短语之后，则 ~ 调用邻近搜索。 如果放在术语末尾，则 ~ 调用模糊搜索。

该术语中，例如“business~analyst”，字符不评估为运算符。 在此情况下，假设查询是术语或短语查询，则使用[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)的[全文搜索](search-lucene-query-architecture.md)会删除 ~ 并将术语“business~analyst”分为两部分：business 或 analyst。

上面的示例是波形符 (~)，不过相同原则也适用于每个运算符。

### <a name="escaping-special-characters"></a>转义特殊字符

为了将任何搜索运算符用作搜索文本的一部分，请使用单个反斜杠 （）`\`的前缀来转义该字符。 例如，对于 在 的`https://`通配符搜索`://`，其中是查询字符串的一部分，可以`search=https\:\/\/*`指定 。 同样，转义电话号码模式可能如下所示`\+1 \(800\) 642\-7676`。

需要转义的特殊字符包括以下内容：  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> 尽管转义会将令牌放在一起，但索引期间的[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)可能会将它们剥离出来。例如，标准 Lucene 分析器将中断连字符、空格和其他字符上的单词。 如果查询字符串中需要特殊字符，则可能需要一个分析器来在索引中保留这些字符。 一些选择包括微软自然[语言分析器](index-add-language-analyzers.md)，它保留连字符词，或一个自定义分析器更复杂的模式。 有关详细信息，请参阅[部分术语、模式和特殊字符](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全和保留字符进行编码。 例如，"*"是一个不安全的字符，因为它是 URL 中的片段/锚点标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 有关详细信息，请参阅[RFC1738：统一资源定位器 （URL）。](https://www.ietf.org/rfc/rfc1738.txt)

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>查询大小限制

 存在对可以发送到 Azure 认知搜索的查询大小的限制。 具体而言，最多可以有 1024 条子句（以 AND、OR 等分隔的表达式）。 此外，查询中任何单个术语的大小限制为大约 32 KB。 如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。  

### <a name="precedence-operators-grouping"></a>优先级运算符（分组）

 可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi||luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

字段分组与之类似，但将分组范围限定为单个字段。 例如，`hotelAmenities:(gym+(wifi||pool))` 在“hotelAmenities”字段中搜索“gym”和“wifi”，或者“gym”和“pool”。  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>布尔搜索

 始终全部以大写字母指定文本布尔运算符 (AND、OR、NOT)。  

### <a name="or-operator-or-or-"></a>OR 运算符 `OR` 或 `||`

OR 运算符是一个竖条或管状字符。 例如：`wifi || luxury` 将搜索包含"wifi"或"luxury"（或两者）的文档。 由于 OR 是默认连接运算符，因此也可以省略，这样 `wifi luxury` 等同于 `wifi || luxury`。

### <a name="and-operator-and--or-"></a>AND 运算符 `AND`、`&&` 或 `+`

AND 运算符为 & 号或加号。 例如：`wifi && luxury` 将搜索包含“wifi”和“luxury”的文档。 加号字符 (+) 用于所需术语。 例如，`+wifi +luxury` 规定两个术语必须出现在单个文档的某个字段中。

### <a name="not-operator-not--or--"></a>NOT 运算符 `NOT`、`!` 或 `-`

NOT 运算符是一个减号。 例如，`wifi –luxury`将搜索具有`wifi`术语和/或没有`luxury`的文档。

查询请求上的**searchMode**参数控制带有 NOT 运算符的术语是"已使用"的，还是带有查询中其他术语的 ORed（假设`+`其他`|`术语上没有或运算符）。 有效值包括 `any` 或 `all`。

`searchMode=any`通过包含更多结果来增加查询的撤回，默认情况下`-`将解释为"OR NOT"。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。

`searchMode=all`通过包含较少的结果来提高查询的精度，默认情况下， 将解释为"不"。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，您应该考虑`searchMode=all`使用而不是`searchMode=any`，如果您想要优化搜索的精度，而不是撤回，*并且*您的用户经常在搜索中使用`-`运算符。

在决定**搜索模式**设置时，请考虑各种应用程序中查询的用户交互模式。 搜索信息的用户更有可能在查询中包括运算符，而不是具有更多内置导航结构的电子商务网站。

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> 字段化搜索

可以使用 `fieldName:searchExpression` 语法定义字段化搜索操作，其中的搜索表达式可以是单个词，也可以是一个短语，或者是括号中的更复杂的表达式，可以选择使用布尔运算符。 下面是部分示例：  

- 流派：爵士乐无历史记录  

- 艺术家：（“Miles Davis”、“John Coltrane”）

如果想要两个字符串评估为单个实体，请务必将多个字符串放置在引号内，正如这个在 `artists` 字段中搜索两个不同艺术家的情况一样。  

`fieldName:searchExpression` 中指定的字段必须是 `searchable` 字段。  有关如何在字段定义中使用索引属性的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。  

> [!NOTE]
> 使用字段化搜索表达式时，不需使用 `searchFields` 参数，因为每个字段化搜索表达式都有一个显式指定的字段名称。 但是，如果需要运行查询，则仍可使用 `searchFields` 参数，其中的某些部分局限于特定字段，其余部分可以应用到多个字段。 例如，查询 `search=genre:jazz NOT history&searchFields=description` 只将 `jazz` 匹配到 `genre` 字段，而它则会将 `NOT history` 与 `description` 字段匹配。 在 `fieldName:searchExpression` 中提供的字段名称始终优先于 `searchFields` 参数，这就是在此示例中我们不需在 `searchFields` 参数中包括 `genre` 的原因。

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>模糊搜索

模糊搜索查找具有类似构造的术语的匹配项，将术语扩展到最多 50 个符合两个或更少距离条件的术语。 有关详细信息，请参阅[模糊搜索](search-query-fuzzy.md)。

 若要进行模糊搜索，请在单个词末尾使用“~”波形符，另附带指定编辑距离的可选参数（0 到 2 [默认] 之间的值）。 例如“blue~”或“blue~1”会返回“blue”、“blues”和“glue”。

 模糊搜索只能应用于术语，不能应用于短语，但是你可以在包含多个部分的名称或短语中将波形符单独追加到每个术语。 例如，“Unviersty~ of~ "Wshington~”会与“University of Washington”匹配。
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>邻近搜索

邻近搜索用于搜索文档中彼此邻近的术语。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。 例如 `"hotel airport"~5` 将查找文档中彼此相距 5 个字以内的术语“酒店”和“机场”。  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>术语提升

术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 这不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。  

以下示例有助于解释这些差异。 假设某个字段中存在提升匹配度的计分概要文件，例如 [musicstoreindex 示例](index-add-scoring-profiles.md#bkmk_ex)中的“流派”**。 术语提升可用于进一步提升高于其他术语的某些搜索词。 例如 `rock^2 electronic` 将提升“流派”字段（高于搜索中其他搜索字段）中包含搜索词的文档。 另外，由于术语提升值 (2)，包含搜索词“rock”的文档的排名要比包含搜索词“electronic”的要高****。  

 若要提升术语，请使用插入符号“^”，并且所搜索术语末尾还要附加提升系数（数字）。 还可以提升短语。 提升系数越高，术语相对于其他搜索词的相关性也越大。 默认情况下，提升系数是 1。 虽然提升系数必须是正数，但可以小于 1（例如 0.20）。  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>正则表达式搜索  
 正则表达式搜索基于正斜杠“/”之间的内容查找匹配项，如在 [RegExp 类](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所记录的那样。  

 例如，若要查找包含“汽车旅馆”或“酒店”的文档，请指定 `/[mh]otel/`。 正则表达式搜索与单个词匹配。

某些工具和语言施加了额外的转义字符要求。 对于 JSON，包含前斜杠的字符串将用向后斜杠转出："microsoft.com/azure/"成为`search=/.*microsoft.com\/azure\/.*/`设置`search=/.* <string-placeholder>.*/`正则表达式的位置，并且`microsoft.com\/azure\/`是具有转义向前斜杠的字符串。

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>通配符搜索  

可将通常可识别的语法用于多个 (*) 或单个 (?) 字符通配符搜索。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

前缀搜索也使用星号 （`*`） 字符。 例如，返回"笔记本"或`search=note*`"记事本"的查询表达式。 前缀搜索不需要完整的 Lucene 语法。 简单语法支持此方案。

后缀搜索（字符串`*`之前或`?`字符串之前）需要完整的 Lucene 语法和正则表达式（不能使用 * 或 ？ 符号作为搜索的第一个字符）。 给定术语"字母数字"，查询表达式 （`search=/.*numeric.*/`） 将查找匹配项。

> [!NOTE]  
> 在查询解析期间，作为前缀、后缀、通配符或正则表达式配制的查询按原样传递给查询树，绕过[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)。 仅当索引包含查询指定的格式的字符串时，才会找到匹配项。 在大多数情况下，在索引期间需要一个替代分析器，以保留字符串完整性，以便部分术语和模式匹配成功。 有关详细信息，请参阅[Azure 认知搜索查询中的部分术语搜索](search-query-partial-matching.md)。

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a> 对通配符和正则表达式查询评分

Azure 认知搜索使用基于频率评分 ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) 进行文本查询。 但是，对于术语范围可能很广的通配符和正则表达式查询，则忽略频率因子，以防止排名偏向于比较少见的术语匹配。 通配符和正则表达式搜索对所有匹配项和正则表达式搜索进行相同处理。

## <a name="see-also"></a>另请参阅

+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [用于筛选器和排序的 OData 表达式语法](query-odata-filter-orderby-syntax.md)   
+ [Azure 认知搜索中的简单查询语法](query-simple-syntax.md)   
