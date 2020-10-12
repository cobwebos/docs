---
title: 增量扩充概念（预览版）
titleSuffix: Azure Cognitive Search
description: 在 Azure 存储中缓存 AI 扩充管道的中间内容和增量更改，以保护对现有已处理文档的投资。 此功能目前处于公开预览状态。
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 9fb76c5c96795b8092c86e22acbab4ea5963b42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971630"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure 认知搜索中的增量扩充和缓存

> [!IMPORTANT] 
> 增量扩充目前以公共预览版提供。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
> [REST API 预览版本](search-api-preview.md) 提供此功能。 目前不支持门户或 .NET SDK。

“增量扩充”是一项针对[技能组](cognitive-search-working-with-skillsets.md)的功能。 它利用 Azure 存储保存扩充管道发出的处理输出，方便在将来的索引器运行中重复使用。 索引器会尽可能重复使用任何仍有效的缓存输出。 

增量扩充不仅可以保护在处理（特别是 OCR 和图像处理）方面的投资，而且还能提高系统的效率。 缓存结构和内容时，索引器可以确定哪些技能已更改，并仅运行已修改的技能以及任何下游相关技能。 

使用增量缓存的工作流包括以下步骤：

1. [创建或标识用于存储缓存的 Azure 存储帐户](../storage/common/storage-account-create.md)。
1. 在索引器中[启用增量扩充](search-howto-incremental-index.md)。
1. [创建索引器](/rest/api/searchservice/create-indexer)加上[技能组](/rest/api/searchservice/create-skillset)以调用管道。 在处理过程中，会为 Blob 存储中的每个文档保存扩充的各个阶段，供将来使用。
1. 测试代码，对其进行更改后使用[更新技能组](/rest/api/searchservice/update-skillset)修改定义。
1. [运行索引器](/rest/api/searchservice/run-indexer)以调用管道，从而检索缓存的输出以进行更快且更经济高效的处理。

若要详细了解使用现有索引器时的步骤和注意事项，请参阅[设置增量扩充](search-howto-incremental-index.md)。

## <a name="indexer-cache"></a>索引器缓存

增量扩充将缓存添加到扩充管道。 索引器将缓存文档破解结果，以及针对每个文档运行每个技能后的输出。 更新技能集后，只会重新运行已更改的技能或下游技能。 更新的结果将写入缓存，文档将在搜索索引或知识存储中更新。

在物理上，缓存存储在 Azure 存储帐户中的 Blob 容器内。 缓存还使用表存储来保存处理更新的内部记录。 搜索服务中的所有索引可以共享索引器缓存的同一存储帐户。 为每个索引器分配了它所用的容器的唯一不可变缓存标识符。

## <a name="cache-configuration"></a>缓存配置

需要在索引器中设置 `cache` 属性才能受益于增量扩充。 以下示例演示了一个已启用缓存的索引器。 以下部分介绍了此配置的具体组成部分。 有关详细信息，请参阅[设置增量扩充](search-howto-incremental-index.md)。

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

在现有索引器中设置此属性需要重置并重新运行该索引器，这会导致再次处理数据源中的所有文档。 必须执行此步骤才能消除以前的技能集版本所扩充的任何文档。 

## <a name="cache-management"></a>缓存管理

缓存的生命周期由索引器管理。 如果索引器中的 `cache` 属性设置为 null，或者连接字符串发生更改，则下一次运行索引器时会删除现有缓存。 缓存生命周期还与索引器生命周期相关联。 如果删除某个索引器，则也会删除关联的缓存。

尽管增量扩充无需你的干预即可检测和响应更改，但也可以使用一些参数来重写默认行为：

+ 指定新文档的优先级
+ 绕过技能集检查
+ 绕过数据源检查
+ 强制技能集评估

### <a name="prioritize-new-documents"></a>指定新文档的优先级

设置 `enableReprocessing` 属性，以控制对缓存中已存在的传入文档的处理。 如果设置为 `true`（默认值），则重新运行索引器时，会重新处理缓存中现有的文档，前提是技能更新会影响该文档。 

如果设置为 `false`，则不会重新处理现有文档，而是有效地使新传入内容的优先级高于现有内容。 只能暂时将 `enableReprocessing` 设置为 `false`。 为了确保数据集间的一致性，大多数时间 `enableReprocessing` 应是 `true`，以确保所有新的和现有的文档都按照当前技能集定义保持有效。

### <a name="bypass-skillset-evaluation"></a>绕过技能集评估

修改技能集和重新处理该技能集通常是一起进行的。 但是，对技能集进行的一些更改不应导致重新处理（例如，将自定义技能部署到新位置，或使用新的访问密钥）。 这些外围修改很可能不会对技能集本身的实质造成真正影响。 

如果你知道对技能集的更改确实是表面性的，应该通过将 `disableCacheReprocessingChangeDetection` 参数设置为 `true` 来重写技能集评估：

1. 调用“更新技能集”并修改技能集定义。
1. 在请求中追加 `disableCacheReprocessingChangeDetection=true` 参数。
1. 提交更改。

设置此参数可以确保仅提交对该技能集定义的更新，而不会评估更改对现有数据集的影响。

以下示例演示带有参数的“更新技能集”请求：

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>绕过数据源验证检查

对数据源定义进行的大部分更改都会使缓存失效。 但是，如果你知道某项更改不会使缓存失效 - 例如，在存储帐户中更改连接字符串或轮换密钥 - 请在数据源更新中追加 `ignoreResetRequirement` 参数。 将此参数设置为 `true` 可让提交操作继续，而不会触发重置条件，导致重新生成并从头开始填充所有对象。

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>强制技能集评估

缓存的目的是避免不必要的处理，但假设你要对索引器不会检测的技能进行更改（例如，在外部代码中更改某项内容，如自定义技能）。

在这种情况下，可以使用[重置技能](/rest/api/searchservice/preview-api/reset-skills)来强制重新处理特定的技能，包括依赖于该技能的输出的任何下游技能。 此 API 接受 POST 请求以及应该失效且标记为重新处理的技能列表。 运行“重置技能”后，运行索引器来调用管道。

## <a name="change-detection"></a>更改检测

启用缓存后，索引器将评估管道组合中的更改，以确定可以重复使用的内容以及需要重新处理的内容。 本部分先后列举了会使缓存彻底失效的更改，以及会触发增量处理的更改。 

### <a name="changes-that-invalidate-the-cache"></a>使缓存失效的更改

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

增量处理将评估技能集定义，确定要重新运行的技能，并选择性地更新文档树的受影响部分。 下面是导致增量扩充的更改的完整列表：

* 技能集中的技能具有不同的类型。 更新技能的 odata 类型
* 更新特定于技能的参数，例如 URL、默认值或其他参数
* 技能输出更改，技能返回其他输出或不同的输出
* 技能更新结果为不同的宗系，技能链发生更改，例如 技能输入
* 如果为任何上游技能提供输入的技能已更新，则此上游技能将会失效
* 更新知识存储投影位置导致重新投影文档
* 更改知识存储投影导致重新投影文档
* 更改索引器结果中的输出字段映射导致将文档重新投影到索引

## <a name="api-reference"></a>API 参考

REST API 版本 `2020-06-30-Preview` 通过索引器中的附加属性提供增量扩充。 技能组和数据源可以使用正式版。 除参考文档以外，另请参阅[为增量扩充配置缓存](search-howto-incremental-index.md)来了解有关如何调用 API 的详细信息。

+ [创建索引器 (api-version=2020-06-30-Preview)](/rest/api/searchservice/create-indexer) 

+ [更新索引器 (api-version=2020-06-30-Preview)](/rest/api/searchservice/update-indexer) 

+ [更新技能组 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)（请求中的新 URI 参数）

+ [重置技能 (api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ 数据库索引器（Azure SQL、Cosmos DB）。 某些索引器通过查询检索数据。 对于检索数据的查询，[更新数据源](/rest/api/searchservice/update-data-source)支持在请求中使用新参数 **ignoreResetRequirement**，如果更新操作不应使缓存失效，则应将此参数设置为 `true`。 

  请尽量少用 **ignoreResetRequirement**，因为它可能会导致意外的数据不一致性，而这种不一致性不容易检测到。

## <a name="next-steps"></a>后续步骤

增量扩充是非常强大的功能，可将更改跟踪扩展到技能集和 AI 扩充。 通过增量扩充，可以在循环访问技能组合设计时重复使用现有已处理的内容。

接下来，请对现有的索引器启用缓存，或者在定义新索引器时添加缓存。

> [!div class="nextstepaction"]
> [为增量扩充配置缓存](search-howto-incremental-index.md)