---
title: 预览功能列表
titleSuffix: Azure Cognitive Search
description: 发布了预览功能，使客户能够在其设计和实用工具上提供反馈。 本文是当前预览版中所有功能的完整列表。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: b952d6b6fec9a1ec0dcd8af1a9566e67d3301d77
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496700"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 认知搜索中的预览功能

本文是公共预览版中所有功能的完整列表。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

将从此列表中删除过渡到正式可用性的预览功能。 如果未在下面列出功能，你可以假定该功能已正式发布。 有关公开上市的公告，请参阅[服务更新](https://azure.microsoft.com/updates/?product=search)或新增[功能](whats-new.md)。

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**Azure 机器学习（AML）技能**](cognitive-search-aml-skill.md) | AI 扩充| 用于将推断终结点与 Azure 机器学习集成的一种新技术类型。 请先查看[此教程](cognitive-search-tutorial-aml-custom-skill.md)。 | 使用[Search REST API 2020-06-30-preview](https://docs.microsoft.com/rest/api/searchservice/)或 2019-05-06-preview。 如果在同一订阅中部署了认知搜索和 Azure ML 服务，则门户中也会提供此功能。 |
| [**featuresMode 参数**](https://docs.microsoft.com/rest/api/searchservice/search-documents#featuresmode) | 相关性（评分） | 相关分数扩展以包括详细信息：每个字段的相似性分数、每个字段的术语频率以及与每个字段匹配的唯一令牌数。 可以在[自定义计分解决方案](https://github.com/Azure-Samples/search-ranking-tutorial)中使用这些数据点。 | 使用[搜索文档（REST）](https://docs.microsoft.com/rest/api/searchservice/search-documents)添加此查询参数，其中 api 版本 = 2020-06-30 或 2019-05-06-preview。 |
| [**托管服务标识**](search-howto-managed-identities-data-sources.md) | 索引器，安全性| 将搜索服务注册到 Azure Active Directory 以使其成为可信服务，然后使用 Azure 数据源上的 RBAC 权限允许索引器进行只读访问。 | 使用门户时，或[创建数据源（REST）](https://docs.microsoft.com/rest/api/searchservice/create-data-source)时访问此功能，api 版本 = 2020-06-30 或 api 版本 = 2019-05-06-01.5.1-preview。 |
| [**调试会话**](cognitive-search-debug-session.md) | 门户、AI 扩充（技能组合） | 用于调查和解决技能组合问题的会话中技能组合编辑器。 在调试会话期间应用的修复可以保存到服务中的技能组合。 | 仅门户，使用 "概述" 页上的中页链接打开调试会话。 |
| [**本机 blob 软删除**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | 索引器，Azure blob| Azure 认知搜索中的 Azure Blob 存储索引器将识别处于软删除状态的 blob，并在索引期间删除相应的搜索文档。 | 使用[Create 索引器（REST）](https://docs.microsoft.com/rest/api/searchservice/create-indexer)添加此配置设置，并使用 api 版本 = 2020-06-30 或 api 版本 = 2019-05-06-01.5.1-preview。 |
| [**自定义实体查找技能**](cognitive-search-skill-custom-entity-lookup.md ) | AI 扩充（技能组合） | 一项认知技能，可查找自定义的、用户定义的单词和短语列表中的文本。 它使用此列表为包含任何匹配实体的所有文档加上标签。 该技能还支持一定程度的模糊匹配，应用此匹配方法可以查找类似但不完全相同的匹配项。 | 使用门户中的技能组合编辑器引用此预览版技能，或使用 api 版本 = 2020-06-30 或 api 版本 = 2019-05-06-01.5.1-Preview[创建技能组合（REST）](https://docs.microsoft.com/rest/api/searchservice/create-skillset) 。 |
| [**PII 检测技巧**](cognitive-search-skill-pii-detection.md) | AI 扩充（技能组合） | 在编制索引期间使用的一项认知技能，可从输入文本中提取个人身份信息，并提供以各种方式从文本中屏蔽该文本的选项。 | 使用门户中的技能组合编辑器引用此预览版技能，或使用 api 版本 = 2020-06-30 或 api 版本 = 2019-05-06-01.5.1-Preview[创建技能组合（REST）](https://docs.microsoft.com/rest/api/searchservice/create-skillset) 。 |
| [**增量扩充**](cognitive-search-incremental-indexing-conceptual.md) | 索引器配置| 将缓存添加到扩充管道，以便在目标修改（如对技能组合或其他对象的更新）不更改内容时重复使用现有输出。 缓存仅适用于技能组合生成的丰富文档。| 使用[Create 索引器（REST）](https://docs.microsoft.com/rest/api/searchservice/create-indexer)添加此配置设置，并使用 api 版本 = 2020-06-30 或 api 版本 = 2019-05-06-01.5.1-preview。 |
| [**Cosmos DB 索引器： MongoDB API、Gremlin API、Cassandra API**](search-howto-index-cosmosdb.md) | 索引器数据源 | 对于 Cosmos DB，SQL API 已正式发布，但 MongoDB、Gremlin 和 Cassandra Api 处于预览阶段。 | 仅适用于 Gremlin 和 Cassandra，[首先注册](https://aka.ms/azure-cognitive-search/indexer-preview)，以便能够在后端为你的订阅启用支持。 可以在门户中配置 MongoDB 数据源。 否则，所有三个 Api 的数据源配置均支持使用[Create Data source （REST）](https://docs.microsoft.com/rest/api/searchservice/create-data-source) ，其 api 版本 = 2020-06-30 或 api 版本 = 2019-05-06-01.5.1-preview。 |
|  [**Azure Data Lake Storage Gen2 索引器**](search-howto-index-azure-data-lake-storage.md) | 索引器数据源 | 索引 Data Lake Storage Gen2 的内容和元数据。| 需要[注册](https://aka.ms/azure-cognitive-search/indexer-preview)才能在后端为你的订阅启用支持。 使用 "[创建数据源（REST）](https://docs.microsoft.com/rest/api/searchservice/create-data-source) " 访问此数据源，其 api 版本 = 2020-06-30，或 api 版本 = 2019-05-06-01.5.1-preview。 |
| [**moreLikeThis**](search-more-like-this.md) | 查询 | 查找与特定文档相关的文档。 早期预览版中已有此功能。 | 在[搜索文档（REST）](https://docs.microsoft.com/rest/api/searchservice/search-documents)调用中添加此查询参数，api 版本为 2020-06-06-30，2019-05-06-preview，2016-09-01-preview，或 2017-11-11-preview。 |

## <a name="calling-preview-rest-apis"></a>调用预览 REST Api

Azure 认知搜索始终先通过 REST API 预发布实验功能，再通过 .NET SDK 的预发布版本进行发布。

预览功能可用于目的为针对功能设计和实现收集反馈的测试和试验。 为此，预览功能可能会随着时间的推移以破坏后向兼容性的方式进行更改。 这与 GA 版本中的功能形成鲜明对比，除了小型的后向兼容性修补程序和增强功能外，这些功能很稳定，不太可能发生变化。 此外，预览功能并不总是纳入 GA 版本。

虽然门户和 .NET SDK 中可能提供某些预览功能，但 REST API 始终具有预览功能。

+ 对于搜索操作， [**`2020-06-30-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-preview) 是当前预览版本。

+ 对于管理操作， [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) 是当前预览版本。

早期预览版仍然可用，但随着时间推移会变得过时。 如果你的代码调用 `api-version=2019-05-06-Preview` 或 `api-version=2016-09-01-Preview` 或，则 `api-version=2017-11-11-Preview` 这些调用仍有效。 但是，只有最新预览版会获得改进。 

以下示例语法说明了对预览 API 版本的调用。

```HTTP
GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2020-06-30-Preview
```

Azure 认知搜索服务在多个版本内可用。 有关详细信息，请参阅 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看搜索 REST 预览 API 参考文档。 如果遇到问题，请向我们提供有关[Stack Overflow](https://stackoverflow.com/)的帮助或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考（预览版）](https://docs.microsoft.com/rest/api/searchservice/index-preview)