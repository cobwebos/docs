---
title: 如何在 Chrome 浏览器中处理 SameSite cookie 更改 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何在 Chrome 浏览器中处理 SameSite cookie 更改。
services: active-directory
documentationcenter: ''
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 8fc1fab89a89fbf7e20414f292a1b02f77ac7907
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776360"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>在 Chrome 浏览器中处理 SameSite Cookie 更改

## <a name="what-is-samesite"></a>什么是 SameSite？

`SameSite` 是一种属性，可在 HTTP cookie 中进行设置，以防止 web 应用程序中出现跨站点请求伪造（CSRF）攻击：

- 将 `SameSite` 设置为**宽松**时，cookie 将在同一站点中的请求和来自其他站点的 GET 请求中发送。 它不是以跨域的 GET 请求发送的。
- 值为 "**严格**" 可确保 cookie 仅在同一站点中的请求中发送。

默认情况下，不会在浏览器中设置 `SameSite` 值，因此在请求中发送的 cookie 没有限制。 应用程序需要根据其要求设置**宽松**或**严格**，来选择 CSRF 保护。

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite 更改和对身份验证的影响

[SameSite 上标准的](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)最新更新建议在未将任何值设置为宽松时，通过使默认行为 `SameSite` 来保护应用。 这是一种缓解方法，这意味着 cookie 将受到限制，但不会从其他站点进行访问。 此外，会引入值**None** ，以消除对发送的 cookie 的限制。 这些更新将在即将发布的 Chrome 浏览器版本中发布。

当 web 应用使用响应模式 "form_post" 向 Microsoft 标识平台进行身份验证时，登录服务器使用 HTTP POST 对应用程序进行响应，以发送令牌或授权代码。 由于此请求是一个跨域请求（从 `login.microsoftonline.com` 到你的域 https://contoso.com/auth) 例如，你的应用设置的 cookie 现在低于 Chrome 中的新规则。 跨站点方案中需要使用的 cookie 是保存*状态*和*nonce*值的 cookie，这些值也是在登录请求中发送的。 Azure AD 丢弃其他 cookie 来保存会话。

如果不更新 web 应用，这种新行为将导致身份验证失败。

## <a name="mitigation-and-samples"></a>缓解和示例

若要解决身份验证失败，通过 Microsoft 标识平台进行身份验证的 web 应用可以将 `SameSite` 属性设置为在 Chrome 浏览器上运行时在跨域方案中使用的 cookie `None`。
其他浏览器（有关完整列表，请参阅[此处](https://www.chromium.org/updates/same-site/incompatible-clients)）按照 `SameSite` 的以前行为进行，并且在设置 `SameSite=None` 时不包括 cookie。
这就是，为了在多个浏览器上支持身份验证，web 应用程序必须将 `SameSite` 值设置为仅 `None` Chrome 上，并在其他浏览器上保留值为空。

下面的代码示例演示了此方法。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

下表显示了在我们的 ASP.NET 和 ASP.NET Core 示例中解决 SameSite 更改的拉取请求。

| 示例 | 拉取请求 |
| ------ | ------------ |
|  [ASP.NET Core Web 应用增量教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [同一站点 cookie 修补 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [同一站点 cookie 修补 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-dotnet-由管理员限制的作用域-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [同一站点 cookie 修补 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

有关如何处理 ASP.NET 和 ASP.NET Core 中的 SameSite cookie 的详细信息，另请参阅：

- [在 ASP.NET Core 中使用 SameSite cookie](https://docs.microsoft.com/aspnet/core/security/samesite) 。
- [SameSite 的 ASP.NET 博客](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="pythontabpython"></a>[Python](#tab/python)

| 示例 |
| ------ |
|  [ms-identity-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="javatabjava"></a>[Java](#tab/java)

| 示例 | 拉取请求 |
| ------ | ------------ |
|  [ms-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [同一站点 cookie 修补 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [同一站点 cookie 修补 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>后续步骤

详细了解 SameSite 和 Web 应用方案：

> [!div class="nextstepaction"]
> [SameSite 上的 Google Chrome 常见问题](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite 页](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [方案：登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)