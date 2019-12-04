---
title: 增量索引（预览）
titleSuffix: Azure Cognitive Search
description: 配置 AI 扩充管道，使数据的最终一致性，处理对技能、技能集、索引器或数据源的任何更新。 此功能目前处于公共预览阶段
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c44228d7e1456bce870765935beb011cb24626d5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790932"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Azure 认知搜索中的增量索引是什么？

> [!IMPORTANT] 
> 增量索引当前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。

增量索引是 Azure 认知搜索的一项新功能，它将缓存和状态添加到认知技能组合中增强的内容，使你可以控制扩充管道中的单个步骤的处理和重新处理。 这不仅会保留你的资金投入，还会使系统更有效。 缓存结构和内容时，索引器可以确定哪些技能发生了变化，并且仅运行了已修改的技能以及任何下游相关技能。 

借助增量索引，当前版本的扩充管道只需执行最少量的工作即可保证索引中所有文档的一致性。 对于需要完全控制的情况，可以使用精细的控件覆盖预期的行为。 有关配置的详细信息，请参阅[设置增量索引](search-howto-incremental-index.md)。

## <a name="indexer-cache"></a>索引器缓存

增量索引将一个索引器缓存添加到扩充管道。 索引器将缓存文档破解结果，并输出每个文档的每项技能。 更新技能集后，只会重新运行已更改的技能或下游技能。 更新的结果将写入缓存，文档将在索引和知识存储中更新。

从物理上讲，缓存是一个存储帐户。 搜索服务中的所有索引可以共享索引器缓存的同一存储帐户。 为每个索引器分配了一个唯一的不可变缓存标识符。

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
    "parameters": {}
}
```

在现有索引器上首次设置此属性时，还需要重置此属性，这将导致再次处理数据源中的所有文档。 递增索引的目标是使索引中的文档与数据源和当前版本的技能集保持一致。 重置索引是实现这种一致性的第一步，因为它可以消除以前版本的技能集扩充的所有文档。 需要重置索引器才能开始与基线保持一致。

### <a name="cache-lifecycle"></a>缓存生命周期

缓存的生命周期由索引器管理。 如果索引器的 `cache` 属性设置为 null 或更改了连接字符串，则将删除现有缓存。 缓存生命周期还与索引器生命周期相关联。 如果删除某个索引器，则也会删除关联的缓存。

### <a name="indexer-cache-mode"></a>索引器缓存模式

索引器缓存可在以下模式下运行：数据只会写入到缓存，或者数据将写入缓存并用于重新扩充文档。  可以通过将缓存中的 `enableReprocessing` 属性设置为 `false` 来暂停增量扩充，以后可以通过将此属性设置为 `true` 来恢复增量扩充并促成最终一致性。 如果你的优先事务是为新文档编制索引而不是确保文档数据集的一致性，则这种控制度特别有用。

## <a name="change-detection-override"></a>更改检测替代

使用增量索引可对扩充管道的各个方面进行精细控制。 利用这种控制度，可以应对某项更改可能会产生意外后果的局面。 例如，编辑技能集和更新自定义技能的 URL 会导致索引器使该技能的缓存结果失效。 如果只是将终结点移到不同的 VM，或使用新的访问密钥重新部署技能，你实际上并不希望重新处理任何现有文档。

若要确保索引器只是明确需要的根据，则对技能组合的更新可以选择性地将 `disableCacheReprocessingChangeDetection` querystring 参数设置为 `true`。 设置此参数后，它可以确保仅提交对该技能集的更新，而不会评估更改对现有数据集的影响。

下面的示例演示查询字符串用法。 它是请求的一部分，具有 & 分隔的键值对。 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>缓存失效

相反的情况是，你可能会部署新版的自定义技能，并且扩充管道中的任何内容都未更改，但你需要使特定的技能失效，并重新处理所有受影响的文档，以反映更新的模型的优势。 在这种情况下，可以针对技能集调用“使技能失效”操作。 重置技能 API 接受包含缓存中应失效的技能输出列表的 POST 请求。 有关重置技能 API 的详细信息，请参阅[重置索引器（搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)。

## <a name="bi-directional-change-detection"></a>双向更改检测

索引器不仅可以前进并处理新文档，而且现在还可以后退并促成前面处理过的文档的一致性。 使用此新功能时，必须了解对扩充管道组件做出的更改如何生成索引器工作。 如果索引器标识的更改无效或相对于缓存的内容不一致，则该索引器会将工作排队。

### <a name="invalidating-changes"></a>使更改失效

使更改失效的情况很少见，但对扩充管道的状态会造成重大影响。 使更改失效意味着整个缓存不再有效。 例如，在更新数据源时就存在使更改失效的情况。 如果你知道更改不应使缓存失效（例如，在存储帐户上轮换密钥），则应将 `ignoreResetRequirement` querystring 参数设置为在特定资源的更新操作 `true`，以确保不会拒绝该操作。

下面是使缓存失效的更改的完整列表：

* 对数据源类型的更改
* 对数据源容器的更改
* 数据源凭据
* 数据源更改检测策略
* 数据源删除检测策略
* 索引器字段映射
* 索引器参数
    * 分析模式
    * 排除的文件扩展名
    * 已编制索引的文件扩展名
    * 仅为超大文档的存储元数据编制索引
    * 分隔的文本标题
    * 分隔的文本分隔符
    * 文档根
    * 图像操作（对图像提取方式的更改）

### <a name="inconsistent-changes"></a>不一致的更改

例如，如果更新技能集会修改某个技能，则此操作就属于不一致的更改。 这种修改可能会使得一部分缓存不一致。 索引器将识别使得内容重新保持一致所要执行的工作。  

导致缓存不一致的更改的完整列表：

* 技能集中的技能具有不同的类型。 更新技能的 odata 类型
* 更新特定于技能的参数，例如 URL、默认值或其他参数
* 技能输出更改，技能返回其他输出或不同的输出
* 技能更新结果为不同的宗系，技能链发生更改，例如 技能输入
* 如果为任何上游技能提供输入的技能已更新，则此上游技能将会失效
* 更新知识存储投影位置导致重新投影文档
* 更改知识存储投影导致重新投影文档
* 更改索引器结果中的输出字段映射导致将文档重新投影到索引

## <a name="rest-api-reference-for-incremental-indexing"></a>增量索引 REST API 引用

REST `api-version=2019-05-06-Preview` 为增量索引提供 Api，并添加到索引器、技能集和数据源。 参考文档当前不包含这些新增内容。 以下部分介绍了 API 更改。

### <a name="indexers"></a>索引器

[Create 索引](https://docs.microsoft.com/rest/api/searchservice/create-indexer)器和[Update 索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)现在将公开与缓存相关的新属性：

* `StorageAccountConnectionString`：将用于缓存中间结果的存储帐户的连接字符串。

* `CacheId`： `cacheId` 是 `annotationCache` 存储帐户中的容器的标识符，该容器将用作此索引器的缓存。 此缓存对于此索引器是唯一的，如果删除此索引器并以相同的名称重新创建，则会重新生成 `cacheId`。 无法设置 `cacheId`，它始终由服务生成。

* `EnableReprocessing`：默认情况下设置为 `true`，设置为 `false`时，将继续将文档写入缓存，但不会基于缓存数据重新处理现有文档。

某些索引器（通过[数据源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)）通过查询检索数据。 对于检索数据的查询，索引器还支持新的查询字符串参数：当更新操作不应使缓存失效时，应将 `ignoreResetRequirement` 设置为 `true`。

### <a name="skillsets"></a>技能集

技能集不支持任何新操作，但支持一个新的查询字符串参数：如果不希望基于当前操作更新现有文档，则应将 `disableCacheReprocessingChangeDetection` 设置为 `true`。

### <a name="datasources"></a>Datasources

Datasources 不支持任何新操作，但支持一个新的查询字符串参数：如果更新操作不应使缓存失效，则应将 `ignoreResetRequirement` 设置为 `true`。

## <a name="best-practices"></a>最佳做法

增量索引的建议使用方法是通过在新索引器中设置缓存属性来配置增量索引，或者重置现有索引器并设置缓存属性。

使用 `ignoreResetRequirement` 谨慎，因为这可能会导致无法轻易检测到的数据不一致。

## <a name="takeaways"></a>要点

增量索引是一项功能强大的功能，它将数据源中的更改跟踪扩展到扩充管道的所有方面，包括数据源、技能组合的当前版本和索引器。 随着技能、技能集或扩充内容的不断演变，扩充管道可尽量确保只需完成最少量的工作，同时仍可促成文档的最终一致性。

## <a name="next-steps"></a>后续步骤

通过将缓存添加到现有索引器或者在定义新索引器时添加缓存，来开始使用增量索引。

> [!div class="nextstepaction"]
> [设置增量索引](search-howto-incremental-index.md)
