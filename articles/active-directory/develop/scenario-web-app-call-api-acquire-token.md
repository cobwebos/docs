---
title: 调用 Web API 的 Web 应用（获取应用的令牌）- Microsoft 标识平台
description: 了解如何生成调用 Web API 的 Web 应用（获取应用的令牌）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860620"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>调用 Web API 的 Web 应用 - 获取应用的令牌

既然已生成客户端应用程序对象，就可以使用它来获取用于调用 web API 的令牌。 在 ASP.NET 或 ASP.NET Core 中，在控制器中调用 web API。 具体操作是：

- 使用令牌缓存获取 Web API 的令牌。 若要获取此令牌，请`AcquireTokenSilent`调用。
- 使用访问令牌调用受保护的 API。

## <a name="aspnet-core"></a>ASP.NET Core

控制器方法受`[Authorize]`属性保护，该特性强制用户进行身份验证，以使用 Web 应用。 下面是调用 Microsoft Graph 的代码。

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

下面是 HomeController 操作的简化代码，该操作获取调用 Microsoft Graph 所需的令牌。

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

若要详细了解此方案所需的代码，请参阅[aspnetcore-webapp](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)教程的第2阶段（[2-1-Web 应用调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步骤。

还有很多其他复杂性，例如：

- 实现 Web 应用的令牌缓存（本教程介绍了几种实现）
- 当用户注销时，从缓存中删除帐户
- 调用多个 API，包括设置增量许可

## <a name="aspnet"></a>ASP.NET

ASP.NET 中的情况类似：

- 受 [授权] 属性保护的控制器操作提取控制器`ClaimsPrincipal`成员的租户 id 和用户 id。 （ASP.NET 使用`HttpContext.User`。）
- 然后，它会生成一个 MSAL.NET `IConfidentialClientApplication`。
- 最后，它调用`AcquireTokenSilent`机密客户端应用程序的方法。

此代码类似于为 ASP.NET Core 显示的代码。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-web-app-call-api-call-api.md)
