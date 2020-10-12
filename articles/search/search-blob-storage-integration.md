---
title: 对 Azure Blob 存储内容进行搜索
titleSuffix: Azure Cognitive Search
description: 了解如何从 Azure blob 提取文本，并使其在 Azure 认知搜索索引中可进行全文搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355289"
---
# <a name="search-over-azure-blob-storage-content"></a>对 Azure Blob 存储内容进行搜索

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 本文介绍了从 blob 中提取内容和元数据并将其发送到 Azure 认知搜索中搜索索引的基本工作流。 可以使用全文搜索查询生成的索引。

> [!NOTE]
> 已熟悉工作流和组合？ 下一步是[如何配置 blob 索引器](search-howto-indexing-azure-blob-storage.md)。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>将全文搜索添加到 Blob 数据意味着什么

Azure 认知搜索是一种搜索服务，它支持在包含云中托管的远程可搜索内容的用户定义索引上索引和查询工作负荷。 若要提高性能，请使用查询引擎来归置您的可搜索内容，这种情况下，用户可能会遇到搜索查询所需的速度。

认知搜索在索引层与 Azure Blob 存储集成，将你的 Blob 内容作为索引的搜索文档导入到 *反转索引* 和其他支持自由格式文本查询和筛选表达式的查询结构。 由于你的 blob 内容已编入索引搜索索引，因此你可以使用 Azure 认知搜索中的所有查询功能来查找 blob 内容中的信息。

输入是 Azure Blob 存储中单个容器内的 Blob。 Blob 几乎可以是任何类型的文本数据。 如果 blob 包含图像，则可以将 [AI 扩充添加到 blob 索引](search-blob-ai-integration.md) ，以创建和提取图像中的文本。

输出始终是 Azure 认知搜索索引，用于在客户端应用程序中快速执行搜索、检索和浏览。 输入与输出之间是索引管道体系结构本身。 该管道基于本文将会详细介绍的索引器功能。**

创建并填充索引后，该索引独立于你的 blob 容器，但你可以重新运行索引操作，以根据已更改的文档刷新索引。 各个 Blob 中的时间戳信息用于执行更改检测。 可以选择按计划执行或按需索引作为刷新机制。

## <a name="required-resources"></a>所需资源

需要 Azure 认知搜索和 Azure Blob 存储。 在 Blob 存储中，需要一个提供源内容的容器。

可以直接在存储帐户门户页中开始。 在左侧导航页中的“Blob 服务”下，单击“添加 Azure 认知搜索”创建新服务或选择现有服务。**** **** 

将 Azure 认知搜索添加到存储帐户后，可以遵循标准过程为 Blob 数据编制索引。 我们建议使用 Azure 认知搜索中的“导入数据”向导以轻松完成初始引入，或使用 Postman 等工具调用 REST API。**** 本教程将引导你完成在 Postman 中调用 REST API 的步骤：[在 Azure 认知搜索中为半结构化数据 (JSON Blob) 编制索引以及搜索此类数据](search-semi-structured-data.md) 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

*索引器*是认知搜索中的数据源感知子查询，它提供了用于采样数据的内部逻辑，读取元数据数据，检索数据，以及将数据从本机格式序列化为 JSON 文档以供以后导入。 

Azure 存储中的 Blob 使用 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)编制索引。 可以使用“导入数据”向导、REST API 或 .NET SDK 调用此索引器。  在代码中，使用此索引器的方式是设置类型，并提供包括 Azure 存储帐户和 Blob 容器的连接信息。 可以通过创建虚拟目录（随后可将其作为参数传递），或者筛选文件类型扩展名，来指定 Blob 的子集。

索引器执行“文档破解”，会打开一个 Blob 来检查内容。 这是连接到数据源后，在管道中发生的第一个步骤。 对于 Blob 数据，此步骤会检测 PDF、Office 文档和其他内容类型。 文档破解和文本提取是免费的。 如果 Blob 包含图像内容，则除非[添加 AI 扩充](search-blob-ai-integration.md)，否则会忽略图像。 标准索引仅适用于文本内容。

Blob 索引器附带配置参数，如果基础数据提供足够的信息，则索引器支持更改跟踪。 可以在 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)中详细了解核心功能。

### <a name="supported-content-types"></a>支持的内容类型

通过对容器运行 Blob 索引器，只需运行单个查询就能从以下内容类型中提取文本和元数据：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>为 Blob 元数据编制索引

用于实现在任何内容类型的 Blob 中轻松进行排序的一个常用方案是为自定义元数据和每个 Blob 的系统属性编制索引。 通过这种方式，系统会对所有 Blob 的信息编制索引（无论文档类型是什么），并将信息存储在搜索服务中的索引内。 通过使用新索引，可以在所有 Blob 存储内容中继续执行排序、筛选和 facet 操作。

> [!NOTE]
> Blob 存储服务会编制 Blob 索引标记的本机索引，并将其公开以用于查询。 如果 blob 的键/值属性需要索引和筛选功能，应利用 Blob 索引标记，而不是元数据。
>
> 若要详细了解 Blob 索引，请参阅[通过 Blob 索引管理和查找 Azure Blob 存储上的数据](../storage/blobs/storage-manage-find-blobs.md)。

### <a name="indexing-json-blobs"></a>对 JSON Blob 编制索引

可将索引器配置为提取包含 JSON 的 Blob 中的结构化内容。 索引器可以读取 JSON blob 并将结构化内容分析为搜索文档的相应字段。 索引器还可以获取包含 JSON 对象数组的 Blob 并将每个元素映射到单独的搜索文档。 可以设置分析模式，以影响索引器创建的 JSON 对象类型。

## <a name="search-blob-content-in-a-search-index"></a>在搜索索引中搜索 Blob 内容 

索引器的输出是一个搜索索引，用于在客户端应用程序中使用自由文本和筛选查询进行交互式浏览。 若要对内容进行初始浏览和验证，我们建议从门户中的[搜索资源管理器](search-explorer.md)开始，以检查文档结构。 可以在搜索资源管理器中使用[简单查询语法](query-simple-syntax.md)、[完整查询语法](query-lucene-syntax.md)和[筛选表达式语法](query-odata-filter-orderby-syntax.md)。

更持久的解决方案是收集查询输入，并在客户端应用程序中以搜索结果的形式提供响应。 以下 C# 教程介绍了如何生成搜索应用程序：[在 Azure 认知搜索中创建第一个应用程序](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>后续步骤

+ [使用 Azure 门户上传、下载和列出 Blob（Azure Blob 存储）](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [设置 Blob 索引器（Azure 认知搜索）](search-howto-indexing-azure-blob-storage.md)