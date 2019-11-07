---
title: 增量索引简介（预览）
titleSuffix: Azure Cognitive Search
description: 配置 AI 扩充管道，使数据的最终一致性，处理对技能、技能集、索引器或数据源的任何更新。 此功能目前处于公共预览阶段
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 94ce056185ff6a804521bf583ac4f6ffaa513fb0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715418"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Azure 认知搜索中的增量索引是什么？

> [!IMPORTANT] 
> 增量索引当前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持门户或 .NET SDK。

增量索引是 Azure 认知搜索的一项新功能，它将缓存和状态添加到认知技能组合中增强的内容，使你可以控制扩充管道中的单个步骤的处理和重新处理。 这不仅会保留你的资金投入，还会使系统更有效。 缓存结构和内容时，索引器可以确定哪些技能发生了变化，并且仅运行了已修改的技能以及任何下游相关技能。 

对于增量索引，扩充管道的当前版本执行的工作量最少，可保证索引中所有文档的一致性。 对于需要完全控制的情况，可以使用精细的控件覆盖预期的行为。 有关配置的详细信息，请参阅[设置增量索引](search-howto-incremental-index.md)。

## <a name="indexer-cache"></a>索引器缓存

增量索引将索引器缓存添加到扩充管道。 索引器将从文档破解和每个文档的每项技能的输出中缓存结果。 更新技能组合时，只会重新运行已更改或下游的技能。 更新的结果将写入缓存，并在索引和知识存储中更新文档。

从物理上讲，缓存是一个存储帐户。 搜索服务中的所有索引可能会为索引器缓存共享相同的存储帐户。 为每个索引器分配一个唯一且不可变的缓存标识符。

### <a name="cache-configuration"></a>缓存配置

需要在索引器上设置 `cache` 属性，以从增量索引启动受益。 下面的示例演示启用了缓存的索引器。 以下部分介绍了此配置的特定部分。

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

在现有索引器上首次设置此属性时，还需要重置此属性，这将导致再次处理数据源中的所有文档。 递增索引的目标是使索引中的文档与数据源和技能组合的当前版本保持一致。 重置索引的第一步是实现此一致性的第一步，因为它消除了以前版本的技能组合所需的任何文档。 此索引器需要重置为以一致的基线开头。

### <a name="cache-lifecycle"></a>缓存生命周期

缓存的生命周期由索引器进行管理。 如果索引器的 `cache` 属性设置为 null 或更改了连接字符串，则将删除现有缓存。 缓存生命周期还与索引器生命周期相关联。 如果删除索引器，则也将删除关联的缓存。

### <a name="indexer-cache-mode"></a>索引器缓存模式

索引器缓存可在仅将数据写入缓存的模式下运行，或将数据写入缓存，并用于重新浓缩文档。  你可以通过将缓存中的 `enableReprocessing` 属性设置为 `false`来暂时暂停增量扩充，并通过将其设置为 `true`恢复增量扩充并实现最终一致性。 当你想要对新文档的索引进行优先级设置以确保语料库文档的一致性时，此控制特别有用。

## <a name="change-detection-override"></a>更改检测替代

增量索引使你可以精细控制扩充管道的各个方面。 利用此控制，您可以处理更改可能会产生意外后果的情况。 例如，编辑技能组合和更新自定义技能的 URL 将导致索引器使该技能的缓存结果失效。 如果只是将终结点移到不同的 VM，或使用新的访问密钥重新部署技能，则实际上不需要重新处理任何现有的文档。

若要确保索引器只是明确需要的根据，则对技能组合的更新可以选择性地将 `disableCacheReprocessingChangeDetection` querystring 参数设置为 `true`。 设置此参数时，此参数将确保只提交对技能组合的更新，并且不会对现有语料库的影响评估更改。

下面的示例演示查询字符串用法。 它是请求的一部分，具有 & 分隔的键值对。 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>缓存失效

相反的情况是，您可以部署新的自定义技术版本，扩充管道中的任何内容都不会改变，但您需要一种特定技能失效，并重新处理所有受影响的文档，以反映更新后的模型的优势。 在这种情况下，可以对技能组合调用无效的技能操作。 重置技能 API 接受 POST 请求，其中包含缓存中应失效的技能列表。 有关重置技能 API 的详细信息，请参阅[重置索引器（搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)。

## <a name="bi-directional-change-detection"></a>双向更改检测

索引器不仅前进并处理新文档，而且现在能够向后移动，并驱动以前处理过的文档以保持一致。 使用这项新功能，必须了解对扩充管道组件所做的更改如何导致索引器工作。 如果索引器标识的更改无效或相对于缓存的内容不一致，则该索引器会将工作排队。

### <a name="invalidating-changes"></a>使更改失效

使更改无效，但对扩充管道的状态有重大影响。 无效的更改是指整个缓存不再有效。 无效更改的一个示例是更新数据源。 如果你知道更改不应使缓存失效（例如，在存储帐户上循环密钥），则应将 `ignoreResetRequirement` querystring 参数设置为在特定资源的更新操作 `true`，以确保操作不被拒绝。

下面是会使缓存无效的更改的完整列表：

* 更改为你的数据源类型
* 更改为数据源容器
* 数据源凭据
* 数据源更改检测策略
* 数据源删除检测策略
* 索引器字段映射
* 索引器参数
    * 分析模式
    * 排除的文件扩展名
    * 索引的文件扩展名
    * 仅为超大文档索引存储元数据
    * 分隔的文本标题
    * 分隔文本分隔符
    * 文档根
    * 图像操作（更改图像提取方式）

### <a name="inconsistent-changes"></a>更改不一致

不一致更改的一个示例是修改技能的技能组合的更新。 修改可以使部分缓存不一致。 索引器将标识工作以使其重新保持一致。  

导致缓存不一致的更改的完整列表：

* 技能组合中的技能具有不同的类型。 已更新技能的 odata 类型
* 更新了特定于技能的参数，例如 url、默认值或其他参数
* 技能产出变化，技能返回其他或不同的输出
* 导致的技能更新不同体系，技能链发生了变化，即 技能输入
* 如果为此技能提供输入的技能已更新，则任何上游技能均失效
* 更新到知识库投影位置，生成 reprojecting 文档
* 对知识库投影的更改，导致 reprojecting 文档
* 索引器上已更改的输出字段映射会将 reprojecting 文档中的结果更改为索引

## <a name="rest-api-reference-for-incremental-indexing"></a>增量索引 REST API 引用

REST `api-version=2019-05-06-Preview` 为增量索引提供 Api，并添加到索引器、技能集和数据源。 参考文档当前不包含这些新增内容。 以下部分介绍了 API 更改。

### <a name="indexers"></a>索引器

[Create 索引](https://docs.microsoft.com/rest/api/searchservice/create-indexer)器和[Update 索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)现在将公开与缓存相关的新属性：

* `StorageAccountConnectionString`：将用于缓存中间结果的存储帐户的连接字符串。

* `CacheId`： `cacheId` 是 `annotationCache` 存储帐户中的容器的标识符，该容器将用作此索引器的缓存。 此缓存对于此索引器是唯一的，如果删除索引器并以相同的名称重新创建，则会重新生成 `cacheId`。 `cacheId` 无法设置，它始终由服务生成。

* `EnableReprocessing`：默认情况下设置为 `true`，设置为 `false`时，将继续将文档写入缓存，但不会基于缓存数据重新处理现有文档。

某些索引器（通过[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)）通过查询检索数据。 对于检索数据的查询，索引器还支持新的查询字符串参数：当更新操作不应使缓存失效时，应将 `ignoreResetRequirement` 设置为 `true`。

### <a name="skillsets"></a>技能集

技能集不支持任何新操作，但将支持新的 querystring 参数：应将 `disableCacheReprocessingChangeDetection` 设置为 `true` 在不需要基于当前操作更新现有文档的情况下。

### <a name="datasources"></a>源

数据源不支持任何新操作，但将支持新的 querystring 参数：当更新操作不应使缓存无效时，应将 `ignoreResetRequirement` 设置为 `true`。

## <a name="best-practices"></a>最佳实践

使用增量索引的建议方法是通过在新索引器上设置缓存属性或重置现有索引器并设置缓存属性来配置增量索引。

使用 `ignoreResetRequirement` 谨慎，因为这可能会导致无法轻易检测到的数据不一致。

## <a name="takeaways"></a>要点

增量索引是一项功能强大的功能，它将数据源中的更改跟踪扩展到扩充管道的所有方面，包括数据源、技能组合的当前版本和索引器。 随着您的技能、技能集或根据的发展，扩充管道可确保完成最少的工作量，同时使您的文档保持最终一致性。

## <a name="next-steps"></a>后续步骤

在定义新索引器时，通过将缓存添加到现有索引器或添加缓存，开始进行增量索引。

> [!div class="nextstepaction"]
> [设置增量索引](search-howto-incremental-index.md)
