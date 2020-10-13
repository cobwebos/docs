---
title: 使用 AI 了解 Blob 存储数据
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索中的自然语言和图像分析功能，以及这些进程如何适用于 Azure blob 中存储的内容。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: a0d32f00bd3c7f8daa2984bdc7c9b9dfb5add218
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362791"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>使用 AI 了解 Blob 存储数据

Azure Blob 存储中的数据通常是各种非结构化内容，例如图像、长文本、PDF 和 Office 文档。 使用 Azure 认知搜索中的 AI 功能，可以理解 Blob 中的有用信息，以及通过多种方式提取这些信息。 将 AI 应用于 Blob 内容的示例包括：

+ 使用光学字符识别 (OCR) 从图像中提取文本
+ 基于照片生成场景说明或标记
+ 检测语言，将文本翻译成不同的语言
+ 通过实体识别查找对人员、日期、地点或组织的引用来推断结构

尽管你可能只需要其中的一项 AI 功能，但常见的做法是将其中的多项功能组合到同一管道中（例如，从扫描的图像中提取文本，然后查找文本中引用的所有日期和地点）。 此外，还可以采用领先的外部包的形式提供自定义 AI 或机器学习处理，也可以根据您的数据和您的需求定制内部模型。

AI 扩充将创建新信息，这些信息以文本的形式捕获，并存储在字段中。 扩充后，可以通过全文搜索从搜索索引访问此信息，或者将扩充的文档发回到 Azure 存储，以便为新的应用体验提供支持，包括探索用于发现或分析方案的数据。 

在本文中，我们将从一个宽广的角度审视 AI 扩充，以便快速掌握整个过程：将 Blob 中的原始数据转换为搜索索引或知识存储中的可查询信息。

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>使用 AI“扩充”Blob 数据是什么意思？

AI 扩充是 Azure 认知搜索的索引编制体系结构的一部分。认知搜索服务集成了 Microsoft 提供的内置 AI 或者客户提供的自定义 AI。  AI 扩充可帮助实现端到端的方案，在其中，你需要处理 Blob（传入的或更新的现有 Blob 和新 Blob），破解所有文件格式以提取图像和文本，使用各种 AI 功能提取所需的信息，在搜索索引中为这些信息编制索引以便快速执行搜索、检索和浏览。 

输入是 Azure Blob 存储中单个容器内的 Blob。 Blob 几乎可以是任何类型的文本或图像数据。 

输出始终是搜索索引，用于在客户端应用程序中快速执行搜索、检索和浏览。 此外，输出还可以是一个 [*知识存储*](knowledge-store-concept-intro.md) ，它将文档扩充到 azure Blob 或 azure 表中，以便在 Power BI 或数据科学工作负荷之类的工具中进行下游分析。

输入与输出之间是管道体系结构本身。 管道基于索引器功能。可将技能集分配到索引器。技能集由一个或多个提供 AI 的技能构成。    管道的用途是生成作为原始内容输入的扩充文档，但在切换不同的管道阶段时，它还会拾取其他结构、上下文和信息。  在编制索引期间，将使用扩充文档来创建要在全文搜索或者浏览和分析中使用的倒排索引与其他结构。

## <a name="required-resources"></a>所需资源

需要使用 Azure Blob 存储、Azure 认知搜索和提供 AI 的第三种服务或机制：

+ 对于内置 AI，认知搜索与 Azure 认知服务视觉和自然语言处理 Api 集成。 可以 [附加认知服务资源](cognitive-search-attach-cognitive-services.md) 来添加光学字符识别 (OCR) 、图像分析或自然语言处理 (语言检测、文本翻译、实体识别、关键短语提取) 。 

+ 对于使用 Azure 资源的自定义 AI，你可以定义用于包装要使用的外部函数或模型的自定义技能。 [自定义技能](cognitive-search-custom-skill-interface.md) 可以使用 Azure Functions、Azure 机器学习、Azure 窗体识别器提供的代码，也可以使用通过 HTTPS 可访问的其他资源。

+ 对于自定义的非 Azure AI，需要通过 HTTP 索引器访问模型或模块。

如果你没有现成的所有服务可用，请直接在存储帐户门户页中开始。 在左侧导航页中的“Blob 服务”下，单击“添加 Azure 认知搜索”创建新服务或选择现有服务。   

将 Azure 认知搜索添加到存储帐户后，可以遵循标准过程来扩充任何 Azure 数据源中的数据。 我们建议通过 Azure 认知搜索中的“导入数据”向导轻松完成 AI 扩充的初次体验。  您可以在工作流中附加认知服务资源。 本快速入门将指导你完成以下步骤：[在门户中创建 AI 扩充管道](cognitive-search-quickstart-blob.md)。 

以下各节将详细介绍组件和工作流。

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

AI 扩充是索引管道的一个加载项，在 Azure 认知搜索中，这些管道构建在索引器的基础之上。  索引器是数据源感知的子服务，其中配备了内部逻辑用于对数据采样、读取元数据、检索数据，以及将数据从本机格式序列化为 JSON 文档供以后导入。 索引器独立于 AI，我们往往单独使用它来执行导入，但是，若要生成 AI 扩充管道，则还需要附带分配索引器和技能集。 本部分重点介绍索引器；下一部分重点介绍技能集。

使用 [blob 索引器](search-howto-indexing-azure-blob-storage.md)对 Azure 存储中的 blob 编制索引。 您可以使用 **导入数据** 向导、REST API 或 SDK 来调用此索引器。 如果索引器使用的数据源是 Azure Blob 容器，则会调用 blob 索引器。 可以通过创建虚拟目录来索引 blob 的子集，然后可以将该虚拟目录作为参数传递，也可以通过筛选文件类型扩展名来进行。

索引器执行“文档破解”，会打开一个 Blob 来检查内容。 这是连接到数据源后，在管道中发生的第一个步骤。 对于 Blob 数据，此步骤会检测 PDF、Office 文档、图像和其他内容类型。 文档破解和文本提取是免费的。 使用图像提取进行文档破解将按照[定价页](https://azure.microsoft.com/pricing/details/search/)中的费率收费。

尽管会破解所有文档，但仅当显式提供了用于扩充的技能时，才会发生扩充。 例如，如果管道只包含图像分析，则会忽略容器或文档中的文本。

Blob 索引器附带了配置参数，并且如果基础数据提供了足够的信息，则支持更改跟踪。 有关详细信息，请参阅 [如何配置 blob 索引器](search-howto-indexing-azure-blob-storage.md)。

## <a name="add-ai-components"></a>添加 AI 组件

AI 扩充是指查找模式或特征的模块，然后相应地执行操作。 代表性的示例包括识别照片中的人脸、识别照片的文本说明、检测文档中的关键短语，以及 OCR（或者识别二进制文件中的打印文本或手写文本）。

在 Azure 认知搜索中，技能是可以单独使用，或者与其他技能结合使用的各个 AI 处理组件。  

+ 内置技能由认知服务提供支持，其中，图像分析基于计算机视觉，自然语言处理基于文本分析。 有关完整列表，请参阅[用于内容扩充的内置技能](cognitive-search-predefined-skills.md)。

+ 自定义技能是允许集成到管道的[接口定义](cognitive-search-custom-skill-interface.md)中封装的自定义代码。 在客户解决方案中，常见的做法是同时使用内置技能和自定义技能，让自定义技能提供开源的第三方或第一方 AI 模块。

技能集是在管道中使用的技能的集合，文档破解阶段完成后，将调用技能集来提供内容。  一个索引器可以正好使用一个技能集，但该技能集独立于索引器，因此可以在其他方案中重复使用该技能集。

自定义技能听起来很复杂，但它的实现可能非常简单直接。 如果你通过现有的包提供模式匹配或分类模型，可将从 Blob 提取的内容传递给这些模型进行处理。 由于 AI 扩充基于 Azure，因此模型也应该在 Azure 上。 一些常用的托管方法包括使用 [Azure Functions](cognitive-search-create-custom-skill-example.md) 或[容器](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)。

认知服务支持的内置技能需要一个[附加的认知服务](cognitive-search-attach-cognitive-services.md)一体式订阅密钥，使你能够访问资源。 使用一体式密钥可以完成图像分析、语言检测、文本翻译和文本分析。 其他内置技能属于 Azure 认知搜索的功能，不需要额外的服务或密钥。 文本整形器、拆分器和合并器是设计管道时有时需要用到的帮助器技能的示例。

如果仅使用自定义技能和内置实用工具技能，则不存在依赖关系，也不会产生认知服务相关的成本。

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>在下游解决方案中使用 AI 扩充的输出

AI 扩充的输出是 Azure 认知搜索中的搜索索引，或者是 Azure 存储中的[知识存储](knowledge-store-concept-intro.md)。

在 Azure 认知搜索中，搜索索引用于在客户端应用中通过自由文本和筛选的查询进行交互式浏览。 通过 AI 创建的扩充文档采用 JSON 格式，其索引编制方式与在 Azure 认知搜索中为所有文档编制索引的方式相同，利用索引器提供的所有优势。 例如，在索引编制期间，Blob 索引器将引用配置参数和设置来利用任何字段映射或更改检测逻辑。 此类设置完全可供常规索引编制和 AI 扩充工作负荷使用。 编制索引后，将内容存储在 Azure 认知搜索中时，可以生成丰富的查询和筛选表达式来了解内容。

在 Azure 存储中，知识存储有两种表现形式：Blob 容器，或表存储中的表。 

+ Blob 容器捕获整个扩充文档，这很有利于将数据馈送到其他过程。 

+ 相比之下，表存储可以容纳扩充文档的物理投影。 可以创建包含或排除特定部分的扩充文档的切片或层。 在 Power BI 中分析时，Azure 表存储中的表将成为用于进一步可视化和浏览的数据源。

位于管道末端的扩充文档与其原始输入版本不同，其中存在额外的字段，这些字段包含扩充期间提取或生成的新信息。 因此，无论使用哪种输出结构，都可以结合使用原始内容和创建的内容。

## <a name="next-steps"></a>后续步骤

在充分利用 Azure 存储中的数据方面，AI 扩充的作用还有很多，包括以不同的方式组合认知服务，以及在没有任何现有认知服务能够解决所需的方案时创作自定义技能。 可通过以下链接了解详细信息。

+ [使用 Azure 门户上传、下载和列出 Blob（Azure Blob 存储）](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [设置 Blob 索引器（Azure 认知搜索）](search-howto-indexing-azure-blob-storage.md) 
+ [AI 扩充概述（Azure 认知搜索）](cognitive-search-concept-intro.md) 
+ [创建技能集（Azure 认知搜索）](cognitive-search-defining-skillset.md)
+ [映射注释树中的节点（Azure 认知搜索）](cognitive-search-output-field-mapping.md)