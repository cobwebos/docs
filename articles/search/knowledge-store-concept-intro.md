---
title: 知识存储概念
titleSuffix: Azure Cognitive Search
description: 将扩充文档发送到 Azure 存储，随后可以在 Azure 认知搜索和其他应用中查看、整形和使用扩充文档。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924293"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure 认知搜索中的知识存储

知识存储是 Azure 认知搜索中的一项功能，它可以保留 [AI 扩充管道](cognitive-search-concept-intro.md)的输出进行独立分析或下游处理。 扩充文档是管道的输出，是基于使用 AI 流程提取、结构化和分析的内容创建的。 在标准的 AI 管道中，扩充文档是临时的，仅在编制索引期间使用，然后被丢弃。 选择创建知识存储将允许你保存扩充文档。 

如果你过去曾经用过认知技能，则已经知道技能集可以通过一系列扩充来移动文档。 结果可以是搜索索引，也可以是知识存储中的投影。 搜索索引和知识存储这两种输出是同一管道的产出；它们派生自相同的输入，但导致以非常不同的方式构建、存储和使用输出。

在物理上，知识存储是一个 [Azure 存储](../storage/common/storage-account-overview.md)，可以是 Azure 表存储和/或 Azure Blob 存储。 任何可以连接到 Azure 存储的工具或进程都可以使用知识存储的内容。


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![管道中的知识存储示意图](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管道中的知识存储示意图")

## <a name="benefits-of-knowledge-store"></a>知识存储的优势

知识存储提供结构、上下文和实际内容 - 这些内容收集自非结构化和半结构化数据文件（例如 Blob）、已经过分析的图像文件甚至结构化数据，并整形为新的形式。 在[分步演练](knowledge-store-create-rest.md)中可以获得有关密集的 JSON 文档如何分区成子结构、如何重新构造成新结构，以及如何可用于下游过程（例如机器学习和数据科学工作负荷）的第一手资料。

尽管了解 AI 扩充管道可以生成哪些内容会很有用，但知识存储的真正潜力是能够整形数据。 你可以从基本技能集入手，然后循环访问它以添加越来越多的结构级别，这样就能将它们合并成新结构，可用于除 Azure 认知搜索以外的其他应用。

知识存储的优势已枚举如下：

+ 在除搜索以外的[分析和报表工具](#tools-and-apps)中使用扩充文档。 包含 Power Query 的 Power BI 就是个极具吸引力的选择，但只要是能连接到 Azure 存储，任何工具或应用都可以从你创建的知识存储中提取文档。

+ 优化 AI 索引管道，同时调试步骤和技能集定义。 知识存储展示 AI 索引管道中的技能集定义的结果。 这些结果可用于设计更好的技能集，因为你可以清楚地看到扩充是什么样的。 可以使用 Azure 存储中的[存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)来查看知识存储的内容。

+ 将数据整形到新表单中。 整形在技能集中编码化，但重点是技能集现在可以提供此功能。 Azure 认知搜索中的[整形程序技能](cognitive-search-skill-shaper.md)已扩展为包含此任务。 通过整形，可以定义与数据预期用途保持一致的投影，同时保留关系。

> [!Note]
> 刚刚接触 AI 扩充和认知技能？ Azure 认知搜索与认知服务视觉和语言功能集成，以对图像文件使用光学字符识别 (OCR)、对文本文件使用实体识别和关键短语提取等来提取和扩充源数据。 有关详细信息，请参阅 [Azure 认知搜索中的 AI 扩充](cognitive-search-concept-intro.md)。

## <a name="physical-storage"></a>物理存储


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


知识存储的物理表达形式是通过技能集中 `knowledgeStore` 定义的 `projections` 元素阐释的。 投影定义输出的结构，使之与预期用途相符。

可将投影阐释为表、对象或文件。

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

在此结构中指定的投影类型确定了知识存储使用的存储类型。

+ 定义 `tables` 时将使用表存储。 如果需要使用表格式报告结构作为分析工具的输入或者要以数据帧的形式导出到其他数据存储，请定义表投影。 可以指定多个 `tables`，以获取扩充文档的子集或截面。 在同一投影组内，将会保留表关系，以便可以使用所有表。

+ 定义 `objects` 或 `files` 时，将使用 Blob 存储。 `object` 的物理表示形式是一个表示扩充文档的分层 JSON 结构。 `file` 是从文档中提取的图像，它按原样传输到 Blob 存储。

单个投影对象包含一组 `tables`、`objects`、`files`，在许多情况下，创建一个投影可能就已足够。 

但是，可以创建多组`table`-`object`-`file` 投影，如果需要不同的数据关系，也可以这样做。 在某个集内，数据是相关的，前提是这些关系存在并且可以检测到它们。 如果创建更多的集，每个组中的文档将永远不相关。 例如，如果你希望将相同的投影数据用于联机系统，而这些数据需要以特定的方式表示，同时，你还希望将相同的投影数据用于以不同方式表示的数据科学管道，则可以使用多个投影组。

## <a name="requirements"></a>要求 

需要 [Azure 存储](../storage/index.yml)。 它提供物理存储。 可以使用 Blob 存储和/或表存储。 Blob 存储用于未修改的扩充文档，通常是当输出要转移到下游过程时使用。 表存储用于扩充文档的切片，通常用于分析和报告。

需要[技能集](cognitive-search-working-with-skillsets.md)。 它包含 `knowledgeStore` 定义，并确定扩充文档的结构和构成部分。 不能使用空技能集创建知识存储。 必须在技能集中至少包含一个技能。

需要[索引器](search-indexer-overview.md)。 技能集由索引器调用，以驱动执行。 索引器附带自身的一组要求和属性。 其中的多个属性直接影响知识存储：

+ 索引器需要[受支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)（最终创建知识存储的管道首先从 Azure 支持的源提取数据）。 

+ 索引器需要搜索索引。 索引器要求提供索引架构，即使你从未打算使用该架构。 最低要求的索引是一个字符串字段，指定为键。

+ 索引器提供可选的字段映射，用于将源字段设置为目标字段的别名。 如果默认字段映射需要修改（以使用不同的名称或类型），可以在索引器中创建[字段映射](search-indexer-field-mappings.md)。 对于知识存储输出，目标可以是 Blob 对象或表中的字段。

+ 索引器具有计划和其他属性（例如各种数据源提供的更改检测机制），也可应用于知识存储。 例如，可以按固定的间隔[计划](search-howto-schedule-indexers.md)扩充以刷新内容。 

## <a name="how-to-create-a-knowledge-store"></a>如何创建知识存储

若要创建知识存储，请使用门户或 REST API (`api-version=2020-06-30`)。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

“导入数据”向导包含用于创建知识存储的选项。 若要进行初始探索，请[通过四个步骤创建第一个知识存储](knowledge-store-connect-power-bi.md)。

1. 选择支持的数据源。

1. 指定扩充：附加资源，选择技能，并指定知识存储。 

1. 创建索引架构。 向导需要该架构，并可以推断一个架构。

1. 运行向导。 提取、扩充和存储操作在此最后一个步骤中发生。

### <a name="use-create-skillset-rest-api"></a>使用“创建技能组”(REST API)

`knowledgeStore` 是在[技能集](cognitive-search-working-with-skillsets.md)中定义的，后者又由[索引器](search-indexer-overview.md)调用。 在扩充期间，Azure 认知搜索会在 Azure 存储帐户中创建一个空间，并根据配置将扩充文档投影到 Blob 或表。

REST API 是一种可以通过编程方式创建知识存储的机制。 一种简单的探索方法是[使用 Postman 和 REST API 创建第一个知识存储](knowledge-store-create-rest.md)。

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>如何连接工具和应用

只要扩充存在于存储中，连接到 Azure Blob 存储或表存储的任何工具或技术，都可用于浏览、分析或使用内容。 请从以下列表入手：

+ [存储资源管理器](knowledge-store-view-storage-explorer.md)：用于查看扩充文档的结构和内容。 将这视为用于查看知识存储内容的基线工具。

+ 用于报告和分析的 [Power BI](knowledge-store-connect-power-bi.md)。 

+ [Azure 数据工厂](../data-factory/index.yml)：用于进一步操作。

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API 参考

REST API 版本 `2020-06-30` 通过技能集中的附加定义提供知识存储。 除了参考资料以外，另请参阅[使用 Postman 创建知识存储](knowledge-store-create-rest.md)来了解有关如何调用 API 的详细信息。

+ [创建技能组 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [更新技能组 (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>后续步骤

知识存储提供扩充文档的持久性，在设计技能集，或者在创建新的结构和内容供可访问 Azure 存储帐户的任何客户端应用程序使用时，知识存储非常有用。

创建扩充文档的最简单方法是[使用门户](knowledge-store-create-portal.md)，但也可以使用 Postman 和 REST API，想要深入了解对象的创建和引用方式时，后者更有用。

> [!div class="nextstepaction"]
> [使用 Postman 和 REST 创建知识存储](knowledge-store-create-rest.md)

详细了解投影、相关功能以及[在技能组中对其进行定义](knowledge-store-projection-overview.md)的方法

> [!div class="nextstepaction"]
> [知识存储中的投影](knowledge-store-projection-overview.md)

有关涵盖高级投影概念（如切片、内联整形和关系）的教程，请先参阅[在知识存储中定义投影](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [在知识存储中定义投影](knowledge-store-projections-examples.md)