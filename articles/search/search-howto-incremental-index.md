---
title: 配置缓存和增量扩充（预览）
titleSuffix: Azure Cognitive Search
description: 启用缓存并保留丰富内容的状态，以便在认知技能集中进行受控处理。 此功能目前处于公开预览状态。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989546"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中配置缓存以增量扩充

> [!IMPORTANT] 
> 增量扩充当前处于公共预览版中。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持门户或 .NET SDK。

本文介绍如何向扩充管道添加缓存，以便无需每次都重新生成即可增量修改步骤。 默认情况下，技能集是无状态的，更改其组合的任何部分都需要完全重新运行索引器。 使用增量扩充，索引器可以根据技能集或索引器定义中检测到的更改确定需要刷新文档树的哪些部分。 尽可能保留和重用现有的已处理输出。 

缓存的内容使用您提供的帐户信息放置在 Azure 存储中。 运行索引器时`ms-az-search-indexercache-<alpha-numerc-string>`将创建名为 的容器。 它应被视为由搜索服务管理的内部组件，不得修改。

如果您不熟悉设置索引器，请从[索引器概述](search-indexer-overview.md)开始，然后继续[技能集](cognitive-search-working-with-skillsets.md)以了解浓缩管道。 有关关键概念的更多背景，请参阅[增量丰富](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="enable-caching-on-an-existing-indexer"></a>在现有索引器上启用缓存

如果您有一个已有技能集的现有索引器，请按照本节中的步骤添加缓存。 作为一次性操作，您必须重新重置和重新运行索引器，然后才能对增量处理生效。

> [!TIP]
> 作为概念证明，您可以运行此[门户快速入门](cognitive-search-quickstart-blob.md)以创建必要的对象，然后使用 Postman 或门户进行更新。 您可能希望附加计费的认知服务资源。 多次运行索引器将耗尽每天的免费分配，然后才能完成所有步骤。

### <a name="step-1-get-the-indexer-definition"></a>第 1 步：获取索引器定义

从具有这些组件的有效现有索引器开始：数据源、技能集和索引。 索引器应可运行。 

使用 API 客户端，构造[GET 索引器请求](https://docs.microsoft.com/rest/api/searchservice/get-indexer)以获取索引器的当前配置。 当您将预览 API 版本用于 GET 索引器时，`cache`将一个设置为 null 的属性添加到定义中。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

从响应复制索引器定义。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>步骤 2：修改索引器定义中的缓存属性

默认情况下，`cache`该属性为空。 使用 API 客户端设置缓存配置（门户不支持此微粒更新）。 

修改缓存对象以包括以下必需和可选属性： 

+ `storageConnectionString`是必需的，必须将其设置为 Azure 存储连接字符串。 
+ 布尔`enableReprocessing`属性是可选的（`true`默认情况下），它指示已启用增量扩充。 在需要时，您可以将其设置为`false`挂起增量处理，同时其他资源密集型操作（如索引新文档）正在进行，然后将其翻转回`true`以后。

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

### <a name="step-3-reset-the-indexer"></a>第 3 步：重置索引器

在为现有索引器设置增量扩充时，需要重置索引器，以确保所有文档处于一致状态。 可以为此任务使用门户或 API 客户端和[重置索引器 REST API。](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>第 4 步：保存更新的定义

使用 PUT 请求[更新索引器](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer)，请求的正文应包含具有缓存属性的更新索引器定义。 如果得到 400，请检查索引器定义以确保满足所有要求（数据源、技能集、索引）。

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

如果现在在索引器上发出另一个 GET 请求，则服务的响应将在缓存对象`ID`中包含一个属性。 字母数字字符串追加到容器的名称中，其中包含此索引器处理的每个文档的所有缓存结果和中间状态。 ID 将用于在 Blob 存储中唯一命名缓存。

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>第 5 步：运行索引器

要运行索引器，可以使用门户或 API。 在门户中，从索引器列表中选择索引器并单击"**运行**"。 使用门户的一个优点是您可以监视索引器状态、记录作业的持续时间以及处理的文档数。 门户页面每隔几分钟刷新一次。

或者，您可以使用 REST[来运行索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer)：

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

索引器运行后，可以在 Azure Blob 存储中找到缓存。 容器名称采用以下格式：`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 重置和重新运行索引器会导致完全重建，以便可以缓存内容。 所有认知丰富将重新运行在所有文档上。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>第 6 步：修改技能集并确认增量扩充

要修改技能集，可以使用门户或 API。 例如，如果您使用的是文本翻译，则从`en`到`es`另一种语言的简单内联更改足以用于增量扩充的概念验证测试。

再次运行索引器。 仅更新富集文档树的这些部分。 如果您使用[门户快速入门](cognitive-search-quickstart-blob.md)作为概念验证，将文本翻译技能修改为"es"，您会注意到只有 8 个文档更新，而不是原始 14 个文档。 不受转换过程影响的图像文件将从缓存中重用。

## <a name="enable-caching-on-new-indexers"></a>在新索引器上启用缓存

要为新索引器设置增量扩充，在调用[Create Indexer （2019-05-06-预览版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer)时，您所要做的就是将`cache`属性包括在索引器定义负载中。 


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

缓存由索引器创建、使用和管理，其内容不以人类可读的格式表示。 确定是否使用缓存的最佳方法是运行索引器并比较执行时间和文档计数的前后指标。 

例如，假设一个技能集，该技能集以扫描文档的图像分析和光学字符识别 （OCR） 开头，然后是对结果文本的下游分析。 如果修改下游文本技能，索引器可以从缓存中检索以前处理的所有图像和 OCR 内容，仅更新和处理编辑中指示的文本相关更改。 在文档计数中，您可以看到的文档数（例如，在原始运行中为 8/8 而不是 14/14），执行时间较短，账单上的费用也更少。

## <a name="working-with-the-cache"></a>使用缓存

缓存运行后，每当调用[Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer)时，索引器都会检查缓存，以查看可以使用现有输出的哪些部分。 

下表总结了各种 API 与缓存的关系：

| API           | 缓存影响     |
|---------------|------------------|
| [创建索引器 （2019-05-06-预览版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | 首次使用时创建并运行索引器，包括在索引器定义指定时创建缓存。 |
| [运行索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | 按需执行浓缩管道。 此 API 如果存在，则从缓存中读取，如果向更新的索引器定义添加缓存，则创建缓存。 当您运行已启用缓存的索引器时，如果可以使用缓存的输出，则索引器将省略步骤。 您可以使用此 API 的一般可用或预览 API 版本。|
| [重置索引器](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| 清除索引器的任何增量索引信息。 下一个索引器运行（按需或计划）是从头开始完全重新处理，包括重新运行所有技能并重建缓存。 它在功能上等效于删除索引器并重新创建索引器。 您可以使用此 API 的一般可用或预览 API 版本。|
| [重置技能 （2019-05-06-预览）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | 指定在下一个索引器上运行时重新运行哪些技能，即使您尚未修改任何技能也是如此。 缓存将相应地更新。 输出（如知识存储或搜索索引）使用缓存中的可重用数据以及根据更新的技能的新内容刷新。 |

有关控制缓存发生的情况的详细信息，请参阅[缓存管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)。

## <a name="next-steps"></a>后续步骤

增量扩充适用于包含技能集的索引器。 作为下一步，请访问技能集文档以了解概念和组成。 

此外，启用缓存后，您需要了解因素分解缓存的参数和 API，包括如何重写或强制特定行为。 有关详细信息，请参阅以下链接。

+ [技能集概念和组合](cognitive-search-working-with-skillsets.md)
+ [如何创建技能集](cognitive-search-defining-skillset.md)
+ [增量浓缩和缓存简介](cognitive-search-incremental-indexing-conceptual.md)
