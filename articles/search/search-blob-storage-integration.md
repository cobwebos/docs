---
title: 将全文搜索添加到 Azure Blob 存储
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中生成全文搜索索引时，提取内容并将结构添加到 Azure Blob。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fd405513f8bdef09e6d3ab996fc2c04bd397db13
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659440"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>使用 Azure 认知搜索将全文搜索添加到 Azure Blob 数据

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，使用 [Azure 认知搜索](search-what-is-azure-search.md)，只需单击几下，便可以为 Blob 中的内容编制索引以及对其进行搜索。 Azure 认知搜索具有内置集成功能，可通过 [Blob 索引器](search-howto-indexing-azure-blob-storage.md) 添加用于索引编制的数据源感知功能，从而为 Blob 存储创建索引。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>将全文搜索功能添加到 Blob 数据意味着什么

Azure 认知搜索是一种云搜索服务，提供索引和查询引擎，可对搜索服务上承载的用户定义的索引进行操作。 在云中使用查询引擎将可搜索的内容归置于一处，这对于实现性能是必要的，这样能以用户期望的搜索查询速度返回结果。

Azure 认知搜索在索引层与 Azure Blob 存储集成，将 Blob 内容作为搜索文档导入，并编制到反向索引和其他支持自由格式文本查询和筛选器表达式的查询结构中。 由于 Blob 内容已编制到搜索索引中，因此对 Blob 内容的访问可利用 Azure 认知搜索中的各种查询功能。

创建并填充索引后，该索引独立于 Blob 容器，但你可以重新运行索引操作，以便在基础容器更改后刷新索引。 各 Blob 的时间戳信息用于更改检测。 可以选择”计划执行”或”按需索引”作为刷新机制。

输入是 Azure Blob 存储中的单个容器中的 Blob。 Blob 几乎可以是任何类型的文本数据。 如果 blob 包含图像，可以将 [AI 扩充功能添加到 Blob 索引](search-blob-ai-integration.md)以便通过图像创建和提取文本。

输出始终为 Azure 认知搜索索引，用于在客户端应用程序中进行快速文本搜索、检索和浏览。 介于两者之间的是索引管道体系结构本身。 管道基于索引器功能，本文会对此进行进一步讨论。

## <a name="start-with-services"></a>从服务开始

你需要 Azure 认知搜索和 Azure Blob 存储。 在 Blob 存储中，需要一个提供源内容的容器。

可以直接在存储帐户门户页中开始操作。 在左侧导航页中的“Blob 服务”下，单击“添加 Azure 认知搜索”以创建新服务或选择现有服务 。 

将 Azure 认知搜索添加到存储帐户后，可以按照标准过程对 Blob 数据编制索引。 建议使用 Azure 认知搜索中的“导入数据”向导，以便获取简单的初始介绍，或使用 Postman 等工具调用 REST API。 本教程介绍和演示使用 Postman 调用 REST API 的步骤：[在 Azure 认知搜索中为半结构化数据 (JSON Blob) 编制索引以及搜索此类数据](search-semi-structured-data.md)。 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

索引器是一种数据源感知的子服务，具备用于实现后列操作的内部逻辑：对数据进行采样，读取元数据，检索数据，以及将数据从本机格式序列化为 JSON 文档以供后续导入。 

Azure 存储中的 Blob 使用 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)进行索引编制。 可以使用“导入数据”向导、REST API 或 .NET SDK 来调用此索引器。 在代码中，可以通过设置类型，也可以通过提供包含 Azure 存储帐户和 Blob 容器信息的连接信息，来使用此索引器。 可以通过创建虚拟目录（然后可以将其作为参数传递），也可以通过筛选文件类型扩展，实现 Blob 子集化。

索引器执行“文档破解”，会打开 Blob 检查内容。 连接到数据源后，这是管道中的第一步操作。 对于 Blob 数据，这是检测 PDF、office 文档和其他内容类型的阶段。 带有文本提取功能的文档破解是免费的。 如果 Blob 包含图像内容，该操作会忽略图像，除非[添加 AI 扩充](search-blob-ai-integration.md)。 标准索引仅适用于文本内容。

Blob 索引器附带了配置参数，并且如果基础数据提供了足够的信息，可支持更改跟踪。 可以详细了解 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)中的核心功能。

### <a name="supported-content-types"></a>支持的内容类型

通过在容器上运行 Blob 索引器，可以使用单个查询从以下内容类型中提取文本和元数据：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>为 Blob 元数据编制索引

用于实现在任何内容类型的 blob 中轻松进行排序的一个常用方案是为自定义元数据和每个 blob 的系统属性编制索引。 通过这种方式，系统会对所有 blob 的信息编制索引，而不考虑文档类型，并存储在搜索服务索引中。 通过使用新索引，可以在所有 Blob 存储内容中继续执行排序、筛选和 facet 操作。

> [!NOTE]
> Blob 存储服务会编制 Blob 索引标记的本机索引，并将其公开以用于查询。 如果 blob 的键/值属性需要索引和筛选功能，应利用 Blob 索引标记，而不是元数据。
>
> 若要详细了解 Blob 索引，请参阅[通过 Blob 索引管理和查找 Azure Blob 存储上的数据](../storage/blobs/storage-manage-find-blobs.md)。

### <a name="indexing-json-blobs"></a>对 JSON Blob 编制索引
可以将索引器配置为提取在包含 JSON 的 blob 中找到的结构化内容。 索引器可以读取 JSON blob 并将结构化内容解析为搜索文档的合适字段。 索引器还可以获取包含 JSON 对象数组的 blob 并将每个元素映射到单独的搜索文档。 可以设置分析模式，用于影响索引器创建的 JSON 对象的类型。

## <a name="search-blob-content-in-a-search-index"></a>在搜索索引中搜索 blob 内容 

索引的输出是一个搜索索引，用于在客户端应用中使用自由文本和筛选查询进行交互式浏览。 对于内容的初始探索和验证，建议先使用门户中的[搜索资源管理器](search-explorer.md)，以此查看文档结构。 可以在搜索资源管理器中使用[简单查询语法](query-simple-syntax.md)、[完整查询语法](query-lucene-syntax.md)和[筛选表达式语法](query-odata-filter-orderby-syntax.md)。

更持久的解决方案是收集查询输入，并在客户端应用程序中以搜索结果的形式显示响应结果。 以下 C# 教程介绍了如何构建搜索应用程序：[在 Azure 认知搜索中创建第一个应用程序](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>后续步骤

+ [使用 Azure 门户（Azure Blob 存储）上传、下载和列出 Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [设置 Blob 索引器（Azure 认知搜索）](search-howto-indexing-azure-blob-storage.md) 
