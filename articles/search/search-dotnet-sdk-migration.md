---
title: "升级到 Azure 搜索 .NET SDK 版本 1.1 | Microsoft Docs"
description: "升级到 Azure 搜索 .NET SDK 版本 1.1"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 66f89958-a320-4a24-87f9-69315848909f
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 9782454e3bfc697b63cde8aa28a14be0c393c36b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>升级到 Azure 搜索 .NET SDK 版本 3
如果使用的是版本 2.0-preview 或更早版本的 [Azure 搜索 .NET SDK](https://aka.ms/search-sdk)，本文有助于升级应用程序，以便使用版本 3。

有关包括示例的 SDK 的更多常规演练，请参阅[如何使用 .NET 应用程序中的 Azure 搜索](search-howto-dotnet-sdk.md)。

版本 3 的 Azure 搜索 .NET SDK 包含了某些针对早期版本进行的更改。 这些更改主要涉及次要版本，因此更改代码的工作量并不是太大。 有关如何更改代码以使用新版 SDK 的说明，请参阅[升级步骤](#UpgradeSteps)。

> [!NOTE]
> 如果使用的是版本 1.0.2-preview 或更早版本，则应该先升级到版本 1.1，再升级到版本 3。 有关说明，请参阅[附录：升级到版本 1.1 的步骤](#UpgradeStepsV1)。
>
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>版本 3 中的新增功能
版本 3 的 Azure 搜索 .NET SDK 针对 Azure 搜索 REST API 的最新正式发布版本，具体来说就是 2016-09-01。 这使得可以在 .NET 应用程序中使用 Azure 搜索的许多新功能，如下所示：

* [自定义分析器](https://aka.ms/customanalyzers)
* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)和 [Azure 表存储](search-howto-indexing-azure-tables.md)索引器支持
* 通过[字段映射](search-indexer-field-mappings.md)实现的索引器自定义
* 用于支持安全并发更新索引定义、索引器和数据源的 ETag 支持
* 支持通过修饰模型类并使用新的 `FieldBuilder` 类，以声明方式构建索引字段定义。
* 对 .NET Core 和 .NET Portable Profile 111 的支持

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
首先，请更新你 NuGet 参考`Microsoft.Azure.Search`使用 NuGet 包管理器控制台或通过右键单击项目引用，并在 Visual Studio 中选择"管理 NuGet 包..."。

在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。 项目重新生成可能会成功，具体取决于代码的结构。 如果成功，一切准备就绪！

如果生成失败，应该会看到如下所示的生成错误：

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

下一步是修复生成错误。 有关出错原因和修复方法的详细信息，请参阅[版本 3 中的重大更改](#ListOfChanges)。

你可能会看到与已过时的方法或属性相关的其他生成警告。 这些警告将包含有关使用哪些功能来替换已弃用功能的说明。 例如，如果应用程序使用了 `IndexingParameters.Base64EncodeKeys` 属性，应该会收到显示 `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."` 的警告

在修复了任何生成错误后，可以对应用程序进行更改，以利用新功能（如果愿意）。 有关 SDK 中的新功能的详细信息，请参阅[版本 3 中的新增功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>版本 3 中的重大更改
除重新生成应用程序之外，版本 3 中只有少量可能需要更改代码的重大更改。

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient 返回类型
`Indexes.GetClient` 方法具有新的返回类型。 以前，它返回 `SearchIndexClient`，但在版本 2.0-preview 中已将此项更改为 `ISearchIndexClient`，而该更改将传递给版本 3。 这是为了支持想要通过返回 `ISearchIndexClient` 的模拟实现，来模拟测试单元的 `GetClient` 方法的客户。

#### <a name="example"></a>示例
如果代码如下所示：

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

若要修复任何生成错误，可以更改为如下所示的代码：

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName、DataType 等不再隐式转换为字符串
Azure 搜索 .NET SDK 中有许多派生自 `ExtensibleEnum` 的类型。 以前，这些类型都隐式转换为 `string` 类型。 但是，在这些类的 `Object.Equals` 实现中发现了 bug，修复 bug 需要禁止此隐式转换。 仍允许显式转换为 `string`。

#### <a name="example"></a>示例
如果代码如下所示：

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

若要修复任何生成错误，可以更改为如下所示的代码：

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>删除了过时成员

你可能会看到与相关的生成错误方法或 2.0 预览并随后删除版本 3 中被标记为过时版本中的属性。 如果遇到此类错误，可按下面所述解决它们：

- 如果使用此构造函数：`ScoringParameter(string name, string value)`，请改为使用这个：`ScoringParameter(string name, IEnumerable<string> values)`
- 如果使用 `ScoringParameter.Value` 属性，请改为使用 `ScoringParameter.Values` 属性或 `ToString` 方法。
- 如果使用 `SearchRequestOptions.RequestId` 属性，请改为使用 `ClientRequestId` 属性。

### <a name="removed-preview-features"></a>删除了预览功能

如果要从版本 2.0-preview 升级到版本 3，请注意，JSON 和 CSV 对 Blob 索引器的分析支持已删除，因为这些功能仍处于预览状态。 具体而言，`IndexingParametersExtensions` 类的以下方法已删除：

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

如果应用程序硬依赖于这些功能，则将不能升级到版本 3 的 Azure 搜索 .NET SDK。 可以继续使用版本 2.0-preview。 但是，请记住，**我们不建议在生产应用程序中使用预览版 SDK**。 预览功能仅用于评估，并且可能会更改。

## <a name="conclusion"></a>结束语
如果需要有关如何使用 Azure 搜索 .NET SDK 的更多详细信息，请参阅我们最近更新的[操作指南](search-howto-dotnet-sdk.md)。

我们欢迎你的反馈在 SDK 上。 如果遇到问题，请随时通过 [Azure 搜索 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)向我们寻求帮助。 如果找到 Bug，可以在 [Azure .NET SDK GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/issues)中提出问题。 务必在问题标题上加前缀“Search SDK:”。

感谢使用 Azure 搜索！

<a name="UpgradeStepsV1"></a>

## <a name="appendix-steps-to-upgrade-to-version-11"></a>附录：升级到版本 1.1 的步骤
> [!NOTE]
> 本部分仅适用于使用 Azure 搜索 .NET SDK 版本 1.0.2-preview 或更早版本的用户。
> 
> 

首先，请更新你 NuGet 参考`Microsoft.Azure.Search`使用 NuGet 包管理器控制台或通过右键单击项目引用，并在 Visual Studio 中选择"管理 NuGet 包..."。

在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。

如果之前使用的是版本 1.0.0-preview、1.0.1-preview 或 1.0.2-preview，生成应该已成功，一切准备就绪！

如果之前使用的是版本 0.13.0-preview 或更早版本，应该会看到如下所示的生成错误：

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

下一步是逐个修复生成错误。 大多数修复需要更改 SDK 中已重命名的一些类和方法名称。 [版本 1.1 中的重大更改列表](#ListOfChangesV1)含有这些名称更改的列表。

如果使用自定义类对文档建模，并且这些类具有不可为 null 的基元类型的属性（例如，C# 中的 `int` 或 `bool`），应知道 1.1 版的 SDK 中会有 Bug 修复。 有关详细信息，请参阅[版本 1.1 中的 Bug 修复](#BugFixesV1)。

最后，在修复了任何生成错误后，可以对应用程序进行更改，以利用新功能（如果愿意）。

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>版本 1.1 中的重大更改列表
以下列表按更改会影响应用程序代码的可能性排序。

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch 和 IndexAction 更改
`IndexBatch.Create` 已重命名为 `IndexBatch.New`，且不再有 `params` 参数。 可以将 `IndexBatch.New` 用于混用不同类型操作（合并、删除等）的 Batch。 此外，还有可用于创建 Batch（其中的所有操作都相同）的新静态方法：`Delete`、`Merge`、`MergeOrUpload` 和 `Upload`。

`IndexAction` 不再有公共构造函数，并且其属性现已不可变。 应该使用新的静态方法针对不同目的创建操作：`Delete`、`Merge`、`MergeOrUpload` 和 `Upload`。 `IndexAction.Create` 已删除。 如果使用了仅需要一个文档的重载，请务必改为使用 `Upload`。

##### <a name="example"></a>示例
如果代码如下所示：

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

若要修复任何生成错误，可以更改为如下所示的代码：

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

如有需要，可以进一步简化为如下所示的代码：

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>IndexBatchException 更改
`IndexBatchException.IndexResponse` 属性已重命名为 `IndexingResults`，且其类型现为 `IList<IndexingResult>`。

##### <a name="example"></a>示例
如果代码如下所示：

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

若要修复任何生成错误，可以更改为如下所示的代码：

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

#### <a name="operation-method-changes"></a>操作方法更改
Azure 搜索 .NET SDK 中的每个操作都公开为同步和异步调用方的一组方法重载。 这些方法重载的签名和分解在版本 1.1 中已更改。

例如，较早版本的 SDK 中的“获取索引统计信息”操作公开以下签名：

在 `IIndexOperations` 中：

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

在 `IndexOperationsExtensions` 中：

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

版本 1.1 中同一操作的方法签名如下所示：

在 `IIndexesOperations` 中：

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

在 `IndexesOperationsExtensions` 中：

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

从版本 1.1 开始，Azure 搜索 .NET SDK 以不同方式组织操作方法：

* 可选参数现已建模为默认参数，而不是附加的方法重载。 这有时可显著减少方法重载的数目。
* 扩展方法现在对调用方隐藏了许多与 HTTP 无关的细节。 例如，较早版本的 SDK 返回响应对象时带有 HTTP 状态代码，通常不需要检查这些状态代码，因为操作方法会为指示错误的任何状态代码引发 `CloudException`。 新的扩展方法仅返回模型对象，从而使您无需解除它们包装在代码中的问题。
* 核心接口现在反而公开了允许在 HTTP 级别进行更多控制的方法（如果需要）。 您现在可以在要包括在请求和新的自定义 HTTP 头中传递`AzureOperationResponse<T>`返回类型可直接访问`HttpRequestMessage`和`HttpResponseMessage`操作。 `AzureOperationResponse` 在 `Microsoft.Rest.Azure` 命名空间中定义，替换 `Hyak.Common.OperationResponse`。

#### <a name="scoringparameters-changes"></a>ScoringParameters 更改
名为 `ScoringParameter` 的新类已添加到最新的 SDK 中，使向搜索查询中的计分配置文件提供参数更为容易。 之前，`SearchParameters` 类的 `ScoringProfiles` 属性以 `IList<string>` 形式键入；现在它以 `IList<ScoringParameter>` 形式键入。

##### <a name="example"></a>示例
如果代码如下所示：

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

若要修复任何生成错误，可以更改为如下所示的代码： 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>模型类更改
由于[操作方法更改](#OperationMethodChanges)中描述的签名更改，`Microsoft.Azure.Search.Models` 命名空间中的许多类已重命名或已删除。 例如：

* `IndexDefinitionResponse` 已替换为 `AzureOperationResponse<Index>`
* `DocumentSearchResponse` 已重名为 `DocumentSearchResult`
* `IndexResult` 已重名为 `IndexingResult`
* `Documents.Count()` 现在返回带有文档计数的 `long`，而不是 `DocumentCountResponse`
* `IndexGetStatisticsResponse` 已重名为 `IndexGetStatisticsResult`
* `IndexListResponse` 已重名为 `IndexListResult`

总之，仅用于包裹模型对象的现有 `OperationResponse` 派生类已删除。 其余类已将其后缀从 `Response` 更改为 `Result`。

##### <a name="example"></a>示例
如果代码如下所示：

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

若要修复任何生成错误，可以更改为如下所示的代码：

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>响应类和 IEnumerable
可能影响代码的其他更改是：保留集合的响应类不再实现 `IEnumerable<T>`。 相反，可以直接访问集合属性。 例如，如果代码如下所示：

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

若要修复任何生成错误，可以更改为如下所示的代码：

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="special-case-for-web-applications"></a>Web 应用程序的特例
如果有一个直接序列化 `DocumentSearchResponse` 以向浏览器发送搜索结果的 Web 应用程序，将需要更改代码，否则结果将不会正确序列化。 例如，如果代码如下所示：

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

可以通过获取搜索响应的 `.Results` 属性来更改代码，以修复结果呈现：

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

必须自己在代码中查找此类情况；**编译器不会警告你**，因为 `JsonResult.Data` 属于类型 `object`。

#### <a name="cloudexception-changes"></a>CloudException 更改
`CloudException` 类已从 `Hyak.Common` 命名空间移动到 `Microsoft.Rest.Azure` 命名空间。 此外，其 `Error` 属性已重名为 `Body`。

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient 和 SearchIndexClient 更改
`Credentials` 属性的类型已从 `SearchCredentials` 更改为其基类（即 `ServiceClientCredentials`）。 如果需要访问 `SearchIndexClient` 或 `SearchServiceClient` 的 `SearchCredentials`，请使用新的 `SearchCredentials` 属性。

在早期版本的 SDK 中，`SearchServiceClient` 和 `SearchIndexClient` 具有需要 `HttpClient` 参数的构造函数。 这些已替换为需要 `HttpClientHandler` 和一个 `DelegatingHandler` 对象数组的构造函数。 这使得必要时安装用于预处理 HTTP 请求的自定义处理程序更为容易。

最后，需要 `Uri` 和 `SearchCredentials` 的构造函数已更改。 例如，如果代码如下所示：

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

若要修复任何生成错误，可以更改为如下所示的代码：

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

另请注意，凭据参数的类型已更改为 `ServiceClientCredentials`。 由于 `SearchCredentials` 派生自 `ServiceClientCredentials`，所以这不太可能影响代码。

#### <a name="passing-a-request-id"></a>传递请求 ID
在早期版本的 SDK 中，可以设置 `SearchServiceClient` 或 `SearchIndexClient` 上的请求 ID，它将包含在对 REST API 的每个请求中。 如果需要与支持人员联系，这对于解决搜索服务的问题非常有用。 不过，为每个操作设置唯一请求 ID 更加有用，而不是将同一 ID 用于所有操作。 出于此原因，`SearchServiceClient` 和 `SearchIndexClient` 的 `SetClientRequestId` 方法已删除。 相反，可以通过可选参数 `SearchRequestOptions` 将请求 ID 传递给每个操作方法。

> [!NOTE]
> 在将来版本的 SDK 中，我们会添加一个新机制，用于在客户端对象上全局设置请求 ID，这与其他 Azure SDK 使用的方法一致。
> 
> 

#### <a name="example"></a>示例
如果代码如下所示：

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

若要修复任何生成错误，可以更改为如下所示的代码：

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>接口名称更改
操作组接口名称已全部更改，与其相应的属性名称保持一致：

* `ISearchServiceClient.Indexes` 的类型已从 `IIndexOperations` 更改为 `IIndexesOperations`。
* `ISearchServiceClient.Indexers` 的类型已从 `IIndexerOperations` 更改为 `IIndexersOperations`。
* `ISearchServiceClient.DataSources` 的类型已从 `IDataSourceOperations` 更改为 `IDataSourcesOperations`。
* `ISearchIndexClient.Documents` 的类型已从 `IDocumentOperations` 更改为 `IDocumentsOperations`。

此更改不太可能影响代码，除非出于测试目的创建了这些接口的模拟。

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>版本 1.1 中的 Bug 修复
与自定义模型类的序列化有关的早期版本的 Azure 搜索 .NET SDK 中存在 Bug。 如果使用不可为 null 的值类型的属性创建了自定义模型类，可能会出现 Bug。

#### <a name="steps-to-reproduce"></a>重现步骤
使用不可为 null 的值类型的属性创建自定义模型类。 例如，添加类型为 `int`（而不是 `int?`）的公共 `UnitCount` 属性。

如果使用该类型的默认值（例如，`int` 的 0）对文档编制索引，该字段在 Azure 搜索将为 null。 如果随后搜索该文档，`Search` 调用会引发 `JsonSerializationException`，声称无法将 `null` 转换为 `int`。

此外，筛选器可能不会按预期工作，因为 null 已写入索引，而不是预期值。

#### <a name="fix-details"></a>修复详细信息
我们修复了版本 1.1 的 SDK 中存在的此问题。 现在，如果模型类如下所示：

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

将 `IntValue` 设置为 0，该值现在线路上正确序列化为 0，并在索引中存储为 0。 往返过程也按预期工作。

使用此方法时有一个潜在的问题需要注意：如果将模型类型与不可为 null 的属性一起使用，必须**保证**索引中的所有文档的对应字段都不包含 null 值。 该 SDK 和 Azure 搜索 REST API 都不会帮助强制实施此检查。

这不只是假想的问题：假设将新字段添加到 `Edm.Int32`类型的现有索引。 更新索引定义后，所有文档的该新字段都具有 null 值（因为 Azure 搜索中的所有类型都可以为 null）。 如果随后使用该字段具有不可为 null `int` 属性的模型类，则在尝试检索文档时会获得如下所示的 `JsonSerializationException`：

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

由于此原因，最佳做法仍建议在模型类中使用可以为 null 的类型。

有关此 Bug 和修复的更多详细信息，请参阅 [GitHub 上的此问题](https://github.com/Azure/azure-sdk-for-net/issues/1063)。

