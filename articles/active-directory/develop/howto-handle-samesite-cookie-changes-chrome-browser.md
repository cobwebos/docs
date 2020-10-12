---
title: 如何在 Chrome 浏览器中处理 SameSite Cookie 更改 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何在 Chrome 浏览器中处理 SameSite Cookie 更改。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 5742ddf9553c3ac9187dbef93fc7927564cbc095
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116965"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>在 Chrome 浏览器中处理 SameSite Cookie 更改

## <a name="what-is-samesite"></a>什么是 SameSite？

`SameSite` 是一个属性，可在 HTTP Cookie 中设置该属性，以防止 Web 应用程序遭受跨站点请求伪造 (CSRF) 攻击：

- 当 `SameSite` 设置为 **Lax** 时，将在同一站点内的请求中以及来自其他站点的 GET 请求中发送 Cookie。 不会在跨域的 GET 请求中发送 Cookie。
- 使用 **Strict** 值可确保仅在同一站点内的请求中发送 Cookie。

默认情况下，不会在浏览器中设置 `SameSite` 值，正因如此，在请求中发送的 Cookie 没有限制。 应用程序需要根据要求设置 **Lax** 或 **Strict** 来启用 CSRF 保护。

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite 更改以及对身份验证的影响

最近[对 SameSite 标准所做的更新](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)提议在未将任何值设置为 Lax 时，通过产生默认的 `SameSite` 行为来保护应用。 此缓解措施意味着，HTTP 请求（从其他站点发出的 GET 除外）中的 Cookie 将受到限制。 此外，引入了 **None** 值来消除对所发送 Cookie 的限制。 这些更新即将在下一个 Chrome 浏览器版本中发布。

当 Web 应用使用响应模式“form_post”通过 Microsoft 标识平台进行身份验证时，登录服务器将使用 HTTP POST 来响应应用程序，以发送令牌或授权代码。 由于这是一个跨域请求（从 `login.microsoftonline.com` 发送到域 - 例如 ）`https://contoso.com/auth`，因此应用设置的 Cookie 现在需要遵守 Chrome 中的新规则。 需要在跨站点方案中使用的 Cookie 是保留 *state* 和 *nonce* 值的 Cookie，它们也在登录请求中发送。 Azure AD 会丢弃其他一些 Cookie 来保留会话。

如果不更新 Web 应用，这种新行为会导致身份验证失败。

## <a name="mitigation-and-samples"></a>缓解措施和示例

若要解决身份验证失败，在 Chrome 浏览器上运行时，对于在跨域方案中使用的 Cookie，通过 Microsoft 标识平台进行身份验证的 Web 应用可将 `SameSite` 属性设置为 `None`。
其他浏览器（参阅[此处](https://www.chromium.org/updates/same-site/incompatible-clients)的完整列表）遵循以前的 `SameSite` 行为，并且在设置了 `SameSite=None` 时不包含 Cookie。
正因如此，为了在多个浏览器中支持身份验证，Web 应用只能在 Chrome 中将 `SameSite` 值设置为 `None`，在其他浏览器中则保留空值。

以下代码示例演示了此方法。

# <a name="net"></a>[.NET](#tab/dotnet)

下表显示了可以解决 ASP.NET 和 ASP.NET Core 示例中的 SameSite 更改的拉取请求。

| 示例 | 拉取请求 |
| ------ | ------------ |
|  [ASP.NET Core Web 应用增量教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [SameSite Cookie 修复 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [SameSite Cookie 修复 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [SameSite Cookie 修复 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

有关如何处理 ASP.NET 和 ASP.NET Core 中的 SameSite Cookie 的详细信息，另请参阅：

- [使用 ASP.NET Core 中的 SameSite Cookie](/aspnet/core/security/samesite)
- [有关 SameSite 问题的 ASP.NET 博客](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| 示例 |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| 示例 | 拉取请求 |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [SameSite Cookie 修复 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [SameSite Cookie 修复 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>后续步骤

详细了解 SameSite 和 Web 应用方案：

> [!div class="nextstepaction"]
> [Google Chrome 中的 SameSite 常见问题解答](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite 页](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [场景：可将用户登录的 Web 应用](scenario-web-app-sign-user-overview.md)