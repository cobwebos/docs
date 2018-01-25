---
title: "Azure Active Directory 条件性访问开发人员指南"
description: "Azure AD 条件性访问开发人员指南和方案"
services: active-directory
keywords: 
author: danieldobalian
manager: mtillman
editor: PatAltimore
ms.author: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 8ad1ed1b8a178cb8906e6233e6bd78f50d01c50c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory 条件性访问开发人员指南

Azure Active Directory (AD) 提供了多种保护应用和服务安全的方法。  其中最具特性的功能就是条件性访问。  通过条件性访问功能，可以让开发人员和企业客户以多种方式保护服务的安全，其中包括：

* 多重身份验证
* 仅允许已注册 Intune 的设备访问特定服务
* 限制用户位置和 IP 范围

有关条件访问完整功能的详细信息，请参阅 [Azure Active Directory 中的条件访问](../active-directory-conditional-access-azure-portal.md)。 

在本文中，我们将重点介绍条件性访问对构建适用于 Azure AD 应用的开发人员的意义。  本文假定你了解[单租户](active-directory-integrating-applications.md)和[多租户](active-directory-devhowto-multi-tenant-overview.md)应用及[常见的身份验证模式](active-directory-authentication-scenarios.md)。

我们将深入探索访问你没有控制权限且可能已应用条件性访问策略的资源的影响。  此外，我们还将了解条件访问对代理流、Web 应用、访问 Microsoft Graph 和调用 API 的含义。

## <a name="how-does-conditional-access-impact-an-app"></a>条件性访问对应用有何影响？

### <a name="app-types-impacted"></a>受影响的应用类型

通常，在多数情况下，条件性访问不会更改应用的行为或要求开发人员进行任何更改。  只有在特定情况下（应用以间接方式或无提示的方式请求服务令牌时），应用才需要进行代码更改以处理条件性访问“质询”。  此过程可能与执行交互式登录请求一样简单。 

具体来说，在以下应用场景下需要代码来处理条件性访问“质询”： 

* 访问 Microsoft Graph 的应用
* 执行代理流的应用
* 访问多个服务/资源的应用
* 使用 ADAL.js 的单页应用
* 调用资源的 Web 应用

条件性访问策略不仅可应用于应用，还可应用于应用访问的 Web API。 若要详细了解如何配置条件性访问策略，请参阅 [Azure Active Directory 条件性访问入门](../active-directory-conditional-access-azure-portal-get-started.md)。

根据具体的情况，企业客户随时可以应用和删除条件性访问策略。  应用新策略后，若要使应用继续正常工作，需执行“质询”处理。 以下示例演示了质询处理的过程。 

### <a name="conditional-access-examples"></a>条件性访问示例

在某些应用场景下，需要进行代码更改来处理条件性访问，而在其他应用场景下一切按原样运行。  以下是使用条件性访问执行多重身份验证的一些应用场景，可以通过它们深入了解其中的差异。

* 你正在构建单租户 iOS 应用，并应用了条件性访问策略。  应用允许用户登录且不请求访问 API。  用户登录后，自动调用策略，用户需要执行多重身份验证 (MFA)。 
* 你正在构建多租户 Web 应用，该应用使用 Microsoft Graph 访问 Exchange 及其他服务。  采用此应用的企业客户设置了有关 Exchange 的策略。  当 Web 应用为 MS Graph 请求令牌时，将不会对应用进行质询以符合策略。  最终用户使用有效令牌进行登录。 当应用尝试针对 Microsoft Graph 使用此令牌来访问 Exchange 数据时，将通过 ```WWW-Authenticate``` 标头将声明“质询”返回给 Web 应用。  然后，应用可以在新请求中使用 ```claims```，并将提示最终用户符合条件。 
* 你正在构建使用中间层服务访问下游 API 的本机应用。  公司中使用此应用的企业客户对下游 API 应用了策略。  最终用户登录时，本机应用将请求访问中间层并将发送令牌。  中间层执行代理流来请求访问下游 API。  此时，将向中间层发出一个声明“质询”。 中间层将质询发送回本机应用，本机应用需符合条件访问策略。

### <a name="complying-with-a-conditional-access-policy"></a>符合条件性访问策略

对于多个不同的应用拓扑，会在建立会话时评估条件性访问策略。  条件性访问策略在应用和服务的粒度上运行时，调用它的时间很大程度上取决于你尝试完成的应用场景。

在应用尝试访问具有条件性访问策略的服务时，可能会遇到条件性访问质询。  此质询编码在 `claims` 参数中，而此参数来自 Azure AD 或 Microsoft Graph 的响应。  以下是此质询参数的示例： 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

开发人员可以接受此质询并将其追加到新的 Azure AD 请求中。  传递此状态将提示最终用户执行任何必要的操作以符合条件性访问策略。 以下应用场景说明了错误的详细信息及如何提取此参数。 

## <a name="scenarios"></a>方案

### <a name="prerequisites"></a>先决条件

Azure AD 条件性访问是 [Azure AD Premium](../active-directory-whatis.md#choose-an-edition) 包含的一项功能。  可以在[未授权用户使用情况报表](../active-directory-conditional-access-unlicensed-usage-report.md)中了解有关许可要求的更多信息。  开发人员可以加入 [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx)，其中包含包括 Azure AD Premium 的企业移动性套件的免费订阅。

### <a name="considerations-for-specific-scenarios"></a>特定应用场景的注意事项

下列信息仅适用于这些条件性访问应用场景：

* 访问 Microsoft Graph 的应用
* 执行代理流的应用
* 访问多个服务/资源的应用
* 使用 ADAL.js 的单页应用

在后续部分中，我们将介绍更为复杂的常见应用场景。  核心运行原则是请求已应用条件访问策略的服务令牌时评估条件访问策略，但在通过 Microsoft Graph 访问时除外。

## <a name="scenario-app-accessing-microsoft-graph"></a>应用场景：访问 Microsoft Graph 的应用

在此应用场景中，我们将演示 Web 应用请求访问 Microsoft Graph 时的场景。 可以将此场景中的条件访问策略分配到通过 Microsoft Graph 将其作为工作负荷访问的 SharePoint、Exchange 或其他的一些服务。  在此示例中，假定有关于 Sharepoint Online 的条件性访问策略。

![访问 Microsoft Graph 流的应用示意图](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

应用首先向 Microsoft Graph 请求授权，此过程需要访问没有条件访问策略的下游工作负荷。  请求成功且未调用任何策略，应用收到 Microsoft Graph 的令牌。  此时，应用可以在所请求的终结点的持有者请求中使用此访问令牌。 现在，应用需要访问 Microsoft Graph 的 Sharepoint Online 终结点，例如：`https://graph.microsoft.com/v1.0/me/mySite`

应用已拥有 Microsoft Graph 的有效令牌，因此，它可以执行新请求，而无需获取新令牌。 此请求失败，并且 Microsoft Graph 以包含 ```WWW-Authenticate``` 质询的“HTTP 403 禁止访问”的形式发出声明质询。
以下是响应示例： 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

声明质询位于 ```WWW-Authenticate``` 标头内，可以分析它来提取下一个请求的声明参数。  将其追加到新请求后，Azure AD 知道了要在用户登录时评估条件性访问策略，并且现在应用已符合条件性访问策略。  重新请求 Sharepoint Online 终结点成功。

```WWW-Authenticate``` 标头确实包含唯一的结构，但为提取值而进行分析并不是件小事。  以下是可帮助的简短方法。

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

有关演示如何处理声明质询的代码示例，请参阅适用于 ADAL .NET 的[代理代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>应用场景：执行代理流的应用

在此应用场景中，我们将演示本机应用调用 Web 服务/API 时的场景。  相反的是，此服务将执行[“代理”流](active-directory-authentication-scenarios.md#application-types-and-scenarios)来调用下游服务。  在本示例中，我们已向下游服务 (Web API 2) 应用条件性访问策略，并且使用的是本机应用，而非服务器/守护程序应用。 

![执行代理流的应用示意图](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

Web API 1 的初始令牌请求未提示最终用户进行多重身份验证，因为 Web API 1 不会始终命中下游 API。  Web API 1 尝试代表 Web API 2 的用户请求令牌时，请求会失败，因为此用户尚未使用多重身份验证进行登录。

Azure AD 返回 HTTP 响应，其中包含一些有价值的数据： 

> [!NOTE]
> 在此实例中，这些数据是多重身份验证错误说明，但很大一部分的 `interaction_required` 可能与条件性访问有关。  

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

在 Web API 1 中，我们捕获到错误 `error=interaction_required`，并向桌面应用发送回 `claims` 质询。  此时，桌面应用可以发出新的 `acquireToken()` 调用并将 `claims` 质询追加为额外的查询字符串参数。  这个新请求需要用户执行多重身份验证，然后将此新令牌发送回 Web API 1，并完成代理流。

若要尝试此应用场景，请参阅 [.NET 代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。  它演示了如何将声明质询从 Web API 1 传送回本机应用，以及如何在客户端应用内构造新请求。 

## <a name="scenario-app-accessing-multiple-services"></a>应用场景：访问多个服务的应用

在此应用场景中，我们将演示 Web 应用访问两个服务且其中之一已分配条件性访问策略时的场景。  可能存在应用无需访问两个 Web 服务的方法，具体要取决于应用逻辑。  在此应用场景中，请求令牌的顺序对最终用户体验有重要影响。

假定有 Web 服务 A 和 Web 服务 B，且 Web 服务 B 已应用条件性访问策略。  虽然初始交互式身份验证请求需要两个服务的许可，但是并非在所有情况都需要条件性访问策略。  如果应用请求 Web 服务 B 的令牌，则将调用策略，并且对 Web 服务 A 的后续请求也将成功，如下所示。

![访问多个服务流的应用示意图](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

或者，如果应用最初请求 Web 服务 A 的令牌，则最终用户将不会调用条件性访问策略。  这样应用开发人员则可以控制最终用户体验，且无需在所有情况下强制调用条件性访问策略。 最复杂的情况是应用随后请求 Web 服务 B 的令牌时。此时，最终用户需要符合条件性访问策略。  应用尝试 `acquireToken` 时，可能会生成以下错误（如下图所示）： 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![访问多个请求新令牌的服务的应用](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

如果应用使用 ADAL 库，将始终以交互方式重新尝试获取令牌并失败。  发生此交互式请求时，最终用户有机会符合条件性访问策略。  上述说法是正确的，但当请求是 `AcquireTokenSilentAsync` 或 `PromptBehavior.Never` 时，应用需要执行交互式 ```AcquireToken``` 请求来为最终用户提供符合策略的机会。 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>应用场景：使用 ADAL.js 的单页应用 (SPA)

在此应用场景中，我们将演示单页应用 (SPA) 使用 ADAL.js 调用条件性访问保护的 Web API 时的场景。  这是一个简单的体系结构，但围绕条件性访问进行开发时需要注意一些细微差异。

在 ADAL.js 中，有多个获取令牌的函数：`login()`、`acquireToken(...)`、`acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)`。 

* `login()` 通过交互式登录请求获取 ID 令牌，但是无法获取任何服务的访问令牌（包括条件性访问保护的 Web API）。  
* 然后可以使用 `acquireToken(…)` 以无提示方式获取访问令牌，这意味着在任何情况下它都不会显示 UI。  
* `acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)` 用于以交互方式请求资源的令牌，这意味着它们始终会显示登录 UI。

应用需要访问令牌来调用 Web API 时，它尝试 `acquireToken(…)`。  如果令牌会话过期或我们需要符合条件性访问策略时，则 *acquireToken* 函数失败，应用将使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。

![使用 ADAL 流的单页应用示意图](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

我们来演示一个使用条件性访问应用场景的示例。  最终用户刚刚登录网站，且没有会话。  我们执行一个 `login()` 调用，未通过多重身份验证获取了 ID 令牌。  然后用户点击一个按钮，要求应用从 Web API 请求数据。  该应用将尝试执行一个 `acquireToken()` 调用，但是失败，因为用户尚未执行多重身份验证，且需要符合条件性访问策略。

Azure AD 发送回以下 HTTP 响应： 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

应用需要捕获 `error=interaction_required`。  然后应用程序可以在同一个资源上使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。  用户被强制执行多重身份验证。 用户完成多重身份验证后，应用针对所请求资源签发一个新访问令牌。

若要尝试此应用场景，请参阅 [JS SPA 代理代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。  此代码示例使用之前通过 JS SPA 注册的条件性访问策略和 Web API 演示此应用场景。 它演示了如何正确处理声明质询并获取可用于 Web API 的访问令牌。 或者，请查看常规的 [Angular.js 代码示例](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)，获取 Angular SPA 方面的指南。


## <a name="see-also"></a>另请参阅

* 若要详细了解这些功能，请参阅 [Azure Active Directory 中的条件访问](../active-directory-conditional-access-azure-portal.md)。
* 若要获取 Azure AD 代码示例，请参阅[代码示例 Github 存储库](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)。 
* 有关 ADAL SDK 和访问参考文档的详细信息，请参阅[库指南](active-directory-authentication-libraries.md)。
* 若要详细了解多租户应用场景，请参阅[如何使用多租户模式让用户进行登录](active-directory-devhowto-multi-tenant-overview.md)。
