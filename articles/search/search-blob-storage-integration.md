---
title: 将全文搜索添加到 Azure Blob 存储
titleSuffix: Azure Search
description: 在 Azure 搜索中生成全文搜索索引时，提取内容并将结构添加到 Azure blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312186"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>使用 Azure 搜索将全文搜索添加到 Azure blob 数据

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，只需单击几下鼠标，即可使用[Azure 搜索](search-what-is-azure-search.md)来索引和搜索 blob 的内容。 Azure 搜索提供内置集成，可通过将数据源感知功能添加到索引的[*blob 索引器*](search-howto-indexing-azure-blob-storage.md)来从 blob 存储中进行索引。

## <a name="supported-content-types"></a>支持的内容类型

通过在容器上运行 Blob 索引器，你可以使用单个查询从以下内容类型中提取文本和元数据：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

（可选）可以附加*技能组合*形式的[AI 扩充](search-blob-ai-integration.md)，以从 blob 创建新的信息和结构，包括图像文件的文本表示形式。 添加 AI 扩充将内容类型列表扩展为包含 JPEG 和 PNG。 它添加了图像处理技能，如[光学字符识别（OCR）](cognitive-search-skill-ocr.md)和[可视功能](cognitive-search-skill-image-analysis.md)的识别，这些功能使你可以为每个图像中的视觉对象建立索引。

## <a name="search-through-your-blob-metadata"></a>在 blob 元数据中进行搜索
用于实现在任何内容类型的 blob 中轻松进行排序的一个常用方案是为自定义元数据和每个 blob 的系统属性编制索引。 通过这种方式，系统会对所有 blob 的信息编制索引，无论文档类型是什么，都是如此。 然后可以在所有 Blob 存储内容中继续执行排序、筛选和 facet 操作。

[了解有关为 blob 元数据编制索引的详细信息。](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>在 JSON blob 中编制索引和执行搜索
可以将 Azure 搜索配置为提取在包含 JSON 的 blob 中找到的结构化内容。 Azure 搜索可以读取 JSON blob 并将结构化内容解析为 Azure 搜索文档的合适字段。 Azure 搜索还可以获取包含 JSON 对象数组的 blob 并将每个元素映射到单独的 Azure 搜索文档。

JSON 解析当前不可通过门户进行配置。 [了解有关 Azure 搜索中的 JSON 解析的详细信息。](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>快速入门
可以直接从 Blob 存储门户页将 Azure 搜索添加到 blob。

![](./media/search-blob-storage-integration/blob-blade.png)

单击“添加 Azure 搜索”将启动一个工作流，可以在其中选择现有 Azure 搜索服务或创建一个新服务。 如果创建新服务，则会离开存储帐户的门户。 可以导航回存储门户页并重新选择“添加 Azure 搜索”选项，然后可以选择现有服务。

## <a name="next-steps"></a>后续步骤
在完整[文档](https://aka.ms/azsblobindexer)中详细了解 Azure 搜索 Blob 索引器。
