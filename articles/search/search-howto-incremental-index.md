---
title: 为基于内容的更改跟踪设置增量索引
titleSuffix: Azure Cognitive Search
description: 对于认知技能组合中的受控处理，启用更改跟踪并保留丰富内容的状态。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ac082d6ecb6624dc0d5bc0ab927ff8b91ebdabce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512179"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>如何在 Azure 中设置增强的文档的增量索引认知搜索

本文介绍如何将状态和缓存添加到通过 Azure 认知搜索扩充管道移动的大量文档，以便可以从任何支持的数据源增量索引文档。 默认情况下，技能组合是无状态的，并且更改其组合的任何部分都需要完全重新运行索引器。 通过增量索引，索引器可以确定管道的哪些部分发生了更改，为未更改的部分重复使用现有的根据，并为确实发生变化的步骤修改根据。 缓存的内容放置在 Azure 存储中。

如果你不熟悉如何设置索引器，请从[索引器概述](search-indexer-overview.md)开始，然后继续转到[技能集](cognitive-search-working-with-skillsets.md)了解扩充管道。 有关关键概念的更多背景信息，请参阅[增量索引](cognitive-search-incremental-indexing-conceptual.md)。

增量索引使用[搜索 REST api 版本 = 2019-05-06-01.5.1](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)进行配置。

> [!NOTE]
> 此功能在门户中尚不可用，必须以编程方式使用。
>

## <a name="modify-an-existing-indexer"></a>修改现有索引器

如果有现有索引器，请按照以下步骤启用增量索引。

### <a name="step-1-get-the-indexer"></a>步骤1：获取索引器

从已定义了所需数据源和索引的有效现有索引器开始。 索引器应该是可运行的。 使用 API 客户端构造[get 请求](https://docs.microsoft.com/rest/api/searchservice/get-indexer)，以获取要向其添加增量索引的索引器的当前配置。

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>步骤2：添加缓存属性

编辑 GET 请求的响应，将 `cache` 属性添加到索引器。 缓存对象只需要一个属性，这是 Azure 存储帐户的连接字符串。

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>步骤3：重置索引器

> [!NOTE]
> 重置索引器将导致再次处理数据源中的所有文档，以便缓存内容。 所有认知根据将在所有文档上重新运行。
>

设置现有索引器的增量索引时需要重置索引器，以确保所有文档都处于一致状态。 使用[REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)重置索引器。

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>步骤4：保存更新的定义

使用 PUT 请求更新索引器定义，请求的正文应包含更新的索引器定义。

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

如果你现在发出索引器上的另一个 GET 请求，该服务的响应将包括缓存对象中的 `cacheId` 属性。 `cacheId` 是将包含此索引器处理的每个文档的所有缓存结果和中间状态的容器的名称。

## <a name="enable-incremental-indexing-on-new-indexers"></a>对新索引器启用增量索引

若要为新索引器设置增量索引，请在索引器定义负载中包含 `cache` 属性。 请确保使用的是 `2019-05-06-Preview` 版本的 API。

## <a name="overriding-incremental-indexing"></a>覆盖增量索引

配置后，增量索引会跟踪索引管道中的更改，并驱动文档以最终一致性地跨索引和投影。 在某些情况下，您将需要重写此行为，以确保索引器不会因更新索引管道而执行额外的工作。 例如，如果数据源发生了更改，更新数据源连接字符串将需要重置并重新索引所有文档。 但是，如果仅使用新密钥更新连接字符串，则不希望更改会导致对现有文档的任何更新。 相反，你可能希望索引器使缓存和丰富文档（即使没有对索引管道进行更改）失效。 例如，如果要使用新模型重新部署自定义技能，并希望在所有文档上重新运行技能，则可能需要使索引器无效。

### <a name="override-reset-requirement"></a>重写重置要求

更改索引管道时，导致缓存失效的任何更改都需要重置索引器。 如果要更改索引器管道，并且不希望更改跟踪使缓存失效，则需要将 `ignoreResetRequirement` querystring 参数设置为索引器或数据源上的操作 `true`。

### <a name="override-change-detection"></a>覆盖更改检测

当对技能组合进行更新时，将导致文档被标记为不一致（例如，在重新部署技能时更新自定义技能 URL），将 `disableCacheReprocessingChangeDetection` 查询字符串参数设置为技能组合更新上 `true`。

### <a name="force-change-detection"></a>强制更改检测

实例化：如果你希望索引管道识别对外部实体的更改（例如部署自定义技能的新版本），则需要通过编辑技能定义来更新技能组合并 "触摸" 特定技能，特别是要强制的 URL更改检测并使该技能的缓存失效。

## <a name="next-steps"></a>后续步骤

本文介绍为包含技能集的索引器提供增量索引。 若要进一步了解你的知识，请查看有关在 Azure 认知搜索中适用于所有索引方案的一般重新编制索引的文章。

+ [如何重建 Azure 认知搜索索引](search-howto-reindex.md)。 
+ [如何索引 Azure 认知搜索中的大型数据集](search-howto-large-index.md)。 
