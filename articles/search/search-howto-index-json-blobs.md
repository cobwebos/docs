---
title: "使用 Azure 搜索 Blob 索引器对 JSON blob 编制索引"
description: "使用 Azure 搜索 Blob 索引器对 JSON blob 编制索引"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: de7af5419aa423734ad06b236e0edf61fbb0cad1

---

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>使用 Azure 搜索 Blob 索引器对 JSON blob 编制索引
本文介绍如何配置 Azure 搜索 Blob 索引器以从包含 JSON 的 blob 中提取结构化内容。

## <a name="scenarios"></a>方案
默认情况下，[Azure 搜索 Blob 索引器](search-howto-indexing-azure-blob-storage.md)会将 JSON blob 分析为单个文本块。 通常，你希望保留 JSON 文档的结构。 例如，以该 JSON 文档为例

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

你可能想要将它分析为带有“text”、“datePublished”和“tags”字段的 Azure 搜索文档。

或者，当你的 blob 包含 **JSON 对象数组**时，可能希望数组的每个元素成为一个单独的 Azure 搜索文档。 例如，以带有此 JSON 的 blob 为例：  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

可以使用 3 个单独的文档（每个都带有“id”和“text”字段）填充你的 Azure 搜索。

> [!IMPORTANT]
> 此功能目前处于预览状态。 它仅在使用版本 **2015-02-28-预览版**的 REST API 中可用。 请记住，预览版 API 仅供测试和评估，不应在生产环境中使用。
>
>

## <a name="setting-up-json-indexing"></a>设置 JSON 索引
若要对 JSON blob 编制索引，请将 `parsingMode` 配置参数设置为 `json`（可将每个 blob 作为单个文件编制索引）或 `jsonArray`（如果你的 blob 包含 JSON 数组）：

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

必要时使用**字段映射**选取源 JSON 文档的属性（该文档用于填充你的目标搜索索引）。  详细介绍如下所示。

> [!IMPORTANT]
> 使用 `json` 或 `jsonArray` 分析模式时，Azure 搜索假定数据源中的所有 blob 都将是 JSON。 如果需要在同一数据源中支持混合使用 JSON 和非 JSON blob，请通过[我们的 UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search)告知我们。
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>使用字段映射生成搜索文档
目前，Azure 搜索不能直接索引任意 JSON 文档，因为它只支持基元数据类型、字符串数组和 GeoJSON 点。 不过，可以使用**字段映射**选取你的 JSON 文档的部分，然后将它们“提升”到搜索文档的顶级字段。 若要了解字段映射的基础知识，请参阅 [Azure 搜索索引器字段映射弥补数据源和搜索索引之间的差异](search-indexer-field-mappings.md)。

回到我们的 JSON 文档示例：

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

假设你具有带有以下字段的搜索索引：类型 Edm.String 的 `text`、类型 Edm.DateTimeOffset 的 `date`、类型 Collection(Edm.String) 的 `tags`。 若要将你的 JSON 映射到所需形状，请使用以下字段映射：

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

使用 [JSON 指针](http://tools.ietf.org/html/rfc6901)表示法指定映射中的源字段名称。 以正斜杠开头引用你的 JSON 文档的根，然后通过使用正斜杠分隔的路径深入所需属性（任意层级的嵌套）。

还可以通过使用从零开始的索引来引用个别数组元素。 例如，若要选取上述示例中“tags”数组的第一个元素，请使用如下所示的字段映射：

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 如果字段映射路径中的源字段名称引用了 JSON 中不存在的属性，则会跳过该映射，不会出错。 如此，我们便可以支持具有不同架构的文档（这是一个常见用例）。 因为没有任何验证，所以需要注意避免字段映射规范中出现拼写错误。
>
>

如果你的 JSON 文档中只包含简单的顶层属性，可能根本不需要使用字段映射。 例如，如果你的 JSON 如下所示，顶层属性“text”、“datePublished”和“tags”将直接映射到搜索索引中的相应字段：

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

## <a name="indexing-nested-json-arrays"></a>对嵌套的 JSON 数组编制索引
假设你想要对一个 JSON 对象数组编制索引，但该数组嵌套在文档的某个位置，该怎么办？ 可以使用 `documentRoot` 配置属性选取包含该数组的属性。 例如，如果你的 blob 如下所示：

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

使用此配置对“level2”属性中包含的数组编制索引：

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


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
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

索引器：

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## <a name="help-us-make-azure-search-better"></a>帮助我们改进 Azure 搜索
如果你有功能请求或改进建议，请通过我们的 [UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search/)与我们联系。



<!--HONumber=Nov16_HO3-->


