---
title: 功能概述
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索的功能类别。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 286d826f61a6c99396ef04af0a0855567bc4d1e4
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951377"
---
# <a name="features-of-azure-cognitive-search"></a>Azure 认知搜索的功能

Azure 认知搜索提供全文搜索引擎、持久存储搜索索引、在索引期间使用的集成 AI，以提取更多文本和结构以及 Api 和工具。 下表按类别汇总了功能。 有关认知搜索如何与其他搜索技术进行比较的详细信息，请参阅 [什么是 Azure 认知搜索？](search-what-is-azure-search.md)。

## <a name="indexing-features"></a>索引功能

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| 数据源 | 搜索索引可以接受来自任何源的文本（前提是它作为 JSON 文档提交）。 <br/><br/> [**索引器**](search-indexer-overview.md) 自动从支持的 Azure 数据源引入数据，并处理 JSON 序列化。 连接到 [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-cosmosdb.md) 或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，以提取主要数据存储中的可搜索内容。 Azure Blob 索引器可以执行“文档破解”[从主要文件格式提取文本](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文档。 |
| 分层的嵌套数据结构 | 使用[**复杂类型**](search-howto-complex-data-types.md)和集合可以在搜索索引中为几乎任何类型的 JSON 结构建模。 可以通过集合、复杂类型和复杂类型集合，以本机方式表示一对多和多对多基数。|
| 语言分析 | 分析器是在编制索引和搜索操作期间用于处理文本的组件。 默认情况下，你可以使用常规用途标准 Lucene 分析器，或使用语言分析器、你配置的自定义分析器或以所需格式生成令牌的其他预定义分析器来替代默认值。 <br/><br/>Lucene 或 Microsoft 的[语言分析器](index-add-language-analyzers.md)用于智能处理特定于语言的语言学，包括谓词时态、词性、不规则复数名词（例如“mouse”与“mice”）、词取消复合、词拆分（对于不带空格的语言）等。 <br/><br/>[**自定义词法分析器**](index-add-custom-analyzers.md) 用于复杂的查询窗体，例如拼音匹配和正则表达式。<br/><br/> |

## <a name="ai-enrichment-and-knowledge-mining"></a>AI 扩充和知识挖掘

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
|在编制索引期间进行 AI 处理 | 适用于图像和文本分析的 [**AI 扩充**](cognitive-search-concept-intro.md)可以应用于索引管道，以从原始内容中提取文本信息。 [内置技术](cognitive-search-predefined-skills.md)的一些示例包括：光学字符识别（使扫描的 JPEG 变得可搜索）、实体识别（标识组织、名称或位置）、关键短语识别。 也可[将自定义技术编码](cognitive-search-create-custom-skill-example.md)，以便将其附加到管道。 也可[集成 Azure 机器学习创作技能](./cognitive-search-tutorial-aml-custom-skill.md)。 |
| 存储丰富的内容以供在非搜索场景中分析和使用 | "[**知识库**](knowledge-store-concept-intro.md)" 是索引管道的替代输出。 你可以根据配置将索引管道创建的已扩充的文档发送到知识库，而不是将其发送到存储在 Azure Blob 存储或表存储中。 从基于 AI 的索引 (技能集) 创建知识商店。 知识存储的目的是为了支持下游分析或处理。 利用知识存储中的新信息和结构，你可以将其附加到机器学习过程或从 Power BI 进行连接来浏览数据。<br/><br/> |
| 缓存内容 | [**增量扩充 (预览) **](cognitive-search-incremental-indexing-conceptual.md) 使用缓存内容对管道中不更改的部件使用缓存内容，将处理限制为只对管道进行特定编辑更改的文档。 |

## <a name="query-and-user-experience"></a>查询和用户体验

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
|自由格式文本搜索 | [全文搜索](search-lucene-query-architecture.md)是大多数基于搜索的应用的主要用例。 查询可以使用支持的语法进行陈述。 <br/><br/>[简单查询语法](query-simple-syntax.md)提供逻辑运算符、短语搜索运算符、后缀运算符和优先运算符。<br/><br/>[**完整的 Lucene 查询语法**](query-lucene-syntax.md) 包括简单语法中的所有操作，以及模糊搜索、邻近搜索、术语提升和正则表达式的扩展。|
| 相关性 | [**简单计分**](index-add-scoring-profiles.md)是 Azure 认知搜索的主要优势。 计分配置文件用于在文档中自行将相关性建模为值的函数。 例如，你可能希望较新产品或打折产品显示在搜索结果的顶部位置。 也可以基于已跟踪和单独存储的客户搜索首选项将标记用于个性化计分，来生成计分配置文件。 |
| 地理搜索 | Azure 认知搜索可以处理、筛选和显示地理位置。 它可以让用户基于搜索结果与物理位置的临近程度浏览数据。 [观看此视频](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)或[查看此示例](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)了解详细信息。 |
| 筛选器和分面导航 | 通过单个查询参数实现[**分面导航**](search-faceted-navigation.md)。 Azure 认知搜索返回一个分面导航结构，可以将该结构用作类别列表背后的代码，用于自定向筛选（例如，按价格范围或品牌来筛选目录项）。 <br/><br/> 可以使用[**筛选器**](query-odata-filter-orderby-syntax.md)将分面导航纳入到应用程序的 UI 中，改进查询表述，以及基于用户或开发人员指定的条件进行筛选。 可以使用 OData 语法创建筛选器。 |
| 用户体验 | 可以为搜索栏中预先键入的查询启用[自动完成](search-autocomplete-tutorial.md)。 <br/><br/>[**搜索建议**](/rest/api/searchservice/suggesters)也基于搜索栏中的部分文本输入开始工作，但结果是索引中的实际文档而不是查询术语。 <br/><br/>[**同义词**](search-synonyms.md)功能无需用户提供替换术语，便可关联隐式扩展查询范围的等效术语。 <br/><br/>[命中项突出显示](/rest/api/searchservice/Search-Documents)向搜索结果中的匹配关键字应用文本格式设置。 可以选择哪些字段返回突出显示的片段。<br/><br/>[**排序**](/rest/api/searchservice/Search-Documents)通过索引架构覆盖多个字段，可以使用一个搜索参数在查询时进行切换。<br/><br/> 通过 Azure 认知搜索所提供的对搜索结果的优化控制，[**分页**](search-pagination-page-layout.md)和限制搜索结果将变得更简单。  <br/><br/>|

## <a name="security-features"></a>安全功能

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| 数据加密 | [Microsoft 托管的静态加密](search-security-overview.md#encrypted-transmissions-and-storage)内置在内部存储层中，它是不可撤消的。 <br/><br/>在 Azure Key Vault 中创建和管理的[**客户托管的加密密钥**](search-security-manage-encryption-keys.md)可用于索引和同义词映射的补充加密。 对于 2020 年 8 月 1 日后创建的服务，CMK 加密延伸到临时磁盘上的数据，以对索引内容进行完全双重加密。|
| Endpoint Protection | 通过[**用于入站防火墙支持的 ip 规则**](service-configure-firewall.md)，可以设置搜索服务将接受请求的 ip 范围。<br/><br/>使用 Azure Private Link[**创建专用终结点**](service-create-private-endpoint.md)，通过虚拟网络强制执行所有请求。 |
| 出站安全（索引器） | [**通过专用终结点的数据访问**](search-indexer-howto-access-private.md) 允许索引器连接到通过 Azure private Link 保护的 azure 资源。<br/><br/>[**使用可信标识的数据访问**](search-howto-managed-identities-data-sources.md) 意味着到外部数据源的连接字符串可以省略用户名和密码。 当索引器连接到数据源时，如果搜索服务以前已注册为受信任的服务，则资源允许连接。 |

## <a name="portal-features"></a>门户功能

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| 用于原型制作和检查的工具 | [**添加索引**](search-what-is-an-index.md) 是门户中的索引设计器，可用于创建包含属性化字段和其他一些设置的基本架构。 保存索引后，可以使用 SDK 或 REST API 填充索引以提供数据。 <br/><br/>[**导入数据向导**](search-import-data-portal.md) 创建索引、索引器、技能集和数据源定义。 如果数据存在于 Azure 中，此向导可节省大量时间和精力，特别是对概念证明调查和探索。 <br/><br/>[**搜索浏览器**](search-explorer.md) 用于测试查询和优化计分配置文件。<br/><br/>[**Create demo 应用**](search-create-app-portal.md) 用于生成可用于测试搜索体验的 HTML 页面。  |
| 监视和诊断 | [启用监视功能](search-monitor-usage.md)可查看除门户中始终可见的一目了然指标外的其他指标。 门户页面中会捕获并报告关于每秒查询数、延迟和限制的指标，无需额外进行配置。|

## <a name="programmability"></a>可编程性

| 类别&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 功能 |
|-------------------|----------|
| REST | [**Service REST API**](/rest/api/searchservice/) 适用于数据平面操作，包括与索引、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**管理 REST API**](/rest/api/searchmanagement/) 用于通过 Azure 资源管理器创建和清理服务。 你还可以使用此 API 来管理密钥和预配服务。|
| 用于 .NET 的 Azure SDK | [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) 用于数据平面操作，包括与索引、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>要通过 Azure 资源管理器进行服务创建和[**清理。**](/dotnet/api/microsoft.azure.management.search) 你还可以使用此 API 来管理密钥和预配服务。|
| 用于 Java 的 Azure SDK | [**com.azure.search.documents**](/java/api/com.azure.search.documents) 用于数据平面操作，包括与索引、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>使用[**的是通过**](/java/api/overview/azure/search/management)azure 资源管理器进行服务创建和清理的。 你还可以使用此 API 来管理密钥和预配服务。|
| 用于 Python 的 Azure SDK | [**azure 搜索-文档**](/python/api/overview/azure/search-documents-readme) 用于数据平面操作，包括与索引、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**azure 管理-搜索**](/python/api/overview/azure/search/management) 用于通过 azure 资源管理器进行服务创建和清理。 你还可以使用此 API 来管理密钥和预配服务。 |
| Azure SDK for JavaScript/TypeScript | [**azure/搜索-文档**](/javascript/api/@azure/search-documents/) 用于数据平面操作，包括与索引、查询和 AI 扩充相关的所有操作。 你还可以使用此客户端库来检索系统信息和统计信息。 <br/><br/>[**azure/arm-搜索**](/javascript/api/@azure/arm-search/) 用于通过 azure 资源管理器进行服务创建和清理。 你还可以使用此 API 来管理密钥和预配服务。 |

## <a name="see-also"></a>另请参阅

+ [认知搜索中的新增功能](whats-new.md)

+ [认知搜索中的预览功能](search-api-preview.md)