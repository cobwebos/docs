---
title: 升级到 Azure 搜索 .NET SDK 版本 10-Azure 搜索
description: 将代码从较旧版本迁移到 Azure 搜索 .NET SDK 版本10。 了解新增功能和所需的代码更改。
author: arv100kri
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: arjagann
ms.custom: seodec2018
ms.openlocfilehash: e4633a1c0543331b0ea9820703ed685fb99f2130
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182363"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-10"></a>升级到 Azure 搜索 .NET SDK 版本10

如果使用的是版本9.0 或更早版本的[Azure 搜索 .NET SDK](https://aka.ms/search-sdk), 本文将帮助你将应用程序升级为使用版本10。

有关包括示例的 SDK 的更多常规演练，请参阅[如何使用 .NET 应用程序中的 Azure 搜索](search-howto-dotnet-sdk.md)。

版本10添加了多个功能和 bug 修复, 使其与 REST API 版本`2019-05-06`的最新版本相同。 如果更改中断了现有代码, 我们将引导你完成[解决问题所需的步骤](#UpgradeSteps)。

> [!NOTE]
> 如果使用版本 8.0-预览版或更低版本, 则应该先升级到版本 9, 然后再升级到版本10。 有关说明, 请参阅[升级到 Azure 搜索 .NET SDK 版本 9](search-dotnet-sdk-migration-version-9.md) 。
>
> Azure 搜索服务实例支持多个 REST API 版本，包括最新的版本。 可以不使用最新版本，但是我们建议迁移代码，以便使用最新版本。 使用 REST API 时，必须在每个请求中通过 api-version 参数指定 API 版本。 使用 .NET SDK 时，使用的 SDK 版本确定对应的 REST API 版本。 如果使用较旧的 SDK，即使已升级服务以支持较新的 API 版本，也可以继续运行代码，而不进行任何更改。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>版本10中的新增功能
版本10的 azure 搜索 .net SDK 以最新的 azure 搜索版本 REST API (`2019-05-06`) 为目标, 其中包含以下更新:

* 引入了两项新的技能-[条件技能](cognitive-search-skill-conditional.md)和[文本翻译技能](cognitive-search-skill-text-translation.md)。
* 已经重构了[整形技能](cognitive-search-skill-shaper.md)输入, 以适应嵌套上下文的合并。 有关详细信息, 请参阅此[示例 JSON 定义](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)。
* 添加了两个新的[字段映射函数](search-indexer-field-mappings.md):
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* 在某些情况下, 在[索引器执行状态](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)中显示的错误和警告可以有有助于调试的其他详细信息。 `IndexerExecutionResult`已更新, 以反映此行为。
* 可以选择通过指定`name`属性来识别[技能组合](cognitive-search-defining-skillset.md)中定义的单个技能。
* `ServiceLimits`显示[复杂类型](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)的限制, `IndexerExecutionInfo`并显示相关的索引器限制/配额。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升级步骤

1. 使用 nuget 包管理器`Microsoft.Azure.Search`控制台或右键单击项目引用并选择 "管理 NuGet 包 ..." 来更新你的 nuget 引用在 Visual Studio 中。

2. 在 NuGet 已下载新的程序包及其依赖项后，请重新生成项目。 

3. 如果生成失败，则需要修复每个生成错误。 有关如何解决每个可能的生成错误的详细信息, 请参阅[版本10中的重大更改](#ListOfChanges)。

4. 在修复了任何生成错误或警告后，可以对应用程序进行更改，以利用新功能（如果愿意）。 [版本10中的新增](#WhatsNew)功能详细说明了 SDK 中的新功能。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>版本10中的重大更改

版本10中有几项重大更改, 可能需要更改代码才能重新生成应用程序。

> [!NOTE]
> 以下更改列表并不详尽。 某些更改可能不会导致生成错误，但在技术上是中断性的，因为它们会破坏与依赖于早期版本 Azure 搜索.NET SDK 程序集的程序集的二进制兼容性。 属于此类别的重大更改也会随建议一起列出。 请在升级到版本10时重新生成应用程序, 以避免出现任何二进制兼容性问题。

### <a name="custom-web-api-skill-definition"></a>自定义 Web API 技能定义

版本9和更早版本中错误地指定了[自定义 WEB API 技能](cognitive-search-custom-skill-web-api.md)的定义。 

`WebApiSkill` 指定`HttpHeaders`为_包含_字典的对象属性的模型。 使用以这种方式`WebApiSkill`构造的创建技能组合将导致异常, 因为 REST API 会认为请求格式不正确。 已更正此问题, 方法是在`HttpHeaders` `WebApiSkill`模型本身上生成**顶级字典属性**, 该属性被视为来自 REST API 的有效请求。

例如, 如果你先前试图实例化`WebApiSkill` , 如下所示:

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

将其更改为以下内容, 以避免来自 REST API 的验证错误:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>整形技术允许嵌套的上下文合并

整形者技能现在允许来自嵌套上下文的输入合并。 若要启用此更改, 我们`InputFieldMappingEntry`进行了修改, 以便可以通过仅指定一个`Source` `SourceContext`属性或同时指定和`Inputs`属性来对其进行实例化。

您很可能不需要进行任何代码更改;但请注意, 只允许使用这两种组合之一。 这意味着：

- `InputFieldMappingEntry` 仅`Source`初始化的创建是有效的。
- `InputFieldMappingEntry`仅创建`SourceContext` 和`Inputs`的初始化是有效的。
- 涉及这三个属性的所有其他组合均无效。

如果决定开始使用这种新功能, 请确保在进行更改之前, 先将所有客户端更新为使用版本 10, 然后再进行更改。 否则, 客户端 (使用较旧版本的 SDK) 进行的更新可能会导致验证错误。

> [!NOTE]
> 即使基础`InputFieldMappingEntry`模型已修改为允许从嵌套上下文进行合并, 但使用仅在整形程序技能定义内有效。 在其他技能中使用此功能, 在编译时有效, 将导致运行时出现验证错误。

## <a name="skills-can-be-identified-by-a-name"></a>可以通过名称来确定技能

技能组合中的每项技能现在都有一个`Name`新属性, 可在代码中对其进行初始化, 以帮助确定技能。 这是可选的, 如果未指定 (默认情况下, 如果未进行显式代码更改), 则将使用技能组合中的技能的从1开始的索引为其分配一个默认名称, 前缀为 "#" 字符。 例如, 在以下技能组合定义中 (对于简洁起见, 将跳过大多数初始化):

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

`SentimentSkill`为分配了一个`#1`名称, 分配`#2`了`ShaperSkill` , `#3`并为其分配了一个名称`WebApiSkill` 。

如果你选择通过自定义名称来确定技能, 请确保先将客户端的所有实例更新为版本10的 SDK。 否则, 使用较旧版本的 SDK `null`的客户端可能会`Name`失去技能的属性, 从而导致客户端回退到默认命名方案。

## <a name="details-about-errors-and-warnings"></a>有关错误和警告的详细信息

`ItemError`在`ItemWarning`索引器执行期间封装发生的错误和警告的详细信息的和模型已修改为包括三个新属性, 目的是为了帮助调试索引器。 这些属性为:

- `Name`：产生错误的源的名称。 例如, 它可以引用附加技能组合中的特定技能。
- `Details`：有关错误或警告的其他详细信息。
- `DocumentationLink`：指向特定错误或警告的故障排除指南的链接。

> [!NOTE]
> 我们已经开始构造错误和警告, 以便尽可能包含这些有用的信息。 我们正在努力确保所有错误和警告都存在这些详细信息, 但这是一项正在进行的工作, 可能不会始终填充这些附加的详细信息。

## <a name="next-steps"></a>后续步骤

- 对整形技能的更改对新的或现有的代码有潜在影响。 下一步, 请务必重新访问此示例, 其中阐释了输入结构:[整形技术 JSON 定义示例](cognitive-search-skill-shaper.md)
- 完成[认知搜索指南简介](cognitive-search-concept-intro.md)。
- 我们欢迎你对 SDK 提供反馈。 如果遇到问题，请随时通过 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) 向我们寻求帮助。 如果找到 Bug，可以在 [Azure .NET SDK GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/issues)中提出问题。 务必在问题标题上加前缀“[Azure 搜索]”。

