---
title: 升级到 .NET SDK 版本11
titleSuffix: Azure Cognitive Search
description: 将代码从较旧版本迁移到 Azure 认知搜索 .NET SDK 版本11。 了解新增功能和所需的代码更改。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 644184f5bb51d049d890655ada5be5ebd4c11bf7
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908388"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>升级到 Azure 认知搜索 .NET SDK 版本11

如果你使用的是版本10.0 或更低版本的[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)，本文将帮助你升级到版本11。

版本11是完全重新设计的客户端库，由 Azure SDK 开发团队发布 (以前版本由 Azure 认知搜索开发团队) 生成。 为了更好地与其他 Azure 客户端库保持一致，已对库进行了重新设计，从而依赖于[Azure Core](https://docs.microsoft.com/dotnet/api/azure.core)和[System.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json)，并为常见任务实现了熟悉的方法。

你在新版本中注意到的一些主要区别包括：

+ 一个包和库，而不是多个
+ 新的包名称： `Azure.Search.Documents` 而不是 `Microsoft.Azure.Search` 。
+ 三个客户端，而不是两个： `SearchClient` 、 `SearchIndexClient` 、`SearchIndexerClient`
+ 跨一系列 Api 命名差异以及简化某些任务的小型结构差异

## <a name="package-and-library-consolidation"></a>包和库合并

版本11将多个包和库合并为一个。 迁移后，可以管理的库就更少。

+ [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [客户端库的 API 参考](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>客户端差异

如果适用，下表将在两个版本之间映射客户端库。

| 操作范围 | &nbsp; (v10) 搜索 | Azure.Search.Documents &nbsp; (v11)  |
|---------------------|------------------------------|------------------------------|
| 用于查询和填充索引的客户端。 | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| 用于索引、分析器、同义词映射的客户端 | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 用于索引器、数据源、技能集的客户端 | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**new**) ](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> 版本11将版本10重命名 `SearchIndexClient` 为 `SearchClient` ，然后重新使用适用于索引、分析器和同义词地图对象的客户端的名称。 更新客户端引用时，请遵循[升级步骤](#UpgradeSteps)中的步骤顺序，以避免在搜索和替换过程中出现混淆。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>命名和其他 API 差异

除了 (前面提到的客户端差异，因此) 将其省略，在某些情况下，已重新设计了多个其他 Api。 下面汇总了类名称差异。 此列表并不详尽，但它按任务进行组 API 更改，这对于特定代码块上的修订非常有用。 有关 API 更新的详细列表，请参阅 GitHub 上的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` 。

### <a name="authentication-and-encryption"></a>身份验证和加密

| 版本10 | 版本11等效项 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (作为一项公开功能存在于[预览版 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview)中)  | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>索引、分析器、同义词映射

| 版本10 | 版本11等效项 |
|------------|-----------------------|
| [索引](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [字段](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [分析器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (还 `AnalyzerName` `LexicalAnalyzerName`)  |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (还 `StandardTokenizerV2` `LuceneStandardTokenizerV2`)  |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [分词器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (还 `TokenizerName` `LexicalTokenizerName`)  |
| [SynonymMap. 格式](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 无。 删除对的引用 `Format` 。 |

简化了字段定义： [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield)、 [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield)、 [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield)是用于创建字段定义的新 api。

### <a name="indexers-datasources-skillsets"></a>索引器、数据源、技能集

| 版本10 | 版本11等效项 |
|------------|-----------------------|
| [索引器](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [技能](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [技能集](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>数据导入

| 版本10 | 版本11等效项 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>查询定义和结果

| 版本10 | 版本11等效项 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1)或[SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1)，具体取决于结果是单个文档还是多个文档。 |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>版本11中的内容

Azure 认知搜索客户端库的每个版本都面向 REST API 的相应版本。 REST API 被视为服务的基础，而各个 Sdk 包装了 REST API 版本。 作为 .NET 开发人员，如果您需要有关特定对象或操作的更多背景知识，那么查看[REST API 文档](https://docs.microsoft.com/rest/api/searchservice/)会很有帮助。

版本11针对[2020-06-30 搜索服务](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)。 由于第11版也是从根本上构建的新的客户端库，因此，大多数开发工作的重点是使用版本10，并且某些 REST API 功能支持仍处于挂起状态。

版本11完全支持以下对象和操作：

+ 索引的创建和管理
+ 同义词映射的创建和管理
+ 除地理空间筛选器外 (所有查询类型和语法) 
+ 用于索引 Azure 数据源的索引器对象和操作，包括数据源和技能集

### <a name="pending-features"></a>挂起的功能

版本11中尚不提供以下版本10功能。 如果使用这些功能，请在迁移之前保留迁移。

+ [地理空间类型](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.serialization.geojsonextensions)
+ 尽管可以使用[此解决方法](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)) [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (。
+ [知识存储](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤

以下步骤通过遍历第一组所需任务（特别是在客户端引用中）来开始执行代码迁移。

1. 右键单击项目引用并选择 "管理 NuGet 包 ..."，安装[Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)在 Visual Studio 中。

1. 将使用指令替换为以下内容：

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 将[SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials)替换为[AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential)。

1. 更新与索引器相关的对象的客户端引用。 如果使用索引器、数据源或技能集，请将客户端引用更改为[SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient)。 此客户端是版本11中的新客户端，没有 antecedent。

1. 更新查询和数据导入的客户端引用。 应将[SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)的实例更改为[SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient)。 若要避免名称混乱，请确保在继续下一步之前捕获所有实例。

1. 更新索引、索引器、同义词映射和分析器对象的客户端引用。 应将[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)的实例更改为[SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient)。 

1. 尽可能多地使用新库的 Api 来更新类、方法和属性。 [命名差异](#naming-differences)部分是一个启动位置，但你也可以查看[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)。

   如果在查找等效 Api 时遇到问题，建议你记录问题， [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) 以便我们可以改进文档或调查问题。

1. 重新生成解决方案。 修复任何生成错误或警告后，你可以对应用程序进行其他更改，以利用[新功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>版本11中的重大更改

由于对库和 Api 进行了全面的更改，升级到版本11是一项非常重要的升级，并在意义上形成了重大更改。 若要全面了解差异，请参阅的[更改日志](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` 。

就服务版本而言，从10个迁移到11会引入以下行为更改： 

+ [BM25 排名算法](index-ranking-similarity.md)将以前的排名算法替换为更新的技术。 新服务将自动使用此算法。 对于现有服务，必须将参数设置为使用新算法。

+ 在此版本中，空值的[排序结果](search-query-odata-orderby.md)已更改，如果排序为，则 null 值将显示为第一个 `asc` `desc` 。 如果你编写代码来处理如何排序 null 值，则应查看并可能删除该代码（如果不再需要）。

## <a name="next-steps"></a>后续步骤

+ [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 上的示例](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Docargumentable API 参考](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)