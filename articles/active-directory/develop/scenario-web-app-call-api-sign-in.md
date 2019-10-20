---
title: 用于调用 web Api 的 web 应用（登录）-Microsoft 标识平台
description: 了解如何生成可调用 web Api 的 Web 应用（登录）
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cb0190f76ddce79012a5bf97e2d813f40f9f018
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596367"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>用于调用 web Api 的 web 应用-登录

你已经了解如何将登录添加到 web 应用。 你将在登录[用户-添加登录的 Web 应用](scenario-web-app-sign-user-sign-in.md)中了解相关情况。

此处的区别在于，当用户从该应用程序或从任何应用程序中注销时，你想要从令牌缓存中删除与用户关联的令牌。

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>在注销后截获回调-单一登录

例如，你的应用程序可以在 `logout` 事件后截获，以清除与注销的帐户相关联的令牌缓存条目。Web 应用将在缓存中存储用户的访问令牌。 在 `logout` 回调后截获，使 web 应用程序能够从令牌缓存中删除用户。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

[WebAppServiceCollectionExtensions # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)的 `AddMsal()` 方法阐释了此机制。

为应用程序注册的**注销 Url**允许实现单一注销。Microsoft 标识平台 `logout` 终结点将调用注册到你的应用程序的**注销 URL** 。 如果从 web 应用或其他 web 应用或浏览器启动注销，则会发生此调用。 有关详细信息，请参阅[单一登录](v2-protocols-oidc.md#single-sign-out)。

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

RemoveAccountAsync [/TokenAcquisition # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)中提供了适用于的代码。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 示例不会在全局注销时删除缓存中的帐户

# <a name="javatabjava"></a>[Java](#tab/java)

Java 示例不会在全局注销时从缓存中删除帐户

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 示例不会在全局注销时删除缓存中的帐户

---

## <a name="next-steps"></a>后续步骤

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [正在获取 web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [正在获取 web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [正在获取 web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [正在获取 web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
