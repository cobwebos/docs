---
title: "使用 Azure 搜索 Blob 索引器对 CSV blob 编制索引 | Microsoft Docs"
description: "了解如何使用 Azure 搜索索引 CSV blob"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/12/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 82c9d68bc3f1650648fac0597f4a329ef053b109


---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜索 Blob 索引器对 CSV blob 编制索引
默认情况下，[Azure 搜索 Blob 索引器](search-howto-indexing-azure-blob-storage.md)会将分隔的文本 blob 分析为单个文本块。 但在 blob 含有 CSV 数据的情况下，你通常希望将 blob 中的每一行视为一个单独文档。 例如，以如下分隔文本为例： 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

你可能希望将它分析为 2 个文档，每个文档都带有“id”、“datePublished”和“tags”字段。

在文本中，你将了解如何使用 Azure 搜索 Blob 索引器分析 CSV blob。 

> [!IMPORTANT]
> 此功能目前处于预览状态。 它仅在使用版本 **2015-02-28-预览版**的 REST API 中可用。 请记住，预览版 API 仅供测试和评估，不应在生产环境中使用。 
> 
> 

## <a name="setting-up-csv-indexing"></a>设置 CSV 索引
若要对 CSV blob 编制索引，请使用 `delimitedText` 分析模式创建或更新索引器定义：  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](search-api-indexers-2015-02-28-preview.md#create-indexer)。

`firstLineContainsHeaders` 指示每个 blob 的第一行（非空）包含标头。
如果 blob 未包含初始标头行，则应在索引器配置中指定标头： 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

目前，仅支持 UTF-8 编码。 此外，仅支持逗号 `','` 字符用作分隔符。 如果需要对其他编码或分隔符的支持，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。

> [!IMPORTANT]
> 当使用分隔文本分析模式时，Azure 搜索假定你的数据源中的所有 blob 都将是 CSV。 如果需要在同一数据源中支持混用 CSV 和非 CSV blob，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。
> 
> 

## <a name="request-examples"></a>请求示例
汇总后，以下是完整的有效负载示例。 

数据源： 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

索引器：

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>帮助我们改进 Azure 搜索
如果你有功能请求或改进建议，请通过我们的 [UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search/)与我们联系。




<!--HONumber=Nov16_HO3-->


