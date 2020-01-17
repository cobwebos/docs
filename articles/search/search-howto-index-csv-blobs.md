---
title: 搜索 CSV blob
titleSuffix: Azure Cognitive Search
description: 使用 delimitedText 分析模式从 Azure Blob 存储提取并导入 CSV。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122315"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中使用 delimitedText 分析模式和 Blob 索引器为 CSV blob 编制索引

默认情况下， [Azure 认知搜索 blob 索引器](search-howto-indexing-azure-blob-storage.md)将分隔的文本 blob 分析为单个文本块。 但在 blob 含有 CSV 数据的情况下，通常希望将 blob 中的每一行视为一个单独文档。 例如，给定以下带分隔符的文本，可能要将其分析为两个文档，每个文档包含“id”、“datePublished”和“tags”字段： 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

本文介绍如何通过设置 `delimitedText` 分析模式，使用 Azure 认知搜索 blob 索引器分析 CSV blob。 

> [!NOTE]
> 按照[一对多索引](search-howto-index-one-to-many-blobs.md)中的索引器配置建议，从一个 Azure blob 输出多个搜索文档。

## <a name="setting-up-csv-indexing"></a>设置 CSV 索引
若要为 CSV blob 编制索引，请在[create](https://docs.microsoft.com/rest/api/searchservice/create-indexer) index 请求上使用 `delimitedText` 分析模式创建或更新索引器定义：

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` 指示每个 blob 的第一行（非空）包含标头。
如果 blob 未包含初始标头行，则应在索引器配置中指定标头： 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

可以使用 `delimitedTextDelimiter` 配置设置来自定义分隔符字符。 例如：

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> 目前，仅支持 UTF-8 编码。 如果需要支持其他编码，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 上为其投票。

> [!IMPORTANT]
> 使用分隔文本分析模式时，Azure 认知搜索假定数据源中的所有 blob 都为 CSV。 如果需要在同一数据源中支持混用 CSV 和非 CSV blob，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 上为其投票。
> 
> 

## <a name="request-examples"></a>请求示例
汇总后，以下是完整的有效负载示例。 

数据源： 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

索引器：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>帮助我们提高 Azure 认知搜索
如果有功能请求或改进建议，请在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 上提供相关意见。

