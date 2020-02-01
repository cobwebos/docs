---
title: REST API 版本 2019-05-06-预览版
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索 service REST API 版本 2019-05-06-Preview 包括试验性功能，如用于增量扩充的知识存储和索引器缓存。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 71c6879f467823ab01f4c60ac4d9f26cffcd4eea
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896115"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure 认知搜索服务 REST api-版本 2019-05-06-预览版

本文介绍搜索服务 REST API 的 `api-version=2019-05-06-Preview` 版本，并提供尚未正式发布的实验性功能。

> [!NOTE]
> 预览功能可用于测试和试验，目的是收集反馈，并且可能会发生变化。 强烈建议不要在生产应用程序中使用预览 Api。

## <a name="features-in-2019-05-06-preview"></a>2019-05-06 中的功能-预览

此部分列出了具有预览状态的功能。 大多数情况下已添加到当前的 2019-05-06-Preview API 中，但有些类似 `moreLikeThis` 来自于早期预览版本，这些版本将被汇总到最新的预览 API。 

预览功能公开上市后，将从此列表中删除。 可以查看[服务更新](https://azure.microsoft.com/updates/?product=search)或有关公开上市的公告的[新增功能](whats-new.md)。

+ [增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)将缓存添加到扩充管道，这样，如果目标修改（如对技能组合或其他对象的更新）不更改内容，则可以重复使用现有输出。 缓存仅适用于技能组合生成的丰富文档。

+ [Cosmos DB 索引器](search-howto-index-cosmosdb.md)支持 MongoDB api （预览）、Gremlin api （预览）和 Cassandra API （预览）。

+ [Azure Data Lake Storage Gen2 索引器（预览版）](search-howto-index-azure-data-lake-storage.md)可通过 Data Lake Storage Gen2 为内容和元数据编制索引。

+ [知识存储](knowledge-store-concept-intro.md)是基于 AI 的扩充管道的新目标。 物理数据结构存在于 Azure Blob 存储和 Azure 表存储中，并在运行具有附加认知技能组合的索引器时创建并填充。 在技能组合定义内指定知识存储本身的定义。 在知识存储定义中，通过确定数据的形状、数据是否存储在表存储或 Blob 存储中，以及是否有多个视图的*投影*元素来控制数据的物理结构。

+ [moreLikeThis 查询参数](search-more-like-this.md)可查找与特定文档相关的文档。 此功能已在以前的预览版中提供。 

## <a name="earlier-preview-features"></a>更早的预览功能

以前的预览中公布的功能（如果尚未转换为公开上市）仍处于公开预览状态。 如果要使用较早的预览版 api 版本调用 API，可以继续使用该版本，或切换到 `2019-05-06-Preview`，而不会更改预期的行为。

## <a name="how-to-call-a-preview-api"></a>如何调用预览 API

旧版本的预览仍在运行，但随着时间的推移会过时。 如果你的代码调用 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，则这些调用仍有效。 不过，只有最新的预览版本才会得到改进。 

下面的示例语法说明了对预览 API 版本的调用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 认知搜索服务在多个版本中可用。 有关详细信息，请参阅[API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看搜索 REST API 参考文档。 如果遇到问题，请向我们提供有关[StackOverflow](https://stackoverflow.com/)的帮助或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 引用](https://docs.microsoft.com/rest/api/searchservice/)