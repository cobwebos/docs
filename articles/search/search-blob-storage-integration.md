---
title: "为 Blob 存储添加 Azure 搜索 | Microsoft Docs"
description: "使用 Azure 搜索 HTTP REST API 在代码中创建索引。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>使用 Azure 搜索来搜索 Blob 存储

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，使用 Azure 搜索，只需单击几下，便可以为 Blob 中的内容编制索引以及对其进行搜索。 在 Blob 存储中进行搜索需要预配 Azure 搜索服务。 可以在[定价页](https://aka.ms/azspricing)上找到 Azure 搜索的各种服务限制和定价层。

## <a name="what-is-azure-search"></a>什么是 Azure 搜索？
[Azure 搜索](https://aka.ms/whatisazsearch)是一个搜索解决方案，它使得开发人员能够轻松为 Web 应用程序和移动应用程序添加可靠的全文搜索体验。 作为一项服务，Azure 搜索不需要管理任何搜索基础结构，同时提供了 [99.9% 运行时间 SLA](https://aka.ms/azuresearchsla)。

通过对 56 种语言的高级支持，Azure 搜索可以对内容进行分析并以智能方式处理特定于语言的构造。 Azure 搜索还提供了工具来打造丰富的搜索用户体验。 使用 Azure 搜索，可以很轻松地向用户界面添加功能，例如分面导航、typeahead 搜索建议以及命中突出显示。 若要了解 Azure 搜索的功能，可以阅读该服务的[文档](https://aka.ms/azsearchdocs)。

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>破解和搜索企业文档格式的内容
借助 Azure Blob 存储中提供的[文档提取](https://aka.ms/azsblobindexer)支持，Azure 搜索可以为 blob 中存储的各种文件类型编制索引：
- PDF
- Microsoft Office：DOCX/DOC、XLSX/XLS、PPTX/PPT、MSG（Outlook 电子邮件）
- HTML
- 纯文本文件

通过提取这些文件类型的文本和元数据，可以轻松通过单个查询在多种文件格式中进行搜索来查找最贴切的文档（无论是什么类型的）。 通过为 Microsoft Office 文档、PDF 和电子邮件的内容和元数据编制索引，使用 Blob 存储和 Azure 搜索可以构建可靠的企业内容管理解决方案。

## <a name="search-through-your-blob-metadata"></a>在 blob 元数据中进行搜索
用于实现在任何内容类型的 blob 中轻松进行排序的一个常用方案是为自定义的用户定义 blob 元数据以及每个 blob 的系统属性编制索引。 通过这种方式，无论 blob 中的文档是什么类型的，都将为每个 blob 的信息编制索引，以便你可以轻松对所有 Blob 存储内容进行排序和分面。

[了解有关为 blob 元数据编制索引的详细信息。](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>图像搜索
Azure 搜索的全文搜索、分面导航和排序功能现在可以应用于 blob 中存储的图像的元数据了。

如果使用 Microsoft 认知服务中的 [Computer Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api) 对这些图像进行了预先处理，则可以为在每个图像中找到的可视内容编制索引，包括 OCR 和手写识别。 我们正在致力于直接向 Azure 搜索中添加 OCR 和其他图像处理功能，如果你对这些功能感兴趣，请在我们的 [UserVoice](https://aka.ms/azsuv) 上提交请求或者[给我们发电子邮件](mailto:azscustquestions@microsoft.com)。

## <a name="index-and-search-through-json-blobs"></a>在 JSON blob 中编制索引和执行搜索
可以将 Azure 搜索配置为提取在包含 JSON 的 blob 中找到的结构化内容。 Azure 搜索可以读取 JSON blob 并将结构化内容解析为 Azure 搜索文档的合适字段。 Azure 搜索还可以获取包含 JSON 对象数组的 blob 并将每个元素映射到单独的 Azure 搜索文档。

请注意，JSON 解析当前不可通过门户进行配置。 [了解有关 Azure 搜索中的 JSON 解析的详细信息。](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>快速入门
可以直接从 Blob 存储门户边栏选项卡将 Azure 搜索添加到 blob。

![](./media/search-blob-storage-integration/blob-blade.png)

单击“添加 Azure 搜索”选项将启动一个工作流，你可以在其中选择现有 Azure 搜索服务或创建一个新服务。 如果创建新服务，则会离开存储帐户的门户。 你需要重新导航到存储门户边栏选项卡并重新选择“添加 Azure 搜索”选项，然后你可以选择现有服务。

### <a name="next-steps"></a>后续步骤
在完整[文档](https://aka.ms/azsblobindexer)中详细了解 Azure Search Blob 索引器。

