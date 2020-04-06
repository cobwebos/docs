---
title: 部分术语、图案和特殊字符
titleSuffix: Azure Cognitive Search
description: 使用通配符、正则表达式和前缀查询在 Azure 认知搜索查询请求中匹配整个或部分术语。 可以使用完整的查询语法和自定义分析器解决包含特殊字符的难以匹配的模式。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 7f001a0d443e4ec668aedaabb7505884163bf37e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666789"
---
# <a name="partial-term-search-and-patterns-with-special-characters---azure-cognitive-search-wildcard-regex-patterns"></a>具有特殊字符的部分术语搜索和模式 - Azure 认知搜索（通配符、正则表达式、模式）

*部分术语搜索*是指由术语片段组成的查询，例如字符串的第一个、最后一个或内部部分。 *模式*可能是片段的组合，有时具有特殊字符（如破折号或斜杠）是查询的一部分。 常见用例包括查询电话号码、URL、人员或产品代码或复合词的某些部分。

如果索引没有模式匹配所需的格式的字词，则部分搜索可能会有问题。 在索引的文本分析阶段，使用默认标准分析器，将丢弃特殊字符，将复合字符串和复合字符串拆分，导致找不到匹配时模式查询失败。 例如，电话号码`+1 (425) 703-6214`（令牌化为`"1"`，、、）`"425"``"703"``"6214"`不会显示在`"3-62"`查询中，因为索引中实际上不存在该内容。 

解决方案是调用保留完整字符串（如有必要）包括空格和特殊字符的分析器，以便您可以支持部分术语和模式。 为完整字符串创建附加字段，以及使用内容保留分析器，是解决方案的基础。

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Azure 认知搜索中的部分搜索是什么

在 Azure 认知搜索中，部分搜索和模式以以下形式提供：

+ [前缀搜索](query-simple-syntax.md#prefix-search)，`search=cap*`如 ，匹配"Cap'n Jack 的海滨旅馆"或"Gacc 资本"。 您可以使用简单的查询语法进行前缀搜索。

+ [通配符搜索](query-lucene-syntax.md#bkmk_wildcard)或用于搜索嵌入字符串的模式或部分（包括后缀）的[正则表达式](query-lucene-syntax.md#bkmk_regex)。 通配符和正则表达式需要完整的 Lucene 语法。 

  部分术语搜索的一些示例包括以下内容。 对于后缀查询，给定术语"字母数字"，您将使用通配符搜索 （）`search=/.*numeric.*/`来查找匹配项。 对于包含字符（如 URL 片段）的部分术语，可能需要添加转义字符。 在 JSON 中，`/`向前斜杠用向后斜杠`\`转出。 因此，URL`search=/.*microsoft.com\/azure\/.*/`片段的语法"microsoft.com/azure/"。

如前所述，上述所有要求索引包含有利于模式匹配的格式的字符串，而标准分析器未提供这种格式。 通过按照本文中的步骤，可以确保存在支持这些方案所需的内容。

## <a name="solving-partial-search-problems"></a>解决部分搜索问题

当您需要搜索模式或特殊字符时，可以使用在更简单的标记规则下操作的自定义分析器覆盖默认分析器，保留整个字符串。 退后一步，该方法如下所示：

+ 定义一个字段以存储字符串的完整版本（假定您需要分析和非分析文本）
+ 选择预定义的分析器或定义自定义分析器以输出完整字符串
+ 将分析器分配给字段
+ 生成和测试索引

> [!TIP]
> 评估分析器是一个迭代过程，需要频繁的索引重建。 您可以使用 Postman、[用于创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)的 REST API、[删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)，使此步骤更加简单。 对于 Load 文档，请求正文应包含要测试的小型代表性数据集（例如，包含电话号码或产品代码的字段）。 使用同一 Postman 集合中的这些 API，您可以快速循环完成这些步骤。

## <a name="duplicate-fields-for-different-scenarios"></a>不同方案的重复字段

分析器按字段分配，这意味着您可以在索引中创建字段以针对不同方案进行优化。 具体而言，您可以定义"功能代码"和"功能代码Regex"，以支持在第一个搜索时的常规全文搜索，并在第二个搜索时支持高级模式匹配。

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="choose-an-analyzer"></a>选择分析器

选择生成整期令牌的分析器时，以下分析器是常见的选择：

| 分析器 | 行为 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整个字段的内容被标记为单个术语。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 仅在空白上分隔。 包含破折号或其他字符的术语被视为单个令牌。 |
| [自定义分析器](index-add-custom-analyzers.md) | （推荐）通过创建自定义分析器，可以同时指定标记器和令牌筛选器。 以前的分析仪必须原样使用。 自定义分析器允许您选择要使用的标记器和令牌筛选器。 <br><br>建议的组合是具有[小写标记筛选器的](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)[关键字标记器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)。 就其本身而言，预定义的[关键字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)不会小写任何大写文本，这可能导致查询失败。 自定义分析器为您提供了添加小写令牌筛选器的机制。 |

如果使用像 Postman 这样的 Web API 测试工具，则可以添加[测试分析器 REST 调用](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)以检查令牌化输出。

您必须具有要处理的现有索引。 给定现有索引和包含破折号或部分术语的字段，您可以尝试针对特定术语的各种分析器以查看发出的令牌。  

1. 检查标准分析器，了解默认情况下术语如何标记。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 评估响应以查看文本在索引中的标记方式。 请注意每个术语如何小写和分解。

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
1. 修改使用`whitespace`或`keyword`分析器的请求：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 现在，响应由单个令牌（大写）组成，其中保留破折号作为字符串的一部分。 如果需要搜索模式或部分术语，查询引擎现在具有查找匹配项的基础。


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
> 请注意，在生成查询树时，查询解析器通常是搜索表达式中的小写术语。 如果使用的分析器不小写文本输入，并且未获得预期结果，这可能是原因。 解决方案是添加小写令牌筛选器，如下所述"使用自定义分析器"部分。

## <a name="configure-an-analyzer"></a>配置分析器
 
无论您是在评估分析器还是推进特定配置，都需要在字段定义上指定分析器，并且如果您不使用内置分析器，则可能需要配置分析器本身。 交换分析器时，通常需要重新生成索引（删除、重新创建和重新加载）。 

### <a name="use-built-in-analyzers"></a>使用内置分析仪

内置或预定义的分析器可以在字段定义的`analyzer`属性上按名称指定，索引中不需要其他配置。 下面的示例演示如何在`whitespace`字段上设置分析器。 有关可用内置分析器的详细信息，请参阅[预定义分析器列表](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

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

如果使用[自定义分析器](index-add-custom-analyzers.md)，请使用用户定义的令牌器、令牌筛选器和可能的配置设置的组合在索引中定义它。 接下来，在字段定义上引用它，就像内置分析器一样。

当目标是整个期限标记化时，建议使用由**关键字标记器**和**小写令牌筛选器**组成的自定义分析器。

+ 关键字标记器为字段的整个内容创建单个令牌。
+ 小写令牌筛选器将大写字母转换为小写文本。 查询解析器通常小写任何大写文本输入。 低封装使用标记化术语使输入均质化。

下面的示例演示了提供关键字标记器和小写令牌筛选器的自定义分析器。

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
> `keyword_v2`系统知道令牌器`lowercase`和令牌筛选器，并使用它们的默认配置，因此无需首先定义它们即可按名称引用它们。

## <a name="build-and-test"></a>生成和测试

使用支持方案的分析器和字段定义定义索引后，加载具有代表性字符串的文档，以便可以测试部分字符串查询。 

前面的各节解释了逻辑。 本节将介绍测试解决方案时应调用的每个 API。 如前所述，如果您使用交互式 Web 测试工具（如 Postman），则可以快速完成这些任务。

+ [删除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)将删除同名的现有索引，以便可以重新创建它。

+ [创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)在搜索服务上创建索引结构，包括分析器定义和具有分析器规范的字段。

+ [加载文档](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)导入具有与索引具有相同结构的文档以及可搜索的内容。 执行此步骤后，索引即可查询或测试。

+ [测试分析器](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)是在[选择分析器](#choose-an-analyzer)中引入的。 使用各种分析器测试索引中的某些字符串，以了解术语如何标记化。

+ [搜索文档](https://docs.microsoft.com/rest/api/searchservice/search-documents)解释了如何使用[简单的语法](query-simple-syntax.md)或通配符和正则表达式[使用完整的 Lucene 语法](query-lucene-syntax.md)构造查询请求。

## <a name="tips-and-best-practices"></a>提示和最佳实践

### <a name="tune-query-performance"></a>调整查询性能

如果实现建议配置，包括keyword_v2令牌器和小写令牌筛选器，您可能会注意到查询性能下降，因为索引中现有令牌处理了额外的令牌筛选器。 

下面的示例添加[EdgeNGramTokenFilter，](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)以使前缀匹配得更快。 其他令牌以 2-25 个字符组合生成，包括字符：不仅 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQ、MSFT/SQL）。 可以想象，额外的标记化会导致更大的索引。

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

分析器在索引期间和查询执行期间调用。 两者使用相同的分析器是很常见的，但您可以为每个工作负载配置自定义分析器。 分析器覆盖在`analyzers`节中的[索引定义](https://docs.microsoft.com/rest/api/searchservice/create-index)中指定，然后引用到特定字段。 

当仅在索引期间需要自定义分析时，您可以将自定义分析器应用于仅索引，并继续使用标准 Lucene 分析器（或其他分析器）进行查询。

要指定特定于角色的分析，可以为每个属性设置字段的属性，设置`indexAnalyzer``searchAnalyzer`而不是默认`analyzer`属性。

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>后续步骤

本文介绍了分析器如何同时导致查询问题和解决查询问题。 作为下一步，请仔细查看分析器对索引和查询处理的影响。 特别是，请考虑使用分析文本 API 返回标记化输出，以便您可以准确查看分析器为索引创建的内容。

+ [语言分析器](search-language-support.md)
+ [用于 Azure 认知搜索中文本处理的分析器](search-analyzers.md)
+ [分析文本 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文搜索的工作原理（查询体系结构）](search-lucene-query-architecture.md)