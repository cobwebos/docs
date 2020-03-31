---
title: 知识存储（预览版）简介
titleSuffix: Azure Cognitive Search
description: 将富集文档发送到 Azure 存储，您可以在 Azure 认知搜索和其他应用程序中查看、重塑和使用富集文档。 此功能目前以公共预览版提供。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942996"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储简介

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

知识存储是 Azure 认知搜索的一项功能，它保留来自[AI 扩充管道](cognitive-search-concept-intro.md)的输出，以便独立分析或下游处理。 扩充文档是管道的输出，是基于使用 AI 流程提取、结构化和分析的内容创建的。** 在标准的 AI 管道中，扩充文档是临时的，仅在编制索引期间使用，然后被丢弃。 扩充文档将通过知识存储保存起来。 

如果你过去使用过认知技能，你已经知道*技能集*通过一系列充实来移动文档。 结果可以是搜索索引，也可以是知识存储中的投影（此预览版中新增的）。 搜索索引和知识存储这两个输出是同一管道的产品;派生自相同的输入，但产生以非常不同的方式结构化、存储和使用输出。

在物理上，知识存储是一个 [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-account-overview)，可以是 Azure 表存储和/或 Azure Blob 存储。 任何可以连接到 Azure 存储的工具或进程都可以使用知识存储的内容。

![管道中的知识存储示意图](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管道中的知识存储示意图")

## <a name="benefits-of-knowledge-store"></a>知识存储的优势

知识存储为您提供结构、上下文和实际内容 - 从非结构化和半结构化数据文件（如 Blob、已进行分析的图像文件，甚至结构化数据）中收集，重新塑造为新窗体。 在[分步演练](knowledge-store-create-rest.md)中，您可以亲眼看到密集的 JSON 文档如何被划分为子结构，重新构建为新结构，并以其他方式提供给下游流程（如机器学习和数据科学工作负载）。

尽管了解 AI 扩充管道可以产生什么非常有用，但知识存储的真正潜力是重塑数据的能力。 你可以从基本技能集入手，然后循环访问它以添加越来越多的结构级别，这样就能将它们合并成新结构，可用于除 Azure 认知搜索以外的其他应用。

知识存储的优势已枚举如下：

+ 在除搜索以外的[分析和报表工具](#tools-and-apps)中使用扩充文档。 使用 Power 查询 Power BI 是一个引人注目的选择，但任何可以连接到 Azure 存储的工具或应用都可以从您创建的知识存储中提取。

+ 优化 AI 索引管道，同时调试步骤和技能集定义。 知识存储展示 AI 索引管道中的技能集定义的结果。 这些结果可用于设计更好的技能集，因为你可以清楚地看到扩充是什么样的。 您可以使用 Azure[存储中的存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)来查看知识存储的内容。

+ 将数据整形到新表单中。 整形在技能集中编码化，但重点是技能集现在可以提供此功能。 Azure 认知搜索中的[整形程序技能](cognitive-search-skill-shaper.md)已扩展为包含此任务。 通过整形，可以定义与数据预期用途保持一致的投影，同时保留关系。

> [!Note]
> AI 丰富和认知技能的新增功能？ Azure 认知搜索与认知服务视觉和语言功能集成，以对图像文件使用光学字符识别 (OCR)、对文本文件使用实体识别和关键短语提取等来提取和扩充源数据。 有关详细信息，请参阅 [Azure 认知搜索中的 AI 扩充](cognitive-search-concept-intro.md)。

## <a name="physical-storage"></a>物理存储

知识存储的物理表达式通过技能集中`projections``knowledgeStore`定义的元素进行阐明。 投影定义输出的结构，使其与预期用途相匹配。

投影可以表述为表、对象或文件。

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

在此结构中指定的投影类型决定了知识存储使用的存储类型。

+ 在定义`tables`时使用表存储。 当您需要用于分析工具输入的表格式报告结构或作为数据帧导出到其他数据存储时，定义表投影。 您可以指定多个以`tables`获取富集文档的子集或横截面。 在同一投影组中，将保留表关系，以便可以使用所有这些关系。

+ 在定义`objects`或`files`时使用 Blob 存储。 的物理表示`object`形式是表示富集文档的分层 JSON 结构。 A`file`是从文档中提取的图像，完整地传输到 Blob 存储。

单个投影对象包含一组`tables`，`objects``files`对于许多方案，创建一个投影可能就足够了。 

`table`-`object`-`file`但是，可以创建多组投影，如果需要不同的数据关系，则可以这样做。 在集内，如果存在这些关系并可以检测到这些关系，数据是相关的。 如果创建其他集，则每个组中的文档永远不会相关。 使用多个投影组的一个示例可能是，如果您希望将相同的数据投影用于联机系统，并且需要以特定方式表示，则还需要相同的投影数据，以便用于表示的数据科学管道不同。

## <a name="requirements"></a>要求 

[Azure 存储](https://docs.microsoft.com/azure/storage/)是必需的。 它提供物理存储。 您可以使用 Blob 存储、表存储或两者。 Blob 存储用于完整丰富的文档，通常在输出进入下游进程时。 表存储用于富集文档切片，通常用于分析和报告。

[技能集](cognitive-search-working-with-skillsets.md)是必需的。 它包含定义`knowledgeStore`，并确定丰富文档的结构和组成。 不能使用空技能集创建知识存储。 技能集中必须至少有一项技能。

索引[器](search-indexer-overview.md)是必需的。 技能集由索引器调用，从而驱动执行。 索引器附带自己的一组要求和属性。 其中几个属性与知识存储有直接关系：

+ 索引器需要[受支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)（最终创建知识存储的管道首先从 Azure 上受支持的源提取数据）。 

+ 索引器需要搜索索引。 索引器要求您提供索引架构，即使您从未计划使用它。 最小索引具有一个字符串字段，指定为键。

+ 索引器提供可选的字段映射，用于将源字段别名到目标字段。 如果默认字段映射需要修改（以使用其他名称或类型），则可以在索引器中创建[字段映射](search-indexer-field-mappings.md)。 对于知识存储输出，目标可以是 blob 对象或表中的字段。

+ 索引器具有计划，其他属性（如由各种数据源提供的更改检测机制）也可以应用于知识存储。 例如，您可以定期[计划](search-howto-schedule-indexers.md)充实以刷新内容。 

## <a name="how-to-create-a-knowledge-store"></a>如何创建知识存储

要创建知识存储，请使用门户或预览 REST API`api-version=2019-05-06-Preview`（ 。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

**"导入数据**"向导包括用于创建知识存储的选项。 对于初始探索，[请分四个步骤创建第一个知识存储](knowledge-store-connect-power-bi.md)。

1. 选择受支持的数据源。

1. 指定扩充：附加资源、选择技能并指定知识存储。 

1. 创建索引架构。 向导需要它，可以为您推断出一个。

1. 运行向导。 提取、浓缩和存储发生在最后一步中。

### <a name="use-create-skillset-and-the-preview-rest-api"></a>使用创建技能集和预览 REST API

在`knowledgeStore`[技能集中](cognitive-search-working-with-skillsets.md)定义 a，而索引[器](search-indexer-overview.md)又调用它。 在充实期间，Azure 认知搜索会在 Azure 存储帐户中创建一个空间，并根据配置将富集文档作为 blob 或项目项目到表中。

目前，预览 REST API 是您可以以编程方式创建知识存储的唯一机制。 一个简单的探索方法是[创建你的第一个知识存储使用邮递员和REST API。](knowledge-store-create-rest.md)

此预览功能的参考内容位于本文的[API 参考](#kstore-rest-api)部分。 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>如何与工具和应用程序连接

只要扩充存在于存储中，连接到 Azure Blob 存储或表存储的任何工具或技术，都可用于浏览、分析或使用内容。 请从以下列表入手：

+ [存储资源管理器](knowledge-store-view-storage-explorer.md)：用于查看扩充文档的结构和内容。 将这视为用于查看知识存储内容的基线工具。

+ [用于报告和分析的 POWER BI。](knowledge-store-connect-power-bi.md) 

+ [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/)：用于进一步操作。

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API 参考

REST API`2019-05-06-Preview`版本通过技能集的其他定义提供知识存储。 除了参考之外，请参阅使用[Postman 创建知识存储](knowledge-store-create-rest.md)，了解有关如何调用 API 的详细信息。

+ [创建技能组 (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [更新技能集（api 版本=2019-05-06-预览版）](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>后续步骤

知识存储提供扩充文档的持久性，在设计技能集，或者在创建新的结构和内容供可访问 Azure 存储帐户的任何客户端应用程序使用时，知识存储非常有用。

创建富集文档的最简单方法是[通过门户](knowledge-store-create-portal.md)，但您也可以使用 Postman 和 REST API，如果您希望深入了解如何创建和引用对象，则这种方法更有用。

> [!div class="nextstepaction"]
> [使用邮递员和 REST 创建知识存储](knowledge-store-create-rest.md)

要了解有关预测、功能以及如何[在技能集中定义它们](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [知识商店中的投影](knowledge-store-projection-overview.md)

有关高级投影概念（如切片、内联整形和关系）的教程，请从[知识存储中定义投影](knowledge-store-projections-examples.md)开始

> [!div class="nextstepaction"]
> [定义知识存储中的投影](knowledge-store-projections-examples.md)