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
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331279"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>使用 Azure 搜索将全文搜索添加到 Azure blob 数据

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，只需单击几下鼠标，即可使用[Azure 搜索](search-what-is-azure-search.md)来索引和搜索 blob 的内容。 Azure 搜索提供内置集成，可通过将数据源感知功能添加到索引的[*blob 索引器*](search-howto-indexing-azure-blob-storage.md)来从 blob 存储中进行索引。

## <a name="supported-content-types"></a>支持的内容类型

通过在容器上运行 Blob 索引器，你可以使用单个查询从以下内容类型中提取文本和元数据：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

（可选）可以附加*技能组合*形式的[AI 扩充](search-blob-ai-integration.md)，以从 blob 创建新的信息和结构，包括图像文件的文本表示形式。 添加 AI 扩充将内容类型列表扩展为包含 JPEG 和 PNG。 它添加了图像处理技能，如[光学字符识别（OCR）](cognitive-search-skill-ocr.md)和[可视功能](cognitive-search-skill-image-analysis.md)的识别，这些功能使你可以为每个图像中的视觉对象建立索引。

## <a name="search-through-your-blob-metadata"></a>在 blob 元数据中进行搜索
用于实现在任何内容类型的 blob 中轻松进行排序的一个常用方案是为自定义元数据和每个 blob 的系统属性编制索引。 通过这种方式，将对所有 blob 的信息编制索引，而不管文档类型是什么，存储在 Azure 搜索的索引中。 然后，可以使用新索引在所有 Blob 存储内容中进行排序、筛选和分面。

### <a name="indexing-json-blobs"></a>为 JSON blob 编制索引
可以将 Azure 搜索配置为提取在包含 JSON 的 blob 中找到的结构化内容。 Azure 搜索可以读取 JSON blob 并将结构化内容解析为 Azure 搜索文档的合适字段。 Azure 搜索还可以获取包含 JSON 对象数组的 blob 并将每个元素映射到单独的 Azure 搜索文档。 可以设置分析模式，以影响索引器创建的 JSON 对象的类型。

## <a name="how-to-get-started"></a>如何入门

你可以直接在存储帐户门户页中开始。 单击“添加 Azure 搜索”将启动一个工作流，可以在其中选择现有 Azure 搜索服务或创建一个新服务。 如果创建新服务，则会离开存储帐户的门户。 可以导航回存储门户页并重新选择“添加 Azure 搜索”选项，然后可以选择现有服务。 

![](./media/search-blob-storage-integration/blob-blade.png)

如果在同一订阅中已经有一个现有搜索服务，则单击 "**添加 Azure 搜索**" 将打开 "导入数据" 向导，以便可以立即逐步完成索引、扩充和索引定义。

还可以[创建 Azure 搜索服务](search-create-index-portal.md)，并定义从 blob 容器提取内容的 blob 索引器。

以下快速入门和教程使用 Blob 数据：

+ [使用 REST Api 为半结构化 blob 编制索引](search-semi-structured-data.md)
+ [在门户中创建 AI 扩充管道](cognitive-search-quickstart-blob.md)
+ [在中创建 AI 扩充管道C#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置 blob 索引器](search-howto-indexing-azure-blob-storage.md) 
