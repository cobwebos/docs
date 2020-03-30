---
title: 使用 AI 了解 Blob 存储数据
titleSuffix: Azure Cognitive Search
description: 使用 Azure 认知搜索中的 AI 扩充管道向 Azure blob 添加语义、自然语言处理和图像分析。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496430"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>使用 AI 了解 Blob 存储数据

Azure Blob 存储中的数据通常是各种非结构化内容，如图像、长文本、PDF 和 Office 文档。 通过使用 Azure 认知搜索中的 AI 功能，您可以通过多种方式从 Blob 中了解和提取有价值的信息。 将 AI 应用于 blob 内容的示例包括：

+ 使用光学字符识别 （OCR） 从图像中提取文本
+ 从照片中生成场景描述或标记
+ 检测语言并将文本翻译成不同的语言
+ 使用命名实体识别 （NER） 处理文本，以查找对人员、日期、地点或组织的引用 

虽然您可能只需要这些 AI 功能之一，但通常将多个功能合并到同一管道中（例如，从扫描的图像中提取文本，然后查找其中引用的所有日期和地点）。 

AI 扩充将创建存储在字段中的新信息（捕获为文本）。 扩充后，您可以通过全文搜索从搜索索引访问此信息，或者将丰富的文档发送回 Azure 存储，以为新的应用程序体验提供支持，其中包括探索发现或分析方案的数据。 

在本文中，我们通过宽镜头查看 AI 扩充，以便您可以快速掌握整个过程，从在 blob 中转换原始数据到搜索索引或知识存储中的可查询信息。

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>使用 AI"丰富"blob 数据意味着什么

*AI 扩充*是 Azure 认知搜索索引体系结构的一部分，该体系结构集成了来自 Microsoft 或您提供的自定义 AI 的内置 AI。 它可以帮助您实现端到端方案，其中您需要处理 Blob（现有 blob 和新 blob 在来或更新时），打开所有文件格式以提取图像和文本，使用各种 AI 功能提取所需的信息，以及索引它们在搜索索引中快速搜索、检索和探索。 

输入是 Azure Blob 存储中单个容器内的 Blob。 Blob 几乎可以是任何类型的文本或图像数据。 

输出始终是一个搜索索引，用于客户端应用程序中的快速文本搜索、检索和浏览。 此外，输出还可以是一*个知识存储，* 用于将富集文档项目项目到 Azure Blob 或 Azure 表中，以便以 Power BI 或数据科学工作负荷等工具进行下游分析。

中间是管道体系结构本身。 管道基于*索引器*功能，您可以为其分配*一个技能集，该技能集*由提供 AI 的一个或多个*技能*组成。 管道的目的是生成*丰富的文档*，这些文档以原始内容形式输入，但在通过管道时获取其他结构、上下文和信息。 在索引过程中使用丰富的文档，以创建用于全文搜索或探索和分析的反转索引和其他结构。

## <a name="start-with-services"></a>从服务开始

需要 Azure 认知搜索和 Azure Blob 存储。 在 Blob 存储中，需要一个提供源内容的容器。

可以直接在存储帐户门户页中开始。 在左侧导航页中的“Blob 服务”下，单击“添加 Azure 认知搜索”创建新服务或选择现有服务。******** 

将 Azure 认知搜索添加到存储帐户后，可以按照标准过程来丰富任何 Azure 数据源中的数据。 我们建议在 Azure 认知搜索中**导入数据**向导，以便轻松初始介绍 AI 充实。 此快速入门将引导您完成以下步骤：[在门户中创建 AI 扩充管道](cognitive-search-quickstart-blob.md)。 

在以下各节中，我们将探讨更多组件和概念。

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

AI 扩充是索引管道的加载项，在 Azure 认知搜索中，这些管道构建在*索引器*之上。 索引器是数据源感知的子服务，其中配备了内部逻辑用于对数据采样、读取元数据、检索数据，以及将数据从本机格式序列化为 JSON 文档供以后导入。 索引器通常由自身用于导入，与 AI 分开，但如果要构建 AI 扩充管道，则需要索引器和技能集来使用它。 本节重点介绍索引器;下一节将重点介绍技能集。

Azure 存储中的 Blob 使用 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)编制索引。 可以使用“导入数据”向导、REST API 或 .NET SDK 调用此索引器。**** 在代码中，使用此索引器的方式是设置类型，并提供包括 Azure 存储帐户和 Blob 容器的连接信息。 可以通过创建虚拟目录（随后可将其作为参数传递），或者筛选文件类型扩展名，来指定 Blob 的子集。

索引器执行“文档破解”，会打开一个 Blob 来检查内容。 这是连接到数据源后，在管道中发生的第一个步骤。 对于 Blob 数据，这是检测 PDF、办公室文档、图像和其他内容类型的位置。 文档破解和文本提取是免费的。 使用图像提取进行文档破解按您可以在[定价页面上](https://azure.microsoft.com/pricing/details/search/)找到的费率收费。

尽管所有文档都将被破解，但仅当您明确提供这样做的技能时，才会进行充实。 例如，如果管道完全由图像分析组成，则忽略容器或文档中的文本。

Blob 索引器附带配置参数，如果基础数据提供足够的信息，则索引器支持更改跟踪。 可以在 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)中详细了解核心功能。

## <a name="add-ai-components"></a>添加 AI 组件

AI 扩充是指查找模式或特征，然后相应地执行操作的模块。 照片中的面部识别、照片的文本描述、检测文档中的关键短语以及 OCR（或识别二进制文件中的打印或手写文本）都是说明性示例。

在 Azure 认知搜索中，*技能*是 AI 处理的各个组件，您可以单独使用，也可以与其他技能结合使用。 

+ 内置技能由认知服务提供支持，基于计算机视觉的图像分析基于文本分析，自然语言处理。 有关完整列表，请参阅[内容丰富的内置技能](cognitive-search-predefined-skills.md)。

+ 自定义技能是自定义代码，包装在允许集成到管道中的[接口定义](cognitive-search-custom-skill-interface.md)中。 在客户解决方案中，通常使用两者，具有提供开源、第三方或第一方 AI 模块的自定义技能。

*技能集*是管道中使用的技能的集合，在文档破解阶段使内容可用后调用它。 索引器可以完全使用一个技能集，但该技能集独立于索引器存在，以便在其他方案中重用它。

自定义技能听起来可能很复杂，但在实施方面可能简单明了。 如果现有包提供模式匹配或分类模型，则可以将从 Blob 中提取的内容传递给这些模型进行处理。 由于 AI 扩充基于 Azure，因此模型也应位于 Azure 上。 一些常见的托管方法包括使用[Azure 函数](cognitive-search-create-custom-skill-example.md)或[容器](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。

认知服务支持的内置技能需要[附加的认知服务](cognitive-search-attach-cognitive-services.md)一体式订阅密钥，以便您访问资源。 一体式密钥为您提供图像分析、语言检测、文本翻译和文本分析。 其他内置技能是 Azure 认知搜索的功能，无需其他服务或密钥。 文本拆分器、拆分器和合并是帮助程序技能的示例，这些技能有时在设计管道时是必需的。

如果您只使用自定义技能和内置实用程序技能，则不存在与认知服务相关的依赖项或成本。

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>在下游解决方案中消耗 AI 丰富的输出

AI 扩充的输出是 Azure 认知搜索上的搜索索引，或 Azure 存储中[的知识存储](knowledge-store-concept-intro.md)。

在 Azure 认知搜索中，搜索索引用于使用客户端应用中的免费文本和筛选查询进行交互式浏览。 通过 AI 创建的丰富文档在 JSON 中设置格式，并对所有文档在 Azure 认知搜索中编制索引的方式进行索引，利用索引器提供的所有优势。 例如，在索引期间，blob 索引器引用配置参数和设置以利用任何字段映射或更改检测逻辑。 此类设置完全可用于常规索引和 AI 富集工作负载。 索引后，当内容存储在 Azure 认知搜索上时，可以生成丰富的查询和筛选器表达式来了解内容。

在 Azure 存储中，知识存储有两种表现形式：blob 容器或表存储中的表。 

+ Blob 容器捕获富集文档的全部内容，如果要馈送到其他进程，这非常有用。 

+ 相反，表存储可以容纳富集文档的物理投影。 您可以创建包含或排除特定部件的富集文档切片或图层。 在 Power BI 中进行分析时，Azure 表存储中的表将成为用于进一步可视化和探索的数据源。

管道末尾的富集文档与其原始输入版本不同，因为存在包含在扩充过程中提取或生成的新信息的其他字段。 因此，您可以使用原始内容和创建内容的组合，而不管使用哪种输出结构。

## <a name="next-steps"></a>后续步骤

对于 Azure 存储中的大部分数据，您可以执行更多 AI 扩充功能，包括以不同的方式组合认知服务，以及为方案没有现有认知服务的情况编写自定义技能。 您可以按照以下链接了解更多信息。

+ [使用 Azure 门户上传、下载和列出 Blob（Azure Blob 存储）](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [设置 Blob 索引器（Azure 认知搜索）](search-howto-indexing-azure-blob-storage.md) 
+ [AI 扩充概述（Azure 认知搜索）](cognitive-search-concept-intro.md) 
+ [创建技能集（Azure 认知搜索）](cognitive-search-defining-skillset.md)
+ [注释树中的映射节点（Azure 认知搜索）](cognitive-search-output-field-mapping.md)
