---
title: 部分术语、模式和特殊字符
titleSuffix: Azure Cognitive Search
description: 使用通配符、正则表达式和前缀查询来匹配 Azure 认知搜索查询请求中的全部或部分字词。 可以使用完整查询语法和自定义分析器来解析包含特殊字符的难以匹配的模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592037"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>部分术语搜索和包含特殊字符的模式（通配符、正则表达式、模式）

*部分术语搜索*是指由术语片段组成的查询，其中，而不是整个术语，可能只是字词的开头、中间或结尾（有时也称为前缀、中缀或后缀查询）。 *模式*可能会组合片段，通常包含特殊字符，如作为查询字符串的一部分的短划线或斜线。 常见的用例包括查询电话号码、URL、人员或产品代码或组合词的部分。

如果索引没有预期格式的字词，则部分和模式搜索可能会出现问题。 在编制索引的 "[词法分析" 阶段](search-lucene-query-architecture.md#stage-2-lexical-analysis)（假定默认的标准分析器）期间，会丢弃特殊字符、拆分和复合字符串并删除空格;当找不到匹配项时，所有这些都可能导致模式查询失败。 例如，类似`+1 (425) 703-6214`于（标记为`"1"`、 `"425"`、 `"703"`、 `"6214"`）的电话号码不会显示在`"3-62"`查询中，因为该内容实际上不存在于索引中。 

解决方法是调用一个分析器，该分析器会保留完整的字符串（如有必要，包括空格和特殊字符），以便能够与部分术语和模式匹配。 为完整的字符串创建其他字段以及使用内容保留分析器是解决方案的基础。

> [!TIP]
> 熟悉 Postman 和 REST Api？ [下载 "查询示例" 集合](https://github.com/Azure-Samples/azure-search-postman-samples/)以查询本文中所述的部分术语和特殊字符。

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Azure 认知搜索中的部分搜索

在 Azure 认知搜索中，部分搜索和模式适用于以下形式：

+ [前缀搜索](query-simple-syntax.md#prefix-search)，如`search=cap*`"Cap'n 插孔的海滨 Inn" 或 "Gacc 资本"。 您可以使用简单查询语法或完整的 Lucene 查询语法来进行前缀搜索。

+ 搜索或[正则表达式](query-lucene-syntax.md#bkmk_regex) [，搜索模式](query-lucene-syntax.md#bkmk_wildcard)或嵌入式字符串的各个部分。 通配符和正则表达式需要完整的 Lucene 语法。 后缀和索引查询被表述为正则表达式。

  部分术语搜索的一些示例包括以下各项。 对于后缀查询，假设术语为 "字母数字"，则使用通配符搜索（`search=/.*numeric.*/`）查找匹配项。 对于包含内部字符的部分术语，例如 URL 片段，可能需要添加转义符。 在 JSON 中，用反`/`斜杠`\`转义正斜杠。 同样， `search=/.*microsoft.com\/azure\/.*/` URL 片段的语法为 "microsoft.com/azure/"。

如上所述，以上所有条件都要求索引包含格式为有利于的字符串，而标准分析器并不提供这些字符串。 按照本文中的步骤，可以确保存在必要的内容来支持这些方案。

## <a name="solving-partialpattern-search-problems"></a>解决部分/模式搜索问题

如果需要搜索片段或模式或特殊字符，可以使用在更简单的标记规则下操作的自定义分析器替代默认分析器，同时保留整个字符串。 返回一步，该方法如下所示：

+ 定义一个字段以存储字符串的完整版本（假设您希望分析和非分析的文本）
+ 选择预定义分析器或定义自定义分析器以输出未经过分析的不完整字符串
+ 向字段分配自定义分析器
+ 生成并测试索引

> [!TIP]
> 评估分析器是一个需要频繁重建索引的迭代过程。 可以通过使用 Postman、用于[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 REST api、[删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)来简化此步骤。 对于加载文档，请求正文应包含要测试的小型代表数据集（例如，具有电话号码或产品代码的字段）。 在同一 Postman 集合中使用这些 Api，可以快速循环执行这些步骤。

## <a name="duplicate-fields-for-different-scenarios"></a>不同方案的重复字段

分析器是根据每个字段进行分配的，这意味着您可以在索引中创建字段，以便针对不同的方案进行优化。 具体来说，您可以定义 "featureCode" 和 "featureCodeRegex" 以支持对第一次进行常规全文搜索，在第二种模式下支持高级模式匹配。

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

选择生成整个术语标记的分析器时，以下分析器是常见选项：

| 分析器 | 行为 |
|----------|-----------|
| [语言分析器](index-add-language-analyzers.md) | 保留组合词或字符串、元音突变和 verb 形式中的连字符。 如果查询模式包含短划线，则使用语言分析器可能就已足够。 |
| [关键字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整个字段的内容将被标记为单个字词。 |
| [白](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 仅分隔空白区域。 包含短划线或其他字符的字词被视为单个标记。 |
| [自定义分析器](index-add-custom-analyzers.md) | 您创建自定义分析器可同时指定标记器和令牌筛选器。 前面的分析器必须按原样使用。 使用自定义分析器可以选取要使用的 tokenizer 和令牌筛选器。 <br><br>建议结合使用带有[小写令牌筛选器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)的[关键字标记器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)。 预定义的[关键字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)本身并不会将任何大写文本都降低，这可能会导致查询失败。 自定义分析器提供添加小写令牌筛选器的机制。 |

如果使用的是 web API 测试工具（如 Postman），则可以添加[测试分析器 REST 调用](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)来检查标记化的输出。

您必须具有要使用的现有索引。 如果给定了现有索引和包含短划线或部分术语的字段，则可以尝试针对特定术语的各种分析器来查看发出的标记。  

1. 检查标准分析器以查看默认情况下如何标记术语。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 计算响应，查看索引中标记文本的方式。 请注意每个术语如何小写并断开。

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
1. 修改请求以使用`whitespace`或`keyword`分析器：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 现在，响应包含单个标记（大写），并以字符串的一部分的形式保留短划线。 如果需要搜索模式或部分术语，则查询引擎现在具有查找匹配项的基础。


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
> 请注意，生成查询树时，查询分析程序经常搜索表达式中的字词。 如果你使用的分析器不是小写文本输入，并且你没有获得预期结果，则这可能是原因。 解决方案是添加小写令牌筛选器，如下面的 "使用自定义分析器" 一节中所述。

## <a name="configure-an-analyzer"></a>配置分析器
 
无论你是要评估分析器还是要使用特定配置前进，都需要在字段定义中指定分析器，如果未使用内置分析器，则可能还需要配置分析器本身。 交换分析器时，通常需要重新生成索引（删除、重新创建和重新加载）。 

### <a name="use-built-in-analyzers"></a>使用内置分析器

内置或预定义分析器可以通过字段定义的`analyzer`属性的名称来指定，索引中不需要其他配置。 下面的示例演示如何对字段设置`whitespace`分析器。 

有关其他方案以及有关其他内置分析器的详细信息，请参阅[预定义分析器列表](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

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

如果使用的是[自定义分析器](index-add-custom-analyzers.md)，请在索引中使用用户定义的标记器、标记筛选器的组合，并在可能的配置设置中定义它。 接下来，在字段定义中引用它，就像内置分析器一样。

如果目标是整术语标记，则建议使用由**关键字标记器**和**小写标记筛选器**组成的自定义分析器。

+ 关键字标记器为字段的全部内容创建单个标记。
+ 小写标记筛选器将大写字母转换为小写的文本。 查询分析器通常是小写的任何大写文本输入。 小写字母 homogenizes 的输入与标记化的字词。

下面的示例演示了提供关键字标记器和小写标记筛选器的自定义分析器。

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
> 标记器`keyword_v2`和`lowercase`令牌筛选器对于系统是已知的，并且使用其默认配置，这就是你可以按名称引用这些筛选器，而无需首先对其进行定义的原因。

## <a name="build-and-test"></a>生成和测试

使用支持方案的分析器和字段定义定义索引后，请加载包含代表性字符串的文档，以便可以测试部分字符串查询。 

前面几节介绍了逻辑。 本部分将逐步介绍在测试解决方案时应调用的每个 API。 如前文所述，如果你使用 Postman 等交互式 web 测试工具，则可以快速执行这些任务。

+ [删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)删除具有相同名称的现有索引，以便可以重新创建该索引。

+ [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index)在搜索服务中创建索引结构，包括分析器定义和具有分析器规范的字段。

+ [加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)导入与您的索引具有相同结构以及可搜索内容的文档。 完成此步骤后，你的索引就可以查询或测试了。

+ 在 "[选择分析器](#choose-an-analyzer)" 中引入了[测试分析器](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)。 使用各种分析器测试索引中的一些字符串，以了解术语如何标记。

+ [搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)说明了如何使用[简单的语法](query-simple-syntax.md)或完整的通配符和正则表达式的[Lucene 语法](query-lucene-syntax.md)来构造查询请求。

  对于部分术语查询，如查询 "3-6214" 以查找 "+ 1 （425） 703-6214" 的匹配项，可以使用简单语法： `search=3-6214&queryType=simple`。

  对于中缀和后缀查询，如查询 "num" 或 "numeric" 以查找 "字母数字" 的匹配项，请使用完整的 Lucene 语法和正则表达式：`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>提示和最佳实践

### <a name="tune-query-performance"></a>调整查询性能

如果实现了包含 keyword_v2 标记器和小写标记筛选器的推荐配置，则可能会注意到，查询性能会下降，因为附加的令牌筛选器是通过索引中的现有标记处理的。 

下面的示例添加[EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)以更快地使前缀匹配。 为2-25 个字符组合生成了附加标记，其中包含字符：（不仅 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL）。 您可以想象，附加的词汇切分会导致更大的索引。

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>使用不同的分析器进行索引和查询处理

在编制索引期间和执行查询期间调用分析器。 同时为两者使用相同的分析器，但你可以为每个工作负荷配置自定义分析器。 分析器替代在`analyzers`部分的[索引定义](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定，然后在特定字段上引用。 

如果在编制索引期间只需要自定义分析，则可以将自定义分析器应用于索引并继续使用标准 Lucene 分析器（或其他分析器）进行查询。

若要指定特定于角色的分析，可以对每个属性的字段设置属性， `indexAnalyzer`设置`searchAnalyzer` ，而不是`analyzer`默认属性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>后续步骤

本文介绍分析器是如何影响查询问题并解决查询问题的。 下一步，详细了解分析器对索引和查询处理的影响。 特别要注意的是，请考虑使用分析文本 API 返回已标记化的输出，以便可以确切地查看分析器为索引创建的内容。

+ [语言分析器](search-language-support.md)
+ [用于 Azure 认知搜索中文本处理的分析器](search-analyzers.md)
+ [分析文本 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文搜索的工作原理（查询体系结构）](search-lucene-query-architecture.md)