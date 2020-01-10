---
title: 配置缓存和增量扩充（预览版）
titleSuffix: Azure Cognitive Search
description: 为认知技能组合中的受控处理启用缓存并保留丰富内容的状态。 此功能目前处于公开预览状态。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1eaf4e7b2d769217ceace3ece339adff727c7835
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832052"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>如何在 Azure 中为增量扩充配置缓存认知搜索

> [!IMPORTANT] 
> 增量扩充目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。

本文介绍如何将缓存添加到扩充管道，以便可以增量修改步骤，而无需每次都重新生成。 默认情况下，技能组合是无状态的，并且更改其组合的任何部分都需要完全重新运行索引器。 通过增量扩充，索引器可以根据在技能组合或索引器定义中检测到的更改来确定需要刷新文档树的哪些部分。 保留现有处理的输出，并尽可能重复使用。 

使用你提供的帐户信息将缓存内容放置在 Azure 存储中。 在运行索引器时，将创建名为 `ms-az-search-indexercache-<alpha-numerc-string>`的容器。 它应被视为由您的搜索服务管理的内部组件，而不能修改。

如果你不熟悉如何设置索引器，请从[索引器概述](search-indexer-overview.md)开始，然后继续转到[技能集](cognitive-search-working-with-skillsets.md)了解扩充管道。 有关关键概念的更多背景信息，请参阅[增量扩充](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="enable-caching-on-an-existing-indexer"></a>对现有索引器启用缓存

如果现有索引器已具有技能组合，请按照本部分中的步骤添加缓存。 作为一次性操作，必须重置并重新运行索引器，然后增量处理才能生效。

> [!TIP]
> 作为概念证明，你可以通过此[门户快速入门](cognitive-search-quickstart-blob.md)来创建必要的对象，然后使用 Postman 或门户进行更新。 你可能想要附加可计费认知服务资源。 多次运行索引器将会耗尽免费的每日分配，然后才能完成所有步骤。

### <a name="step-1-get-the-indexer-definition"></a>步骤1：获取索引器定义

从具有以下组件的有效现有索引器开始：数据源、技能组合、索引。 索引器应该是可运行的。 使用 API 客户端构造获取索引器[请求](https://docs.microsoft.com/rest/api/searchservice/get-indexer)，以获取索引器的当前配置。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

从响应中复制索引器定义。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>步骤2：修改索引器定义中的缓存属性

默认情况下，`cache` 属性为 null。 使用 API 客户端添加缓存配置（门户不支持此 particulate 更新）。 

修改缓存对象，使其包含以下必需属性和可选属性： 

+ `storageConnectionString` 是必需的，必须将其设置为 Azure 存储连接字符串。 
+ `enableReprocessing` 布尔属性是可选的（默认情况下`true`），它指示已启用增量扩充。 你可以将其设置为 `false` 以暂停增量处理，而其他占用大量资源的操作（例如为新文档编制索引）正在进行，然后将其重新翻转到 `true`。

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

### <a name="step-3-reset-the-indexer"></a>步骤3：重置索引器

为现有索引器设置增量扩充时需要重置索引器，以确保所有文档都处于一致状态。 你可以使用门户或 API 客户端以及此任务的[重置索引器 REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) 。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>步骤4：保存更新的定义

使用 PUT 请求更新索引器定义，请求的正文应包含具有缓存属性的已更新索引器定义。 如果收到400，请检查索引器定义，以确保满足所有要求（数据源、技能组合、索引）。

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
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

如果你现在发出索引器上的另一个 GET 请求，该服务的响应将包括缓存对象中的 `ID` 属性。 将字母数字字符串追加到容器的名称，该容器包含此索引器处理的每个文档的所有缓存结果和中间状态。 该 ID 将用于在 Blob 存储中对缓存进行唯一命名。

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>步骤5：运行索引器

若要运行索引器，还可以使用门户。 从 "索引器" 列表中，选择该索引器，然后单击 "**运行**"。 使用门户的一个优点是您可以监视索引器状态、记录作业的持续时间以及处理的文档数。 每隔几分钟刷新一次门户页面。

或者，可以使用 REST 运行索引器：

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

在索引器运行后，可以在 Azure blob 存储中查找缓存。 容器名称采用以下格式： `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 重置并重新运行索引器将导致完全重新生成，以便缓存内容。 所有认知根据将在所有文档上重新运行。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>步骤6：修改技能组合并确认增量扩充

若要修改技能组合，可以使用门户编辑 JSON 定义。 例如，如果使用文本转换，则从 `en` 到 `es` 或其他语言的简单内联更改足以满足增量扩充的概念证明测试。

再次运行索引器。 仅更新已丰富文档树的这些部分。 如果你使用[门户快速入门](cognitive-search-quickstart-blob.md)作为概念证明，将文本翻译技能修改为 "es"，你会注意到仅更新了8个文档而不是原始14个文档。 转换过程不影响的图像文件将从缓存中重复使用。

## <a name="enable-caching-on-new-indexers"></a>对新索引器启用缓存

若要为新索引器设置增量扩充，只需在调用[Create 索引](https://docs.microsoft.com/rest/api/searchservice/create-indexer)器时，将在索引器定义负载中包含 `cache` 属性。 使用此属性创建索引器时，请务必指定 API 的 `2019-05-06-Preview` 版本。 


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

缓存由索引器创建、使用和管理，并且其内容不以用户可读的格式表示。 若要确定是否使用缓存，最好的方法是运行索引器，并比较前后前后的执行时间和文档计数。 

例如，假定一个技能组合，它以图像分析和扫描文档的光学字符识别（OCR）开头，后跟结果文本的下游分析。 如果修改下游文本技能，索引器可以从缓存中检索所有以前处理过的图像和 OCR 内容，更新和处理所做的更改所指示的与文本相关的更改。 您可以在文档计数中看到较少的文档（例如，8/8，而不是原始运行中的14/14），更短的执行时间，还可以减少帐单的费用。

## <a name="working-with-the-cache"></a>使用缓存

缓存运行后，索引器将在调用[运行索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer)时检查缓存，以查看可使用现有输出的哪些部分。 

下表总结了各种 Api 与缓存的关系：

| API           | 缓存影响     |
|---------------|------------------|
| [创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer) | 创建并运行首次使用的索引器，包括创建缓存（如果索引器定义指定了缓存）。 |
| [运行索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | 按需执行扩充管道。 此 API 从缓存中读取（如果存在），如果已将缓存添加到更新索引器定义，则会创建缓存。 当你运行已启用缓存的索引器时，如果可以使用缓存的输出，则索引器将省略步骤。 |
| [重置索引器](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| 清除任何增量索引信息的索引器。 下一次索引器运行（按需或按计划）完全重新处理，包括重新运行所有技能和重新生成缓存。 它在功能上等效于删除索引器并重新创建它。 |
| [重置技能](preview-api-resetskills.md) | 指定在下次运行索引器时要重新运行的技能，即使您没有修改任何技能也是如此。 缓存会相应地进行更新。 将使用缓存中的可重用数据以及每个更新的技能的新内容来刷新输出，如知识存储或搜索索引。 |

有关控制缓存发生的情况的详细信息，请参阅[缓存管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)。

## <a name="next-steps"></a>后续步骤

增量扩充适用于包含技能集的索引器。 下一步，请访问技能组合文档以了解概念和组合。 

此外，在启用缓存后，你需要了解有关缓存的参数和 Api 的信息，包括如何覆盖或强制特定行为。 有关详细信息，请参阅以下链接。

+ [技能组合概念和组合](cognitive-search-working-with-skillsets.md)
+ [如何创建技能组合](cognitive-search-defining-skillset.md)
+ [增量扩充和缓存简介](cognitive-search-incremental-indexing-conceptual.md)
