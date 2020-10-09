---
title: Azure 认知搜索简介
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索是 Microsoft 提供的完全托管式云搜索服务。 了解用例、开发工作流、与其他 Microsoft 搜索产品的比较，以及入门方法。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperfq1
ms.openlocfilehash: 26a448ded06b32fef80fee06568655067a727620
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91320399"
---
# <a name="what-is-azure-cognitive-search"></a>Azure 认知搜索是什么？

Azure 认知搜索（[以前称为“Azure 搜索”](whats-new.md#new-service-name)）是一种云搜索服务，它为开发人员提供 API 和工具，以便基于 Web、移动和企业应用程序中的专用异类内容构建丰富的搜索体验。

创建认知搜索服务时，你将获得一个进行索引编制和执行查询的搜索引擎、你创建和管理的索引的持久存储，以及用于编写从简单到复杂的查询的查询语言。 可以选择让搜索服务以“索引器”（自动从 Azure 数据源引入/检索数据）和“技能组”（引入认知服务（例如图像和文本分析）中的可消耗 AI，或者引入你在 Azure 机器学习中创建的或在 Azure Functions 内包装的自定义 AI）的形式与其他 Azure 服务集成。

![Azure 认知搜索体系结构](media/search-what-is-azure-search/azure-search-diagram.svg "Azure 认知搜索体系结构")

从体系结构方面来讲，搜索服务位于外部数据存储（包含未索引数据）与客户端应用（向搜索索引发送查询请求并处理响应）之间。  索引架构决定了可搜索内容的结构。 

搜索服务的两个主要工作负荷是索引编制和查询。

+ 索引编制将文本插入到搜索服务中，并使其可供搜索。 在内部，入站文本将处理成标记并存储在倒排索引中，方便快速扫描。在编制索引期间，你可以选择添加认知技能（Microsoft 提供的预定义技能或你创建的自定义技能）。 后续的分析和转换可能会导致生成以前不存在的新信息和结构，为许多搜索和知识挖掘方案提供高实用性。

+ 使用可搜索的数据填充索引后，客户端应用会将查询请求发送到搜索服务，并处理响应。 所有查询执行都基于你在服务中创建、拥有和存储的搜索索引。 在客户端应用中，搜索体验是使用 Azure 认知搜索中的 API 定义的，可能包括相关性调整、自动完成、同义词匹配、模糊匹配、模式匹配、筛选和排序。

功能通过简单的 [REST API](/rest/api/searchservice/) 或 [.NET SDK](search-howto-dotnet-sdk.md) 公开，消除了信息检索固有的复杂性。 你还可以使用 Azure 门户，通过用于原型制作以及查询索引和技能组的工具进行服务管理和内容管理。 因为服务在云中运行，所以基础结构和可用性由 Microsoft 管理。

## <a name="when-to-use-cognitive-search"></a>何时使用认知搜索

Azure 认知搜索非常适合以下应用方案：

+ 将异构内容类型整合成专用的用户定义的搜索索引。 可以在搜索索引中填充来自任何源的 JSON 文档流。 对于 Azure 上支持的源，请使用索引器来自动编制索引。 对索引架构和刷新计划进行控制是使用认知搜索的主要原因。

+ 轻松实现搜索相关的功能。 搜索 API 简化了查询构造、分面导航、筛选器（包括地理空间搜索）、同义词映射、自动完成和相关性优化。 使用内置功能可以满足最终用户对搜索体验的预期，使其觉得该体验类似于商用 Web 搜索引擎。

+ 原始内容是 Azure Blob 存储或 Cosmos DB 中存储的大型无差别文本、图像文件或应用程序文件。 在索引编制期间，你可以应用[认知技能](cognitive-search-concept-intro.md)来识别和提取文本、创建结构或创建新信息（例如已翻译的文本或实体）。

+ 内容需要语言分析或自定义文本分析。 如果你使用非英语内容，Azure 认知搜索支持 Lucene 分析器和 Microsoft 的自然语言处理器。 还可以配置分析器以实现原始内容的专业处理，例如筛选出标注字符，或识别并保留字符串中的模式。

有关特定功能的详细信息，请参阅 [Azure 认知搜索的 功能](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>如何使用认知搜索

### <a name="step-1-provision-service"></a>步骤 1：预配服务

可以[创建免费服务](search-create-service-portal.md)与其他订阅者共享，也可以创建一个[付费层](https://azure.microsoft.com/pricing/details/search/)，其中的资源专供你的服务使用。 所有快速入门和教程都可以通过免费服务完成。

对于付费层，你可以在两个维度中缩放服务，以根据生产要求校准资源：

+ 添加副本以增长容量来处理重型查询负载
+ 添加分区以便为更多文档增加存储

### <a name="step-2-create-an-index"></a>步骤 2：创建索引

定义要映射的索引架构，以反映要搜索的文档的结构，这类似于数据库中的字段。 搜索索引是一种专用的数据结构，经过优化后可快速执行查询。

通常[在 Azure 门户中创建索引架构](search-what-is-an-index.md)，或者使用 [.NET SDK](search-howto-dotnet-sdk.md) 或 [REST API](/rest/api/searchservice/) 以编程方式创建。

> [!TIP]
> 从[快速入门：导入数据向导](search-get-started-portal.md)开始，在几分钟内创建、加载和查询索引。

### <a name="step-3-load-data"></a>步骤 3：加载数据

定义索引后，便可以上传内容。 可以使用推送或提取模型。

推送模型使用 [SDK](search-howto-dotnet-sdk.md) 或 [REST](/rest/api/searchservice/addupdate-or-delete-documents) 中的 API 将 JSON 文档“推送”到索引。 外部数据集几乎可以是任何数据源，只要文档是 JSON 即可。

拉取模型从 Azure 上的源中“拉取”数据，并将其发送到搜索索引。 拉取模型是通过[索引器](/rest/api/searchservice/Indexer-operations)实现的。索引器可以简化和自动化数据引入的方方面面，例如，连接、读取和序列化数据。 支持的数据源包括 Azure Cosmos DB、Azure SQL 和 Azure 存储。

### <a name="step-4-send-queries-and-handle-responses"></a>步骤 4：发送查询并处理响应

填充索引后，可以通过将简单的 HTTP 请求与 [REST API](/rest/api/searchservice/Search-Documents) 或 [.NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations) 结合使用，向服务终结点[发出搜索查询](search-query-overview.md)。

逐步完成[创建第一个搜索应用](tutorial-csharp-create-first-app.md)以进行构建，然后扩展用于收集用户输入并处理结果的网页。 还可以使用 [Postman 进行交互式 REST](search-get-started-postman.md) 调用，或使用 Azure 门户中内置的[搜索浏览器](search-explorer.md)来查询现有索引。

## <a name="how-it-compares"></a>它如何进行比较

客户常常询问 Azure 认知搜索与其他搜索相关解决方案有何不同。 下表总结主要区别。

| 比较对象 | 主要区别 |
|-------------|-----------------|
|必应 | [必应 Web 搜索 API](../cognitive-services/bing-web-search/index.yml) 在 Bing.com 上搜索索引以匹配提交的搜索词。 索引从 HTML、XML 和公共网站上的其他 Web 内容生成。 [必应自定义搜索](/azure/cognitive-services/bing-custom-search/)构建于同一基础之上，针对 Web 内容类型提供相同的爬网技术，范围覆盖单个网站。<br/><br/>Azure 认知搜索可搜索定义的索引，填充拥有的数据和文档，常常来自多个不同的源。 Azure 认知搜索通过[索引器](search-indexer-overview.md)具有一些数据源的爬网功能，但可以将符合索引架构的任何 JSON 文档推送到单个统一的可搜索资源。 |
|数据库搜索 | 许多数据库平台都包含内置的搜索体验。 SQL Server 具有[全文搜索](/sql/relational-databases/search/full-text-search)。 Cosmos DB 及类似技术具有可查询的索引。 在评估结合使用搜索和存储的产品时，确定要采用哪种方式可能颇具挑战性。 许多解决方案同时使用两种：使用 DBMS 进行存储，使用 Azure 认知搜索获取专业搜索功能。<br/><br/>与 DBMS 搜索相比，Azure 认知搜索存储来自不同来源的内容，并提供专用文本处理功能，例如 [56 种语言](/rest/api/searchservice/language-support)中的语言感知文本处理（词干化、词元化、词形式）。 它还支持拼写错误单词的自动更正、[同义词](/rest/api/searchservice/synonym-map-operations)、[建议](/rest/api/searchservice/suggestions)、[评分控制](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)，[Facet](./search-filters-facets.md) 和[自定义词汇切分](/rest/api/searchservice/custom-analyzers-in-azure-search)。 Azure 认知搜索中的[全文搜索引擎](search-lucene-query-architecture.md)基于 Apache Lucene，它是信息检索方面的行业标准。 虽然 Azure 认知搜索以倒排索引的形式持久存储数据，但它不能替代真正的数据存储，建议不要在该容量中使用它。 有关详细信息，请参阅此[论坛帖子](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)。 <br/><br/>资源利用是这个类别的另一个转折点。 索引和一些查询操作通常是计算密集型的。 将搜索从 DBMS 卸载到云中的专用解决方案可以节省用于事务处理的系统资源。 此外，通过将搜索外部化，可以根据查询量轻松调整规模。|
|专用搜索解决方案 | 假设已决定使用全频谱功能进行专用搜索，则需要在本地解决方案或云服务之间进行最终的分类比较。 许多搜索技术提供对索引和查询管道的控制、对更丰富查询和筛选语法的访问、对设置级别和相关性的控制以及自导智能搜索功能。 <br/><br/>如果想要获得一个开销和维护工作量极少且规模可调的统包解决方案，则云服务是适当的选择。 <br/><br/>在云的范式中，许多提供程序提供相当的基线功能，以及全文搜索、地理搜索，并且能够处理搜索输入中一定程度的模糊性。 通常，它是一项[专用功能](search-features-list.md)，或者是 API、工具以及用于确定最匹配项的管理功能的易化和总体简化。 |

在所有云提供程序中，对于主要依赖于信息检索搜索和内容导航的应用，Azure 认知搜索在处理 Azure 上的内容存储和数据库的全文搜索工作负荷方面最为强大。 

主要优势包括：

+ 在索引层的 Azure 数据集成（爬网程序）
+ 用于集中管理的 Azure 门户
+ Azure 可伸缩性、可靠性和世界一流的可用性
+ 对原始数据进行 AI 处理，使其更易于搜索，包括识别图像中的文本，或查找非结构化内容中的模式。
+ 语言分析和自定义分析，提供分析器，用于支持以 56 种语言进行可靠的全文搜索
+ [对以搜索为中心的应用通用的核心功能](search-features-list.md)：评分、分面、建议、同义词、地理搜索，等等。

在我们的所有客户中，能够利用 Azure 认知搜索中最广泛功能的客户包括在线目录、业务线程序以及文档发现应用程序。

## <a name="watch-this-video"></a>观看此视频

在这个 15 分钟的视频中，项目经理 Luis Cabrera 介绍了 Azure 认知搜索。

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]