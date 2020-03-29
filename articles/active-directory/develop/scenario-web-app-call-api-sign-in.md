---
title: 注销时从令牌缓存中删除帐户 - Microsoft 标识平台 | Azure
description: 了解如何在注销时从令牌缓存中删除帐户
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
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758864"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>调用 Web API 的 Web 应用：从全局注销的令牌缓存中删除帐户

您学习了如何在[Web 应用中向登录用户的 Web 应用添加登录：登录和注销](scenario-web-app-sign-user-sign-in.md)。

对于调用 web api 的 web 应用，注销是不同的。 当用户从你的应用程序或从任何应用程序中注销时，你必须从令牌缓存中删除与该用户关联的令牌。

## <a name="intercept-the-callback-after-single-sign-out"></a>在单一注销后拦截回调

要清除与已注销帐户相关联的令牌缓存条目，应用程序可以拦截 `logout` 后事件。 Web 应用会在令牌缓存中存储每个用户的访问令牌。 通过拦截 `logout` 后回调，Web 应用程序可以从缓存中删除用户。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

对于 ASP.NET Core，[WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157) 的 `AddMsal()` 方法中说明了拦截机制。

您以前为应用程序注册的注销 URL 使您能够实现单一注销。Microsoft 标识平台`logout`终结点调用注销 URL。 如果注销是从你的 Web 应用或者另一 Web 应用或浏览器启动的，则会发生此调用。 有关详细信息，请参阅[单一注销](v2-protocols-oidc.md#single-sign-out)。

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

[Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288) 中提供了 `RemoveAccountAsync` 的代码。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 示例不会在全局注销时删除缓存中的帐户。

# <a name="java"></a>[Java](#tab/java)

Java 示例不会在全局注销时删除缓存中的帐户。

# <a name="python"></a>[Python](#tab/python)

Python 示例不会在全局注销时删除缓存中的帐户。

---

## <a name="next-steps"></a>后续步骤

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
