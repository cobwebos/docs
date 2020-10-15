---
title: 教程：创建自定义分析器
titleSuffix: Azure Cognitive Search
description: 了解如何构建自定义分析器以提高 Azure 认知搜索中搜索结果的质量。
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: ac7cee2c1d72b4102fb397aa8093c2d38686fc88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397260"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>教程：为电话号码创建自定义分析器

[分析器](search-analyzers.md)是任何搜索解决方案中的关键组件。 若要提高搜索结果的质量，请务必了解分析器如何工作并影响这些结果。

在某些情况下（例如对于自定义文本字段），只需选择正确的[语言分析器](index-add-language-analyzers.md)即可改善搜索结果。 但是，一些场景（例如准确搜索电话号码、URL 或电子邮件）可能需要使用自定义分析器。

本教程使用 Postman 和 Azure 认知搜索的 [REST API](/rest/api/searchservice/) 执行以下任务：

> [!div class="checklist"]
> * 说明分析器的工作方式
> * 定义用于搜索电话号码的自定义分析器
> * 测试自定义分析器切分文本的方式
> * 创建单独的分析器用于索引和搜索，以进一步改善结果

## <a name="prerequisites"></a>先决条件

本教程需要以下服务和工具。

+ [Postman 桌面应用](https://www.getpostman.com/)
+ [创建](search-create-service-portal.md)或[查找现有搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>下载文件

本教程的源代码位于 [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub 存储库中的 [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) 文件夹中。

## <a name="1---create-azure-cognitive-search-service"></a>1 - 创建 Azure 认知搜索服务

若要完成本教程，你需要有一个 Azure 认知搜索服务，该服务可以[在门户中创建](search-create-service-portal.md)。 可使用免费层完成本演练。

要执行下一步骤，你需要了解搜索服务的名称及其 API 密钥。 如果你不确定如何查找这些项，请查看此[快速入门](search-create-service-portal.md#get-a-key-and-url-endpoint)。


## <a name="2---set-up-postman"></a>2 - 设置 Postman

接下来，启动 Postman 并导入从 [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) 下载的集合。

若要导入集合，请转到“文件” > “导入”，然后选择要导入的集合文件 。

对于每个请求，需要：

1. 请将 `<YOUR-SEARCH-SERVICE>` 替换为搜索服务的名称。

1. 将 `<YOUR-ADMIN-API-KEY>` 替换为搜索服务的主要密钥或辅助密钥。

  :::image type="content" source="media/search-get-started-postman/postman-url.png" alt-text="Postman 请求 URL 和标头" border="false":::

如果不熟悉 Postman，请参阅[使用 Postman 探索 Azure 认知搜索 REST API](search-get-started-postman.md)。

## <a name="3---create-an-initial-index"></a>3 - 创建初始索引

在此步骤中，我们将创建一个初始索引，将文档加载到索引中，然后查询文档以查看初始搜索的执行情况。

### <a name="create-index"></a>创建索引

首先，我们创建一个名为 `tutorial-basic-index` 的简单索引，其中包含两个字段：`id` 和 `phone_number`。 我们尚未定义分析器，因此默认使用 `standard.lucene` 分析器。

为创建该索引，我们发送以下请求：

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>加载数据

接下来，我们将数据加载到索引中。 在某些情况下，你可能无法控制引入的电话号码的格式，因此我们将针对不同的格式进行测试。 理想情况下，搜索解决方案将返回所有匹配的电话号码，而不考虑它们的格式。

使用以下请求将数据加载到索引中： 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

索引中包含数据后，我们即可开始搜索。

### <a name="search"></a>搜索

为了使搜索直观，最好不要期望用户以特定的方式设置查询格式。 用户可以使用上面显示的任何格式搜索 `(425) 555-0100`，并且仍然期望返回结果。 在此步骤中，我们将测试几个示例查询，查看它们的执行情况。

首先，搜索 `(425) 555-0100`：

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

此查询返回四个预期结果中的三个结果，同时还返回两个意外结果 ：

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

接下来，让我们搜索不带任何格式的号码 `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

但这一次情况更糟，此查询只返回了四个正确匹配项之一。

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

对于出现这些结果，不止你一个人会感觉到困惑。 在下一部分中，我们将深入探讨为什么会得到这些结果。

## <a name="4---debug-search-results"></a>4 - 调试搜索结果

若要理解这些搜索结果，首先必须了解分析器的工作方式。 然后，我们可以使用[分析文本 API](/rest/api/searchservice/test-analyzer) 测试默认分析器，然后创建满足我们需要的分析器。

### <a name="how-analyzers-work"></a>分析器的工作方式

[分析器](search-analyzers.md)是[全文搜索引擎](search-lucene-query-architecture.md)的组成部分，负责在查询字符串和带索引文档中进行文本处理。 不同的分析器根据具体的方案以不同的方式处理文本。 对于此场景，我们需要构建一个专为电话号码定制的分析器。

分析器包含三个组件：

+ [字符筛选器](#CharFilters)，用于从输入文本中删除或替换单个字符。
+ [Tokenizer](#Tokenizers)，用于将输入文本分解为标记，这些标记将成为输入索引中的密钥。
+ [标记筛选器](#TokenFilters)，用于处理 tokenizer 生成的标记。

在下图中，可以看到这三个组件如何协同工作以切分一个句子：

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Postman 请求 URL 和标头":::

这些标记随后被存储在倒排索引中，以便进行快速的全文搜索。  倒排索引通过将在词法分析过程中提取的所有唯一词映射到它们所在的文档来实现全文搜索。 下图提供了一个示例：

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Postman 请求 URL 和标头":::

所有搜索均可归结为搜索存储在倒排索引中的字词。 用户发出查询时：

1. 解析查询并分析查询词。
1. 然后扫描倒排索引以查找具有匹配词的文档。
1. 最后，根据[相似性算法](index-ranking-similarity.md)对检索到的文档进行排序。

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Postman 请求 URL 和标头":::

如果查询词与倒排索引中的词不匹配，则不会返回结果。 若要详细了解查询的工作方式，请参阅这篇关于[全文搜索](search-lucene-query-architecture.md)的文章。

> [!Note]
> 对于此规则，[部分字词查询](search-query-partial-matching.md)是一个重要的例外情况。 与常规字词查询不同，这些查询（前缀查询、通配符查询、正则表达式查询）绕过了词法分析过程。 部分字词在与索引中的字词匹配之前只使用小写。 如果分析器未配置为支持这些类型的查询，你通常会收到意外的结果，因为索引中不存在匹配的字词。

### <a name="test-analyzer-using-the-analyze-text-api"></a>使用分析文本 API 测试分析器

Azure 认知搜索提供了一个[分析文本 API](/rest/api/searchservice/test-analyzer)，通过该 API 可测试分析器以了解它们如何处理文本。

使用以下请求调用分析文本 API：

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
      "text": "(425) 555-0100",
      "analyzer": "standard.lucene"
  }
```

然后，API 返回从文本中提取的标记列表。 你可以看到，标准 Lucene 分析器将电话号码拆分成了三个单独的标记：

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

相反，格式化为没有任何标点符号的电话号码 `4255550100` 被切分为单个标记。

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

请记住，查询词和索引文档都会得到分析。 回顾上一步的搜索结果，我们可以开始了解为什么会返回这些结果。

在第一个查询中，返回了不正确的电话号码，因为其中一个词 `555` 与我们搜索的一个词匹配。 在第二个查询中，只返回了一个号码，因为它是唯一具有与 `4255550100` 匹配的词的记录。

## <a name="5---build-a-custom-analyzer"></a>5 - 生成自定义分析器

现在我们理解了我们所看到的结果，接着我们生成一个自定义分析器来改善词汇切分逻辑。

目的是提供对电话号码的直观搜索，无论查询或索引字符串采用哪种格式。 为实现此结果，我们将指定[字符筛选器](#CharFilters)、[tokenizer](#Tokenizers) 和[标记筛选器](#TokenFilters)。

<a name="CharFilters"></a>

### <a name="character-filters"></a>字符筛选器

字符筛选器用于在将文本馈送到 tokenizer 之前对其进行处理。 字符筛选器的常见用途包括筛选出 HTML 元素或替换特殊字符。

对于电话号码，我们希望删除空格和特殊字符，因为并非所有电话号码格式都包含相同的特殊字符和空格。

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

上方的筛选器将从输入中删除 `-`、`(`、`)`、`+`、`.` 和空格。

|输入|输出|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizer

Tokenizer 将文本拆分为标记，并在此过程中丢弃某些字符，如标点符号。 在许多情况下，词汇切分的目标是将句子拆分为单个单词。

对于此场景，我们使用关键字 tokenizer (`keyword_v2`)，因为我们想要将电话号码捕获为单个词。 请注意，这并不是解决此问题的唯一方法。 请参阅下面的[备用方法](#Alternate)部分。

关键字 tokenizer 始终将收到的相同文本作为单个词输出。

|输入|输出|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>标记筛选器

标记筛选器将筛选出或修改由 tokenizer 生成的标记。 标记筛选器的一种常见用法是使用小写标记筛选器将所有字符小写。 另一种常见用法是筛选出非索引字，例如 `the`、`and` 或 `is`。

在此场景中我们不需要使用上述任何一种筛选器，而将使用 nGram 标记筛选器以允许对电话号码进行部分搜索。

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[nGram_v2 标记筛选器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)根据 `minGram` 和 `maxGram` 参数将标记拆分为给定大小的 n 元语法。

对于电话分析器，我们将 `minGram` 设置为 `3`，因为这是我们希望用户搜索的最短子字符串。 将 `maxGram` 设置为 `20`，确保所有电话号码（即使是分机号）都可放入单个 n 元语法中。

 令人遗憾的是，n 元语法的副作用是会返回一些误报。 我们将在第 7 步中解决此问题，方法是为不包含 n 元语法标记筛选器的搜索构建一个单独的分析器。

|输入|输出|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>分析器

有了字符筛选器、tokenizer 和标记筛选器，我们就可以定义分析器了。

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|输入|输出|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

请注意，现在可以搜索输出中的任何标记。 如果查询包含这些标记中的任何一个，则返回电话号码。

定义自定义分析器后，重新创建索引，以便在下一步中可使用自定义分析器进行测试。 为简单起见，Postman 集合将使用我们定义的分析器创建名为 `tutorial-first-analyzer` 的新索引。

## <a name="6---test-the-custom-analyzer"></a>6 - 测试自定义分析器

创建索引后，现在可以使用以下请求测试创建的分析器：

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

然后，你将能够看到由电话号码生成的标记集合：

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - 为查询生成自定义分析器

使用自定义分析器对索引进行一些示例查询后，你会发现召回率有所改进，并且所有匹配的电话号码现在均已返回。 但是，n 元语法标记筛选器也会返回一些误报。 这是 n 元语法标记筛选器的常见副作用。

为了防止误报，我们将创建一个单独的分析器用于查询。 此分析器将与我们已创建的分析器相同，但没有 `custom_ngram_filter`。

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

然后，在索引定义中，同时指定 `indexAnalyzer` 和 `searchAnalyzer`。

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

进行该更改后，一切准备就绪。 重新创建索引，为数据编制索引，并再次测试查询以验证搜索是否按预期方式工作。 如果你使用的是 Postman 集合，它将创建第三个名为 `tutorial-second-analyzer` 的索引。

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>备用方法

以上分析器旨在最大程度地提高搜索灵活性。 但这样做的代价是，在索引中存储许多可能无关紧要的字词。

下面的示例显示了另一个同样可执行此任务的分析器。 

除了难以按逻辑方式划分电话号码的输入数据（例如 `14255550100`），该分析器运行良好。 例如，分析器将无法区分国家/地区代码 `1` 和区号 `425`。 如果用户在搜索中未包含国家/地区代码，则该差异将导致不返回上述号码。

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

在下面的示例中，你可以看到电话号码被拆分为你通常希望用户搜索的区块。

|输入|输出|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

根据你的需求，这可能是解决问题的更有效方法。

## <a name="reset-and-rerun"></a>重置并重新运行

为简单起见，本教程指导你创建了三个新索引。 但是，在开发的早期阶段删除和重新创建索引是很常见的。 可以在 Azure 门户中或使用以下 API 调用删除索引：

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>要点

本教程演示了生成和测试自定义分析器的过程。 你已创建了索引、为数据编制了索引，然后针对索引进行了查询以查看返回的搜索结果。 然后，你使用分析文本 API 查看实际的词法分析过程。

本教程中定义的分析器为搜索电话号码提供了一种简单的解决方案，而这一同样的过程也可以用于针对你可能遇到的任何场景构建自定义电话分析器。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时删除不再需要的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接在门户中查找和管理资源。

## <a name="next-steps"></a>后续步骤

现在，你已经熟悉了如何创建自定义分析器，接下来让我们看看可用于构建丰富的搜索体验的所有不同筛选器、tokenizer 和分析器。

> [!div class="nextstepaction"]
> [Azure 认知搜索中的自定义分析器](index-add-custom-analyzers.md)