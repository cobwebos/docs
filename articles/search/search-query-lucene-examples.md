---
title: Azure 搜索的 Lucene 查询示例 | Microsoft Docs
description: 在 Azure 搜索服务中进行模糊搜索、邻近搜索、术语提升、正则表达式搜索和通配符搜索的 Lucene 查询语法。
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: liamca
ms.openlocfilehash: d90a7b2d12a147b8020abbd51ef055f0e70471fb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365422"
---
# <a name="lucene-syntax-query-examples-for-building-advanced-queries-in-azure-search"></a>在 Azure 搜索中生成高级查询的 Lucene 语法查询示例
在构造 Azure 搜索的查询时，可以将默认的[简单查询分析器](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)替换为另一种 [Azure 搜索中的 Lucene 查询分析器](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)，以便制定专用的高级查询定义。 

Lucene 查询分析器支持较复杂的查询构造，如字段范围查询、模糊搜索和通配符搜索、邻近搜索、字词提升和正则表达式搜索。 功能的提升也带了额外的处理要求。 本文展示了使用完整语法时的查询操作示例，可以按照这些示例逐步操作。

> [!Note]
> 通过完整的 Lucene 查询语法实现的专用查询构造很多都不是[按文本分析的](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)，所以并不涉及词干分解和词形还原，这一点有些出人意料。 只会对完整字词（字词查询或短语查询）进行词法分析。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对不完整查询字词执行的唯一转换操作是转换为小写。 
>

## <a name="formulate-requests-in-postman"></a>在 Postman 中创建请求

下面的示例使用“纽约工作岗位”搜索索引，它包含基于[纽约市开放数据](https://nycopendata.socrata.com/)计划提供的数据集得出的岗位。 此数据不应认为是最新或完整数据。 该索引位于 Microsoft 提供的一项沙盒服务上，也就是说无需 Azure 订阅或 Azure 搜索即可试用这些查询。

要在 GET 上发出 HTTP 请求，需具备 Postman 或其等效工具。 有关详细信息，请参阅[使用 REST 客户端进行测试](search-fiddler.md)。

### <a name="set-the-request-header"></a>设置请求标头

1. 在请求标头中，将“Content-Type”设为 `application/json`。

2. 添加 api-key，并将其设为此字符串：`252044BE3886FE4A8E3BAA4F595114BB`。 它是托管“纽约工作岗位”索引的沙盒搜索服务的查询密钥。

指定请求标头后，只需更改“search=”字符串即可在本文中的各项查询中重复使用。 

  ![Postman 请求标头](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>设置请求 URL

请求是一个与包含 Azure 搜索终结点和搜索字符串的 URL 配对的 GET 命令。

  ![Postman 请求标头](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL 组合具备以下元素：

+ `https://azs-playground.search.windows.net/` 是由 Azure 搜索开发团队维护的沙盒搜索服务。 
+ **`indexes/nycjobs/`** 是该服务的索引集合中的 NYC 作业索引。 请求中需同时具备服务名称和索引。
+ **`docs`** 是包含所有可搜索内容的文档集合。 请求标头中提供的查询 api-key 仅适用于针对文档集合的读取操作。
+ **`api-version=2017-11-11`** 设置了 api-version，这是每个请求都需要的参数。
+ **`search=*`** 是查询字符串，它在初始查询中为 NULL，返回前 50 个结果（默认情况下）。

## <a name="send-your-first-query"></a>发送自己的第一个查询

进行验证，将以下请求粘贴至 GET 并单击“发送”。 结果会以详细的 JSON 文档形式返回。 可以复制粘贴下方第一个示例中的此 URL。

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

查询字符串 `search=*` 是一个与 NULL 或空搜索等效的未指定的搜索。 它不是很有用，但它是最简单的搜索。

可以选择将 `$count=true` 添加到该 URL，以便返回符合搜索条件的文档的计数。 在空搜索字符串上，这就是索引中的文档总数。例如在纽约工作岗位中，数量为 2802。

## <a name="how-to-invoke-full-lucene-parsing"></a>如何调用完整 Lucene 分析

添加 queryType=full 可调用完整查询语法，替代默认的简单查询语法。 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&queryType=full&search=*
```

本文中的所有示例都指定了 queryType=full 搜索参数，指明由 Lucene 查询分析程序处理完整语法。 

## <a name="example-1-field-scoped-query"></a>示例 1：字段范围查询

第一个查询演示的不是完整 Lucene 语法（它适用于简单语法和完整语法），不过我们用此示例来引出生成合理可读的 JSON 响应的基线查询概念。 出于简洁目的，该查询仅针对 business_title 字段并指定仅返回职位。 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=*
```

**searchFields** 参数将搜索限制在 business title 字段范围内。 Select 参数决定结果集中包含哪些字段。

此查询的响应应与以下屏幕截图类似。

  ![Postman 示例响应](media/search-query-lucene-examples/postman-sample-results.png)

你可能已经注意到，即使没有指定搜索分数，返回的每个文档也包含搜索分数。 这是因为搜索分数是元数据，它的值指示结果的排名顺序。 当搜索不是全文搜索或者没有要应用的条件时，则不存在排名，统一的搜索分数为 1。 对于 NULL 搜索，没有搜索条件，返回的行是任意顺序。 随着搜索条件带来更多的定义，你会看到搜索分数变为有意义的值。

## <a name="example-2-in-field-filtering"></a>示例 2：字段内筛选

完整 Lucene 语法支持字段内的表达式。 此查询搜索其中带有术语“高级”而非“初级”的 business title：

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:senior+NOT+junior
```

通过指定 fieldname:searchterm 构造，可以定义现场查询操作，该操作的字段是一个词，搜索词也是一个词或短语，并且根据需要使用布尔运算符。 一些示例包括以下内容：

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

如果想要两个字符串评估为单个实体，请务必将多个字符串放置在引号内，正如这个在位置字段中搜索两个不同城市的情况一样。 此外，请确保运算符大写，就像你看到的 NOT 和 AND 一样。

在 **fieldname:searchterm** 中指定的字段必须是可搜索字段。 有关如何在字段定义中使用索引属性的详细信息，请参阅[创建索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)。

## <a name="example-3-fuzzy-search"></a>示例 3：模糊搜索

完整 Lucene 语法还支持模糊搜索，能对构造相似的术语进行匹配。 若要执行模糊搜索，请在单个字词的末尾追加“`~`”波形符，后跟指定编辑距离的可选参数（介于 0 到 2 之间的值）。 例如，`blue~` 或 `blue~1` 会返回 blue、blues 和 glue。

此查询搜索带有术语“associate”（故意拼错）的作业：

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:asosiate~
```

对于 [Lucene 文档](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)，模糊搜索基于 [Damerau-Levenshtein 距离](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance)。

> [!Note]
> 不会对模糊查询进行[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)。 字词不完整的查询类型（前缀查询、通配符查询、正则表达式查询、模糊查询）会被直接添加到查询树中，绕过分析阶段。 对不完整查询字词执行的唯一转换操作是转换为小写。
>

## <a name="example-4-proximity-search"></a>示例 4：邻近搜索
邻近搜索用于搜索文档中彼此邻近的术语。 在短语末尾插入波形符“~”，后跟创建邻近边界的词数。 例如“酒店机场”~5 将查找文档中彼此之间 5 个字以内的术语“酒店”和“机场”。

在此查询中，对于包含术语“senior analyst”的作业（其中分隔字数不超过一个字）：

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~1
```

再次尝试删除术语“高级分析师”之间的词。 请注意，此查询返回了 8 个文档，而前面的查询中返回了 10 个文档。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>示例 5：术语提升
术语提升是指相对于不包含术语的文档，提高包含提升术语的文档排名。 若要提升术语，请使用插入符号“^”，并且所搜索术语末尾还要附加提升系数（数字）。 

在“before”查询中，搜索包含术语“computer analyst”的作业时，你会发现没有同时包含“computer”和“analyst”的结果，但“computer”作业排在结果顶部。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst
```

在“after”查询中，请重试该搜索，如果两个词都不存在，此时会提升包含术语“analyst”而非“computer”的结果。 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:computer%20analyst%5e2
```
上述查询有一个更能让人理解的版本：`search=business_title:computer analyst^2`。 对于可操作的查询，`^2` 被编码为 `%5E2`，这比较不容易理解。

术语提升不同于计分配置文件，因为计分配置文件提升某些字段，而非特定术语。 以下示例有助于解释这些差异。

请考虑在某个字段中提升匹配项的计分配置文件，例如 musicstoreindex 示例中的“流派”。 术语提升可用于进一步提升高于其他术语的某些搜索词。 例如“rock^2 electronic”将提升在“流派”字段（高于搜索中的其他搜索字段）中包含搜索词的文档。 另外，由于术语提升值 (2) 的原因，包含搜索词“rock”的文档的排名要比包含搜索词“electronic”的要高。

在设置因素级别时，提升系数越高，术语相对于其他搜索词的相关性也越大。 默认情况下，提升系数是 1。 虽然提升系数必须是整数，但可以小于 1（例如 0.2）。


## <a name="example-6-regex"></a>示例 6：正则表达式

正则表达式搜索基于正斜杠“/”之间的内容查找匹配项，如在 [RegExp 类](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)中所记录的那样。

在此查询中，搜索包含术语“Senior”或“Junior”的作业：`search=business_title:/(Sen|Jun)ior/``。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:/(Sen|Jun)ior/
```
> [!Note]
> 不会对正则表达式查询进行[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)。 对不完整查询字词执行的唯一转换操作是转换为小写。
>

## <a name="example-7-wildcard-search"></a>示例 7：通配符搜索
可将通常可识别的语法用于多个 (\*) 或单个 (?) 字符通配符搜索。 请注意，Lucene 查询分析器支持将这些符号与单个术语一起使用，但不能与短语一起使用。

在此查询中，搜索包含前缀“prog”的作业，这会包含带有术语“编程”和“程序员”的职位。

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&queryType=full&search=business_title:prog*
```
不得将 * 或 ? 符号用作搜索的第一个字符。

> [!Note]
> 不会对通配符查询进行[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)。 对不完整查询字词执行的唯一转换操作是转换为小写。
>

## <a name="next-steps"></a>后续步骤
请尝试在代码中指定 Lucene 查询分析器。 以下链接介绍如何为 .NET 和 REST API 设置搜索查询。 链接使用默认的简单语法，因此需要应用从本文中所学知识指定 **queryType**。

* [使用 .NET SDK 查询 Azure 搜索索引](search-query-dotnet.md)
* [使用 REST API 查询 Azure 搜索索引](search-query-rest-api.md)

可在以下链接找到其他语法参考、查询体系结构和示例：

+ [简单语法查询示例](search-query-simple-examples.md)
+ [Azure 搜索中全文搜索的工作原理](search-lucene-query-architecture.md)
+ [简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [完整 Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)