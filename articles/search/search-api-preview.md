---
title: REST API 版本 2019-05-06-预览版
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索 service REST API 版本 2019-05-06-Preview 包括试验性功能，如知识 store 和客户管理的加密密钥。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a4daaac6abababcedb5d6dd7eb2122ef29846ef4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792539"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure 认知搜索服务 REST api-版本 2019-05-06-预览版

本文介绍搜索服务 REST API 的 `api-version=2019-05-06-Preview` 版本，并提供尚未正式发布的实验性功能。

> [!NOTE]
> 预览功能可用于目的为收集反馈的测试和试验，可能随时更改。 强烈建议不要在生产应用程序中使用预览版 API。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06 中的新增-预览

[**知识存储**](knowledge-store-concept-intro.md)是基于 AI 的扩充管道的新目标。 除索引外，你现在还可以保存在 Azure 存储中编制索引期间创建的已填充数据结构。 您可以通过技能组合中的元素来控制数据的物理结构，其中包括数据的整形方式、数据是存储在表存储中还是存储在 Blob 存储中，以及是否有多个视图。

用于服务端加密的[**客户管理的加密密钥**](search-security-manage-encryption-keys.md)也是一项全新的预览功能。 除了由 Microsoft 管理的内置静态加密外，还可以应用其他加密层，其中你是密钥的唯一所有者。

## <a name="other-preview-features"></a>其他预览功能

公共预览版中仍包含早期预览版中发布的功能。 如果你正在调用使用较早预览版 API 版本的 API，可以继续使用该版本或切换到 `2019-05-06-Preview`，而不会对预期行为作出任何更改。

+ [moreLikeThis 查询参数](search-more-like-this.md)查找与特定文档相关的文档。 早期预览版中已有此功能。 
* [CSV blob 索引](search-howto-index-csv-blobs.md)为每行创建一个文档，而不是每个文本 blob 创建一个文档。
* [Cosmos DB 索引器的 MONGODB API 支持](search-howto-index-cosmosdb.md)处于预览阶段。


## <a name="how-to-call-a-preview-api"></a>如何调用预览版 API

早期预览版仍然可用，但随着时间推移会变得过时。 如果代码调用 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些调用仍然有效。 但是，只有最新预览版会获得改进。 

以下示例语法说明了对预览 API 版本的调用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 认知搜索服务在多个版本中可用。 有关详细信息，请参阅 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看搜索 REST API 参考文档。 如果遇到问题，请向我们提供有关[StackOverflow](https://stackoverflow.com/)的帮助或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 引用](https://docs.microsoft.com/rest/api/searchservice/)