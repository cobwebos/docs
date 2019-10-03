---
title: Azure Active Directory 条件性访问的开发人员指南
description: Azure AD 条件性访问的开发人员指南和方案
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc93a7de824aeaf173e7179de0b0233b73488feb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321160"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Azure Active Directory 条件性访问的开发人员指南

Azure Active Directory (Azure AD) 中的条件性访问功能提供了几种方法之一, 可用于保护应用和保护服务。 条件性访问使开发人员和企业客户能够以多种方式保护服务, 包括:

* 多重身份验证
* 仅允许已注册 Intune 的设备访问特定服务
* 限制用户位置和 IP 范围

有关条件性访问的完整功能的详细信息, 请参阅[Azure Active Directory 中的条件访问](../active-directory-conditional-access-azure-portal.md)。

对于构建 Azure AD 的应用程序的开发人员, 本文介绍如何使用条件性访问, 你还将了解访问无法控制的资源 (可能已应用条件性访问策略) 所造成的影响。 本文还探讨了代理流、web 应用、访问 Microsoft Graph 和调用 Api 中的条件性访问的含义。

本文假定已了解[单租户](quickstart-v1-integrate-apps-with-azure-ad.md)和[多租户](howto-convert-app-to-be-multi-tenant.md)应用以及[常见的身份验证模式](authentication-scenarios.md)。

## <a name="how-does-conditional-access-impact-an-app"></a>条件性访问如何影响应用程序？

### <a name="app-types-impacted"></a>受影响的应用类型

在大多数常见情况下, 条件性访问不会更改应用的行为, 也不需要开发人员进行任何更改。 仅在某些情况下, 当应用间接或无提示地请求服务的令牌时, 应用需要更改代码来处理条件性访问 "质询"。 此过程可能与执行交互式登录请求一样简单。

具体而言, 以下方案需要代码来处理条件性访问 "质询":

* 执行代理流的应用
* 访问多个服务/资源的应用
* 使用 ADAL.js 的单页应用
* 调用资源的 Web 应用

条件性访问策略可应用到应用, 但也可应用于应用访问的 web API。 若要详细了解如何配置条件性访问策略, 请参阅[快速入门:需要对具有 Azure Active Directory 条件性访问](../conditional-access/app-based-mfa.md)的特定应用执行 MFA。

企业客户可以随时应用和删除条件性访问策略, 具体取决于方案。 应用新策略后，若要使应用继续正常工作，需执行“质询”处理。 以下示例演示了质询处理的过程。

### <a name="conditional-access-examples"></a>条件访问示例

某些情况下, 需要更改代码来处理条件性访问, 而其他方案则按原样工作。 下面是使用条件性访问进行多重身份验证的几个方案, 可帮助深入了解差异。

* 你正在构建单租户 iOS 应用并应用条件访问策略。 应用允许用户登录且不请求访问 API。 用户登录后，自动调用策略，用户需要执行多重身份验证 (MFA)。 
* 你正在构建使用中间层服务访问下游 API 的本机应用。 公司中使用此应用的企业客户对下游 API 应用了策略。 最终用户登录时，本机应用将请求访问中间层并将发送令牌。 中间层执行代理流来请求访问下游 API。 此时，将向中间层发出一个声明“质询”。 中间层将质询发送回本机应用, 该应用需要符合条件性访问策略。

#### <a name="microsoft-graph"></a>Microsoft Graph

在条件访问环境中生成应用时, Microsoft Graph 有特殊的注意事项。 通常情况下, 条件性访问机制的行为相同, 但用户看到的策略将基于应用从图形请求的基础数据。 

具体而言, 所有 Microsoft Graph 作用域都表示一个可单独应用策略的数据集。 由于为条件访问策略分配了特定的数据集, 因此 Azure AD 会根据图形背后的数据 (而非图形本身) 强制实施条件性访问策略。

例如, 如果应用程序请求以下 Microsoft Graph 范围,

```
scopes="Bookings.Read.All Mail.Read"
```

应用可能期望其用户满足在预订和 Exchange 上设置的所有策略。 某些范围可能会映射到多个数据集 (如果它授予访问权限)。 

### <a name="complying-with-a-conditional-access-policy"></a>符合条件性访问策略

对于多个不同的应用程序拓扑, 在建立会话时, 将计算条件性访问策略。 在应用程序和服务的粒度上运行条件性访问策略时, 调用它的时间点很大程度上取决于你尝试完成的方案。

当应用尝试使用条件性访问策略访问服务时, 可能会遇到条件访问质询。 此质询编码在来自 Azure AD `claims`的响应的参数中。 以下是此质询参数的示例： 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

开发人员可以接受此质询并将其追加到新的 Azure AD 请求中。 如果传递此状态, 则会提示最终用户执行符合条件性访问策略所需的任何操作。 以下应用场景说明了错误的详细信息及如何提取此参数。

## <a name="scenarios"></a>方案

### <a name="prerequisites"></a>系统必备

Azure AD 条件访问是[Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)中包含的一项功能。 可以在[未授权用户使用情况报表](../active-directory-conditional-access-unlicensed-usage-report.md)中了解有关许可要求的更多信息。 开发人员可以加入 [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx)，其中包含企业移动性套件（含有 Azure AD Premium）免费订阅。

### <a name="considerations-for-specific-scenarios"></a>特定应用场景的注意事项

以下信息仅适用于这些条件访问方案:

* 执行代理流的应用
* 访问多个服务/资源的应用
* 使用 ADAL.js 的单页应用

以下各部分讨论更复杂的常见应用场景。 核心操作原则是在为应用了条件性访问策略的服务请求令牌时评估的条件性访问策略。

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>场景：执行代理流的应用

在此应用场景中，我们将演示本机应用调用 Web 服务/API 时的场景。 反过来, 此服务执行 "委托" 流来调用下游服务。 在我们的示例中, 我们已将条件访问策略应用到下游服务 (Web API 2), 并且使用的是本机应用, 而不是服务器/守护程序应用。 

![执行代理流的应用示意图](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1 的初始令牌请求未提示最终用户进行多重身份验证，因为 Web API 1 不会始终命中下游 API。 Web API 1 尝试代表 Web API 2 的用户请求令牌时，请求会失败，因为此用户尚未使用多重身份验证进行登录。

Azure AD 返回 HTTP 响应，其中包含一些有价值的数据：

> [!NOTE]
> 在此示例中, 它是多重身份验证错误说明, 但有很多`interaction_required`可能与条件性访问有关。

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

在 Web API 1 中，我们捕获到错误 `error=interaction_required`，并向桌面应用发送回 `claims` 质询。 此时，桌面应用可以发出新的 `acquireToken()` 调用并将 `claims` 质询追加为额外的查询字符串参数。 这个新请求需要用户执行多重身份验证，然后将此新令牌发送回 Web API 1，并完成代理流。

若要尝试此应用场景，请参阅 [.NET 代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。 它演示了如何将声明质询从 Web API 1 传送回本机应用，以及如何在客户端应用内构造新请求。

## <a name="scenario-app-accessing-multiple-services"></a>方案：访问多个服务的应用

在此方案中, 我们将逐步介绍 web 应用访问两个服务的情况, 其中一项已分配条件性访问策略。 可能存在应用无需访问两个 Web 服务的方法，具体要取决于应用逻辑。 在此应用场景中，请求令牌的顺序对最终用户体验有重要影响。

假设我们已有 web 服务 A 和 B, 并且 web 服务 B 应用了条件访问策略。 虽然初始交互身份验证请求要求对这两种服务进行同意, 但在所有情况下都不需要使用条件性访问策略。 如果应用请求 Web 服务 B 的令牌，则将调用策略，并且对 Web 服务 A 的后续请求也将成功，如下所示。

![访问多个服务流的应用示意图](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

或者, 如果应用最初请求 web 服务 A 的令牌, 则最终用户不会调用条件访问策略。 这使应用程序开发人员能够控制最终用户体验, 而不会在所有情况下强制调用条件性访问策略。 难用的情况是, 应用程序随后请求 web 服务 B 的令牌。此时, 最终用户需要符合条件性访问策略。 应用尝试 `acquireToken` 时，可能会生成以下错误（如下图所示）：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![访问多个请求新令牌的服务的应用](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

如果应用使用 ADAL 库，将始终以交互方式重新尝试获取令牌并失败。 当发生此交互式请求时, 最终用户有机会符合条件性访问。 除非请求为`AcquireTokenSilentAsync` , 或者`PromptBehavior.Never`在这种情况下, 应用需要执行交互式```AcquireToken```请求, 以向最终用户提供符合策略的机会。

## <a name="scenario-single-page-app-spa-using-adaljs"></a>场景：使用 ADAL.js 的单页应用 (SPA)

在此方案中, 我们将演练使用单页面应用 (SPA) 的情况, 并使用 ADAL 调用条件访问受保护的 web API。 这是一个简单的体系结构, 但在围绕条件性访问进行开发时需要考虑一些细节。

在 ADAL.js 中，有多个获取令牌的函数：`login()`、`acquireToken(...)`、`acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)`。

* `login()`通过交互式登录请求获取 ID 令牌, 但不获取任何服务的访问令牌 (包括条件访问受保护的 web API)。
* 然后可以使用 `acquireToken(…)` 以无提示方式获取访问令牌，这意味着在任何情况下它都不会显示 UI。
* `acquireTokenPopup(…)` 和 `acquireTokenRedirect(…)` 用于以交互方式请求资源的令牌，这意味着它们始终会显示登录 UI。

应用需要访问令牌来调用 Web API 时，它尝试 `acquireToken(…)`。 如果令牌会话已过期, 或者我们需要符合条件性访问策略, 则*acquireToken*函数会失败, 并且应用程序将使用`acquireTokenPopup()`或`acquireTokenRedirect()`。

![使用 ADAL 流的单页应用示意图](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

我们来看一个示例, 介绍如何使用条件性访问方案。 最终用户刚刚登录网站，且没有会话。 我们执行一个 `login()` 调用，未通过多重身份验证获取了 ID 令牌。 然后用户点击一个按钮，要求应用从 Web API 请求数据。 应用尝试执行`acquireToken()`调用但失败, 因为用户尚未执行多重身份验证, 并且需要符合条件性访问策略。

Azure AD 发送回以下 HTTP 响应：

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

应用需要捕获 `error=interaction_required`。 然后应用程序可以在同一个资源上使用 `acquireTokenPopup()` 或 `acquireTokenRedirect()`。 用户被强制执行多重身份验证。 用户完成多重身份验证后，应用针对所请求资源签发一个新访问令牌。

若要尝试此应用场景，请参阅 [JS SPA 代理代码示例](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)。 此代码示例使用前面使用 JS SPA 注册的条件性访问策略和 web API 来演示此方案。 它演示了如何正确处理声明质询并获取可用于 Web API 的访问令牌。 或者，请查看常规的 [Angular.js 代码示例](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)，获取 Angular SPA 方面的指南。

## <a name="see-also"></a>请参阅

* 若要详细了解这些功能，请参阅 [Azure Active Directory 中的条件访问](../active-directory-conditional-access-azure-portal.md)。
* 若要获取更多 Azure AD 代码示例，请参阅 [GitHub 存储库的代码示例](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)。
* 有关 ADAL SDK 和访问参考文档的详细信息，请参阅[库指南](active-directory-authentication-libraries.md)。
* 若要详细了解多租户应用场景，请参阅[如何使用多租户模式让用户进行登录](howto-convert-app-to-be-multi-tenant.md)。
