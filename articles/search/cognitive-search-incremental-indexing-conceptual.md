---
title: 增量扩充（预览版）
titleSuffix: Azure Cognitive Search
description: 在 Azure 存储中缓存来自 AI 扩充管道的中间内容和增量更改，以保留现有已处理文档的投资。 此功能目前处于公开预览状态。
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: a5b12a426e52c3b80c58a30b320b2f746bbe990d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832199"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure 认知搜索中的增量扩充和缓存简介

> [!IMPORTANT] 
> 增量扩充目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前没有门户或 .NET SDK 支持。

增量扩充将缓存和有状态添加到扩充管道，在现有输出中保留投资，同时更改受特定修改影响的文档。 这不仅会使你的资金投入处理（特别是 OCR 和图像处理），而且还可以提高系统的效率。 缓存结构和内容时，索引器可以确定哪些技能发生了变化，并且仅运行了已修改的技能以及任何下游相关技能。 

## <a name="indexer-cache"></a>索引器缓存

增量扩充将缓存添加到扩充管道。 索引器将从文档破解和每个文档的每项技能的输出中缓存结果。 更新技能集后，只会重新运行已更改的技能或下游技能。 更新的结果将写入缓存，并在搜索索引或知识库中更新文档。

在物理上，缓存存储在 Azure 存储帐户中的 blob 容器中。 搜索服务中的所有索引可以共享索引器缓存的同一存储帐户。 为每个索引器分配一个唯一且不可变的缓存标识符到它所使用的容器。

## <a name="cache-configuration"></a>缓存配置

需要在索引器上设置 `cache` 属性，以从增量扩充启动受益。 下面的示例演示启用了缓存的索引器。 以下部分介绍了此配置的特定部分。 有关详细信息，请参阅[Set up 扩充](search-howto-incremental-index.md)。

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

在现有索引器上设置此属性将要求你重置并重新运行索引器，这将导致再次处理数据源中的所有文档。 此步骤是消除以前版本的技能组合所丰富的任何文档所必需的。 

## <a name="cache-management"></a>缓存管理

缓存的生命周期由索引器管理。 如果索引器的 `cache` 属性设置为 null 或连接字符串已更改，则在下一次索引器运行时将删除现有缓存。 缓存生命周期还与索引器生命周期相关联。 如果删除某个索引器，则也会删除关联的缓存。

虽然增量扩充旨在检测并响应不需要干预的更改，但有一些参数可用于重写默认行为：

+ 挂起缓存
+ 绕过技能组合检查
+ 绕过数据源检查
+ 强制技能组合评估

### <a name="suspend-caching"></a>挂起缓存

可以通过将缓存中的 `enableReprocessing` 属性设置为 `false` 来暂停增量扩充，以后可以通过将此属性设置为 `true` 来恢复增量扩充并促成最终一致性。 如果你的优先事务是为新文档编制索引而不是确保文档数据集的一致性，则这种控制度特别有用。

### <a name="bypass-skillset-evaluation"></a>绕过技能组合评估

修改该技能组合的技能组合和重新处理通常是非常好的。 但是，对技能组合进行的一些更改不会导致重新处理（例如，将自定义技能部署到新位置或使用新的访问密钥）。 大多数情况下，这些是对技能组合本身的实质没有真正影响的外围修改。 

如果你知道对技能组合的更改的确是表面，则应通过将 `disableCacheReprocessingChangeDetection` 参数设置为 `true`来覆盖技能组合评估：

1. 调用 Update 技能组合并修改技能组合定义。
1. 将 `disableCacheReprocessingChangeDetection=true` 参数追加到请求上。
1. 提交更改。

设置此参数可确保仅提交对技能组合定义的更新，并且不会对现有语料库的影响评估更改。

下面的示例演示具有参数的 Update 技能组合请求：

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>绕过数据源验证检查

对数据源定义所做的大多数更改都将使缓存失效。 但是，对于知道更改不应使缓存失效的情况（例如，更改连接字符串或轮换存储帐户上的密钥），请将`ignoreResetRequirement` 参数追加到数据源更新上。 如果将此参数设置为 `true`，则可以进行提交，而不触发重置条件，这将导致重新生成所有对象并从头开始填充。

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>强制技能组合评估

缓存的目的是为了避免不必要的处理，但假设您已更改了索引器未检测到的技能或技能组合（例如，对自定义技能组合之类的外部组件进行更改）。 

在这种情况下，您可以使用[重置技能](preview-api-resetskills.md)API 来强制重新处理特定技能，包括依赖于该技能的输出的任何下游技能。 此 API 接受 POST 请求，其中包含应失效并重新运行的技能列表。 重置技能后，运行索引器以执行操作。

## <a name="change-detection"></a>更改检测

启用缓存后，索引器会评估管道组合中的更改，以确定可重用的内容和需要重新处理的内容。 此部分枚举了彻底使缓存无效的更改，以及触发增量处理的更改。 

### <a name="changes-that-invalidate-the-cache"></a>使缓存无效的更改

使更改失效意味着整个缓存不再有效。 例如，在更新数据源时就存在使更改失效的情况。 下面是使缓存失效的更改的完整列表：

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

### <a name="changes-that-trigger-incremental-processing"></a>触发增量处理的更改

增量处理会评估技能组合定义，并确定重新运行的技能，并有选择地更新文档树的受影响部分。 下面是导致增量扩充的更改的完整列表：

* 技能集中的技能具有不同的类型。 更新技能的 odata 类型
* 更新特定于技能的参数，例如 URL、默认值或其他参数
* 技能输出更改，技能返回其他输出或不同的输出
* 技能更新结果为不同的宗系，技能链发生更改，例如 技能输入
* 如果为任何上游技能提供输入的技能已更新，则此上游技能将会失效
* 更新知识存储投影位置导致重新投影文档
* 更改知识存储投影导致重新投影文档
* 更改索引器结果中的输出字段映射导致将文档重新投影到索引

## <a name="api-reference-content-for-incremental-enrichment"></a>增量扩充的 API 参考内容

REST `api-version=2019-05-06-Preview` 为增量扩充提供 Api，并将其添加到索引器、技能集和数据源。 [官方参考文档](https://docs.microsoft.com/rest/api/searchservice/)适用于公开发布的 api，不涉及预览版功能。 以下部分提供受影响的 Api 的参考内容。

可在[为增量扩充配置缓存](search-howto-incremental-index.md)中找到使用情况信息和示例。

### <a name="indexers"></a>索引器

[Create 索引](https://docs.microsoft.com/rest/api/searchservice/create-indexer)器和[Update 索引器](https://docs.microsoft.com/rest/api/searchservice/update-indexer)现在将公开与缓存相关的新属性：

+ `StorageAccountConnectionString`：将用于缓存中间结果的存储帐户的连接字符串。

+ `EnableReprocessing`：默认情况下设置为 `true`，设置为 `false`时，将继续将文档写入缓存，但不会基于缓存数据重新处理现有文档。

+ `ID` （只读）： `ID` 是 `annotationCache` 存储帐户中的容器的标识符，该容器将用作此索引器的缓存。 此缓存对于此索引器是唯一的，如果删除此索引器并以相同的名称重新创建，则会重新生成 `ID`。 无法设置 `ID`，它始终由服务生成。

### <a name="skillsets"></a>技能集

+ [Update 技能组合](https://docs.microsoft.com/rest/api/searchservice/update-skillset)支持请求中的新参数： `disableCacheReprocessingChangeDetection`，如果不想基于当前操作对现有文档进行更新，则应将其设置为 `true`。

+ [重置技能](preview-api-resetskills.md)是用于使技能组合失效的新操作。

### <a name="datasources"></a>Datasources

+ 某些索引器通过查询检索数据。 对于检索数据的查询，[更新数据源](https://docs.microsoft.com/rest/api/searchservice/update-datasource)支持 `ignoreResetRequirement`上的新参数，在更新操作不应使缓存失效时，应将其设置为 `true`。

使用 `ignoreResetRequirement` 谨慎，因为这可能会导致无法轻易检测到的数据不一致。

## <a name="next-steps"></a>后续步骤

增量扩充是一项功能强大的功能，可将更改跟踪扩展到技能集和 AI 扩充。 随着技能集的发展，增量扩充可确保完成最少的工作量，同时使你的文档保持最终一致性。

在定义新索引器时，通过将缓存添加到现有索引器或添加缓存来开始。

> [!div class="nextstepaction"]
> [为增量扩充配置缓存](search-howto-incremental-index.md)
