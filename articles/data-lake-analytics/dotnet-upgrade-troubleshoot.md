---
title: 如何排除由于 .NET 框架 4.7.2 升级而导致的 Azure 数据湖分析 U-SQL 作业故障
description: 由于升级到 .NET 框架 4.7.2，因此排除 U-SQL 作业故障。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213583"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure 数据湖分析正在升级到 .NET 框架 v4.7.2

Azure 数据湖分析默认运行时正在从 .NET 框架 v4.5.2 升级到 .NET 框架 v4.7.2。 如果 U-SQL 代码使用自定义程序集，并且这些自定义程序集使用 .NET 库，则此更改会带来中断更改的小风险。

从 .NET Framework 4.5.2 升级到版本 4.7.2 意味着部署在 U-SQL 运行时（默认运行时）中的 .NET 框架现在始终为 4.7.2。 .NET 框架版本没有并行选项。

此升级到 .NET Framework 4.7.2 完成后，系统的托管代码将运行为版本 4.7.2，用户提供的库（如 U-SQL 自定义程序集）将在向后兼容模式下运行，该模式适用于程序集已过的版本生成。

- 如果为版本 4.5.2 生成程序集 DLL，则部署的框架将将它们视为 4.5.2 库，提供（少数例外）4.5.2语义。
- 现在，如果您的目标是 .NET Framework 4.7.2，则可以使用使用版本 4.7.2 功能的 U-SQL 自定义程序集。

由于此升级到 .NET Framework 4.7.2，因此可能会对使用 .NET 自定义程序集的 U-SQL 作业引入重大更改。 我们建议您使用以下步骤检查向后兼容性问题。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>如何检查向后兼容性问题

通过在 U-SQL 自定义程序集中运行 .NET 兼容性检查，检查存在向后兼容性中断问题的可能性。

> [!Note]
> 该工具不会检测到实际的中断更改。 它仅标识可能（对于某些输入）可能导致问题的称为 .NET API。 如果您收到问题通知，您的代码可能仍然正常，但应签入更多详细信息。

1. 在 .NET DLL 上运行向后兼容性检查器，要么通过
   1. 在[.NET 可移植性分析器可视化工作室扩展](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)处使用可视化工作室扩展
   1. 下载并使用独立工具从[GitHub点网港](https://github.com/microsoft/dotnet-apiport)。 运行独立工具的说明位于[GitHub 点网端口突发更改](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 对于 4.7.2。 兼容性，`read isRetargeting == True`识别可能的问题。
2. 如果该工具指示代码是否可能受到任何可能的向后不兼容的影响（下面列出了一些常见的不兼容示例），则可以通过
   1. 分析代码并确定代码是否将值传递给受影响的 API
   1. 执行运行时检查。 运行时部署在 ADLA 中不是并行进行的。 您可以在升级前执行运行时检查，使用 VisualStudio 的本地运行与本地 .NET 框架 4.7.2 针对具有代表性的数据集。
3. 如果确实受到向后不兼容的影响，请采取必要的步骤来修复它（例如修复数据或代码逻辑）。

在大多数情况下，不应受到向后不兼容的影响。

## <a name="timeline"></a>时间线

您可以在此处检查新运行时的部署[运行时故障排除](runtime-troubleshoot.md)，并查看任何以前成功的工作。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>如果无法及时审核代码，该怎么办？

您可以针对旧的运行时版本（针对 4.5.2 构建）提交作业，但由于缺少 .NET Framework 并行功能，它仍将仅在 4.5.2 兼容模式下运行。 由于此行为，您仍可能遇到一些向后兼容性问题。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>您可能会遇到的最常见向后兼容性问题

检查器可能识别的最常见向后不兼容是（我们通过在我们自己的内部 ADLA 作业上运行检查程序生成此列表），哪些库受到影响（注意：您可以仅间接调用库，因此执行所需的操作#1检查作业是否受到影响）以及可能的操作来补救非常重要。 注意：在几乎所有情况下，对于我们自己的工作，由于大多数中断更改的狭隘性质，警告被证明是误报。

- IAsyncResult.CompletedSynchronously 属性必须正确，才能完成生成的任务
  - 在调用 TaskFactory.FromAsync 时，必须正确实现 IAsyncResult.CompletedSynchronously 属性，才能完成生成的任务。 即，当且仅当同步完成实现时，该属性才应返回 true。 之前，属性未被选中。
  - 受影响的库：mscorlib，系统.线程.任务
  - 建议的操作：确保任务工厂。从Async返回正确

- DataObject.GetData 现在将数据检索为 UTF-8
  - 对于面向 .NET Framework 4 或者在 .NET Framework 4.5.1 或早期版本上运行的应用，DataObject.GetData 将 HTML 格式的数据检索为 ASCII 字符串。 因此，非 ASCII 字符（ASCII 代码大于 0x7F 的字符）由两个随机字符表示。 #N_#N_对于针对 .NET 框架 4.5 或更高版本并在 .NET 框架 4.5.2 上运行并在 .NET 框架 4.5.2 上运行的应用，`DataObject.GetData`检索 HTML 格式的数据为 UTF-8，表示正确大于 0x7F 的字符。
  - 受影响的库：Glo
  - 建议的操作：确保检索的数据是您想要的格式

- XmlWriter 引发无效的代理项对
  - 对于面向 .NET Framework 4.5.2 或以前的版本的应用程序，使用异常回退处理编写无效的代理项对并不会总是引发异常。 对于面向 .NET Framework 4.6 的应用，尝试编写无效的代理项对会引发 `ArgumentException`。
  - 受影响的库：系统.Xml，系统.Xml.ReaderWriter
  - 建议的操作：确保您没有编写将导致参数异常的无效代理项对

- HtmlTextWriter 未正确呈现 `<br/>` 元素
  - 从 .NET Framework 4.6 开始，调用带有 `<BR />` 的 `HtmlTextWriter.RenderBeginTag()` 和 `HtmlTextWriter.RenderEndTag()` 将正确插入唯一 `<BR />`（而非两个）
  - 受影响的库：系统.Web
  - 建议的操作：确保插入预期看到的数量，`<BR />`以便在生产作业中看不到随机行为

- 调用具有 null 自变量的 CreateDefaultAuthorizationContext 的方式已更改
  - 调用具有 null authorizationPolicies 自变量的 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` 所返回的 AuthorizationContext 实现更改了其在 .NET Framework 4.6 中的实现。
  - 受影响的库：系统.身份模型
  - 建议的操作：确保在存在空授权策略时处理新的预期行为
  
- RSACng 现在可正确加载非标准密钥大小的 RSA 密钥
  - 在 .NET Framework 4.6.2 之前的版本中，使用非标准密钥大小的 RSA 证书的客户无法通过 `GetRSAPublicKey()` 和 `GetRSAPrivateKey()` 扩展方法访问这些密钥。 将`CryptographicException`引发一条消息"不支持请求的密钥大小" 使用 .NET 框架 4.6.2 此问题已修复。 同样，`RSA.ImportParameters()``RSACng.ImportParameters()`现在使用非标准密钥大小，而不引发`CryptographicException`。
  - 受影响的库：mscorlib，系统。
  - 建议的操作：确保 RSA 密钥按预期工作

- 路径冒号检查更严格
  - 在 .NET Framework 4.6.2 中，为了支持以前不受支持的路径，进行了大量更改（无论是在长度方面还是在格式方面）。 检查正确的驱动器分隔符（冒号）语法变得更加严格，这样做的副作用是阻止了少量特选路径 API 中的某些 URI 路径，这些曾经是可以容忍的。
  - 受影响的库：mscorlib，系统.运行时.扩展
  - 建议的操作：

- 调用 ClaimsIdentity 构造函数
  - 从 .NET Framework 4.6.2 开始，具有 `T:System.Security.Principal.IIdentity` 参数的 `T:System.Security.Claims.ClaimsIdentity` 构造函数设置 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性的方式发生了变化。 如果 `T:System.Security.Principal.IIdentity` 参数是 `T:System.Security.Claims.ClaimsIdentity` 对象，且该 `T:System.Security.Claims.ClaimsIdentity` 对象的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性不为 `null`，则 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性是使用 `M:System.Security.Claims.ClaimsIdentity.Clone` 方法附加的。 在框架 4.6.1 和早期版本中，`P:System.Security.Claims.ClaimsIdentify.Actor`该属性将作为现有引用附加。 由于此更改，从 .NET 框架 4.6.2 开始`P:System.Security.Claims.ClaimsIdentify.Actor`，新`T:System.Security.Claims.ClaimsIdentity`对象的属性不等于`P:System.Security.Claims.ClaimsIdentify.Actor`构造函数参数的属性。 `T:System.Security.Principal.IIdentity` 在 .NET Framework 4.6.1 及更早版本中，它们是相等的。
  - 受影响的库：mscorlib
  - 建议的操作：确保声明身份在新运行时按预期工作

- 使用 DataContractJsonSerializer 控制字符的序列化现在与 ECMAScript V6 和 V8 兼容
  - 在 .NET 框架 4.6.2 和早期版本中，DataContractJson 序列化器没有以与 ECMAScript V6 和 V8 标准兼容的方式序列化某些特殊控制字符，如 \b、\f 和 \t。 从 .NET Framework 4.7 开始，这些控制字符的序列化与 ECMAScript V6 和 V8 兼容。
  - 受影响的库：系统.运行时.序列化。Json
  - 建议的操作：使用数据合同Jon序列化器确保相同的行为
