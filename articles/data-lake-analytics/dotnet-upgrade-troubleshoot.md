---
title: 由于 .NET Framework 4.7.2 升级，如何排查 Azure Data Lake Analytics U-SQL 作业失败
description: 由于升级到 .NET Framework 4.7.2，排除了排除 SQL 作业故障。
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79213583"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics 升级到 .NET Framework v 4.7。2

Azure Data Lake Analytics 默认运行时从 .NET Framework v 4.5.2 升级到 .NET Framework v 4.7.2。 如果你的 U SQL 代码使用自定义程序集，而这些自定义程序集使用 .NET 库，则此更改会导致重大更改的一小部分风险。

从 .NET Framework 4.5.2 到版本4.7.2 的这一升级意味着部署在 U SQL 运行时（默认运行时）的 .NET Framework 现在总是4.7.2。 没有 .NET Framework 版本的并行选项。

完成此升级到 .NET Framework 4.7.2 后，系统的托管代码将作为版本4.7.2 运行，用户提供的库（如 U SQL 自定义程序集）将在适用于该程序集的版本的向后兼容模式下运行为生成的。

- 如果为版本4.5.2 生成程序集 Dll，则已部署的框架会将它们视为4.5.2 库，并提供（但有少数例外）4.5.2 语义。
- 如果以 .NET Framework 4.7.2 为目标，则现在可以使用使用版本4.7.2 功能的 U SQL 自定义程序集。

由于此升级到 .NET Framework 4.7.2，因此可能会引入使用 .NET 自定义程序集的 U SQL 作业的重大更改。 建议使用以下过程检查是否存在向后兼容性问题。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>如何检查向后兼容性问题

通过在你的 .NET 代码中的 .NET 代码上运行 .NET 兼容性检查来检查是否存在向后兼容性中断问题。

> [!Note]
> 该工具不会检测实际的重大更改。 它仅标识可能（对于某些输入）导致问题的已调用 .NET Api。 如果收到有关问题的通知，你的代码仍可正常工作，但是你应该查看更多详细信息。

1. 通过以下方式在 .NET Dll 上运行向后兼容性检查器
   1. 使用[.net 可移植性分析器](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)上的 Visual Studio 扩展 Visual studio 扩展
   1. 下载并使用[GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport)中的独立工具。 有关运行独立工具的说明，请参阅[GitHub dotnetapiport 重大更改](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 对于4.7.2。 兼容性，`read isRetargeting == True` 确定可能的问题。
2. 如果该工具指示你的代码是否可能会受到任何可能的向后不兼容性的影响（下面列出了一些常见的不兼容示例），可以通过以下方式进一步检查：
   1. 分析代码并确定代码是否正在将值传递给受影响的 Api
   1. 执行运行时检查。 不会在 ADLA 中并行执行运行时部署。 你可以在升级之前执行运行时检查，并使用 VisualStudio 的本地运行，并使用本地 .NET Framework 4.7.2 针对有代表性的数据集。
3. 如果你确实受到了向后兼容性的影响，请执行必要的步骤来修复此问题（例如修复你的数据或代码逻辑）。

在大多数情况下，不应受到向后兼容性的影响。

## <a name="timeline"></a>时间线

你可以通过在[运行时进行故障排除](runtime-troubleshoot.md)以及查看任何之前的成功作业来检查新运行时的部署。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>如果无法及时查看代码，会发生什么情况

你可以针对旧的运行时版本（以4.5.2 为基础）提交作业，但是由于缺少并行功能 .NET Framework，它仍将仅在4.5.2 兼容模式下运行。 由于此行为，你可能仍会遇到一些向后兼容性问题。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>你可能会遇到的最常见向后兼容性问题

检查器很有可能确定的最常见的向后不兼容性是（我们通过在我们自己的内部 ADLA 作业上运行检查器来生成此列表），哪些库会受到影响（注意：你只能间接调用库，因此必须采取必要操作 #1 来检查作业是否受影响）和可能的补救措施。 注意：几乎在所有情况下，在所有情况下，由于最大程度的重大更改，警告都是误报的性质。

- System.iasyncresult.completedsynchronously 属性必须正确才能完成生成的任务
  - 调用 TaskFactory 时，System.iasyncresult.completedsynchronously 属性的实现必须是正确的，才能完成生成的任务。 也就是说，当且仅当实现同步完成时，属性必须返回 true。 以前，属性未被选中。
  - 受影响的库： mscorlib、System.object
  - 建议的操作：确保 TaskFactory 正确返回 true

- DataObject 现在以 UTF-8 形式检索数据
  - 对于面向 .NET Framework 4 或在 .NET Framework 4.5.1 或更早版本上运行的应用程序，DataObject 将 HTML 格式的数据检索为 ASCII 字符串。 因此，非 ASCII 字符（ASCII 代码大于0x7F 的字符）由两个随机字符表示。 #N # #N # 对于面向 .NET Framework 4.5 或更高版本的应用程序，并在 .NET Framework 4.5.2 上运行，`DataObject.GetData` 将 HTML 格式的数据检索为 UTF-8，这表示正确的字符。
  - 受影响的库： Glo
  - 建议的操作：确保检索的数据是所需的格式

- XmlWriter 对无效代理项对引发
  - 对于面向 .NET Framework 4.5.2 或早期版本的应用程序，使用异常回退处理编写无效的代理项对并不会总是引发异常。 对于面向 .NET Framework 4.6 的应用，尝试编写无效的代理项对会引发 `ArgumentException`。
  - 受影响的库： ReaderWriter
  - 建议的操作：确保未编写将导致参数异常的无效代理项对

- HtmlTextWriter 未正确呈现 `<br/>` 元素
  - 从 .NET Framework 4.6 开始，使用 `<BR />` 元素调用 `HtmlTextWriter.RenderBeginTag()` 和 `HtmlTextWriter.RenderEndTag()` 只会正确插入一个 `<BR />` （而不是两个）
  - 受影响的库： System.web
  - 建议的操作：确保插入你希望看到的 `<BR />` 量，以便不会在生产作业中看到随机行为

- 使用空参数调用 CreateDefaultAuthorizationContext 已更改
  - 使用 null authorizationPolicies 参数对 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` 的调用所返回的 AuthorizationContext 的实现已在 .NET Framework 4.6 中更改其实现。
  - 受影响的库： System.identitymodel
  - 建议的操作：确保在存在 null 授权策略时处理新的预期行为
  
- RSACng 现已正确加载非标准密钥大小的 RSA 密钥
  - 在4.6.2 之前 .NET Framework 版本中，RSA 证书的非标准密钥大小的客户无法通过 `GetRSAPublicKey()` 和 `GetRSAPrivateKey()` 扩展方法访问这些密钥。 引发消息 "不支持请求的密钥大小" 的 `CryptographicException`。 在 .NET Framework 4.6.2，此问题已得到解决。 同样，`RSA.ImportParameters()` 和 `RSACng.ImportParameters()` 现在可以处理非标准密钥大小，而不会抛出 `CryptographicException`。
  - 受影响的库： mscorlib、System.object
  - 建议的操作：确保 RSA 密钥按预期方式工作

- 路径冒号检查更严格
  - 在 .NET Framework 4.6.2 中，进行了一些更改以支持以前不受支持的路径（长度和格式）。 检查正确的驱动器分隔符（冒号）语法是否更正确，其副作用是在几个选择路径 Api 中阻止某些 URI 路径，在这些路径中，这些路径是可接受的。
  - 受影响的库： mscorlib、system.exception
  - 建议的操作：

- 调用 ClaimsIdentity 构造函数
  - 从 .NET Framework 4.6.2 开始，具有 `T:System.Security.Principal.IIdentity` 参数 `T:System.Security.Claims.ClaimsIdentity` 构造函数的方式发生了如何设置 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性。 如果 `T:System.Security.Principal.IIdentity` 参数是一个 `T:System.Security.Claims.ClaimsIdentity` 对象，而该 `T:System.Security.Claims.ClaimsIdentity` 对象的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性未 `null`，则使用 `P:System.Security.Claims.ClaimsIdentify.Actor` 方法附加 `M:System.Security.Claims.ClaimsIdentity.Clone` 属性。 在 Framework 4.6.1 及更早版本中，`P:System.Security.Claims.ClaimsIdentify.Actor` 属性作为现有引用附加。 由于此更改，从 .NET Framework 4.6.2 开始，新 `T:System.Security.Claims.ClaimsIdentity` 对象的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性与构造函数的 `T:System.Security.Principal.IIdentity` 参数的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性不相等。 在 .NET Framework 4.6.1 及更早版本中，它是相等的。
  - 受影响的库： mscorlib
  - 建议的操作：确保 ClaimsIdentity 在新运行时按预期方式工作

- 用 DataContractJsonSerializer 对控制字符进行序列化现在与 ECMAScript V6 和 V8 兼容
  - 在 .NET framework 4.6.2 及更早版本中，DataContractJsonSerializer 不会序列化一些特殊控制字符（如 \b、\f 和 \t），这种方式与 ECMAScript V6 和 V8 标准兼容。 从 .NET Framework 4.7 开始，这些控制字符的序列化与 ECMAScript V6 和 V8 兼容。
  - 受影响的库：
  - 建议的操作：确保与 DataContractJsonSerializer 相同的行为
