---
title: 升级到 Azure 搜索 .NET SDK 版本 9 - Azure 搜索
description: 从旧版本将代码迁移到 Azure 搜索 .NET SDK 版本 9。 了解新增功能和所需的代码更改。
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 32908ab209cbe05a0acf9da896e1e1fb11e6f5dd
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183239"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>升级到 Azure 搜索 .NET SDK 版本 9

如果你使用的是版本 7.0-preview 或更早版本的 [Azure 搜索 .NET SDK](https://aka.ms/search-sdk)，本文可帮助你升级应用程序，以使用版本 9。

> [!NOTE]
> 若要使用版本 8.0-preview 来评估尚未推出正式版的功能，也可以遵照本文中的说明从旧版升级到 8.0-preview。

有关包括示例的 SDK 的更多常规演练，请参阅[如何使用 .NET 应用程序中的 Azure 搜索](search-howto-dotnet-sdk.md)。

Azure 搜索 .NET SDK 的版本 9 包含了许多针对早期版本进行的更改。 其中一些更改是中断性变更，但它们只需对代码进行相对次要的更改。 有关如何更改代码以使用新版 SDK 的说明，请参阅[升级步骤](#UpgradeSteps)。

> [!NOTE]
> 如果使用的是版本 4.0-preview 或更早版本，则应先升级到版本 5，再升级到版本 9。 有关说明，请参阅[升级到 Azure 搜索 .NET SDK 版本 5](search-dotnet-sdk-migration-version-5.md)。
>
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>版本 9 中的新增功能
Azure 搜索 .NET SDK 的版本 9 针对 Azure 搜索 REST API 的最新正式发布版本，具体来说就是 2019-05-06。 这使得可在 .NET 应用程序中使用 Azure 搜索的新功能，如下所示：

* [认知搜索](cognitive-search-concept-intro.md)是 Azure 搜索中的 AI 功能，用于从图像、blob 和其他非结构化的数据源中提取文本 - 丰富内容以使其在 Azure 搜索索引中更易于搜索。
* 使用[复杂类型](search-howto-complex-data-types.md)支持几乎可为 Azure 搜索索引中的任何嵌套式 JSON 结构建模。
* [自动完成](search-autocomplete-tutorial.md)可以取代**建议** API 来实现“一边键入，一边搜索”的行为。 “自动完成”是指“完成”用户当前键入的单词或短语。
* [JsonLines 分析模式](search-howto-index-json-blobs.md)（Azure Blob 编制索引的一部分）可为每个 JSON 实体创建以换行符分隔的搜索文档。

### <a name="new-preview-features-in-version-80-preview"></a>版本 8.0-preview 中的新预览版功能
Azure 搜索.NET SDK 版本 8.0-preview 面向 API 版本 2017-11-11-Preview。 此版本包含版本 9 的所有功能，加上：

* 用于服务端静态加密的[客户托管加密密钥](search-security-manage-encryption-keys.md)是一项新的预览版功能。 如果你是密钥的唯一所有者，则除了应用 Microsoft 管理的内置静态加密以外，还可以应用一个额外的加密层。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
首先，按以下方法操作更新 `Microsoft.Azure.Search` 的 NuGet 引用：使用 NuGet 包管理器控制台，或者在 Visual Studio 中右键单击项目引用，然后选择“管理 NuGet 程序包...”。

在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。 项目重新生成可能会成功，具体取决于代码的结构。 如果成功，一切准备就绪！

如果生成失败，则需要修复每个生成错误。 有关如何解决每个潜在生成错误的详细信息，请参阅[版本 9 中的中断性变更](#ListOfChanges)。

可能会看到与已过时方法或属性有关的其他生成警告。 这些警告将包含有关使用哪些功能来替换已弃用功能的说明。 例如，如果应用程序使用 `DataSourceType.DocumentDb` 属性，将有警告指出“此成员已弃用。 请改用 CosmosDb”。

在修复了任何生成错误或警告后，可以对应用程序进行更改，以利用新功能（如果愿意）。 有关 SDK 中的新功能的详细信息，请参阅[版本 9 中的新增功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>版本 9 中的中断性变更

除重新生成应用程序以外，版本 9 中还有几项可能需要更改代码的中断性变更。

> [!NOTE]
> 以下更改列表并不详尽。 某些更改可能不会导致生成错误，但在技术上是中断性的，因为它们会破坏与依赖于早期版本 Azure 搜索.NET SDK 程序集的程序集的二进制兼容性。 下面未列出此类更改。 升级到版本 9 时请重新生成应用程序，以免出现任何二进制兼容性问题。

### <a name="immutable-properties"></a>不可变属性

多个模型类的公共属性现在是不可变的。 如果需要创建这些类的自定义实例用于测试，可以使用新的参数化构造函数：

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>对字段的更改

`Field` 类现已更改，它还可以表示复杂字段。

以下 `bool` 属性现在可为 null：

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

这是因为，在使用复杂字段时这些属性现在必须为 `null`。 如果有代码读取这些属性，必须让该代码准备好处理 `null`。 请注意，`Field` 的所有其他属性过去一直可为 null，将来继续可为 null。其中的属性在使用复杂字段时也将为 `null` -- 具体而言：

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

`Field` 的无参数构造函数已指定为 `internal`。 从现在起，每个 `Field` 在构造时都需要提供显式名称和数据类型。

### <a name="simplified-batch-and-results-types"></a>简化的批和结果类型

在 7.0-preview 和更低版本中，封装文档组的各个类已组织成并行类层次结构：

  -  `DocumentSearchResult` 和 `DocumentSearchResult<T>` 继承自 `DocumentSearchResultBase`
  -  `DocumentSuggestResult` 和 `DocumentSuggestResult<T>` 继承自 `DocumentSuggestResultBase`
  -  `IndexAction` 和 `IndexAction<T>` 继承自 `IndexActionBase`
  -  `IndexBatch` 和 `IndexBatch<T>` 继承自 `IndexBatchBase`
  -  `SearchResult` 和 `SearchResult<T>` 继承自 `SearchResultBase`
  -  `SuggestResult` 和 `SuggestResult<T>` 继承自 `SuggestResultBase`

没有泛型类型参数的派生类型需在“动态类型化”方案中使用，并采用 `Document` 类型。

从版本 8.0-preview 开始，基类和非泛型派生类全部被删除。 对于动态类型化方案，可以使用 `IndexBatch<Document>`、`DocumentSearchResult<Document>` 等。
 
### <a name="removed-extensibleenum"></a>已删除 ExtensibleEnum

已删除 `ExtensibleEnum` 基类。 派生自此类的所有类现在都是结构，例如 `AnalyzerName`、`DataType` 和 `DataSourceType`。 已删除其 `Create` 方法。 由于这些类型可从字符串隐式转换，因此只需删除对 `Create` 的调用即可。 如果这样做会导致编译器错误，则你可以通过强制转换为消歧类型，来显式调用转换运算符。 例如，可按如下所示更改代码：

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

更改为：

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

包含这些类型的可选值的属性现在会显式类型为可为 null，因此它们仍然是可选的。

### <a name="removed-facetresults-and-hithighlights"></a>已删除 FacetResults 和 HitHighlights

已删除 `FacetResults` 和 `HitHighlights` 类。 分面结果现在会类型化为 `IDictionary<string, IList<FacetResult>>`，命中结果突出显示为 `IDictionary<string, IList<string>>`。 若要解决此项更改造成的生成错误，快捷的方法是在使用已删除类型的每个文件的顶部添加 `using` 别名。 例如：

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>对 SynonymMap 的更改 

`SynonymMap` 构造函数不再为 `SynonymMapFormat` 提供 `enum` 参数。 此枚举只有一个值，因此是多余的。 如果因此看到生成错误，只需删除对 `SynonymMapFormat` 参数的引用即可。

### <a name="miscellaneous-model-class-changes"></a>其他模型类更改

`AutocompleteParameters` 的 `AutocompleteMode` 属性不再可为 null。 如果代码将此属性分配到 `null`，则你只需删除该代码，该属性会自动初始化为默认值。

`IndexAction` 构造函数的参数顺序现已更改，此构造函数会自动生成。 我们建议使用工厂方法 `IndexAction.Upload`、`IndexAction.Merge` 等，而不要使用此构造函数。

### <a name="removed-preview-features"></a>删除了预览功能

如果从版本 8.0-preview 升级到版本 9，请注意，使用客户托管密钥进行加密的功能已删除，因为此功能仍为预览版。 具体而言，已删除 `Index` 和 `SynonymMap` 的 `EncryptionKey` 属性。

如果应用程序硬依赖于这些功能，则将无法升级到 Azure 搜索 .NET SDK 的版本 9。 可继续使用版本 8.0-preview。 但是，请记住，**我们不建议在生产应用程序中使用预览版 SDK**。 预览功能仅用于评估，并且可能会更改。

> [!NOTE]
> 如果使用 8.0-preview 版 SDK 创建了加密的索引或同义词映射，则仍可以使用它们，并可以使用 SDK 版本 9 来修改其定义，而不会对其加密状态造成负面影响。 SDK 版本 9 不会将 `encryptionKey` 属性发送到 REST API，因此，REST API 不会更改资源的加密状态。 

### <a name="behavioral-change-in-data-retrieval"></a>数据检索的行为变更

如果使用返回 `Document` 的类型实例的“动态类型化”`Search`、`Suggest` 或 `Get` API，请注意，它们现在会将空的 JSON 数组反序列化为 `object[]` 而不是 `string[]`。

## <a name="conclusion"></a>结束语
如果需要有关如何使用 Azure 搜索 .NET SDK 的更多详细信息，请参阅 [.NET 操作指南](search-howto-dotnet-sdk.md)。

我们欢迎你对 SDK 提供反馈。 如果遇到问题，请随时通过 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) 向我们寻求帮助。 如果找到 Bug，可以在 [Azure .NET SDK GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/issues)中提出问题。 务必在问题标题上加前缀“[Azure 搜索]”。

感谢使用 Azure 搜索！
