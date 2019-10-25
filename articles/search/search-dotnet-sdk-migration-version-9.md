---
title: 升级到 Azure 搜索 .NET SDK 版本9
titleSuffix: Azure Cognitive Search
description: 将代码迁移到较旧版本的 Azure 搜索 .NET SDK 版本9。 了解新增功能和所需的代码更改。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793015"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>升级到 Azure 搜索 .NET SDK 版本9

如果使用的是版本 7.0-预览版或更早版本的[Azure 搜索 .NET SDK](https://aka.ms/search-sdk)，本文将帮助你将应用程序升级为使用版本9。

> [!NOTE]
> 如果你希望使用版本 8.0-preview 来评估目前尚不可用的功能，你还可以按照本文中的说明升级到从以前版本到 8.0-preview。

有关包括示例的 SDK 的更多常规演练，请参阅[如何使用 .NET 应用程序中的 Azure 搜索](search-howto-dotnet-sdk.md)。

Azure 搜索 .NET SDK 的第9版包含较早版本中的许多更改。 其中一些是重大更改，但它们只需要对代码进行相对较小的更改。 有关如何更改代码以使用新版 SDK 的说明，请参阅[升级步骤](#UpgradeSteps)。

> [!NOTE]
> 如果使用版本 4.0-预览版或更低版本，则应该先升级到版本5，然后再升级到版本9。 有关说明，请参阅[升级到 Azure 搜索 .NET SDK 版本 5](search-dotnet-sdk-migration-version-5.md) 。
>
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>版本9中的新增功能
Azure 搜索 .NET SDK 的第9版面向最新的 Azure 搜索版本 REST API，特别为2019-05-06。 这使得可在 .NET 应用程序中使用 Azure 搜索的新功能，如下所示：

* [AI 扩充](cognitive-search-concept-intro.md)是从图像、blob 和其他非结构化数据源提取文本的能力-丰富内容，使其在 Azure 搜索索引中更具可搜索性。
* 对[复杂类型](search-howto-complex-data-types.md)的支持使你可以在 Azure 搜索索引中为几乎任何嵌套的 JSON 结构建模。
* [自动完成](search-autocomplete-tutorial.md)为**建议**用于实现 "类型搜索" 行为的 API 提供了替代方法。 自动完成 "完成" 用户当前键入的词或短语。
* [JsonLines 分析模式](search-howto-index-json-blobs.md)是 Azure Blob 索引的一部分，它为每个由换行符分隔的 JSON 实体创建一个搜索文档。

### <a name="new-preview-features-in-version-80-preview"></a>版本8.0 中的新预览功能-预览
版本 8.0-预览 Azure 搜索 .NET SDK 目标 API 版本 2017-11-11-预览。 此版本包含版本9的所有相同功能，以及：

* 用于服务端加密的[客户管理的加密密钥](search-security-manage-encryption-keys.md)是一项新的预览功能。 除了由 Microsoft 管理的内置静态加密外，还可以应用其他加密层，其中你是密钥的唯一所有者。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤
首先，按以下方法操作更新 `Microsoft.Azure.Search` 的 NuGet 引用：使用 NuGet 包管理器控制台，或者在 Visual Studio 中右键单击项目引用，然后选择“管理 NuGet 程序包...”。

在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。 项目重新生成可能会成功，具体取决于代码的结构。 如果成功，一切准备就绪！

如果生成失败，将需要修复每个生成错误。 有关如何解决每个可能的生成错误的详细信息，请参阅[版本9中的重大更改](#ListOfChanges)。

可能会看到与已过时方法或属性有关的其他生成警告。 这些警告将包含有关使用哪些功能来替换已弃用功能的说明。 例如，如果你的应用程序使用 `DataSourceType.DocumentDb` 属性，则你会收到一条警告，指出 "此成员已弃用。 改为使用 CosmosDb "。

在修复了任何生成错误或警告后，可以对应用程序进行更改，以利用新功能（如果愿意）。 [版本9中的新增](#WhatsNew)功能详细说明了 SDK 中的新功能。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>版本9中的重大更改

版本9中有几项重大更改，可能需要更改代码才能重新生成应用程序。

> [!NOTE]
> 下面的更改列表并不完整。 某些更改可能不会导致生成错误，但在技术上是中断的，因为它们打破了与依赖于 Azure 搜索 .NET SDK 程序集的早期版本的程序集的二进制兼容性。 下面未列出此类更改。 请在升级到版本9时重新生成应用程序，以避免出现任何二进制兼容性问题。

### <a name="immutable-properties"></a>不可变属性

一些模型类的公共属性现在是不可变的。 如果需要为测试创建这些类的自定义实例，则可以使用新的参数化构造函数：

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>对字段的更改

现在，`Field` 类已更改，它还可以表示复杂字段。

以下 `bool` 属性现在可以是可以为 null 的：

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

这是因为在复杂字段的情况下，这些属性现在必须是 `null` 的。 如果代码读取了这些属性，则必须准备好处理 `null`。 请注意，`Field` 的所有其他属性始终都是可以为 null，并且其中的某些属性也会在复杂字段的情况下 `null`，具体如下：

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

已 `internal``Field` 的无参数构造函数。 从现在开始，每个 `Field` 都需要在构造时使用显式名称和数据类型。

### <a name="simplified-batch-and-results-types"></a>简化的批处理和结果类型

在版本 7.0-预览版和更早版本中，封装文档组的各种类将结构化为并行类层次结构：

  -  `DocumentSearchResult` 和 `DocumentSearchResult<T>` 从 `DocumentSearchResultBase` 继承
  -  `DocumentSuggestResult` 和 `DocumentSuggestResult<T>` 从 `DocumentSuggestResultBase` 继承
  -  `IndexAction` 和 `IndexAction<T>` 从 `IndexActionBase` 继承
  -  `IndexBatch` 和 `IndexBatch<T>` 从 `IndexBatchBase` 继承
  -  `SearchResult` 和 `SearchResult<T>` 从 `SearchResultBase` 继承
  -  `SuggestResult` 和 `SuggestResult<T>` 从 `SuggestResultBase` 继承

不带泛型类型参数的派生类型旨在用于 "动态类型化" 方案并假定 `Document` 类型的用法。

从版本 8.0-preview 开始，已删除基类和非泛型派生类。 对于动态类型方案，可以使用 `IndexBatch<Document>`、`DocumentSearchResult<Document>`等。
 
### <a name="removed-extensibleenum"></a>删除的 ExtensibleEnum

已删除 `ExtensibleEnum` 的基类。 派生自它的所有类现在都是结构，例如 `AnalyzerName`、`DataType`和 `DataSourceType`。 还删除了其 `Create` 方法。 只需删除对 `Create` 的调用，因为这些类型可从字符串隐式转换。 如果这导致编译器错误，则可以通过强制转换为歧义类型显式调用转换运算符。 例如，你可以更改代码，如下所示：

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

保留这些类型的可选值的属性现在显式键入为可为 null，因此它们继续是可选的。

### <a name="removed-facetresults-and-hithighlights"></a>已删除 FacetResults 和 HitHighlights

`FacetResults` 和 `HitHighlights` 类已被移除。 分面结果现在被类型化为 `IDictionary<string, IList<FacetResult>>`，并将突出显示为 `IDictionary<string, IList<string>>`。 解决此更改引入的生成错误的快速方法是在每个使用已删除类型的文件的顶部添加 `using` 别名。 例如：

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>更改为 SynonymMap 

`SynonymMap` 构造函数不再为 `SynonymMapFormat` 提供 `enum` 参数。 此枚举只有一个值，因此是多余的。 如果因此看到生成错误，只需删除对 `SynonymMapFormat` 参数的引用即可。

### <a name="miscellaneous-model-class-changes"></a>其他模型类更改

`AutocompleteParameters` 的 `AutocompleteMode` 属性不再可以为 null。 如果有将此属性分配给 `null`的代码，则只需删除该属性，属性就会自动初始化为默认值。

由于此构造函数是自动生成的，因此 `IndexAction` 构造函数的参数顺序已更改。 建议不要使用构造函数，而是建议使用 `IndexAction.Upload`、`IndexAction.Merge`等的工厂方法。

### <a name="removed-preview-features"></a>删除了预览功能

如果要从版本 8.0-预览版升级到版本9，请注意，由于此功能仍处于预览阶段，因此已删除客户托管密钥的加密。 具体而言，将删除 `Index` 和 `SynonymMap` 的 `EncryptionKey` 属性。

如果你的应用程序对此功能有硬性依赖，你将无法升级到 Azure 搜索 .NET SDK 的第9版。 你可以继续使用版本 8.0-preview。 但是，请记住，**我们不建议在生产应用程序中使用预览版 SDK**。 预览功能仅用于评估，并且可能会更改。

> [!NOTE]
> 如果使用版本 8.0-预览版创建了加密索引或同义词映射，则仍可使用 sdk 的版本9并使用 SDK 的版本9修改其定义，而不会对其加密状态产生负面影响。 版本9的 SDK 不会将 `encryptionKey` 属性发送到 REST API，因此 REST API 不会更改资源的加密状态。 

### <a name="behavioral-change-in-data-retrieval"></a>数据检索的行为更改

如果你使用的是 "动态类型化" `Search`、`Suggest`或返回 `Document`类型实例的 `Get` Api，请注意，它们现在会将空 JSON 数组反序列化为 `object[]` 而不是 `string[]`。

## <a name="conclusion"></a>结束语
如果需要有关如何使用 Azure 搜索 .NET SDK 的更多详细信息，请参阅 [.NET 操作指南](search-howto-dotnet-sdk.md)。

我们欢迎你对 SDK 提供反馈。 如果遇到问题，请随时咨询获取有关[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)的帮助。 如果找到 Bug，可以在 [Azure .NET SDK GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/issues)中提出问题。 务必在问题标题上加前缀“[Azure 搜索]”。

感谢使用 Azure 搜索！
