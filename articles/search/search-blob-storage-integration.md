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
ms.openlocfilehash: db54c7886f5256dab41325cd12f1b893be732d72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529049"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>使用 Azure 搜索将全文搜索添加到 Azure blob 数据

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，只需单击几下鼠标，即可使用[Azure 搜索](search-what-is-azure-search.md)来索引和搜索 blob 的内容。 Azure 搜索提供内置集成，可通过将数据源感知功能添加到索引的[*blob 索引器*](search-howto-indexing-azure-blob-storage.md)来从 blob 存储中进行索引。

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>将全文搜索添加到 blob 数据的意义

Azure 搜索是一种云搜索服务，提供索引和查询引擎，可对搜索服务上承载的用户定义索引执行操作。 若要提高性能，可以在云中使用查询引擎将可搜索的内容共存，并以用户从搜索查询中获得的速度来返回结果。

Azure 搜索在索引层与 Azure Blob 存储集成，将你的 Blob 内容作为索引的搜索文档导入到*反转索引*中，并将其他查询结构作为支持自由格式文本查询和筛选器表达式的查询结构。 由于你的 blob 内容已索引为搜索索引，因此，对 blob 内容的访问可利用 Azure 搜索中的各种查询功能。

创建并填充索引后，该索引独立于你的 blob 容器，但你可以重新运行索引操作，以便通过更改基础容器来刷新索引。 各个 blob 的时间戳信息用于更改检测。 您可以选择计划执行或按需索引作为刷新机制。

输入是在 Azure Blob 存储中的单个容器中的 blob。 Blob 几乎可以是任何类型的文本数据。 如果 blob 包含图像，则可以将[AI 扩充添加到 blob 索引](search-blob-ai-integration.md)，以创建和提取图像中的文本。

输出始终是一个 Azure 搜索索引，用于在客户端应用程序中快速搜索、检索和浏览。 介于之间是索引管道体系结构本身。 该管道基于*索引器*功能，在本文中对此进行了进一步讨论。

## <a name="start-with-services"></a>从服务开始

需要 Azure 搜索和 Azure Blob 存储。 在 Blob 存储中，需要一个提供源内容的容器。

你可以直接在存储帐户门户页中开始。 在左侧导航页的 " **Blob 服务**" 下，单击 "**添加 Azure 搜索**" 以创建新服务或选择现有服务。 

将 Azure 搜索添加到存储帐户后，可以按照标准过程对 blob 数据进行索引。 建议在 Azure 搜索中使用 "**导入数据**" 向导，以便进行简单的初始简介，或使用 Postman 等工具调用 REST api。 本教程将指导你完成在[Azure 搜索中调用 Postman： Index 和 search 半结构化数据（JSON blob）](search-semi-structured-data.md)中的 REST API 的步骤。 

## <a name="use-a-blob-indexer"></a>使用 Blob 索引器

*索引器*是一个数据源感知子查询，它具有用于采样数据、读取元数据、检索数据和将本机格式的数据序列化为 JSON 文档以供后续导入的内部逻辑。 

Azure 存储中的 blob 使用[Azure 搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)进行索引。 您可以使用**导入数据**向导、REST API 或 .net SDK 来调用此索引器。 在代码中，可以通过设置类型来使用此索引器，还可以提供包含 Azure 存储帐户和 blob 容器的连接信息。 您可以通过创建虚拟目录子集化 blob，然后可以将该虚拟目录作为参数传递，也可以通过筛选文件类型扩展。

索引器执行 "文档破解"，打开一个 blob 来检查内容。 连接到数据源后，它是管道中的第一步。 对于 blob 数据，将在其中检测到 PDF、office 文档和其他内容类型。 带有文本提取的文档破解是免费的。 如果 blob 包含图像内容，则将忽略图像，除非你[添加 AI 扩充](search-blob-ai-integration.md)。 标准索引仅适用于文本内容。

Blob 索引器附带了配置参数，并且如果基础数据提供了足够的信息，则支持更改跟踪。 可以在[Azure 搜索 Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)中了解有关核心功能的详细信息。

### <a name="supported-content-types"></a>支持的内容类型

通过在容器上运行 Blob 索引器，你可以使用单个查询从以下内容类型中提取文本和元数据：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>为 blob 元数据编制索引

使您能够轻松地对任何内容类型的 blob 进行排序的常见方案是为每个 blob 的自定义元数据和系统属性编制索引。 通过这种方式，将对所有 blob 的信息编制索引，而不管文档类型是什么，存储在 Azure 搜索的索引中。 然后，可以使用新索引在所有 Blob 存储内容中进行排序、筛选和分面。

### <a name="indexing-json-blobs"></a>为 JSON blob 编制索引
可以将 Azure 搜索配置为提取在包含 JSON 的 blob 中找到的结构化内容。 Azure 搜索可以读取 JSON blob 并将结构化内容解析为 Azure 搜索文档的合适字段。 Azure 搜索还可以获取包含 JSON 对象数组的 blob 并将每个元素映射到单独的 Azure 搜索文档。 可以设置分析模式，以影响索引器创建的 JSON 对象的类型。

## <a name="search-blob-content-in-a-search-index"></a>在搜索索引中搜索 blob 内容 

索引的输出是一个搜索索引，用于在客户端应用中使用自由文本和筛选查询进行交互式浏览。 对于内容的初始探索和验证，建议从门户中的 "[搜索资源管理器](search-explorer.md)" 开始检查文档结构。 可以在 "搜索资源管理器" 中使用[简单查询语法](query-simple-syntax.md)、[完整查询语法](query-lucene-syntax.md)和[筛选表达式语法](query-odata-filter-orderby-syntax.md)。

更永久性的解决方案是收集查询输入，并在客户端应用程序中以搜索结果的形式显示响应。 以下C#教程介绍了如何构建搜索应用程序：[在 Azure 搜索中创建第一个应用程序](tutorial-csharp-create-first-app.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置 blob 索引器](search-howto-indexing-azure-blob-storage.md) 
