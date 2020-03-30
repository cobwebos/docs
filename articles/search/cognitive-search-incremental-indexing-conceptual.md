---
title: 增量扩充（预览）
titleSuffix: Azure Cognitive Search
description: 缓存 Azure 存储中的 AI 充实管道的中间内容和增量更改，以保留对现有已处理文档的投资。 此功能目前处于公开预览状态。
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024137"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure 认知搜索中增量扩充和缓存简介

> [!IMPORTANT] 
> 增量扩充当前处于公共预览版中。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持门户或 .NET SDK。

增量扩充增加了浓缩管道的缓存和状态，保留了对现有产出的投资，同时仅更改受特定修改影响的文档。 这不仅保留了您在处理（特别是 OCR 和图像处理）方面的货币投资，还使系统更加高效。 缓存结构和内容时，索引器可以确定哪些技能已更改，并且仅运行已修改的技能以及任何下游相关技能。 

## <a name="indexer-cache"></a>索引器缓存

增量富集向富集管道添加缓存。 索引器缓存文档开裂的结果以及每个文档的每个技能的输出。 更新技能集后，只会重新运行已更改的技能或下游技能。 更新的结果将写入缓存，并在搜索索引或知识存储中更新文档。

在物理上，缓存存储在 Azure 存储帐户中的 Blob 容器中。 缓存还使用表存储进行处理更新的内部记录。 搜索服务中的所有索引可以共享索引器缓存的同一存储帐户。 为每个索引器为其使用的容器分配了一个唯一且不可变的缓存标识符。

## <a name="cache-configuration"></a>缓存配置

您需要在`cache`索引器上设置属性，以便开始从增量扩充中获益。 下面的示例演示了启用缓存的索引器。 此配置的特定部分在以下部分中介绍。 有关详细信息，请参阅[设置增量富集](search-howto-incremental-index.md)。

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

在现有索引器上设置此属性需要重置和重新运行索引器，这将导致数据源中的所有文档再次被处理。 此步骤对于消除由技能集的早期版本所丰富的任何文档是必需的。 

## <a name="cache-management"></a>缓存管理

缓存的生命周期由索引器管理。 如果索引`cache`器上的属性设置为 null 或连接字符串已更改，则在下次索引器运行时将删除现有缓存。 缓存生命周期还与索引器生命周期相关联。 如果删除某个索引器，则也会删除关联的缓存。

虽然增量扩充旨在检测和响应更改，无需干预，但可以使用一些参数来覆盖默认行为：

+ 确定新文档的优先级
+ 绕过技能集检查
+ 绕过数据源检查
+ 部队技能集评估

### <a name="prioritize-new-documents"></a>确定新文档的优先级

将`enableReprocessing`属性设置为控制对缓存中已表示的传入文档的处理。 当`true`（默认）时，在重新运行索引器时，将重新处理缓存中已有的文档，前提是您的技能更新会影响该文档。 

当`false`不重新处理 现有文档时，可以有效地将新传入内容优先于现有内容。 您只能临时设置`enableReprocessing``false`。 为了确保整个语料库的一致性`enableReprocessing`，在大多数情况下`true`，确保所有文档（无论是新的文档还是现有文档）都根据当前技能集定义有效。

### <a name="bypass-skillset-evaluation"></a>旁路技能集评估

修改技能集和重新处理该技能集通常齐头并进。 但是，对技能集的某些更改不应导致后处理（例如，将自定义技能部署到新位置或使用新的访问密钥）。 最有可能的是，这些是外围修改，对技能集本身的实质没有真正的影响。 

如果您知道对技能集的更改确实是肤浅的，则应通过将`disableCacheReprocessingChangeDetection`参数设置为`true`：

1. 调用更新技能集并修改技能集定义。
1. 在`disableCacheReprocessingChangeDetection=true`请求上追加参数。
1. 提交更改。

设置此参数可确保仅提交对技能集定义的更新，并且不会评估更改对现有语料库的影响。

下面的示例显示了具有参数的更新技能集请求：

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>绕过数据源验证检查

对数据源定义的大多数更改将使缓存无效。 但是，对于您知道更改不应使缓存无效的情况（例如更改连接字符串或旋转存储帐户上的密钥）时，会追加数据源更新上的`ignoreResetRequirement`参数。 设置此参数以`true`允许提交通过，而不会触发重置条件，这将导致从头开始重新生成和填充所有对象。

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>部队技能集评估

缓存的目的是避免不必要的处理，但假设您对索引器未检测到的技能进行更改（例如，更改外部代码中的内容，如自定义技能）。

在这种情况下，可以使用[重置技能](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)强制重新处理特定技能，包括依赖于该技能输出的任何下游技能。 此 API 接受 POST 请求，该请求具有应失效并标记为后处理的技能列表。 重置技能后，运行索引器以调用管道。

## <a name="change-detection"></a>更改检测

启用缓存后，索引器将评估管道组合中的更改，以确定哪些内容可以重复使用，哪些内容需要重新处理。 本节列举使缓存完全失效的更改，然后是触发增量处理的更改。 

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

增量处理评估技能集定义并确定要重新运行的技能，有选择地更新文档树的受影响部分。 下面是导致增量扩充的更改的完整列表：

* 技能集中的技能具有不同的类型。 更新技能的 odata 类型
* 更新特定于技能的参数，例如 URL、默认值或其他参数
* 技能输出更改，技能返回其他输出或不同的输出
* 技能更新结果为不同的宗系，技能链发生更改，例如 技能输入
* 如果为任何上游技能提供输入的技能已更新，则此上游技能将会失效
* 更新知识存储投影位置导致重新投影文档
* 更改知识存储投影导致重新投影文档
* 更改索引器结果中的输出字段映射导致将文档重新投影到索引

## <a name="api-reference"></a>API 参考

REST API`2019-05-06-Preview`版本通过索引器、技能集和数据源上的其他属性提供增量扩充。 除了参考文档之外，有关如何调用 API 的详细信息，请参阅[配置缓存以进行增量扩充](search-howto-incremental-index.md)。

+ [创建索引器（api 版本=2019-05-06-预览版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [更新索引器（api 版本=2019-05-06-预览版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [更新技能集（api 版本=2019-05-06-预览版）（](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset)请求上的新 URI 参数）

+ [重置技能 (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ 数据库索引器（Azure SQL、宇宙数据库）。 某些索引器通过查询检索数据。 对于检索数据的查询，[更新数据源](https://docs.microsoft.com/rest/api/searchservice/update-data-source)支持请求**忽略Reset要求**的新参数，该参数应设置为`true`更新操作不应使缓存无效时。 

  使用**忽略 Reset 要求**，因为它可能会导致数据中意外的不一致，不容易检测到。

## <a name="next-steps"></a>后续步骤

增量扩充是一个强大的功能，可将更改跟踪扩展到技能集和 AI 充实。 增量扩充可在迭代技能集设计时重用现有已处理的内容。

作为下一步，在现有索引器上启用缓存，或在定义新索引器时添加缓存。

> [!div class="nextstepaction"]
> [配置缓存以进行增量扩充](search-howto-incremental-index.md)
