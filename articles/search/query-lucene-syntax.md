---
title: Lucene 查询语法
titleSuffix: Azure Cognitive Search
description: 在适用于通配符、模糊搜索、正则表达式和其他高级查询构造的 Azure 认知搜索中使用的完整 Lucene 查询语法的参考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
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
ms.openlocfilehash: 3bf9dc0e69707eaed8c2a844f6ed3169e65a5342
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564083"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的 Lucene 查询语法

可以基于用于专用查询窗体的丰富 [Lucene 查询分析](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)语法写入针对 Azure 认知搜索的查询：通配符、模糊搜索、邻近搜索、正则表达式等。 除了通过 `$filter` 表达式在 Azure 认知搜索中构造的“范围搜索”之外，大部分 Lucene 查询分析器语法都[在 Azure 认知搜索中完整实现](search-lucene-query-architecture.md)  。 

> [!NOTE]
> 完整 Lucene 语法用于在[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents) API 的**搜索**参数中传递的查询表达式，不要与用于该 API 的 [$filter](search-filters.md) 参数的 [OData 语法](query-odata-filter-orderby-syntax.md)相混淆。 这两个不同的语法有各自的用于构造查询、转义字符串等操作的规则。

## <a name="invoke-full-parsing"></a>调用完整分析

设置 `queryType` 搜索参数来指定要使用的分析。 有效值包括 `simple|full`，其中默认值为 `simple`，`full` 则用于 Lucene。 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>显示完整语法的示例

下面的示例使用 Lucene 查询语法在索引中查找文档，其在 `queryType=full` 参数中清晰易见。 此查询返回酒店，其中类别字段包含字词“budget”和所有包含短语“recently renovated”的可搜索字段。 作为字词提升值 (3)，包含短语“最近更新”的文档排名会更高。  

`searchMode=all` 参数是在此示例中是相关的。 无论运算符何时出现在查询上，通常都应该设置 `searchMode=all` 以确保匹配所有条件  。

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2020-06-30&querytype=full
```

 或者使用 POST：  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
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

下面的语法基础适用于所有使用 Lucene 语法的查询。  

### <a name="operator-evaluation-in-context"></a>上下文中的运算符评估

位置决定符号解释为运算符或者解释为字符串中的另一个字符。

例如，Lucene 完整语法中，波浪线 (~) 用于模糊搜索和邻近搜索。 如果放在引用短语之后，则 ~ 调用邻近搜索。 如果放在术语末尾，则 ~ 调用模糊搜索。

该术语中，例如“business~analyst”，字符不评估为运算符。 在此情况下，假设查询是术语或短语查询，则使用[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)的[全文搜索](search-lucene-query-architecture.md)会删除 ~ 并将术语“business~analyst”分为两部分：business 或 analyst。

上面的示例是波形符 (~)，不过相同原则也适用于每个运算符。

### <a name="escaping-special-characters"></a>转义特殊字符

若要使用任何搜索运算符作为搜索文本的一部分，请使用一个反斜杠 (`\`) 作为前缀对该字符进行转义。 例如，若要执行对 `https://` 的通配符搜索（其中 `://` 是查询字符串的一部分），需要指定 `search=https\:\/\/*`。 同样，转义的电话号码模式可能类似于 `\+1 \(800\) 642\-7676`。

需要转义的特殊字符包括下列项：  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> 尽管转义将标记保留在一起，但在编制索引期间，[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)可能会将它们去除。例如，标准 Lucene 分析器会在连字符、空格和其他字符处断开单词。 如果需要在查询字符串中使用特殊字符，则可能需要使用会将它们保留在索引中的分析器。 可供选择的一些项包括 Microsoft 自然[语言分析器](index-add-language-analyzers.md)（它会保留带连字符的单词）和自定义分析器（用于更复杂的模式）。 有关详细信息，请参阅[部分词语、模式和特殊字符](search-query-partial-matching.md)。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>对 URL 中的不安全及保留字符进行编码

请确保对 URL 中的所有不安全和保留字符进行编码。 例如，“#”是不安全字符，因为它是 URL 中的片段/定位标识符。 如果用于 URL，则该字符必须编码为 `%23`。 由于“&”和“=”在 Azure 认知搜索中分隔参数并指定值，因而是保留字符的示例。 请参阅 [RFC1738：统一资源定位器 (URL)](https://www.ietf.org/rfc/rfc1738.txt) 获取更多详细信息。

不安全字符为 ``" ` < > # % { } | \ ^ ~ [ ]``。 保留字符为 `; / ? : @ = + &`。

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> 查询大小限制

 存在对可以发送到 Azure 认知搜索的查询大小的限制。 具体而言，最多可以有 1024 条子句（以 AND、OR 等分隔的表达式）。 此外，查询中任何单个术语的大小限制为大约 32 KB。 如果应用程序以编程方式生成搜索查询，则建议将其设计为不会生成无限大小的查询。  

### <a name="precedence-operators-grouping"></a>优先运算符（分组）

 可以使用圆括号创建子查询，其包括附加说明语句中的运算符。 例如，`motel+(wifi||luxury)` 将搜索包含“motel”术语以及“wifi”或“luxury”（或两者）的文档。

字段分组与之类似，但将分组范围限定为单个字段。 例如，`hotelAmenities:(gym+(wifi||pool))` 在“hotelAmenities”字段中搜索“gym”和“wifi”，或者“gym”和“pool”。  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a> 布尔值搜索

 始终全部以大写字母指定文本布尔运算符 (AND、OR、NOT)。  

### <a name="or-operator-or-or-"></a>OR 运算符 `OR` 或 `||`

OR 运算符是一个竖条或管状字符。 例如：`wifi || luxury` 将搜索包含"wifi"或"luxury"（或两者）的文档。 由于 OR 是默认连接运算符，因此也可以省略，这样 `wifi luxury` 等同于 `wifi || luxury`。

### <a name="and-operator-and--or-"></a>AND 运算符 `AND`、`&&` 或 `+`

AND 运算符为 & 号或加号。 例如：`wifi && luxury` 将搜索包含“wifi”和“luxury”的文档。 加号字符 (+) 用于所需术语。 例如，`+wifi +luxury` 规定两个术语必须出现在单个文档的某个字段中。

### <a name="not-operator-not--or--"></a>NOT 运算符 `NOT`、`!` 或 `-`

NOT 运算符是一个减号。 例如：`wifi –luxury` 将搜索包含 `wifi` 词语且/或不包含 `luxury` 的文档。

查询请求中的 searchMode  参数控制具有 NOT 运算符的词语是通过 AND 运算符还是通过 OR 运算符与查询中的其他词语组合到一起（假定其他词语中没有 `+` 或 `|` 运算符）。 有效值包括 `any` 或 `all`。

`searchMode=any` 通过包含更多结果来提高查询的查全率，且默认情况下 `-` 会被解释为“OR NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条或不包含 `luxury` 词条的文档。

`searchMode=all` 通过包含更少结果来提高查询的查准率，且默认情况下“-”会被解释为“AND NOT”。 例如，`wifi -luxury` 将匹配包含 `wifi` 词条且不包含“luxury”词条的文档。 这对于 `-` 运算符来说可能是更直观的行为。 因此，如果想要优化搜索的查准率（而非查全率），且  用户在搜索中频繁使用 `-` 运算符，则应考虑使用 `searchMode=all` 而不是 `searchMode=any`。

在决定 searchMode  设置时，请考虑不同应用程序中的查询的用户交互模式。 搜索信息的用户更有可能在查询中包含运算符，相对而言，电子商务网站具有更多的内置导航结构。

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> 字段化搜索

可以使用 `fieldName:searchExpression` 语法定义字段化搜索操作，其中的搜索表达式可以是单个词，也可以是一个短语，或者是括号中的更复杂的表达式，可以选择使用布尔运算符。 下面是部分示例：  

- 流派：爵士乐无历史记录  

- 艺术家：（“Miles Davis”、“John Coltrane”）

如果想要两个字符串评估为单个实体，请务必将多个字符串放置在引号内，正如这个在 `artists` 字段中搜索两个不同艺术家的情况一样。  

`fieldName:searchExpression` 中指定的字段必须是 `searchable` 字段。  有关如何在字段定义中使用索引属性的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)。  

> [!NOTE]
> 使用字段化搜索表达式时，不需使用 `searchFields` 参数，因为每个字段化搜索表达式都有一个显式指定的字段名称。 但是，如果需要运行查询，则仍可使用 `searchFields` 参数，其中的某些部分局限于特定字段，其余部分可以应用到多个字段。 例如，查询 `search=genre:jazz NOT history&searchFields=description` 只将 `jazz` 匹配到 `genre` 字段，而它则会将 `NOT history` 与 `description` 字段匹配。 在 `fieldName:searchExpression` 中提供的字段名称始终优先于 `searchFields` 参数，这就是在此示例中我们不需在 `searchFields` 参数中包括 `genre` 的原因。

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> 模糊搜索

模糊搜索查找字词中具有类似构造的匹配项，将一个字词最多扩展为符合距离条件（2 或更低）的 50 个字词。 有关详细信息，请参阅[模糊搜索](search-query-fuzzy.md)。

 若要进行模糊搜索，请在单个词末尾使用“~”波形符，另附带指定编辑距离的可选参数（0 到 2 [默认] 之间的值）。 例如“blue~”或“blue~1”会返回“blue”、“blues”和“glue”。

 模糊搜索只能应用于术语，不能应用于短语，但是你可以在包含多个部分的名称或短语中将波形符单独追加到每个术语。 例如，“Unviersty~ of~ "Wshington~”会与“University of Washington”匹配。
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> 邻近搜索

邻近搜索用于搜索文档中彼此邻近的术语。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。 例如 `"hotel airport"~5` 将查找文档中彼此相距 5 个字以内的术语“酒店”和“机场”。  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> 术语提升

术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 这不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。  

以下示例有助于解释这些差异。 假设某个字段中存在提升匹配度的计分概要文件，例如 [musicstoreindex 示例](index-add-scoring-profiles.md#bkmk_ex)中的“流派”  。 术语提升可用于进一步提升高于其他术语的某些搜索词。 例如 `rock^2 electronic` 将提升“流派”字段（高于搜索中其他搜索字段）中包含搜索词的文档。 另外，由于术语提升值 (2)，包含搜索词“rock”的文档的排名要比包含搜索词“electronic”的要高   。  

 若要提升术语，请使用插入符号“^”，并且所搜索术语末尾还要附加提升系数（数字）。 还可以提升短语。 提升系数越高，术语相对于其他搜索词的相关性也越大。 默认情况下，提升系数是 1。 虽然提升系数必须是正数，但可以小于 1（例如 0.20）。  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> 正则表达式搜索  
 正则表达式搜索基于 Apache Lucene 下有效的模式找到匹配项，如[RegExp 类](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)中所述。 在 Azure 认知搜索中，正则表达式括在正斜杠之间 `/` 。

 例如，若要查找包含“汽车旅馆”或“酒店”的文档，请指定 `/[mh]otel/`。 正则表达式搜索与单个词匹配。

某些工具和语言施加了额外的转义字符要求。 对于 JSON，包含正斜杠的字符串将使用反斜杠进行转义：“microsoft.com/azure/”变成 `search=/.*microsoft.com\/azure\/.*/`，其中 `search=/.* <string-placeholder>.*/` 设置正则表达式，`microsoft.com\/azure\/` 是包含转义后的正斜杠的字符串。

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> 通配符搜索

对于多（ `*` ）或单个（ `?` ）字符通配符搜索，可以使用公认的语法。 例如，查询表达式 `search=alpha*` 返回 "字母数字" 或 "字母"。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

Full Lucene 语法支持前缀、中缀和后缀匹配。 但是，如果你只需要前缀匹配，则可以使用简单语法（这两种情况下都支持前缀匹配）。

后缀匹配、 `*` `?` 字符串（如中的或之前 `search=/.*numeric./` ）或中缀匹配要求完整的 Lucene 语法，以及正则表达式正斜杠 `/` 分隔符。 不得将 * 或 ?  符号作为术语的第一个字符，或在不包含的词中 `/` 。 

> [!NOTE]  
> 通常，模式匹配速度较慢，因此你可能想要浏览其他方法，例如，创建字词中字符序列的标记的其他方法，如边缘 n 语法词汇。 索引将更大，但查询执行速度可能更快，具体取决于模式构造和要编制索引的字符串的长度。
>
> 在查询分析期间，以前缀、后缀、通配符或正则表达式形式构建的查询将绕过[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)，按原样传递到查询树。 仅当索引包含查询所指定的格式的字符串时，才会查找匹配项。 在大多数情况下，在编制索引期间需要使用一个可以保留字符串完整性的替代分析器，使部分字词和模式匹配能够成功。 有关详细信息，请参阅 [Azure 认知搜索查询中的部分字词搜索](search-query-partial-matching.md)。

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a> 对通配符和正则表达式查询评分

Azure 认知搜索使用基于频率的评分（[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)）进行文本查询。 但是，对于术语范围可能很广的通配符和正则表达式查询，则忽略频率因子，以防止排名偏向于比较少见的术语匹配。 通配符和正则表达式搜索对所有匹配项和正则表达式搜索进行相同处理。

## <a name="see-also"></a>另请参阅

+ [简单搜索的查询示例](search-query-simple-examples.md)
+ [完整 Lucene 搜索的查询示例](search-query-lucene-examples.md)
+ [搜索文档](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [用于筛选器和排序的 OData 表达式语法](query-odata-filter-orderby-syntax.md)   
+ [Azure 认知搜索中的简单查询语法](query-simple-syntax.md)   
