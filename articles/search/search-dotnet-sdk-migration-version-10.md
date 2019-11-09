---
title: 升级到 Azure 认知搜索 .NET SDK 版本10
titleSuffix: Azure Cognitive Search
description: 将代码从较旧版本迁移到 Azure 认知搜索 .NET SDK 版本10。 了解新增功能和所需的代码更改。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847525"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>升级到 Azure 认知搜索 .NET SDK 版本10

如果你使用的是 9.0 或更低版本的 [Azure 搜索 .NET SDK](https://aka.ms/search-sdk)，本文可帮助你升级应用程序，以使用版本 10。

Azure 搜索已重命名为版本10中的 Azure 认知搜索，但命名空间和包名称不变。 先前版本的 SDK （9.0 及更早版本）继续使用以前的名称。 有关使用 SDK 的详细信息（包括示例），请参阅[如何通过 .Net 应用程序使用 Azure 认知搜索](search-howto-dotnet-sdk.md)。

版本 10 中增加了多项功能和 bug 修复，其功能级别与最新的 REST API 版本 `2019-05-06` 相同。 如果某项更改会中断现有的代码，我们将[逐步引导你解决相关的问题](#UpgradeSteps)。

> [!NOTE]
> 如果你使用的是版本 8.0-preview 或更低版本，应先升级到版本 9，再升级到版本 10。 有关说明，请参阅[升级到 Azure 搜索 .NET SDK 版本 9](search-dotnet-sdk-migration-version-9.md)。
>
> 搜索服务实例支持多个 REST API 版本，包括最新版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>版本 10 中的新增功能
Azure 认知搜索 .NET SDK 的第10版使用以下更新面向 REST API （`2019-05-06`）的最新正式发布版本：

* 引入了两项新技能 - [条件技能](cognitive-search-skill-conditional.md)和[文本翻译技能](cognitive-search-skill-text-translation.md)。
* 重新组织了[整形程序技能](cognitive-search-skill-shaper.md)输入的结构，以适应嵌套上下文的合并。 有关详细信息，请参阅此[示例 JSON 定义](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)。
* 添加了两个新的[字段映射函数](search-indexer-field-mappings.md)：
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* 在某些情况下，[索引器执行状态](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)中显示的错误和警告可以提供有助于调试的更多详细信息。 `IndexerExecutionResult` 已更新，以反映此行为。
* 可以选择性地通过指定 [ 属性来识别](cognitive-search-defining-skillset.md)技能集`name`中定义的单个技能。
* `ServiceLimits` 显示[复杂类型](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)的限制，`IndexerExecutionInfo` 显示相关的索引器限制/配额。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤

1. 按以下方式更新 `Microsoft.Azure.Search` 的 NuGet 引用：使用 NuGet 包管理器控制台，或者在 Visual Studio 中右键单击项目引用，然后选择“管理 NuGet 程序包...”。

2. 在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。 

3. 如果生成失败，则需要修复每个生成错误。 有关如何解决每个潜在生成错误的详细信息，请参阅[版本 10 中的中断性变更](#ListOfChanges)。

4. 在修复了任何生成错误或警告后，可以对应用程序进行更改，以利用新功能（如果愿意）。 有关 SDK 中的新功能的详细信息，请参阅[版本 10 中的新增功能](#WhatsNew)。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>版本 10 中的中断性变更

除重新生成应用程序以外，版本 10 中还有几项可能需要更改代码的中断性变更。

> [!NOTE]
> 以下更改列表并不详尽。 某些更改可能不会导致生成错误，但从技术上说，它们打破了与依赖于 Azure 认知搜索 .NET SDK 程序集的早期版本的程序集的二进制兼容性。 属于此类别的重大更改也会随建议一起列出。 升级到版本 10 时请重新生成应用程序，以免出现任何二进制兼容性问题。

### <a name="custom-web-api-skill-definition"></a>自定义 Web API 技能定义

版本 9 和更低版本中错误地指定了[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)的定义。 

`WebApiSkill` 的模型将 `HttpHeaders` 指定为包含字典的对象属性。 以这种方式创建带有 `WebApiSkill` 构造的技能集会导致异常，因为 REST API 会将请求视为格式不当。 此问题已得到更正，`HttpHeaders` 现在会设置为  **模型本身上的**顶级字典属性`WebApiSkill` - 请求被视为来自 REST API 的有效请求。

例如，如果你以前尝试按如下所示实例化 `WebApiSkill`：

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

将它更改为以下代码可以避免 REST API 出现验证错误：

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>整形程序技能允许嵌套的上下文合并

整形程序技能现在允许合并嵌套上下文中的输入。 为了实现此更改，我们修改了 `InputFieldMappingEntry`，这样，只需指定 `Source` 属性，或者同时指定 `SourceContext` 和 `Inputs` 属性，即可将其实例化。

你很有可能不需要进行任何代码更改；但请注意，只允许这两种组合中的一种。 这意味着：

- 创建只初始化 `InputFieldMappingEntry` 的 `Source` 是有效操作。
- 创建只初始化 `InputFieldMappingEntry` 和 `SourceContext` 的 `Inputs` 是有效操作。
- 涉及这三个属性的所有其他组合是无效的。

如果你决定开始利用此新功能，请确保在实施该更改之前，先将所有客户端更新为使用版本 10。 否则，客户端（使用较旧版本的 SDK）对整形程序技能做出的更新可能导致验证错误。

> [!NOTE]
> 即使已将底层 `InputFieldMappingEntry` 模型修改为允许从嵌套上下文合并，但只能在整形程序技能的定义内使用该模型。 在其他技能中使用此功能会在运行时导致验证错误（尽管在编译时是正常的）。

## <a name="skills-can-be-identified-by-a-name"></a>可以按名称识别技能

技能集中的每个技能现在包含一个新属性 `Name`，可以在代码中初始化该属性，以帮助识别该技能。 这是可选的操作 - 如果未指定（如果未进行显式代码更改，则默认不会指定），将使用技能集中技能的从 1 开始的索引，为该技能分配一个带有“#”前缀字符的默认名称。 例如，在以下技能集定义中（对简洁起见，已跳过大部分初始化步骤）：

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

为 `SentimentSkill` 分配了名称 `#1`，为 `WebApiSkill` 分配了名称 `#2`，为 `ShaperSkill` 分配了名称 `#3`，依此类推。

如果选择按自定义名称识别技能，请务必先将客户端的所有实例更新到 SDK 版本 10。 否则，可能会出现这种问题：使用较旧 SDK 版本的客户端将技能的 `null` 属性设置为 `Name`，从而导致客户端回退到默认命名方案。

## <a name="details-about-errors-and-warnings"></a>有关错误和警告的详细信息

`ItemError` 和 `ItemWarning` 模型（分别用于封装索引器执行期间发生的错误和警告的详细信息）已经过修改，现在包含三个旨在帮助调试索引器的新属性。 这些属性为：

- `Name`：产生错误的源的名称。 例如，错误中可能会提到附加的技能集中的特定技能。
- `Details`：有关错误或警告的其他详细信息。
- `DocumentationLink`：指向特定错误或警告的故障排除指南的链接。

> [!NOTE]
> 我们已开始组织错误和警告的内容，以尽可能地包含这些有用的信息。 我们正努力确保在所有错误和警告中提供这些详细信息，但这项工作正在进行，并且不一定总会填充这些附加的详细信息。

## <a name="next-steps"></a>后续步骤

- 更改整形程序技能很有可能会对新代码或现有代码造成影响。 下一步，请务必重新访问此示例，其中阐释了输入结构：[整形技巧 JSON 定义示例](cognitive-search-skill-shaper.md)
- 通读[AI 扩充概述](cognitive-search-concept-intro.md)。
- 我们欢迎你对 SDK 提供反馈。 如果遇到问题，请随时通过 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) 向我们寻求帮助。 如果找到 Bug，可以在 [Azure .NET SDK GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/issues)中提出问题。 请确保用 "[Azure 认知搜索]" 作为问题标题的前缀。

