---
title: Azure Active Directory 条件访问开发人员指南
titleSuffix: Microsoft identity platform
description: 适用于 Azure AD 条件访问和 Microsoft 标识平台的开发人员指南与方案。
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: b1bfefb3b72c151e7a61068b3c0ad9f3e2bc4a6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88120620"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory 条件访问开发人员指南

保护应用安全和保护服务的方法有多种，Azure Active Directory (Azure AD) 中的条件访问功能便是其中之一。 通过条件访问功能，可以让开发人员和企业客户以多种方式保护服务的安全，其中包括：

* [多重身份验证](../authentication/concept-mfa-howitworks.md)
* 仅允许已注册 Intune 的设备访问特定服务
* 限制用户位置和 IP 范围

有关条件访问完整功能的详细信息，请参阅[什么是条件访问](../conditional-access/overview.md)一文。

对于构建 Azure AD 应用的开发人员，本文演示了条件访问的使用方法，并介绍了访问应用了条件访问策略且你无权控制的资源时将产生的影响。 此外，本文还探讨了条件访问对代理流、Web 应用、访问 Microsoft Graph 和调用 API 的影响。

本文假定已了解[单租户](quickstart-register-app.md)和[多租户](howto-convert-app-to-be-multi-tenant.md)应用以及[常见的身份验证模式](./authentication-vs-authorization.md)。

> [!NOTE]
> 使用此功能需要 Azure AD Premium P1 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版、基本版和高级版的正式发布功能](https://azure.microsoft.com/pricing/details/active-directory/)。
> 拥有 [Microsoft 365 商业版许可证](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客户也可以访问条件访问功能。

## <a name="how-does-conditional-access-impact-an-app"></a>条件访问对应用有何影响？

### <a name="app-types-impacted"></a>受影响的应用类型

通常，在多数情况下，条件访问不会更改应用的行为或要求开发人员进行任何更改。 只有在特定情况下（应用以间接方式或无提示的方式请求服务令牌时），应用才需要进行代码更改以处理条件访问“质询”。 此过程可能与执行交互式登录请求一样简单。

具体来说，在以下应用场景下要求代码处理条件访问“质询”：

* 执行代理流的应用
* 访问多个服务/资源的应用
* 使用 MSAL.js 的单页应用
* 调用资源的 Web 应用

条件访问策略不仅可应用于应用，还可应用于应用访问的 Web API。 若要详细了解如何配置条件访问策略，请参阅[快速入门：使用 Azure Active Directory 条件访问要求针对特定应用进行 MFA](../authentication/tutorial-enable-azure-mfa.md)。

根据具体的情况，企业客户随时可以应用和删除条件访问策略。 应用新策略后，若要使应用继续正常工作，需执行“质询”处理。 以下示例演示了质询处理的过程。

### <a name="conditional-access-examples"></a>条件访问示例

在某些应用场景下，需要进行代码更改来处理条件访问，而在其他应用场景下一切按原样运行。 下面是使用条件访问执行多重身份验证的一些应用场景，可以通过它们深入了解其中的差异。

* 你正在构建单租户 iOS 应用，并应用了条件访问策略。 应用允许用户登录且不请求访问 API。 用户登录后，自动调用策略，用户需要执行多重身份验证 (MFA)。
* 你正在构建使用中间层服务访问下游 API 的本机应用。 公司中使用此应用的企业客户对下游 API 应用了策略。 最终用户登录时，本机应用将请求访问中间层并将发送令牌。 中间层执行代理流来请求访问下游 API。 此时，将向中间层发出一个声明“质询”。 中间层将质询发送回本机应用，本机应用需符合条件访问策略。

#### <a name="microsoft-graph"></a>Microsoft Graph

在条件访问环境中构建应用时，Microsoft Graph 具有特殊注意事项。 一般来说，条件访问的机制是相同的，但是用户看到的策略将基于应用程序从图形中请求的基础数据。

具体来说，所有 Microsoft Graph 范围都表示可以单独应用策略的某些数据集。 由于条件访问策略分配给了特定的数据集，Azure AD 将基于图形背后的数据而不是图形本身强制实施条件访问策略。

例如，如果某个应用程序请求下列 Microsoft Graph 范围，

```
scopes="Bookings.Read.All Mail.Read"
```

应用可能期望其用户完成所有关于预订和交换的策略。 如果授予访问权限，某些范围可能映射到多个数据集。

### <a name="complying-with-a-conditional-access-policy"></a>符合条件访问策略

对于多个不同的应用拓扑，会在建立会话时评估条件访问策略。 条件访问策略在应用和服务粒度级别运行时，调用它的时间很大程度上取决于你尝试完成的应用场景。

在应用尝试访问具有条件访问策略的服务时，可能会遇到条件访问质询。 此质询编码在 `claims` 参数中，而此参数来自 Azure AD 的响应。 以下是此质询参数的示例：

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

开发人员可以接受此质询并将其追加到新的 Azure AD 请求中。 传递此状态将提示最终用户执行任何必要的操作以符合条件访问策略。 以下应用场景说明了错误的详细信息及如何提取此参数。

## <a name="scenarios"></a>方案

### <a name="prerequisites"></a>先决条件

Azure AD 条件访问是 [Azure AD Premium](../fundamentals/active-directory-whatis.md) 包含的一项功能。 拥有 [Microsoft 365 商业版许可证](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)的客户也可以访问条件访问功能。

### <a name="considerations-for-specific-scenarios"></a>特定应用场景的注意事项

下列信息仅适用于这些条件访问应用场景：

* 执行代理流的应用
* 访问多个服务/资源的应用
* 使用 MSAL.js 的单页应用

以下各部分讨论更复杂的常见应用场景。 核心运行原则是请求已应用条件访问策略的服务令牌时评估条件访问策略。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>方案：执行代理流的应用

在此应用场景中，我们将演示本机应用调用 Web 服务/API 时的场景。 而此服务又会执行“代理”流来调用下游服务。 在本示例中，我们已向下游服务 (Web API 2) 应用了条件访问策略，并且使用的是本机应用，而非服务器/守护程序应用。

![执行代理流的应用示意图](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 的初始令牌请求未提示最终用户进行多重身份验证，因为 Web API 1 不会始终命中下游 API。 Web API 1 尝试代表 Web API 2 的用户请求令牌时，请求会失败，因为此用户尚未使用多重身份验证进行登录。

Azure AD 返回 HTTP 响应，其中包含一些有价值的数据：

> [!NOTE]
> 在此实例中，这些数据是多重身份验证错误说明，但很大一部分的 `interaction_required` 可能与条件访问有关。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

在 Web API 1 中，我们捕获到错误 `error=interaction_required`，并向桌面应用发送回 `claims` 质询。 此时，桌面应用可以发出新的 `acquireToken()` 调用并将 `claims` 质询追加为额外的查询字符串参数。 这个新请求需要用户执行多重身份验证，然后将此新令牌发送回 Web API 1，并完成代理流。

若要尝试此应用场景，请参阅 [.NET 代码示例](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-)。 它演示了如何将声明质询从 Web API 1 传送回本机应用，以及如何在客户端应用内构造新请求。

## <a name="scenario-app-accessing-multiple-services"></a>方案：访问多个服务的应用

在此方案中，我们将演示 Web 应用访问两个服务且其中之一已分配条件访问策略时的场景。 可能存在应用无需访问两个 Web 服务的方法，具体要取决于应用逻辑。 在此应用场景中，请求令牌的顺序对最终用户体验有重要影响。

假定有 Web 服务 A 和 Web 服务 B，且 Web 服务 B 已应用了条件访问策略。 虽然初始交互式身份验证请求需要两个服务的同意，但是并非在所有情况都需要条件访问策略。 如果应用请求 Web 服务 B 的令牌，则将调用策略，并且对 Web 服务 A 的后续请求也将成功，如下所示。

![访问多个服务流的应用示意图](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

或者，如果应用最初请求 Web 服务 A 的令牌，则最终用户将不会调用条件访问策略。 这样应用开发人员可以控制最终用户体验，且无需在所有情况下强制调用条件访问策略。 最复杂的情况是应用随后请求 Web 服务 B 的令牌。此时，最终用户需要符合条件访问策略。 应用尝试 `acquireToken` 时，可能会生成以下错误（如下图所示）：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![访问多个请求新令牌的服务的应用](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

如果应用使用 MSAL 库，则会始终以交互方式重新尝试获取令牌并失败。 发生此交互式请求时，最终用户有机会符合条件访问策略。 上述说法是正确的，但当请求是 `AcquireTokenSilentAsync` 或 `PromptBehavior.Never` 时，应用需要执行交互式 ```AcquireToken``` 请求来为最终用户提供符合策略的机会。

## <a name="scenario-single-page-app-spa-using-msaljs"></a>方案：使用 MSAL.js 的单页应用 (SPA)

在此方案中，我们将演示单页应用 (SPA) 使用 MSAL.js 调用由条件访问保护的 Web API 时的场景。 这是一个简单的体系结构，但围绕条件访问进行开发时需要注意其中的一些细微差异。

在 MSAL.js 中，有多个获取令牌的函数：`loginPopup()`、`acquireTokenSilent(...)`、`acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)`。

* `loginPopup()` 通过交互式登录请求来获取 ID 令牌，但是无法获取任何服务的访问令牌（包括由条件访问保护的 Web API）。
* 然后可以使用 `acquireTokenSilent(…)` 以无提示方式获取访问令牌，这意味着在任何情况下它都不会显示 UI。
* `acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)` 用于以交互方式请求资源的令牌，这意味着它们始终会显示登录 UI。

应用需要访问令牌来调用 Web API 时，它尝试 `acquireTokenSilent(…)`。 如果令牌会话过期或我们需要符合条件访问策略时，则 *acquireToken* 函数失败，应用将使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。

![使用 MSAL 流的单页应用示意图](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

我们来看一个使用条件访问方案的示例。 最终用户刚刚登录网站，且没有会话。 我们执行一个 `loginPopup()` 调用，未通过多重身份验证获取了 ID 令牌。 然后用户点击一个按钮，要求应用从 Web API 请求数据。 该应用尝试执行一个 `acquireTokenSilent()` 调用，但是失败，因为用户尚未执行多重身份验证，且需要符合条件访问策略。

Azure AD 发送回以下 HTTP 响应：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

应用需要捕获 `error=interaction_required`。 然后应用程序可以在同一个资源上使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。 用户被强制执行多重身份验证。 用户完成多重身份验证后，应用针对所请求资源签发一个新访问令牌。

若要尝试此应用场景，请参阅 [JS SPA 代理代码示例](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md)。 此代码示例使用之前通过 JS SPA 注册的条件访问策略和 Web API 演示此应用场景。 它演示了如何正确处理声明质询并获取可用于 Web API 的访问令牌。 或者，请查看常规的 [Angular.js 代码示例](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2)，获取 Angular SPA 方面的指南。

## <a name="see-also"></a>另请参阅

* 若要详细了解这些功能，请参阅 [Azure Active Directory 中的条件访问](../conditional-access/overview.md)。
* 有关更多的 Azure AD 代码示例，请参阅[示例](sample-v2-code.md)。
* 有关 MSAL SDK 的详细信息以及如何访问参考文档，请参阅 [Microsoft 身份验证库概述](msal-overview.md)。
* 若要详细了解多租户应用场景，请参阅[如何使用多租户模式让用户进行登录](howto-convert-app-to-be-multi-tenant.md)。
* 详细了解[条件访问以及如何保护对 IoT 应用的访问](/azure/architecture/example-scenario/iot-aad/iot-aad)。