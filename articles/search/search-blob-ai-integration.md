---
title: 使用 AI 了解 Blob 数据
titleSuffix: Azure Search
description: 在 Azure 搜索中使用 AI 扩充管道将语义、自然语言处理和图像分析添加到 Azure blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 192d1a7b3bb10395aa662a4b915fe0189b1306b5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434034"
---
# <a name="use-ai-to-understand-blob-data"></a>使用 AI 了解 Blob 数据

Azure Blob 存储中的数据通常是多种非结构化内容，例如图像、长文本、Pdf 和 Office 文档。 通过使用 Azure 搜索中的 AI 功能，你可以通过多种方式了解并从 blob 中提取有价值的信息。 将 AI 应用于 blob 内容的示例包括：

+ 使用光学字符识别（OCR）从图像中提取文本
+ 从照片生成场景说明或标记
+ 检测语言并将文本转换为不同的语言
+ 用命名实体识别（NER）处理文本，查找对人员、日期、地点或组织的引用 

尽管你可能只需要其中的一种 AI 功能，但通常会将其中的多个转换为同一管道（例如，从扫描的图像中提取文本，然后查找其中所引用的所有日期和位置）。 

AI 扩充创建作为文本捕获的新信息，并将其存储在字段中。 扩充后，可以通过全文搜索从搜索索引访问此信息，或将丰富的文档发送回 Azure 存储，以提供新的应用程序体验，包括探索用于发现或分析方案的数据。 

在本文中，我们将通过宽镜头查看 AI 扩充，使你可以快速了解整个过程，即从将 blob 中的原始数据转换为搜索索引或知识存储中的可查询信息。

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>用 AI "充实" blob 数据意味着什么？

*AI 扩充*是 Azure 搜索的索引体系结构的一部分，它将内置 ai 与你提供的 Microsoft 或自定义 ai 相集成。 它可帮助实现端到端方案，在这些方案中，你需要处理 blob （现有的 blob 和新的 blob，因为它们是在更新时）、使用各种 AI 功能提取所需的信息，以及在 Azure 搜索索引中编制索引，以便快速搜索、检索和浏览。 

输入是在 Azure Blob 存储中的单个容器中的 blob。 Blob 几乎可以是任何类型的文本或图像数据。 

输出始终是一个 Azure 搜索索引，用于在客户端应用程序中快速搜索、检索和浏览。 此外，输出还可以是一个*知识存储*，它将文档扩充到 azure Blob 或 azure 表中，以便在 Power BI 或数据科学工作负荷之类的工具中进行下游分析。

介于之间为管道体系结构本身。 管道基于*索引器*功能，可以为其分配*技能组合*，该功能由提供 AI 的一项或多项*技能*组成。 管道的用途是生成*丰富的文档*，这些文档输入作为原始内容，但通过管道移动时选取其他结构、上下文和信息。 在编制索引期间，会使用已扩充的文档，以创建要在全文搜索或探索和分析中使用的反转索引和其他结构。

## <a name="start-with-services-and-data"></a>从服务和数据开始

需要 Azure 搜索和 Azure Blob 存储。 在 Blob 存储中，需要一个提供源内容的容器。

你可以直接在存储帐户门户页中开始。 在左侧导航页的 " **Blob 服务**" 下，单击 "**添加 Azure 搜索**" 以创建新服务或选择现有服务。 

将 Azure 搜索添加到你的存储帐户后，你可以遵循标准过程来丰富任何 Azure 数据源中的数据。 建议在 Azure 搜索中使用 "**导入数据**" 向导，简单了解 AI 扩充。 本快速入门介绍了[在门户中创建 AI 扩充管道](cognitive-search-quickstart-blob.md)的步骤： 

在以下部分中，我们将探讨更多组件和概念。

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

AI 扩充是索引管道的外接程序，在 Azure 搜索中，这些管道建立在*索引器*之上。 索引器是一个数据源感知子查询，它具有用于采样数据、读取元数据、检索数据和将本机格式的数据序列化为 JSON 文档以供后续导入的内部逻辑。 索引器通常单独使用索引器进行导入，与 AI 分离，但如果想要生成 AI 扩充管道，则需要使用索引器和技能组合。 本部分重点介绍索引器;下一节重点介绍技能集。

Azure 存储中的 blob 使用[Azure 搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)进行索引。 您可以使用**导入数据**向导、REST API 或 .net SDK 来调用此索引器。 在代码中，可以通过设置类型来使用此索引器，还可以提供包含 Azure 存储帐户和 blob 容器的连接信息。 您可以通过创建虚拟目录子集化 blob，然后可以将该虚拟目录作为参数传递，也可以通过筛选文件类型扩展。

索引器执行 "文档破解"，打开一个 blob 来检查内容。 连接到数据源后，它是管道中的第一步。 对于 blob 数据，将在其中检测到 PDF、office 文档、图像和其他内容类型。 带有文本提取的文档破解是免费的。 采用图像提取的文档破解按照您可以在 Azure 搜索[定价页](https://azure.microsoft.com/pricing/details/search/)中找到的费率收费。

尽管所有文档都将被破译，但仅当您显式提供了执行此操作的技巧时才会发生扩充。 例如，如果管道只包含图像分析，则容器或文档中的文本将被忽略。

Blob 索引器附带了配置参数，并且如果基础数据提供了足够的信息，则支持更改跟踪。 可以在[Azure 搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)中了解有关核心功能的详细信息。

## <a name="add-ai-components"></a>添加 AI 组件

AI 扩充是指查找模式或特征的模块，然后相应地执行操作。 照片中的面部识别，照片的文本说明，检测文档中的关键短语，OCR （或在二进制文件中识别打印文本或手写文本）是演示示例。

在 Azure 搜索中，*技能*是 AI 处理的单个组件，可以单独使用，也可以与其他技能结合使用。 

+ 内置技能由认知服务提供支持，并基于基于计算机视觉的图像分析和基于文本分析的自然语言处理。 可在[内容扩充的预定义技能](cognitive-search-predefined-skills.md)中查看内置技能的完整列表。

+ 自定义技能是在允许集成到管道的[接口定义](cognitive-search-custom-skill-interface.md)中的自定义代码。 在客户解决方案中，通常同时使用这两种方法，同时提供自定义技能，提供开源、第三方或第一方 AI 模块。

*技能组合*是管道中使用的技能的集合，在 "文档破解" 阶段提供内容后调用。 索引器可以仅使用一个技能组合，但该技能组合独立于索引器存在，因此你可以在其他情况下重复使用它。

自定义技能可能会很复杂，但在实现方面非常简单直接。 如果现有的包提供模式匹配或分类模型，则可以将从 blob 中提取的内容传递到这些模型进行处理。 由于 AI 扩充是基于 Azure 的，因此模型也应在 Azure 上。 一些常见的宿主方法包括使用[Azure Functions](cognitive-search-create-custom-skill-example.md)或[容器](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。

认知服务支持的内置技能需要一个附加的[认知服务](cognitive-search-attach-cognitive-services.md)多个订阅密钥，使你能够访问资源。 单个键提供图像分析、语言检测、文本翻译和文本分析。 其他内置技能是 Azure 搜索的功能，不需要其他服务或密钥。 文本整形器、拆分器和合并是在设计管道时有时很有必要的帮助程序技能的示例。

如果仅使用自定义技能和内置实用工具技能，则没有与认知服务相关的依赖关系或成本。

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>在下游解决方案中使用 AI 丰富的输出

AI 扩充的输出是 Azure 搜索上的搜索索引，或 Azure 存储中的[知识存储](knowledge-store-concept-intro.md)。

在 Azure 搜索中，搜索索引用于在客户端应用中使用自由文本和筛选查询进行交互式浏览。 通过 AI 创建的已扩充文档采用 JSON 格式，并以在 Azure 搜索中编制索引的相同方式编制索引，利用索引器提供的所有权益。 例如，在索引过程中，blob 索引器将引用配置参数和设置以利用任何字段映射或更改检测逻辑。 此类设置完全可用于常规索引和 AI 负载更丰富的工作负载。 索引后，当内容存储在 Azure 搜索中时，可以生成丰富的查询和筛选表达式来了解内容。

在 Azure 存储中，知识存储有两个表现形式：一个 blob 容器或表存储中的表。 

+ Blob 容器完整地捕获已扩充的文档，这在要送入其他进程时非常有用。 

+ 与此相反，表存储可以容纳大量文档的物理投影。 你可以创建包含或排除特定部分的丰富文档的切片或层。 为了在 Power BI 中进行分析，Azure 表存储中的表将成为数据源，以便进行进一步的可视化和探索。

管道末尾的扩充文档与其原始输入版本不同，因为存在包含在扩充过程中提取或生成的新信息的附加字段。 因此，无论使用哪种输出结构，都可以结合使用原始内容和创建的内容。

## <a name="next-steps"></a>后续步骤

使用 AI 扩充可以更多地充分利用 Azure 存储空间中的数据，包括以不同方式合并认知服务，以及在不存在方案的现有认知服务的情况下创作自定义技能。 可以通过以下链接了解更多信息。

> [!div class="nextstepaction"]
> [AI 扩充概述](cognitive-search-concept-intro.md) 
> [在批注树中](cognitive-search-output-field-mapping.md)[创建技能组合](cognitive-search-defining-skillset.md)
>  映射节点
