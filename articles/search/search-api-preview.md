---
title: 预览功能列表
titleSuffix: Azure Cognitive Search
description: 发布了预览功能，以便客户可以提供有关其设计和实用工具的反馈。 本文完整列出了当前预览版中的所有功能。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 983322800198246bcfaecec92fe0b2b74e587756
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952091"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 认知搜索中的预览版功能

本文完整列出了公共预览版中的所有功能。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此列表中未包括已转变为正式版的预览版功能。 如果某个功能未在下面列出，你可以认为该功能已正式发布。 有关正式版的公告，请查看[服务更新](https://azure.microsoft.com/updates/?product=search)或[新增功能](whats-new.md)。

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [**Azure 机器学习 (AML) 技能**](cognitive-search-aml-skill.md) | AI 扩充| 用于将推断终结点与 Azure 机器学习集成的一种新技术类型。 请先查看[此教程](cognitive-search-tutorial-aml-custom-skill.md)。 | 使用 [Search REST API 2020-06-30-preview](/rest/api/searchservice/) 或 2019-05-06-preview。 如果在同一订阅中部署了认知搜索和 Azure ML 服务，则门户中也会提供此功能。 |
| [featuresMode 参数](/rest/api/searchservice/search-documents#featuresmode) | 相关性（评分） | 展开相关性分数以显示详细信息：单字段相似度得分、单字段术语频率，以及单字段匹配的唯一标记数。 你可以在[自定义评分解决方案](https://github.com/Azure-Samples/search-ranking-tutorial)中使用这些数据点。 | 使用 api-version 为 2020-06-30-Preview 或 2019-05-06-Preview 的[搜索文档 (REST)](/rest/api/searchservice/search-documents) 添加此查询参数。 |
| [**调试会话**](cognitive-search-debug-session.md) | 门户、AI 扩充 (技能组合)  | 用于调查和解决技能组合问题的会话中技能组合编辑器。 在调试会话期间应用的修复可以保存到服务中的技能组合。 | 仅门户，使用 "概述" 页上的中页链接打开调试会话。 |
| [本机 blob 软删除](search-howto-index-changed-deleted-blobs.md) | 索引器、Azure blob| Azure 认知搜索中的 Azure Blob 存储索引器会识别处于软删除状态的 blob，并在编制索引过程中删除相应的搜索文档。 | 使用 api-version=2020-06-30-Preview 或 api-version=2019-05-06-Preview 的[创建索引器 (REST)](/rest/api/searchservice/create-indexer) 添加此配置设置。 |
| [自定义实体查找技能](cognitive-search-skill-custom-entity-lookup.md ) | AI 扩充（技能组） | 可在用户自定义的单词和短语列表中查找文本的一项认知技能。 它使用此列表为包含任何匹配实体的所有文档加上标签。 该技能还支持一定程度的模糊匹配，应用此匹配方法可以查找类似但不完全相同的匹配项。 | 请使用门户中的技能组编辑器或使用 api-version=2020-06-30-Preview 或 api-version=2019-05-06-Preview 的[创建技能组 (REST)](/rest/api/searchservice/create-skillset) 来引用此预览版技能。 |
| [PII 检测技能](cognitive-search-skill-pii-detection.md) | AI 扩充（技能组） | 在编制索引期间使用的一项认知技术，该技术从输入文本中提取个人信息，并使您能够以各种方式从该文本中屏蔽该文本。 | 请使用门户中的技能组编辑器或使用 api-version=2020-06-30-Preview 或 api-version=2019-05-06-Preview 的[创建技能组 (REST)](/rest/api/searchservice/create-skillset) 来引用此预览版技能。 |
| [增量扩充](cognitive-search-incremental-indexing-conceptual.md) | 索引器配置| 将缓存添加到扩充管道。如果有针对性的修改（例如更新技能集或另一对象）不会更改内容，则此功能可让你重复使用现有的输出。 缓存仅适用于技能集生成的扩充文档。| 使用 api-version=2020-06-30-Preview 或 api-version=2019-05-06-Preview 的[创建索引器 (REST)](/rest/api/searchservice/create-indexer) 添加此配置设置。 |
| [Cosmos DB 索引器：MongoDB API、Gremlin API、Cassandra API](search-howto-index-cosmosdb.md) | 索引器数据源 | 对于 Cosmos DB，SQL API 已正式发布，但 MongoDB、Gremlin 和 Cassandra API 为预览版。 | 请[先注册](https://aka.ms/azure-cognitive-search/indexer-preview)，以便我们可以在后端为你的订阅启用支持（仅适用于 Gremlin 和 Cassandra）。 MongoDB 数据源可以在门户中配置。 否则，使用 api-version=2020-06-30-Preview 或 api-version=2019-05-06-Preview 的[创建数据源 (REST)](/rest/api/searchservice/create-data-source) 为所有三个 API 的数据源配置提供支持。 |
|  [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md) | 索引器数据源 | 为 Data Lake Storage Gen2 中的内容和元数据编制索引。| 你需要[注册](https://aka.ms/azure-cognitive-search/indexer-preview)，然后我们才能在后端为你的订阅启用支持。 使用 api-version=2020-06-30-Preview 或 api-version=2019-05-06-Preview 的[创建数据源 (REST)](/rest/api/searchservice/create-data-source) 访问此数据源。 |
| [moreLikeThis](search-more-like-this.md) | 查询 | 查找与特定文档相关的文档。 早期预览版中已有此功能。 | 在 api-version 为 2020-06-30-Preview、2019-05-06-Preview、2016-09-01-Preview 或 2017-11-11-Preview 的[搜索文档 (REST)](/rest/api/searchservice/search-documents) 调用中添加此查询参数。 |

## <a name="how-to-call-a-preview-rest-api"></a>如何调用预览版 REST API

Azure 认知搜索始终先通过 REST API 预发布实验功能，再通过 .NET SDK 的预发布版本进行发布。

预览功能可用于目的为针对功能设计和实现收集反馈的测试和试验。 为此，预览功能可能会随着时间的推移以破坏后向兼容性的方式进行更改。 这与 GA 版本中的功能形成鲜明对比，除了小型的后向兼容性修补程序和增强功能外，这些功能很稳定，不太可能发生变化。 此外，预览功能并不总是纳入 GA 版本。

虽然某些预览版功能在门户和 .NET SDK 中可能可用，但 REST API 始终具有预览版功能。

+ 对于搜索操作，当前预览版版本为 [ **`2020-06-30-Preview`** ](/rest/api/searchservice/index-preview)。

+ 对于管理操作，当前预览版版本为 [ **`2019-10-01-Preview`** ](/rest/api/searchmanagement/index-2019-10-01-preview)。

早期预览版仍然可用，但随着时间推移会变得过时。 如果代码调用 `api-version=2019-05-06-Preview`、`api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，则这些调用仍然有效。 但是，只有最新预览版会获得改进。

以下示例语法说明了对预览 API 版本的调用。

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure 认知搜索服务在多个版本内可用。 有关详细信息，请参阅 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看搜索 REST 预览 API 参考文档。 如果遇到问题，请向我们提供有关 [Stack Overflow](https://stackoverflow.com/) 的帮助或 [联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考（预览版）](/rest/api/searchservice/index-preview)