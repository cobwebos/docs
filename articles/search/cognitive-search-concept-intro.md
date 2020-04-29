---
title: AI 扩充简介
titleSuffix: Azure Cognitive Search
description: 内容提取、自然语言处理（NLP）和图像处理用于使用预定义的认知技能和自定义 AI 算法在 Azure 认知搜索索引中创建可搜索的内容。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283015"
---
# <a name="getting-started-with-ai-enrichment"></a>AI 扩充入门

AI 扩充是一种 Azure 认知搜索索引功能，用于从图像、blob 和其他非结构化数据源提取文本。 扩充和提取使你的内容在[索引](search-what-is-an-index.md)或[知识存储](knowledge-store-concept-intro.md)中更具可搜索性。 使用附加到索引管道的*认知技能*实现提取和扩充。 服务内置的认知技能分为以下几类： 

+ “自然语言处理”技能包括[实体识别](cognitive-search-skill-entity-recognition.md)、[语言检测](cognitive-search-skill-language-detection.md)、[关键短语提取](cognitive-search-skill-keyphrases.md)、文本操作、[情绪检测](cognitive-search-skill-sentiment.md)和 [PII 检测](cognitive-search-skill-pii-detection.md)****。 利用这些技能，非结构化文本将映射为索引中的可搜索和可筛选字段。

+ **图像处理**技能包括[光学字符识别（OCR）](cognitive-search-skill-ocr.md)和[可视功能](cognitive-search-skill-image-analysis.md)的识别，如面部检测、图像解释、图像识别（著名人物和特征点）或图像方向等属性。 这些技能创建图像内容的文本表示形式，使其能够使用 Azure 认知搜索的查询功能进行搜索。

![扩充管道关系图](./media/cognitive-search-intro/cogsearch-architecture.png "扩充管道概述")

Azure 认知搜索中的认知技能基于认知服务 API 中预先训练的机器学习模型：[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)和[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)。 

数据引入阶段应用了自然语言和图形处理，其结果会成为 Azure 认知搜索的可搜索索引中文档撰写内容的一部分。 数据作为 Azure 数据集的来源，然后使用任意所需的[内置技能](cognitive-search-predefined-skills.md)通过索引管道进行推送。 体系结构可扩展，因此如果内置技能不足，可以创建并附加[自定义技能](cognitive-search-create-custom-skill-example.md)，以集成自定义处理。 示例包括面向特定领域（例如金融、科技出版或医疗）的自定义实体模块或文档分类器。

## <a name="when-to-use-ai-enrichment"></a>何时使用 AI 扩充

如果原始内容为非结构化文本、图像内容或需要语言检测和翻译的内容，则应考虑使用内置认知技能。 通过内置认知技能应用 AI，可以对此内容进行解锁，在搜索和数据科学应用中提高其价值和实用性。 

此外，如果你有要集成到管道中的开源、第三方或第一方代码，则可以考虑添加自定义技能。 标识各种文档类型的突出特征的分类模型属于此类别，但是可以使用将值添加到内容的任何包。

### <a name="more-about-built-in-skills"></a>有关内置技能的详细信息

使用内置技能汇编的[技能组合](cognitive-search-defining-skillset.md)非常适合以下应用程序方案：

+ 需要对其启用全文搜索的已扫描文档 (JPEG)。 可以附加光学字符识别 (OCR) 技能，以便标识、提取和引入 JPEG 文件中的文本。

+ 组合使用图像和文本的 PDF。 PDF 中的文本可以在索引期间提取，不需使用扩充步骤，但在添加图像并进行自然语言处理的情况下，所产生的结果通常比标准索引提供的结果要好。

+ 需对其应用语言检测并可能对其应用文本翻译的多语言内容。

+ 非结构化或半结构化的文档，其中包含的内容有固有的含义，或者其上下文隐藏在更大的文档中。 

  具体说来，Blob 通常包含大量的内容，这些内容打包到单个“字段”中。 将图像和自然语言处理技能附加到索引器以后，即可创建新信息，该信息存在于原始内容中，但在其他情况下并不显示为非重复字段。 某些对你有帮助的可用内置认知技能：关键短语提取、情绪分析、实体识别（人、组织和位置）。

  另外，内置技能还可以用来通过文本拆分、合并和形状操作来重新构造内容。

### <a name="more-about-custom-skills"></a>有关自定义技能的详细信息

自定义技能可以支持更复杂的方案，例如识别表单，或者使用你提供的模型进行自定义实体检测，以及在[自定义技能 Web 界面](cognitive-search-custom-skill-interface.md)中进行包装。 自定义技能的一些示例：[表单识别器](/azure/cognitive-services/form-recognizer/overview)、集成[必应实体搜索 API](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)、[自定义实体识别](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。


## <a name="steps-in-an-enrichment-pipeline"></a>扩充管道中的步骤

扩充管道基于[*索引器*](search-indexer-overview.md)。 索引器根据索引和数据源之间的字段到字段映射填充用于文档破解的索引。 技能现在附加到索引器，根据您定义的技能组合来截获和丰富文档。 编制索引后，可以使用所有[受 Azure 认知搜索支持的查询类型](search-query-overview.md)通过搜索请求来访问内容。  本部分引导索引器的新手完成这些步骤。

### <a name="step-1-connection-and-document-cracking-phase"></a>步骤1：连接和文档破解阶段

管道开始时，有非结构化文本或非文本内容（如图像、扫描的文档或 JPEG 文件）。 数据必须存在于可由索引器访问的 Azure 数据存储服务中。 索引器可以“破解”源文档，以从源数据提取文本。 文档破解是在索引期间从非文本源提取或创建文本内容的过程。

![文档破解阶段](./media/cognitive-search-intro/document-cracking-phase-blowup.png "文档破解")

 支持的源包括 Azure Blob 存储、Azure 表存储、Azure SQL 数据库和 Azure Cosmos DB。 可从以下文件类型提取基于文本的内容：PDF、Word、PowerPoint、CSV 文件。 有关完整列表，请参阅[支持的格式](search-howto-indexing-azure-blob-storage.md#supported-document-formats)。 编制索引需要花费一定的时间，因此请从较少的有代表性数据集着手，然后随着解决方案的不断成熟，逐渐增加数据集的大小。

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>步骤2：认知技能和扩充阶段

扩充是通过执行原子操作的*认知技能*来执行的。 例如，在解开 PDF 后，可以应用实体识别、语言检测或关键短语提取，以生成索引中不在源中本机可用的新字段。 管道中使用的技能的集合统称为技能集。**  

![扩充阶段](./media/cognitive-search-intro/enrichment-phase-blowup.png "扩充阶段")

技能集基于你提供的、与该技能集连接的[内置认知技能](cognitive-search-predefined-skills.md)或[自定义技能](cognitive-search-create-custom-skill-example.md)。 技能集既可以很精简，也可以很复杂，它不仅确定处理的类型，而且还确定运算的顺序。 技能集以及定义为索引器一部分的字段映射全面指定扩充管道。 有关将所有组成部分一起提取的详细信息，请参阅[定义技能集](cognitive-search-defining-skillset.md)。

在内部，管道生成扩充文档的集合。 可以确定要将扩充文档的哪些部分映射到搜索索引中可编制索引的字段。 例如，如果应用了关键短语提取和实体识别技能，则这些新字段将成为已扩充文档的一部分，并且可以映射到索引上的字段。 请参阅[注释](cognitive-search-concept-annotations-syntax.md)详细了解输入/输出的形成。

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>添加用于保存扩充的 knowledgeStore 元素

[搜索 REST api-version=2019-05-06-Preview](search-api-preview.md) 使用 `knowledgeStore` 定义扩展技能集。该定义提供 Azure 存储连接以及描述如何存储扩充的投影。 这是索引的补充。 在标准的 AI 管道中，扩充文档是临时的，仅在编制索引期间使用，然后被丢弃。 扩充文档将通过知识存储保存起来。 有关详细信息，请参阅[知识存储（预览版）](knowledge-store-concept-intro.md)。

### <a name="step-3-search-index-and-query-based-access"></a>步骤3：搜索索引和基于查询的访问

完成处理后，便会获得由扩充的文档组成的搜索索引，这些文档在 Azure 认知搜索中可全文搜索。 开发者和用户可以通过[查询索引](search-query-overview.md)来访问管道生成的扩充内容。 

![带搜索图标的索引](./media/cognitive-search-intro/search-phase-blowup.png "带搜索图标的索引")

索引类似于可为 Azure 认知搜索创建的其他任何对象：可以使用自定义分析器进行补充、调用模糊搜索查询、添加筛选的搜索结果，或试着使用评分配置文件为搜索结果重新整型。

索引从某个索引架构生成。该架构定义字段、属性，以及附加到特定索引的其他构造，例如评分配置文件和同义词映射。 定义并填充索引后，可以增量方式编制索引，以拾取新的和更新的源文档。 某些修改需要完全重新生成。 在架构设计稳定之前，应使用小型数据集。 有关详细信息，请参阅[如何重新生成索引](search-howto-reindex.md)。

**清单：典型工作流**

1. 将 Azure 源数据分解为代表性样本。 编制索引需要花费一定的时间，因此请从较少的有代表性数据集着手，然后随着解决方案的不断成熟，逐渐增加数据集的大小。

1. 在 Azure 认知搜索中创建[数据源对象](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，以便提供用于数据检索的连接字符串。

1. 使用扩充步骤创建[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。

1. 定义[索引架构](https://docs.microsoft.com/rest/api/searchservice/create-index)。 字段集合包含源数据中的字段**。 还应该抽出其他字段，以保存扩充期间创建的内容的生成值。

1. 定义引用数据源、技能集和索引的[索引器](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。

1. 在索引器中，添加 outputFieldMappings**。 此节将技能集的输出（步骤 3）映射到索引架构中的输入字段（步骤 4）。

1. 发送刚刚创建的“创建索引器”请求（一个 POST 请求，其请求正文包含索引器定义），用于表示 Azure 认知搜索中的索引器**。 通过此步骤运行索引器，并调用管道。

1. 运行查询以评估结果，并修改代码以更新技能集、架构或索引器配置。

1. 重新生成管道之前[重置索引器](search-howto-reindex.md)。

## <a name="next-steps"></a>后续步骤

+ [AI 扩充文档链接](cognitive-search-resources-documentation.md)
+ [示例：为 AI 扩充创建自定义技能（c #）](cognitive-search-create-custom-skill-example.md)
+ [快速入门：在门户演练中试用 AI 扩充](cognitive-search-quickstart-blob.md)
+ [教程：了解 AI 扩充 Api](cognitive-search-tutorial-blob.md)
+ [知识存储（预览版）](knowledge-store-concept-intro.md)
+ [在 REST 中创建知识存储](knowledge-store-create-rest.md)
+ [故障排除提示](cognitive-search-concept-troubleshooting.md)
