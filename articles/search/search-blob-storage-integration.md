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
ms.openlocfilehash: 72d00b70cf3568466715668aa441ee295614c740
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935239"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>使用 Azure 认知搜索将全文搜索添加到 Azure Blob 数据

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，使用 [Azure 认知搜索](search-what-is-azure-search.md)，只需单击几下，便可以为 Blob 中的内容编制索引以及对其进行搜索。 Azure 认知搜索提供内置的集成，用于通过 [*Blob 索引器*](search-howto-indexing-azure-blob-storage.md)（添加数据源感知的索引编制功能）为 Blob 存储编制索引。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>将全文搜索添加到 Blob 数据意味着什么

Azure 认知搜索是一个提供索引和查询引擎的云搜索服务，这些引擎针对搜索服务中托管的用户定义的索引运行。 为了提高性能，需将可搜索的内容与云中的查询引擎共置在一起，使返回结果的速度与用户预期的搜索查询速度相当。

Azure 认知搜索在索引层与 Azure Blob 存储集成，可将 Blob 内容作为已编制成倒排索引的搜索文档导入，并可导入其他支持自由格式文本查询和筛选器表达式的查询结构。** 由于 Blob 内容已编制成搜索索引，因此，对 Blob 内容的访问可以利用 Azure 认知搜索中的各项查询功能。

创建并填充索引后，该索引将独立于 Blob 容器，但你可以重新运行索引操作，以使用底层容器的更改来刷新索引。 各个 Blob 中的时间戳信息用于执行更改检测。 可以选择按计划执行或按需索引作为刷新机制。

输入是 Azure Blob 存储中单个容器内的 Blob。 Blob 几乎可以是任何类型的文本数据。 如果 Blob 包含图像，可[将 AI 扩充添加到 Blob 索引](search-blob-ai-integration.md)，以便从图像创建和提取文本。

输出始终是 Azure 认知搜索索引，用于在客户端应用程序中快速执行搜索、检索和浏览。 输入与输出之间是索引管道体系结构本身。 该管道基于本文将会详细介绍的索引器功能。**

## <a name="start-with-services"></a>从服务开始

需要 Azure 认知搜索和 Azure Blob 存储。 在 Blob 存储中，需要一个提供源内容的容器。

可以直接在存储帐户门户页中开始。 在左侧导航页中的“Blob 服务”下，单击“添加 Azure 认知搜索”创建新服务或选择现有服务。**** **** 

将 Azure 认知搜索添加到存储帐户后，可以遵循标准过程为 Blob 数据编制索引。 我们建议使用 Azure 认知搜索中的“导入数据”向导以轻松完成初始引入，或使用 Postman 等工具调用 REST API。**** 本教程将引导你完成在 Postman 中调用 REST API 的步骤：[在 Azure 认知搜索中为半结构化数据 (JSON Blob) 编制索引以及搜索此类数据](search-semi-structured-data.md) 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

索引器是数据源感知的子服务，其中配备了内部逻辑用于对数据采样、读取元数据、检索数据，以及将数据从本机格式序列化为 JSON 文档供以后导入。** 

Azure 存储中的 Blob 使用 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)编制索引。 可以使用“导入数据”向导、REST API 或 .NET SDK 调用此索引器。**** 在代码中，使用此索引器的方式是设置类型，并提供包括 Azure 存储帐户和 Blob 容器的连接信息。 可以通过创建虚拟目录（随后可将其作为参数传递），或者筛选文件类型扩展名，来指定 Blob 的子集。

索引器执行“文档破解”，会打开一个 Blob 来检查内容。 这是连接到数据源后，在管道中发生的第一个步骤。 对于 Blob 数据，此步骤会检测 PDF、Office 文档和其他内容类型。 文档破解和文本提取是免费的。 如果 Blob 包含图像内容，则除非[添加 AI 扩充](search-blob-ai-integration.md)，否则会忽略图像。 标准索引仅适用于文本内容。

Blob 索引器附带配置参数，如果基础数据提供足够的信息，则索引器支持更改跟踪。 可以在 [Azure 认知搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)中详细了解核心功能。

### <a name="supported-content-types"></a>支持的内容类型

通过对容器运行 Blob 索引器，只需运行单个查询就能从以下内容类型中提取文本和元数据：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>为 Blob 元数据编制索引

用于实现在任何内容类型的 Blob 中轻松进行排序的一个常用方案是为自定义元数据和每个 Blob 的系统属性编制索引。 通过这种方式，系统会对所有 Blob 的信息编制索引（无论文档类型是什么），并将信息存储在搜索服务中的索引内。 然后，可以使用新索引对所有 Blob 存储内容继续执行排序、筛选和分面操作。

> [!NOTE]
> Blob 存储服务会编制 Blob 索引标记的本机索引，并将其公开以用于查询。 如果 blob 的键/值属性需要索引和筛选功能，应利用 Blob 索引标记，而不是元数据。
>
> 若要详细了解 Blob 索引，请参阅[通过 Blob 索引管理和查找 Azure Blob 存储上的数据](../storage/blobs/storage-manage-find-blobs.md)。

### <a name="indexing-json-blobs"></a>对 JSON Blob 编制索引
可将索引器配置为提取包含 JSON 的 Blob 中的结构化内容。 索引器可以读取 JSON blob 并将结构化内容分析为搜索文档的相应字段。 索引器还可以获取包含 JSON 对象数组的 Blob 并将每个元素映射到单独的搜索文档。 可以设置分析模式，以影响索引器创建的 JSON 对象类型。

## <a name="search-blob-content-in-a-search-index"></a>在搜索索引中搜索 Blob 内容 

索引的输出是一个搜索索引，用于在客户端应用中通过自由文本和筛选的查询进行交互式浏览。 若要对内容进行初始浏览和验证，我们建议从门户中的[搜索资源管理器](search-explorer.md)开始，以检查文档结构。 可以在搜索资源管理器中使用[简单查询语法](query-simple-syntax.md)、[完整查询语法](query-lucene-syntax.md)和[筛选表达式语法](query-odata-filter-orderby-syntax.md)。

更持久的解决方案是收集查询输入，并在客户端应用程序中以搜索结果的形式提供响应。 以下 C# 教程介绍了如何生成搜索应用程序：[在 Azure 认知搜索中创建第一个应用程序](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>后续步骤

+ [使用 Azure 门户上传、下载和列出 Blob（Azure Blob 存储）](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [设置 Blob 索引器（Azure 认知搜索）](search-howto-indexing-azure-blob-storage.md)