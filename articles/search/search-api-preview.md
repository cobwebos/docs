---
title: REST API 中的预览功能
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索服务 REST API 版本 2019-05-06-预览包括用于增量扩充的实验性功能，如知识存储和索引器缓存。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162270"
---
# <a name="preview-features-in-azure-cognitive-search"></a>预览 Azure 认知搜索中的功能

本文列出了当前预览中的功能。 从此列表中删除从预览过渡到通用可用性的功能。 有关常规可用性的公告，您可以查看[服务更新](https://azure.microsoft.com/updates/?product=search)或[新增功能](whats-new.md)。

虽然门户和 .NET SDK 中可能提供某些预览功能，但 REST API 始终具有预览功能。

+ 对于搜索操作，[**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)是当前预览版本。
+ 对于管理操作，[**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)是当前预览版本。

> [!IMPORTANT]
> 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="ai-enrichment-features"></a>AI 浓缩功能

通过[预览搜索 API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)探索 AI 扩充的最新增强功能。

|||
|-|-|
| [自定义实体查找技能（预览）](cognitive-search-skill-custom-entity-lookup.md ) | 从自定义的、用户定义的单词和短语列表中查找文本的认知技能。 使用此列表，它将使用任何匹配的实体标记所有文档。 该技能还支持一定程度的模糊匹配，可用于查找相似但不精确的匹配。 | 
| [PII 检测技能（预览）](cognitive-search-skill-pii-detection.md) | 索引期间使用的认知技能，从输入文本中提取个人身份信息，并允许您选择以各种方式从该文本中屏蔽这些信息。| 
| [增量扩充（预览）](cognitive-search-incremental-indexing-conceptual.md) | 将缓存添加到扩充管道中，如果目标修改（如对技能集或其他对象的更新）不更改内容，则可以重用现有输出。 缓存仅适用于技能集生成的丰富文档。| 
| [知识存储（预览版）](knowledge-store-concept-intro.md) | 基于 AI 的浓缩管道的新目的地。 物理数据结构存在于 Azure Blob 存储和 Azure 表存储中，并在运行具有附加认知技能集的索引器时创建和填充它。 知识存储本身的定义在技能集定义中指定。 在知识存储定义中，您可以通过确定数据形状、数据是否存储在表存储或 Blob 存储中以及是否有多个视图*的投影*元素来控制数据的物理结构。| 

## <a name="indexing-and-query-features"></a>索引和查询功能

索引器预览功能在预览搜索 API 中可用。 

|||
|-|-|
| [Cosmos DB 索引器](search-howto-index-cosmosdb.md) | 支持蒙戈DB API（预览）、格雷姆林 API（预览）和卡桑德拉 API（预览）API 类型。 | 
|  [Azure 数据湖存储第 2 代索引器（预览版）](search-howto-index-azure-data-lake-storage.md) | 索引来自数据存储库第 2 代的内容和元数据。| 
| [更多类似于此查询参数（预览）](search-more-like-this.md) | 查找与特定文档相关的文档。 早期预览版中已有此功能。 | 

## <a name="management-features"></a>管理功能

|||
|-|-|
| [专用终结点支持](service-create-private-endpoint.md) | 您可以使用安全客户端（如虚拟机）创建虚拟网络，然后创建使用专用终结点的搜索服务。 |
| IP 访问限制 | 使用[`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)管理 REST API，您可以创建一个服务，该服务对允许访问哪些 IP 地址有限制。 |

## <a name="earlier-preview-features"></a>早期预览功能

在早期预览版中宣布的功能（如果尚未转换为通用版）仍处于公共预览版中。 如果你正在调用使用较早预览版 API 版本的 API，可以继续使用该版本或切换到 `2019-05-06-Preview`，而不会对预期行为作出任何更改。

## <a name="how-to-call-a-preview-api"></a>如何调用预览版 API

早期预览版仍然可用，但随着时间推移会变得过时。 如果代码调用 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些调用仍然有效。 但是，只有最新预览版会获得改进。 

以下示例语法说明了对预览 API 版本的调用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 认知搜索服务在多个版本内可用。 有关详细信息，请参阅 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看搜索 REST API 参考文档。 如果遇到问题，请通过 [StackOverflow](https://stackoverflow.com/) 向我们寻求帮助，或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考](https://docs.microsoft.com/rest/api/searchservice/)