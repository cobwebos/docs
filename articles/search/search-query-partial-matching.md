---
title: 部分字词、模式和特殊字符
titleSuffix: Azure Cognitive Search
description: 使用通配符、正则表达式和前缀查询来匹配 Azure 认知搜索查询请求中的完整或部分字词。 可以使用完整查询语法和自定义分析器来解析包含特殊字符的难以匹配的模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d562931b7578935a4544dfd953ff2de74a5350a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260978"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>部分字词搜索和包含特殊字符（通配符、正则表达式、模式）的模式

部分字词搜索是指由字词片段组成的查询，其中没有包含整个字词，而可能只是包含字词的开头、中间部分或末尾（有时称为前缀查询、中缀查询或后缀查询）  。 部分术语搜索可能包括片段的组合，通常包含特殊字符，如作为查询字符串的一部分的短划线或斜线。 常见的用例包括电话号码、URL、代码或带连字符的组合词的部分。

如果索引未采用预期格式的标记，则包含特殊字符的部分术语搜索和查询字符串可能会出现问题。 在编制索引的 "[词法分析" 阶段](search-lucene-query-architecture.md#stage-2-lexical-analysis)（假定默认的标准分析器）期间，会丢弃特殊字符、拆分组合词和删除空格;当找不到匹配项时，所有这些都可能导致查询失败。 例如，类似于 `+1 (425) 703-6214` 的电话号码（标记化为 `"1"`、`"425"`、`"703"`、`"6214"`）不会显示在 `"3-62"` 查询中，因为该内容并不实际存在于索引中。 

解决方法是在编制索引期间调用分析器，以保留完整的字符串，包括空格和特殊字符（如有必要），以便可以在查询字符串中包含空格和字符。 同样，如果将未标记为较小部分的完整字符串用于 "开始时间" 或 "结束于" 查询，则可以针对不是由词法分析转换的字词来计算所提供的模式。 为完整的字符串创建其他字段，以及使用发出整术语标记的内容保留分析器，是用于模式匹配的解决方案，用于匹配包含特殊字符的查询字符串。

> [!TIP]
> 如果你熟悉 Postman 和 REST Api，请[下载 "查询示例" 集合](https://github.com/Azure-Samples/azure-search-postman-samples/)以查询本文中所述的部分术语和特殊字符。

## <a name="what-is-partial-term-search-in-azure-cognitive-search"></a>Azure 认知搜索中的部分术语搜索

Azure 认知搜索会扫描索引中的整个标记术语，而在部分术语中找不到匹配项，除非包含通配符占位符运算符（ `*` 和 `?` ）或将查询格式设置为正则表达式。 部分术语是使用以下方法指定的：

+ [正则表达式查询](query-lucene-syntax.md#bkmk_regex)可以是在 Apache Lucene 下有效的任何正则表达式。 

+ [带有前缀匹配项的通配符运算符](query-simple-syntax.md#prefix-search)是指一般识别的模式，该模式包括某个字词的开头、后跟 `*` 或 `?` 后缀运算符（例如， `search=cap*` 在 "Cap'n 插孔的海滨 Inn" 或 "Gacc 大写字母" 上进行匹配）。 简单和完整的 Lucene 查询语法支持前缀匹配。

+ [带中缀和后缀匹配的通配符](query-lucene-syntax.md#bkmk_wildcard)将 `*` 和 `?` 运算符置于字词的内部或开头，并需要正则表达式语法（其中，表达式用正斜杠括起来）。 例如，查询字符串（ `search=/.*numeric*./` ）以 "字母数字" 和 "字母数字" 作为后缀和中缀匹配返回结果。

对于部分术语或模式搜索以及模糊搜索等其他一些查询窗体，查询时不使用分析器。 对于这些查询窗体，分析器会将其与运算符和分隔符一起检测到，查询字符串将传递给引擎，而不进行词法分析。 对于这些查询窗体，将忽略在字段上指定的分析器。

> [!NOTE]
> 当部分查询字符串包含字符（如 URL 片段中的斜杠）时，可能需要添加转义符。 在 JSON 中，使用反斜杠 `\` 来转义正斜杠 `/`。 因此，`search=/.*microsoft.com\/azure\/.*/` 是 URL 片段“microsoft.com/azure/”的语法。

## <a name="solving-partialpattern-search-problems"></a>解决部分搜索/模式搜索的问题

如果需要搜索片段或模式或特殊字符，可以使用在更简单的标记规则下操作的自定义分析器替代默认分析器，同时保留索引中的整个字符串。 退一步讲，该方法如下所述：

+ 定义一个字段以存储字符串的完整版本（假设您希望在查询时分析和未分析的文本）
+ 评估和选择各种分析器，在适当的粒度级别发出令牌
+ 将分析器分配给字段
+ 生成并测试索引

> [!TIP]
> 评估分析器是需要频繁重建索引的迭代过程。 可以使用 Postman 以及[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)、[删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents) REST API 来简化此步骤。 使用“加载文档”时，请求正文应包含要测试的小型代表性数据集（例如，包含电话号码或产品代码的字段）。 在同一 Postman 集合中使用这些 API 可以快速循环执行这些步骤。

## <a name="duplicate-fields-for-different-scenarios"></a>不同方案的重复字段

分析器确定如何在索引中标记字词。 由于分析器是根据每个字段进行分配的，因此可以在索引中创建字段，以便针对不同的方案进行优化。 例如，你可以定义 "featureCode" 和 "featureCodeRegex" 以支持对第一次执行常规全文搜索，并在第二个模式下支持高级模式匹配。 分配给每个字段的分析器确定如何在索引中标记每个字段的内容。  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>选择分析器

选择可生成完整字词标记的分析器时，以下分析器是常用选项：

| 分析器 | 行为 |
|----------|-----------|
| [语言分析器](index-add-language-analyzers.md) | 保留组合词或字符串、元音变化和动词形式中的连字符。 如果查询模式包含短划线，则使用语言分析器可能已足够。 |
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整个字段的内容将标记化为单个字词。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 仅在空白位置分隔。 包含短划线或其他字符的字词被视为单个标记。 |
| [自定义分析器](index-add-custom-analyzers.md) | （建议）创建自定义分析器可以指定标记器 (tokenizer) 和标记筛选器。 以前的分析器必须按原样使用。 使用自定义分析器可以选取要使用的标记器和标记筛选器。 <br><br>建议的组合是[关键字标记器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)加[小写标记筛选器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)。 预定义的[关键字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)本身不会将任何大写文本转换为小写，这可能导致查询失败。 自定义分析器提供一个机制用于添加小写标记筛选器。 |

如果使用 Postman 等 Web API 测试工具，则可以添加[测试分析器 REST 调用](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)来检查标记化的输出。

您必须具有填充的索引才能使用。 对于现有的索引以及包含短划线或部分字词的字段，可以针对特定字词尝试各种分析器，以查看发出哪些标记。  

1. 首先，检查标准分析器以查看默认情况下如何标记术语。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 评估响应，以查看文本如何在索引中标记化。 注意每个字词如何转换为小写和分解。 只有与这些标记匹配的查询才会在结果中返回此文档。 包含 "10-或" 的查询会失败。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 现在修改请求以使用 `whitespace` 或 `keyword` 分析器：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 现在，响应包含单个大写的标记，其中包含作为字符串一部分保留的短划线。 如果需要搜索模式或部分术语（如 "10-or"），则查询引擎现在具有查找匹配项的基础。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 请注意，在生成查询树时，查询分析程序往往会将搜索表达式中的字词小写。 如果在编制索引期间使用的分析器不会用到小写文本输入，并且没有获得预期结果，这可能是原因。 解决方法是根据下面的“使用自定义分析器”部分所述添加小写标记筛选器。

## <a name="configure-an-analyzer"></a>配置分析器
 
无论你是在评估分析器还是继续进行特定的配置，都需要在字段定义中指定分析器，如果未使用内置分析器，则可能还需要配置分析器本身。 交换分析器时，通常需要重建索引（删除、重新创建并重新加载）。 

### <a name="use-built-in-analyzers"></a>使用内置分析器

可以在字段定义的 `analyzer` 属性中按名称指定内置或预定义的分析器，不需要在索引中进行其他配置。 以下示例演示如何在字段中设置 `whitespace` 分析器。 

有关其他方案以及其他内置分析器的详细信息，请参阅[预定义分析器列表](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>使用自定义分析器

如果使用[自定义分析器](index-add-custom-analyzers.md)，请在索引中使用用户定义的组合（其中包含标记器、标记筛选器和可能的配置设置）来定义该分析器。 接下来，在字段定义中引用它，就像引用内置分析器一样。

如果目标是完整字词标记化，我们建议使用一个由**关键字标记器**和**小写标记筛选器**组成的自定义分析器。

+ 关键字标记器为字段的整个内容创建单个标记。
+ 小写标记筛选器将大写字母转换为小写文本。 查询分析程序通常将任何大写文本输入小写。 小写过程可将包含标记化字词的输入均匀化。

以下示例演示了一个提供关键字标记器和小写标记筛选器的自定义分析器。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` 标记器和 `lowercase` 令牌筛选器对于系统是已知的，它们使用默认配置，正因如此，可以按名称引用它们，而无需先定义它们。

## <a name="build-and-test"></a>生成和测试

使用支持方案的分析器和字段定义定义索引后，请加载包含代表性字符串的文档，以便可以测试部分字符串查询。 

前面的部分介绍了逻辑。 本部分将逐步介绍在测试解决方案时应调用的每个 API。 如前所述，如果使用 Postman 等交互式 Web 测试工具，则可快速逐步完成这些任务。

+ “[删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)”会删除同名的现有索引，以便可以重新创建该索引。

+ “[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)”会在搜索服务中创建索引结构，包括分析器定义以及附带分析器规范的字段。

+ “[加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)”会导入与索引具有相同结构的文档以及可搜索的内容。 完成此步骤后，索引便可供查询或测试。

+ [选择分析器](#choose-an-analyzer)中介绍了[测试分析器](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)。 请使用各种分析器来测试索引中的一些字符串，以了解字词的标记化方式。

+ [搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)中介绍了如何使用通配符和正则表达式的[简单语法](query-simple-syntax.md)或[完整 Lucene 语法](query-lucene-syntax.md)来构造查询请求。

  若要进行部分字词查询，例如，查询“3-6214”以找到“+1 (425) 703-6214”的匹配项，可以使用简单语法：`search=3-6214&queryType=simple`。

  若要进行中缀和后缀查询，例如，查询“num”或“numeric”以找到“alphanumeric”的匹配项，请使用完整 Lucene 语法和正则表达式：`search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>调整查询性能

如果实现包含 keyword_v2 标记器和小写标记筛选器的建议配置，可能会注意到查询性能下降，因为需要对索引中的现有标记进行额外的标记筛选处理。 

以下示例添加一个 [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)，使前缀匹配速度更快。 为包含字符的 2-25 个字符的组合生成额外的标记：（不局限于 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL）。 

可以想象到，额外的标记化操作会导致索引变得更大。 如果你有足够的容量来容纳较大的索引，则此方法的响应时间更快可能是更好的解决方案。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>后续步骤

本文说明了分析器如何造成查询问题和解决查询问题。 接下来，请更详细地了解分析器对索引编制和查询处理的影响。 具体而言，请考虑使用分析文本 API 返回标记化的输出，以便可以确切地了解分析器正在为索引创建哪些内容。

+ [语言分析器](search-language-support.md)
+ [Azure 认知搜索中用于文本处理的分析器](search-analyzers.md)
+ [分析文本 API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文搜索的工作原理（查询体系结构）](search-lucene-query-architecture.md)
