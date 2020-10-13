---
title: 搜索纯文本 blob
titleSuffix: Azure Cognitive Search
description: 配置搜索索引器，以便从 Azure blob 提取纯文本，以便在 Azure 认知搜索中进行全文搜索。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533919"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>如何在 Azure 中为纯文本 blob 编制索引认知搜索

使用 [blob 索引器](search-howto-indexing-azure-blob-storage.md) 提取全文搜索的可搜索文本时，可以调用各种分析模式来获取更好的索引结果。 默认情况下，索引器将分隔的文本 blob 分析为单个文本块。 但是，如果所有 blob 都包含采用相同编码的纯文本，则可以使用 **文本分析模式**大幅提高索引性能。

## <a name="set-up-plain-text-indexing"></a>设置纯文本索引

若要为纯文本 blob 编制索引，请 `parsingMode` `text` 在 [create](/rest/api/searchservice/create-indexer) index 请求上使用配置属性创建或更新索引器定义：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

默认情况下将采用 `UTF-8` 编码。 若要指定不同的编码，请使用 `encoding` 配置属性： 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>请求示例

分析模式在索引器定义中指定。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>帮助我们改善 Azure 认知搜索

如果有功能请求或改进建议，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 上提供相关意见。 如果需要使用现有功能的帮助，请在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上发布问题。

## <a name="next-steps"></a>后续步骤

* [Azure 认知搜索中的索引器](search-indexer-overview.md)
* [如何配置 blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Blob 索引概述](search-blob-storage-integration.md)