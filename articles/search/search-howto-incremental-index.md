---
title: 配置缓存和增量扩充（预览版）
titleSuffix: Azure Cognitive Search
description: 在认知技能集中启用缓存并保留扩充内容的状态以进行受控处理。 此功能目前处于公开预览状态。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971611"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中为增量扩充配置缓存

> [!IMPORTANT] 
> 增量扩充目前以公共预览版提供。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
> [REST API 预览版本](search-api-preview.md) 提供此功能。 目前不支持门户或 .NET SDK。

本文介绍如何将缓存添加到扩充管道，以便能够以增量方式修改步骤，而无需每次更改后都要重新生成。 默认情况下，技能集是无状态的，更改其任何构成部分都要从头到尾地重新运行索引器。 使用增量扩充时，索引器可以根据技能集或索引器定义中检测到的更改，确定需要刷新文档树的哪些组成部分。 现有的已处理输出将会保留，在可能的情况下可供重复使用。 

使用提供的帐户信息将缓存的内容放置在 Azure 存储中。 在运行索引器时，将创建名为 `ms-az-search-indexercache-<alpha-numerc-string>` 的容器。 应将此容器视为搜索服务管理的内部组件，不得对它进行修改。

如果你不熟悉如何设置索引器，请从[索引器概述](search-indexer-overview.md)着手，然后继续学习[技能集](cognitive-search-working-with-skillsets.md)来了解扩充管道。 有关重要概念的更多背景信息，请参阅[增量扩充](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="enable-caching-on-an-existing-indexer"></a>对现有的索引器启用缓存

如果现有的索引器已有一个技能集，请遵循本部分中的步骤添加缓存。 在增量处理生效之前，必须先重置并从头到尾地重新运行索引器。这是一次性的操作。

> [!TIP]
> 作为概念证明，可以通读此[门户快速入门](cognitive-search-quickstart-blob.md)来创建必要的对象，然后使用 Postman 或门户进行更新。 你可能想要附加计费的认知服务资源。 多次运行索引器会耗尽每日的免费资源分配，导致无法完成所有步骤。

### <a name="step-1-get-the-indexer-definition"></a>步骤 1：获取索引器定义

从包含以下组件的现有有效索引器开始：数据源、技能集和索引。 索引器应可运行。 

使用 API 客户端构造[获取索引器请求](/rest/api/searchservice/get-indexer)，以获取索引器的当前配置。 使用预览版 API 获取索引器时，会将一个设置为 null 的 `cache` 属性添加到定义中。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

复制响应中的索引器定义。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>步骤 2：修改索引器定义中的缓存属性

`cache` 属性默认为 null。 使用 API 客户端设置缓存配置（门户不支持这种特殊的更新）。 

修改缓存对象，以包含以下必需属性和可选属性： 

+ `storageConnectionString` 是必需的，必须设置为 Azure 存储连接字符串。 
+ `enableReprocessing` 布尔属性是可选的（默认为 `true`），指示已启用增量扩充。 如果需要，可将其设置为 `false` 以暂停增量处理，同时让其他资源密集型操作（例如，为新文档编制索引）继续进行，完成这些操作后再将此属性改回 `true`。

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>步骤 3：重置索引器

为现有索引器设置增量扩充时需要重置索引器，以确保所有文档处于一致状态。 可以使用门户或 API 客户端以及[重置索引器 REST API](/rest/api/searchservice/reset-indexer) 完成此任务。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>步骤 4：保存更新的定义

使用 PUT 请求[更新索引器](/rest/api/searchservice/preview-api/update-indexer)；请求正文应包含具有缓存属性的已更新索引器定义。 如果收到 400 错误，请检查索引器定义，确保满足所有要求（数据源、技能集和索引）。

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

如果现在针对索引器发出另一个 GET 请求，服务的响应将包含缓存对象中的 `ID` 属性。 将字母数字字符串追加到容器的名称，该容器包含此索引器处理的每个文档的所有缓存结果和中间状态。 ID 用于唯一命名 Blob 存储中的缓存。

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>步骤 5：运行索引器

可以使用门户或 API 运行索引器。 在门户上的“索引器”列表中选择该索引器，然后单击“运行”。 使用门户的一个优势是可以监视索引器状态、观察作业持续时间以及处理的文档数。 门户页面每隔几分钟刷新一次。

或者，可以使用 REST [运行索引器](/rest/api/searchservice/run-indexer)：

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

运行索引器后，可以查找 Azure Blob 存储中的缓存。 容器名称采用以下格式：`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 重置和重新运行索引器会导致完全重新生成，以便能够缓存内容。 所有认知扩充将针对所有文档重新运行。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>步骤 6：修改技能集并确认增量扩充

可以使用门户或 API 修改技能集。 例如，如果你正在使用文本翻译，从 `en` 到 `es` 或其他语言的简单内联更改便足以实现增量扩充的概念证明测试。

再次运行索引器。 只更新了扩充文档树的那些组成部分。 如果使用[门户快速入门](cognitive-search-quickstart-blob.md)作为概念证明，将文本翻译技能修改为“es”时你会发现，只更新了 8 个文档，而不是 14 个原始文档。 将在缓存中重复使用不受翻译过程影响的图像文件。

## <a name="enable-caching-on-new-indexers"></a>对新索引器启用缓存

若要为新索引器设置增量扩充，只需在调用[创建索引器 (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) 时，将 `cache` 属性包含在索引器定义有效负载中。 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>检查缓存的输出

缓存由索引器创建、使用和管理，其内容不是以用户可读的格式表示。 若要确定是否使用了缓存，最好的方法是运行索引器，并比较执行之前和之后的指标以及文档计数。 

例如，假设某个技能集首先对扫描的文档执行图像分析和光学字符识别 (OCR)，然后对生成的文本执行下游分析。 如果修改下游文本技能，则索引器可以从缓存中检索所有以前处理的图像和 OCR 内容，仅更新并处理你在编辑内容中指示的文本相关更改。 预期会看到更少的文档计数（例如 8/8，而不是原始运行中的 14/14）、执行时间更短，且帐单费用更低。

## <a name="working-with-the-cache"></a>使用缓存

缓存正常运行后，索引器在每次调用[运行索引器](/rest/api/searchservice/run-indexer)时都会检查缓存，以确定可以使用现有输出的哪些组成部分。 

下表汇总了各个 API 与缓存之间的关系：

| API           | 缓存影响     |
|---------------|------------------|
| [创建索引器 (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer) | 创建并运行首次使用的索引器，包括创建缓存（如果索引器定义指定了缓存）。 |
| [运行索引器](/rest/api/searchservice/run-indexer) | 按需执行扩充管道。 此 API 从缓存（如果存在）中读取内容；如果已将缓存添加到更新的索引器定义，则此 API 会创建缓存。 运行已启用缓存的索引器时，如果可以使用缓存的输出，则索引器将省略步骤。 可以使用此 API 的正式版或预览版。|
| [重置索引器](/rest/api/searchservice/reset-indexer)| 清除任何增量索引信息的索引器。 下一次运行索引器（按需或按计划）时会从头开始完全重新处理，包括重新运行所有技能并重新生成缓存。 它在功能上等同于删除再重新创建索引器。 可以使用此 API 的正式版或预览版。|
| [重置技能](/rest/api/searchservice/preview-api/reset-skills) | 指定在下一次运行索引器时要重新运行哪些技能，即使未修改任何技能。 缓存会相应地更新。 将使用缓存中的可重用数据以及每个已更新技能的新内容来刷新知识存储或搜索索引等输出。 |

有关控制缓存发生的情况的详细信息，请参阅[缓存管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)。

## <a name="next-steps"></a>后续步骤

增量扩充适用于包含技能集的索引器。 接下来，请访问技能集文档以了解概念和构成部分。 

此外，在启用缓存后，需要了解缓存涉及的参数和 API，包括如何重写或强制特定的行为。 有关详细信息，请参阅以下链接。

+ [技能集的概念和构成部分](cognitive-search-working-with-skillsets.md)
+ [如何创建技能集](cognitive-search-defining-skillset.md)
+ [增量扩充和缓存简介](cognitive-search-incremental-indexing-conceptual.md)