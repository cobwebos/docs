---
title: 升级到 .NET SDK 版本 11
titleSuffix: Azure Cognitive Search
description: 将代码从 Azure 认知搜索 .NET SDK 的旧版本迁移到版本 11。 了解新增功能和所需的代码更改。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f6953f145621e11506a009fa59d67a5f40508a13
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539565"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>升级到 Azure 认知搜索 .NET SDK 版本 11

如果你使用的是 10.0 或更低版本的 [.NET SDK](/dotnet/api/overview/azure/search)，则本文可帮助你升级到版本 11。

版本 11 是完全重新设计的客户端库，由 Azure SDK 开发团队发布（以前的版本由 Azure 认知搜索开发团队生成）。 已对该库进行了重新设计，使其与其他 Azure 客户端库更加一致，依赖于 [Azure.Core](/dotnet/api/azure.core) 和 [System.Text.Json](/dotnet/api/system.text.json)，并为常见任务实现了熟悉的方法。

新版本中的一些主要区别包括：

+ 一个包和库，而不是多个
+ 新包名称是 `Azure.Search.Documents`，而不是 `Microsoft.Azure.Search`。
+ 三个客户端（而不是两个）：`SearchClient`、`SearchIndexClient`、`SearchIndexerClient`
+ 一系列 API 的命名差异，以及简化了某些任务的小的结构差异

> [!NOTE]
> 有关 .NET SDK 版本 11 中的变更的详细列表，请查看[**更改日志**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

## <a name="package-and-library-consolidation"></a>包和库合并

版本 11 将多个包和库合并成了一个。 迁移后，需要管理的库较少。

+ [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [客户端库的 API 参考](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>客户端差异

下表映射了两个版本的客户端库（适用情况下）。

| 操作作用域 | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| 用于查询以及用于填充索引的客户端。 | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| 用于索引、分析器、同义词映射的客户端 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 用于索引器、数据源、技能组的客户端 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient（**新增**）](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` 在两个版本中均存在，但支持不同的功能。 在版本 10 中，`SearchIndexClient` 创建索引和其他对象。 在版本 11 中，`SearchIndexClient` 处理现有索引。 为了避免在更新代码时产生混淆，请注意更新客户端引用时的顺序。 按照[升级步骤](#UpgradeSteps)中的顺序进行操作应当有助于缓解任何字符串替换问题。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>命名和其他 API 差异

除了客户端差异以外（前面已提到，因此在此处省略），还重命名了多个其他 API，在某些情况下对其进行了重新设计。 下面汇总了类名称差异。 此列表并非详尽无遗，但它确实按任务将 API 更改进行了分组，这对于特定代码块的修订很有帮助。 有关 API 更新的详细列表，请参阅 GitHub 上 `Azure.Search.Documents` 的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

### <a name="authentication-and-encryption"></a>身份验证和加密

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey`（已作为正式版功能存在于[预览版 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) 中） | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>索引、分析器、同义词映射

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| 字段 | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)（另外，`AnalyzerName` 对应于 `LexicalAnalyzerName`） |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer)（另外，`StandardTokenizerV2` 对应于 `LuceneStandardTokenizerV2`） |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [分词器](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer)（另外，`TokenizerName` 对应于 `LexicalTokenizerName`） |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 无。 删除了对 `Format` 的引用。 |

简化了字段定义：[SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield)、[SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield)、[ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) 是用于创建字段定义的新 API。

### <a name="indexers-datasources-skillsets"></a>索引器、数据源、技能组

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [索引器](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [技能集](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>数据导入

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>查询定义和结果

| 版本 10 | 版本 11 等效项 |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) 或 [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)，具体取决于结果是单个文档还是多个文档。 |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>版本 11 中的功能

Azure 认知搜索客户端库的每个版本都面向 REST API 的一个对应版本。 REST API 被视为服务的基础，而各个 SDK 用于包装 REST API 的版本。 作为 .NET 开发人员，如果想要了解有关特定对象或操作的更多背景知识，查看 [REST API 文档](/rest/api/searchservice/)会很有帮助。

版本 11 对应于 [2020-06-30 搜索服务](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)。 因为版本 11 也是从头开始构建的新客户端库，所以大部分开发工作都集中在与版本 10 的等效性上，一些 REST API 功能支持尚待提供。

版本 11.0 完全支持以下对象和操作：

+ 索引创建和管理
+ 同义词映射创建和管理
+ 所有查询类型和语法（地理空间筛选器除外）
+ 用于为 Azure 数据源（包括数据源和技能组）编制索引的索引器对象和操作

版本 11.1 添加了以下项：

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder)（在 11.1 中添加）
+ [序列化程序属性](/dotnet/api/azure.search.documents.searchclientoptions.serializer)（在 11.1 中添加），用于支持自定义序列化

### <a name="pending-features"></a>待解决功能

版本 10 中的以下功能在版本 11 中尚不可用。 如果需要这些功能，请推迟迁移，直到这些功能受支持。

+ 地理空间类型
+ [知识存储](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤

以下步骤通过遍历第一组必需任务（特别是在客户端引用方面）开始执行代码迁移。

1. 在 Visual Studio 中，右键单击你的项目引用并选择“管理 NuGet 包...”，以安装 [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)。

1. 将用于 Microsoft.Azure.Search 的 using 指令替换为以下项：

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 对于需要 JSON 序列化的类，请将 `using Newtonsoft.Json` 替换为 `using System.Text.Json.Serialization`。

1. 修订客户端身份验证代码。 在以前的版本中，你会使用客户端对象上的属性（例如，[SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) 属性）设置 API 密钥。 在当前版本中，请使用 [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) 类将密钥作为凭据传递，以便在需要时可以更新 API 密钥，而无需创建新的客户端对象。

   客户端属性已精简，仅剩 `Endpoint`、`ServiceName` 和 `IndexName`（如果适用）。 下面的示例使用系统 [Uri](/dotnet/api/system.uri) 类提供终结点，并使用 [Environment](/dotnet/api/system.environment) 类来读取密钥值：

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. 为索引器相关对象添加新的客户端引用。 如果使用的是索引器、数据源或技能组，请将客户端引用更改为 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)。 此客户端是版本 11 中的新客户端，之前没有。

1. 为查询和数据导入更新客户端引用。 应将 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) 的实例更改为 [SearchClient](/dotnet/api/azure.search.documents.searchclient)。 为了避免名称混乱，请确保在继续下一步之前捕获所有实例。

1. 为索引、索引器、同义词映射和分析器对象更新客户端引用。 应将 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) 的实例更改为 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient)。 

1. 尽可能多地更新类、方法和属性以使用新库的 API。 可以从[命名差异](#naming-differences)部分来开始了解，但也可以查看[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

   如果在查找等效 API 时遇到困难，建议你在 [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) 上记录问题，以便我们可以改进文档或调查问题。

1. 重新生成解决方案。 在修复了任何生成错误或警告后，可以对应用程序进行其他更改，以利用[新功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>版本 11 中的中断性变更

考虑到对库和 API 的全面更改，升级到版本 11 是一项重大升级，你的代码将不再后向兼容版本 10 及更低版本。从这个意义上说，这是一项中断性变更。 若要全面了解差异，请参阅 `Azure.Search.Documents` 的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

就服务版本更新而言，版本 11 中的代码更改与现有功能（而不仅仅是 API 重构）相关，你会发现以下行为更改：

+ [BM25 排名算法](index-ranking-similarity.md)将以前的排名算法替换为更新的技术。 新服务将自动使用此算法。 对于现有服务，必须将参数设置为使用新算法。

+ 在此版本中，NULL 值的[排序结果](search-query-odata-orderby.md)发生了变化，如果排序是 `asc`，则 NULL 值首先出现；如果排序是 `desc`，则 NULL 值最后出现。 如果你编写了代码来处理如何对 NULL 值排序，则应查看该代码并可在不再需要时将其删除。

## <a name="next-steps"></a>后续步骤

+ [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 上的示例](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API 参考](/dotnet/api/overview/azure/search.documents-readme)